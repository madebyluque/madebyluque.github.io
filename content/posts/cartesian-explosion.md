+++
title = "EF Core - Explosão Cartesiana"
date = "2024-03-10T11:56:22-03:00"
#dateFormat = "YYYY-MM-DD" # This value can be configured for per-post date formatting
author = "Lucas Silva"
authorTwitter = "" #do not include @
cover = ""
tags = ["dotnet", "ef"]
keywords = ["", ""]
description = "Melhore a performance das suas queries e evite um problemão de leitura de dados"
showFullContent = false
readingTime = false
hideComments = false
+++

## Introdução
Atuei em vários projetos que utilizavam EF Core, e em uma determinada ocasião, encontrei, junto ao meu time, um problema que não fazia muito sentido: uma query simples, com poucos Includes, em tabelas que não possuíam tanto registros (quando comparada a outras tabelas do mesmo banco), estourava timeout. Após análise, o time entendeu que o problema ocorria devido a um fenômeno chamado **Explosão cartesiana**.

## O que é?
A **explosão cartesiana** ocorre quando, ao realizar queries com joins em propriedades de navegação de entidades no mesmo nível hierárquico em bancos relacionais, o banco de dados retorna um produto vetorial (cross product). Ou seja, cada linha de uma propriedade de navegação é combinada com cada linha da outra propriedade.

Esse comportamento pode gerar um número inesperado de combinações entre os dados, aumentando drasticamente o volume de informações trafegadas entre o banco de dados e a aplicação. **Consequentemente, isso pode afetar o desempenho da aplicação, aumentar os custos de infraestrutura e consumo de recursos, além de tornar a depuração e otimização mais complexa.**

## Como resolver?
Uma das melhores formas de lidar com isso é utilizando queries separadas para carregar os dados envolvidos. O EF Core oferece uma solução simples para isso através do método `AsSplitQuery()`, que carregará as coleções utilizando múltiplas queries, em vez de uma única query.

Os exemplos abaixo foram feitos com uma coleção de 100000 blogs armazenados em uma instância de SQL Server 2022, rodando através de um contêiner Docker.

### Exemplo sem AsSplitQuery

Código:

```
var withoutSplitQuery = context.Blogs
	.Include(b => b.Posts)
	.Include(b => b.BlogContributors)
		.ThenInclude(bc => bc.Contributor)
	.ToList();
```

Nesse exemplo, cada Blog possui uma coleção de Posts e uma de BlogContributors. Como ambas estão no mesmo nível, cada post será combinado com cada contribuinte, o que resulta em um número de linhas maior do que o necessário.

Query gerada:

```
SELECT [b].[BlogId], [b].[Name], [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title], [t].[BlogId], [t].[ContributorId], [t].[ContributorId0], [t].[Name]
      FROM [Blogs] AS [b]
      LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
      LEFT JOIN (
          SELECT [b0].[BlogId], [b0].[ContributorId], [c].[ContributorId] AS [ContributorId0], [c].[Name]
          FROM [BlogContributors] AS [b0]
          INNER JOIN [Contributors] AS [c] ON [b0].[ContributorId] = [c].[ContributorId]
      ) AS [t] ON [b].[BlogId] = [t].[BlogId]
      ORDER BY [b].[BlogId], [p].[PostId], [t].[BlogId], [t].[ContributorId]
```

Resultado na busca de 100000 registros:

```
*************************************************************************
Tempo de execução sem splitquery: 4526ms
Número de blogs retornados: 100000
*************************************************************************
```

### Exemplo com AsSplitQuery

Código:

```
var withSplitQuery = context.Blogs
	.Include(b => b.Posts)
	.Include(b => b.BlogContributors)
		.ThenInclude(bc => bc.Contributor)
	.AsSplitQuery()
	.ToList();
```

Queries geradas:

```
SELECT [b].[BlogId], [b].[Name]
      FROM [Blogs] AS [b]
      ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title], [b].[BlogId]
      FROM [Blogs] AS [b]
      INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
      ORDER BY [b].[BlogId]

SELECT [t].[BlogId], [t].[ContributorId], [t].[ContributorId0], [t].[Name], [b].[BlogId]
      FROM [Blogs] AS [b]
      INNER JOIN (
          SELECT [b0].[BlogId], [b0].[ContributorId], [c].[ContributorId] AS [ContributorId0], [c].[Name]
          FROM [BlogContributors] AS [b0]
          INNER JOIN [Contributors] AS [c] ON [b0].[ContributorId] = [c].[ContributorId]
      ) AS [t] ON [b].[BlogId] = [t].[BlogId]
      ORDER BY [b].[BlogId]
```

Resultado na busca de 100000 registros:

```
*************************************************************************
Tempo de execução com splitquery: 3664ms
Número de blogs retornados: 100000
*************************************************************************
```

Por mais que os exemplos acima tenham utilizado um número relativamente pequeno de dados, os resultados de 10 execuções do teste com dados diferentes foram consistentes: as buscas com queries separadas foram mais rápidas que as buscas com queries únicas. Inclusive, o próprio EF lança um erro ao executar uma query que possa causar uma explosão cartesiana:

```
Compiling a query which loads related collections for more than onecollection navigation, either via 'Include' or through projection, but no 'QuerySplittingBehavior' has been configured. By default, Entity Framework will use 'QuerySplittingBehavior.SingleQuery', which can potentially result in slow query performance.
```

> **Importante!**
> Há inúmeras variáveis que podem impactar na performance de uma query. Esse texto e os testes acima tratam exclusivamente sobre o problema de **explosão cartesiana.**

### Configuração via DbContext
O EF permite a configuração do comportamento como padrão. Para isso, basta configurar o DbContext:

```
optionsBuilder
    .UseSqlServer("ConnectionString", o =>
    .UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

## Fontes e recursos
- [How To Improve Performance With EF Core Query Splitting](https://www.milanjovanovic.tech/blog/how-to-improve-performance-with-ef-core-query-splitting);
- [Consultas únicas vs. consultas divididas](https://learn.microsoft.com/pt-br/ef/core/querying/single-split-queries);