+++
title = "TestContainers para testes de integração com .Net"
date = "2024-02-06T11:54:32-03:00"
#dateFormat = "YYYY-MM-DD" # This value can be configured for per-post date formatting
author = "Lucas Silva"
authorTwitter = "" #do not include @
cover = ""
tags = ["dotnet", "tests", "containers"]
keywords = ["", ""]
description = "Containers, containers, containers, containers..."
showFullContent = false
readingTime = false
hideComments = false
+++

## Introdução
Diferente de testes de unidade, os testes de integração permitem validar o comportamento de uma aplicação quando todos os componentes dela são utilizados em conjunto. Isso inclui bancos de dados, serviços de cache, serviços de mensageria etc. 

Na teoria, tudo parece interessante e simples. Mas esses testes podem gerar e alterar um grande volume de dados, então é necessário tomar cuidado com os recursos utilizados. Até porque acidentes acontecem, e talvez, em um descuido, você pode acabar executando um DELETE sem WHERE, levando à exclusão total de uma tabela. 😅

Para evitar esse tipo de problemas, é possível criar esses recursos a partir de containers Docker por meio da lib [TestContainers](https://testcontainers.com/).

Neste tutorial, explicarei os passos para a utilização desses containers em uma API .Net.

## API
O projeto completo pode ser encontrado [neste link](https://github.com/madebyluque/TutorialIntegrationTests). Trata-se de uma API de gerenciamento de tarefas (a famosa "To-Do list"). Ela consiste de basicamente 3 partes: 

Uma entidade:
```
namespace IntegrationTestingDemo.API;

public class Todo
{
    public Todo()
    {
    }

    public Todo(string title, string description)
    {
        Title = title;
        Description = description;
        Id = Guid.NewGuid().ToString().Replace("-", "");
        CreatedAt = DateTime.UtcNow;
        Done = false;
    }

    public string Id { get; set; }
    public string Title { get; set; }
    public string Description { get; set; }
    public bool Done { get; set; }
    public DateTime CreatedAt { get; set; }
    public DateTime? CompletedAt { get; set; }
}

```

Um service com a lógica da aplicação (visando a simplicidade, algumas operações não foram criadas):
```
public class TodoService(TodoContext context) : ITodoService
{
    private readonly TodoContext _context = context;

    public async Task<string> Create(string title, string description)
    {
        var todo = new Todo(title, description);
        await _context.AddAsync(todo);
        await _context.SaveChangesAsync();
        return todo.Id;
    }

    public async Task<List<Todo>> GetAll()
    {
        return await _context.Todos.OrderBy(x => x.CreatedAt).ToListAsync();
    }

    public async Task<Todo> GetById(string id)
    {
        return await _context.Todos.FirstOrDefaultAsync(x => x.Id == id);
    }
}
```

E um controller:
```
[ApiController]
[Route("[controller]")]
public class TodoController(ITodoService todoService) : ControllerBase
{
    private readonly ITodoService _todoService = todoService;

    [HttpPost]
    public async Task<IActionResult> Create([FromBody] CreateTodoModel model)
    {
        var result = await _todoService.Create(model.Title, model.Description);
        return CreatedAtRoute(nameof(GetById), routeValues: new { Id = result }, result);
    }

    [HttpGet]
    public async Task<IActionResult> GetAll()
    {
        var todos = await _todoService.GetAll();
        return Ok(todos);
    }

    [HttpGet("{id}", Name = "GetById")]
    public async Task<IActionResult> GetById(string id)
    {
        var todo = await _todoService.GetById(id);
        return Ok(todo);
    }
}
```

## Testes
A configuração de **TestContainer** é feita na criação da **WebApplicationFactory** para os testes de integração. Neste tutorial, decidi utilizar PostgreSQL. A criação de um container desse banco de dados pode ser feita da seguinte forma:
```
private readonly PostgreSqlContainer _postgres = new PostgreSqlBuilder().WithUsername("postgres").WithPassword("postgres").Build();
```
É possível alterar o usuário e a senha da forma que desejar. Há, inclusive, a opção de alterar outras configurações no builder, como o nome do db, o host etc.

Com o container criado, é possível obter a connection string dele da seguinte forma:
```
_postgres.GetConnectionString()
```
Pode ser necessário remover o dbContext da aplicação para adicionar um novo com a connection string do container de teste. Nesse caso, é possível fazê-lo da seguinte forma:
```
var context = services.FirstOrDefault(descriptor => descriptor.ServiceType == typeof(TodoContext));
if (context != null)
{
    services.Remove(context);
    var options = services.Where(r => (r.ServiceType == typeof(DbContextOptions))
      || (r.ServiceType.IsGenericType && r.ServiceType.GetGenericTypeDefinition() == typeof(DbContextOptions<>))).ToArray();
    foreach (var option in options)
    {
        services.Remove(option);
    }
}

services.AddDbContext<TodoContext>(options =>
{
    options.UseNpgsql(_postgres.GetConnectionString());
});
```

Por fim, é interessante que sua classe de **WebApplicationFactory** implemente a interface IAsyncLifetime para que o container criado seja inicializado / parado.
```
public Task InitializeAsync()
{
    return _postgres.StartAsync();
}

public new Task DisposeAsync()
{
    return _postgres.StopAsync();
}
```

Com a configuração feita, já é possível criar testes de integração. No teste abaixo, utilizei o TodoService para criar uma tarefa, e então verifiquei se os dados no banco de dados estavam de acordo com o que deveriam:
```
[Fact]
public async Task Create_ShouldCreateTodoAndReturnItsId()
{
    // Act
    var result = await _todoService.Create(TestTitle, TestDescription);

    // Assert
    var todo = await _dbContext.Todos.FirstOrDefaultAsync(x => x.Id == result);
    Assert.NotNull(todo);
    Assert.False(todo.Done);
    Assert.Equal(TestTitle, todo.Title);
    Assert.Equal(TestDescription, todo.Description);
}
```

(Acesse o [repositório](https://github.com/madebyluque/TutorialIntegrationTests) para verificar os demais testes.)

O exemplo acima testa uma classe simples. Entretanto, poderia testar uma classe mais complexa, como um Handler, que manipula os dados através de diversos objetos. Além disso, o service foi criado para não testar as chamadas diretas ao controller.

## GitHub actions
É possível integrar os testes à pipeline. [Essa postagem do Milan Jovanović](https://www.milanjovanovic.tech/blog/testcontainers-integration-testing-using-docker-in-dotnet) mostra como integrá-los a uma Github Action:
```
name: Run Tests 🚀

on:
  workflow_dispatch:
  push:
    branches:
      - main

jobs:
  run-tests:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Setup .NET
        uses: actions/setup-dotnet@v3
        with:
          dotnet-version: '8.0.x'

      - name: Restore
        run: dotnet restore ./IntegrationTestingDemo.sln

      - name: Build
        run: dotnet build ./IntegrationTestingDemo.sln --no-restore

      - name: Test
        run: dotnet test ./IntegrationTestingDemo.sln --no-build
```

---

Este é o fim do tutorial. Espero que esse texto tenha sido o suficiente para te ajudar a implementar testes de integração com TestContainers na sua aplicação. 

Até a próxima!