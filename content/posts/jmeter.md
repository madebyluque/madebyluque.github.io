+++
title = "Automatizando a criação de requisições no JMeter utilizando Postman"
date = "2022-07-18T11:49:46-03:00"
#dateFormat = "YYYY-MM-DD" # This value can be configured for per-post date formatting
author = "Lucas Silva"
authorTwitter = "" #do not include @
cover = ""
tags = ["dotnet", "tests"]
keywords = ["", ""]
description = "Facilitando a criação de requisições no JMeter"
showFullContent = false
readingTime = false
hideComments = false
+++

Faz um tempo que não escrevo por aqui, mas hoje, enquanto estudava para resolver um problema do trampo, me deparei com uma técnica que facilitou muito a forma de criar requisições no JMeter, então decidi compartilhá-la.

Antes de mais nada, quero agradecer aos meus amigos [Jurno](https://twitter.com/ojuninrocha) e [João](https://github.com/poferrari), que me guiaram na busca pela solução.

Sem mais delongas, bora pro problema.

## O problema
Imagine que em um determinado momento, você e seu time precisarão realizar um teste de carga na aplicação que vocês desenvolvem. Entretanto, vocês possuem pouco ou nenhum conhecimento com a ferramenta JMeter, uma das mais utilizadas para esse tipo de tarefa. 
Uma das possíveis soluções é gastar um bom tempo lendo a documentação para aprender a criar as requisições uma a uma. Inclusive, essa teria sido a minha decisão, não fosse pelos conselhos dos amigos citados acima.

## A solução
É possível adicionar um elemento ao plano de teste do JMeter chamado [**HTTP(S) Test Script Recorder**](https://jmeter.apache.org/usermanual/jmeter_proxy_step_by_step.html). Ele é responsável por gravar requisições http e transformá-las em uma HTTP Request (sampler). 
![Imagem da UI do JMeter mostrando o HTTPS Test Script Recorder](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6gkliswkgkqjfr4upy0i.png)
Com esse elemento adicionado, basta ajustar as configurações de proxy do Postman (ou qualquer outro sistema que você utiliza) para o endereço do Test Script Recorder (localhost:8888 por default), rodar o elemento e então realizar uma requisição no sistema a ser testado. 
![Configurações de proxy do postman](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rcnltqf07ag0br92nhlv.png)
Fiz uma requisição de exemplo na [PokéAPI](https://pokeapi.co/api/v2/pokemon/ditto), e eis a requisição montada automaticamente no JMeter:
![Interface do JMeter mostrando a requisição montada automaticamente](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/29p2sjk2d7iewegegped.png)

É possível que seja necessário realizar algumas alterações na requisição gerada, mas creio que o procedimento descrito acima seja um excelente ponto de entrada para requisições mais elaboradas.

Bem, caso haja alguma dúvida sobre o texto ou alguma sugestão de melhoria, peço que envie um comentário 😎.
Até a próxima!