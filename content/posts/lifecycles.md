+++
title = ".Net - Scoped, transient e singleton"
date = "2022-04-19T11:47:35-03:00"
#dateFormat = "YYYY-MM-DD" # This value can be configured for per-post date formatting
author = "Lucas Silva"
authorTwitter = "" #do not include @
cover = ""
tags = ["dotnet", "tips"]
keywords = ["", ""]
description = "Entenda os ciclos de vida dos objetos em .NET"
showFullContent = false
readingTime = false
hideComments = false
+++

Vejo bastante gente com dificuldade em entender a diferença entre os principais tipos de lifecycle quando falamos sobre injeção de dependência em .net. Criei um exemplo no [GitHub](https://github.com/lukelima/LifecycleExample.) e vou falar sobre ele.

Basicamente criei 3 serviços bastante simples e parecidos (apenas para fins didáticos) e os injetei utilizando AddSingleton, AddScoped e AddTransient.
![Injeção dos serviços no arquivo Program.cs](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8noxoqf10ivwjukmajcf.png)
 
Esses serviços só possuem uma propriedade, o Id, que é criada no momento da instanciação. 
Criei um controller e injetei os serviços por construtor.![Injeção dos serviços no construtor](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/gtxlecy6yja6fdi733us.png)

Também injetei uma instância do serviço em cada método.
![Injeção dos serviços em cada método](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/sbzeyh86fimx3xji9aeu.png)

## Singleton
O serviço é criado **uma única vez** e a mesma instância é passada para todas as chamadas dele. Veja que o id, criado no momento da instanciação, foi o mesmo (e continua sendo o mesmo independente da requisição): 
![Imagem mostra que uma única instância foi criada](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/djf0pqcgxgmr0k59iem2.png)

## Scoped
**Uma única instância é criada por requisição**. Então se o serviço for chamado em vários lugares em uma única requisição, a mesma instância será passada. Outras instâncias serão criadas para as próximas requisições.

Mesma requisição:
![Imagem mostra que uma única instância foi criada](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/wxkot9z4wtnarvh9hvxt.png)

Requisição diferente:
![Imagem mostra que uma nova instância foi criada para uma segunda requisição](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/tg603oilzwhz3luzoo6h.png)
  
## Transient
Ele é o mais custoso, pois se o serviço for chamado em diversas classes, **cada uma delas terá uma instância diferente**:
![Imagem mostra que uma instância foi criada para cada chamada do serviço](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6ynbb4lr6b78cbg1zx5t.png)
 