+++
title = "Como usar Redis em uma solução .Net"
date = "2021-11-29T11:41:55-03:00"
#dateFormat = "YYYY-MM-DD" # This value can be configured for per-post date formatting
author = "Lucas Silva"
authorTwitter = "" #do not include @
cover = ""
tags = ["dotnet", "redis"]
keywords = ["", ""]
description = "Adicione cache distribuído com Redis de forma rápida"
showFullContent = false
readingTime = false
hideComments = false
+++

Olá! 

Faz algum tempo que não escrevo por aqui mas senti saudades disso. Desde que escrevi meu último artigo grande ([uma introdução sobre flutter](https://dev.to/cephalopodluke/introducao-a-programacao-com-dart-aji)), comecei a trabalhar com .Net e C#. É possível ver algumas semelhanças em ambas linguagens e isso facilitou bastante meu aprendizado nela.

Bem, tenho desenvolvido algumas APIs e trabalhado com alguns problemas envolvendo implantações de aplicações, bem coisa de backend mesmo. Um dos primeiros problemas que precisei resolver foi implementar uma forma de uso de cache utilizando Redis em uma API. 

Apenas para esclarecimento, cache é um local de armazenamento de dados temporários que servirão para ajudar aplicações web, mobile etc. Implementar cache na sua solução pode trazer diversos benefícios como economizar banda, reduzir tempo de resposta, realizar menos chamadas ao banco em alguns casos específicos etc. Entretanto também pode causar inúmeros problemas. Um deles é o de os dados não estarem mais atualizados em soluções que esses dados mudam frequentemente. 

Criei um código para esse artigo que pode ser encontrado [nesse repositório do GitHub](https://github.com/lukelima/ExemploRedis). Nele, criei consumi a [PokéApi](https://pokeapi.co/), uma API com dados de quase todos os pokémons lançados até hoje. Segui a política de fair use dela que diz _"Locally cache resources whenever you request them."_ (armazene recursos em cache quando os consultar) e salvei todas as requisições em cache por um tempo limitado. 

A estrutura de pastas do projeto ficou da seguinte forma:za
```
ExemploRedis/
├─ Controllers/
│  ├─ PokemonController.cs
├─ Extensions/
│  ├─ DistributedCacheExtension.cs
├─ Services/
│  ├─ Interfaces/
│  │  ├─ ICacheService.cs
│  │  ├─ IPokemonService.cs
│  ├─ PokemonCacheService.cs
│  ├─ PokemonService.cs
├─ Pokemon.cs
```
O arquivo **Pokemon.cs** contém as informações de Pokémons da API. Para a simplicidade do exemplo, adicionei apenas 3 propriedades:
```
public class Pokemon
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Weight { get; set; }
}
```
Adicionei o pacote **Microsoft.Extensions.Caching.Redis** NuGet para trabalhar com o Redis. Com ele, criei a extensão **Extensions/DistributedCacheExtension.cs** para adicionar o serviço à API:
```
public static IServiceCollection AddDistributedCache(
    this IServiceCollection services,
    IConfiguration configuration)
{
    services.AddDistributedRedisCache(options =>
    {
        options.Configuration = 
            configuration.GetConnectionString("Redis");
        options.InstanceName = 
            configuration["Redis:InstanceName"];
    });
    return services;
}
```

As options da configuração são autoexplicativas: string de conexão ao serviço e nome da instância.
Adicionei essa extensão no arquivo Startup.cs, no método **ConfigureServices**, com a seguinte linha de código:
```
services.AddDistributedCache(Configuration);
```
Com o Redis configurado, desenvolvi um serviço que me auxiliaria a buscar e adicionar dados a ele visando evitar repetição de código. 
```
public interface ICacheService<T>
{
    Task<T> Get(int id);
    Task Set(T content);
}
```
O código acima é da interface do serviço. Decidi usar um tipo genérico nela para que ela seja reaproveitada para qualquer objeto que eu precisar. O serviço que a implementa ficou assim:
```
public class PokemonCacheService : ICacheService<Pokemon>
{
    private readonly IDistributedCache _distributedCache;
    private readonly DistributedCacheEntryOptions _options;
    private const string Prefix = "pokemon_";

    public PokemonCacheService(IDistributedCache distributedCache)
    {
        _distributedCache = distributedCache;
        _options = new DistributedCacheEntryOptions
        {
            AbsoluteExpirationRelativeToNow = 
                TimeSpan.FromSeconds(120),
            SlidingExpiration = TimeSpan.FromSeconds(60)
        };
    }

    public async Task<Pokemon> Get(int id)
    {
        var key = Prefix + id;
        var cache = await _distributedCache.GetStringAsync(key);
        if (cache is null)
        {
            return null;
        }
        var pokemon = JsonConvert.DeserializeObject<Pokemon> 
            (cache);
        return pokemon;
    }

    public async Task Set(Pokemon content)
    {
        var key = Prefix + content.Id;
        var pokemonString = JsonConvert.SerializeObject(content);
        await _distributedCache.SetStringAsync(key, pokemonString, 
            _options);
    }
}
```
Passando por todos os pontos:
```
private readonly IDistributedCache _distributedCache;
private readonly DistributedCacheEntryOptions _options;
private const string Prefix = "pokemon_";

public PokemonCacheService(IDistributedCache distributedCache)
{
    _distributedCache = distributedCache;
    _options = new DistributedCacheEntryOptions
    {
        AbsoluteExpirationRelativeToNow =   
            TimeSpan.FromSeconds(120),
        SlidingExpiration = TimeSpan.FromSeconds(60)
    };
}
```
Os dois primeiros campos, **_distributedCache** e **_options** estão relacionados diretamente à configuração do Redis. **IDistributedCache** é a interface que utilizei para acessar o Redis através de injeção de dependência. Já **DistributedCacheEntryOptions** é a classe responsável por configurar opções como **AbsoluteExpirationRelativeToNow** e **SlidingExpiration**, que referem-se ao tempo total que um dado ficará armazenado e ao tempo que ele pode ficar armazenado sem ser consultado (nunca maior que o tempo absoluto), respectivamente. **Prefix** refere-se ao prefixo que utilizo para armazenar os pokemons. Utilizo ele + o id do pokemon para armazenar os dados com chaves únicas -- o que facilita a consulta posterior.

Sobre o método Get:
```
public async Task<Pokemon> Get(int id)
{
    var key = Prefix + id;
    var cache = await _distributedCache.GetStringAsync(key);
    if (cache is null)
    {
        return null;
    }
    var pokemon = JsonConvert.DeserializeObject<Pokemon>(cache);
    return pokemon;
}
```
Nele, crio a chave como prefixo + id do pokemon e busco o dado no cache utilizando o método **GetStringAsync(key)** da interface IDistributedCache. Se ele for nulo, retorno nulo (ou poderia notificar um erro ou lançar uma exceção). Caso o contrário, deserializo a string obtida em pokemon e retorno o valor.

Sobre o método Set:
```
public async Task Set(Pokemon content)
{
    var key = Prefix + content.Id;
    var pokemonString = JsonConvert.SerializeObject(content);
    await _distributedCache.SetStringAsync(key, pokemonString, 
        _options);
}
```
Nele, crio a chave da mesma forma, com o prefixo + o id do pokemon informado, então serializo o objeto em string e armazeno no Redis utilizando o método **SetStringAsync()** da interface IDistributedCache. Esse método recebe a chave que será utilizada para armazenamento (a mesma a ser consultada depois), o dado que será armazenado e as opções de armazenamento (lembra que configurei a expiração dos dados no construtor ? Pois bem, é aqui que informo essa configuração).

Com o serviço de cache pronto, desenvolvi o serviço de consulta à PokéApi. Interface:
```
public interface IPokemonService
{
    Task<Pokemon> GetPokemon(int id);
}
```
E o serviço que a implementa:
```
public class PokemonService : IPokemonService
{
    private readonly HttpClient _httpClient;

    public PokemonService(HttpClient httpClient)
    {
        _httpClient = httpClient;
        _httpClient.BaseAddress = new 
            Uri("https://pokeapi.co/api/v2/");
    }

    public async Task<Pokemon> GetPokemon(int id)
    {
        var response = await 
            _httpClient.GetAsync($"pokemon/{id}");
        var content = await response.Content.ReadAsStringAsync();
        var pokemon = JsonConvert.DeserializeObject<Pokemon> 
            (content);
        return pokemon;
    }
}
```
O serviço é bem simples: possui um campo HttpClient que foi injetado e o método GetPokemon(int id), que faz a chamada à API e retorna um pokémon. Adicionei o HttpClient ao startup utilizando a seguinte linha de código no arquivo **Startup.cs**:
```
    services.AddHttpClient<IPokemonService, PokemonService>();
```
É importante notar que esse método adiciona tanto o client como o serviço a ser injetado por dependência.

Com tudo pronto, criei um controller para servir esses dados:
```
[ApiController]
[Route("api/[controller]")]
public class PokemonController : ControllerBase
{
    private readonly IPokemonService _pokemonService;
    private readonly ICacheService<Pokemon> _pokemonCacheService;

    public PokemonController(IPokemonService pokemonService, 
        ICacheService<Pokemon> pokemonCacheService)
    {
        _pokemonService = pokemonService;
        _pokemonCacheService = pokemonCacheService;
    }

    [HttpGet("{id}")]
    public async Task<IActionResult> Get(int id)
    {
        Pokemon pokemon = await _pokemonCacheService.Get(id);
        if (pokemon is null)
        {
            pokemon = await _pokemonService.GetPokemon(id);
            await _pokemonCacheService.Set(pokemon);
        }
        return Ok(pokemon);
    }
}
```
Nele, injeto tanto o PokemonService quanto o CacheService pelo construtor. Como essa Api faz apenas a pesquisa de pokemons, o controller possui apenas 1 método: Get. Ele recebe um id na rota, então verifica se há no cache um pokemon com esse id. Se houver, ele retorna o pokémon. Caso não haja, ele chama o service que consultará a API e então devolve o pokémon. 

E esse é o fim do tutorial. Lembre-se de baixar o repositório no [GitHub](https://github.com/lukelima/ExemploRedis) caso queira estudá-lo melhor. Caso queira, deixei um arquivo docker-compose.yml para facilitar o uso do Redis.