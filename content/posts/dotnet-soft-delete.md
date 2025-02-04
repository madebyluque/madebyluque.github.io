+++
title = "Uma forma simples de realizar soft delete em .Net utilizando Entity Framework Core"
date = "2024-01-19T12:03:34-03:00"
#dateFormat = "YYYY-MM-DD" # This value can be configured for per-post date formatting
author = "Lucas Silva"
authorTwitter = "" #do not include @
cover = ""
tags = ["dotnet", "ef"]
keywords = ["", ""]
description = "Você pode até chamar de anti-pattern, mas vai precisar implementar algum dia"
showFullContent = false
readingTime = false
hideComments = false
+++

Ao desenvolver APIs, é comum ter de alterar o comportamento de DELETE para que, em vez de excluir o registro, simplesmente alterar uma propriedade booleana e considerar que aquele recurso não existe caso o valor da propriedade seja _false_. Essa técnica é conhecida como **"Soft Delete"**.

Há várias formas de implementar esse comportamento. A forma apresentada neste texto pode ser resumida aos seguintes passos:
1- Adicionar uma propriedade booleana à classe desejada;
2- Sobrescrever o método **SaveChangesAsync** da classe que estende **DbContext**;
3- Adicionar um filtro na configuração da entidade para que as entidades "inativas" não sejam retornadas em queries.

## Adicionar propriedade booleana
O primeiro passo é autoexplicativo. Crie uma entidade e adicione a propriedade booleana a ela. Caso várias entidades precisem desse comportamento, considere criar uma classe base ou interface que a contenha.

## Sobrescrever o método SaveChangesAsync
Se você utiliza o EF, é provável que tenha estendido a classe **DbContext** para criar um contexto próprio. A classe base possui um método chamado **SaveChangesAsync**, o qual salva as alterações feitas no banco de dados.  
A alteração que será realizada no método visa detectar o estado das  entidades rastreadas pelo [ChangeTracker](https://learn.microsoft.com/en-us/ef/core/change-tracking/). Caso alguma tenha sido deletada, será necessário mudar o estado dela para **Unchanged** (em vez de **Deleted**), e então alterar a propriedade booleana adicionada no passo anterior.

```
public override Task<int> SaveChangesAsync(bool acceptAllChangesOnSuccess, CancellationToken cancellationToken = default)
{
    foreach (var entry in ChangeTracker.Entries<T>()) // Onde "T" é o tipo da entidade
    {
        switch (entry.State)
        {
            case EntityState.Deleted:
                entry.State = EntityState.Unchanged;
                entry.Entity.PropriedadeBooleana = false; // Prefira utilizar um método para isso.
                break;
        }
    }
    return base.SaveChangesAsync(acceptAllChangesOnSuccess, cancellationToken);
}
```

## Adicionar filtro
Com a alteração feita, já é possível alterar todas as queries para que elas deconsiderem entidades às quais o valor da propriedade booleana seja **false**. Entretanto, dependendo do tamanho da sua aplicação, essa tarefa poderá ser bastante trabalhosa.

Para evitar a situação descrita acima, o EF fornece uma forma de realizar isso de forma automática através da configuração da entidade. Exemplo:
```
public class SuaEntidadeConfiguration: IEntityTypeConfiguration<SuaEntidade>
{
    public void Configure(EntityTypeBuilder<SuaEntidade> builder)
    {
        builder.HasQueryFilter(x => x.Active);
    }
}
```

O código acima faz com que somente as entidades que possuam a propriedade **Active** como **true** sejam retornadas em caso de queries. 

Chegamos ao fim do tutorial. Espero que este texto tenha sido o suficiente para a implementação de um comportamento básico de **Soft delete** na sua aplicação. 
