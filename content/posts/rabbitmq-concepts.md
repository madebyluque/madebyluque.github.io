+++
title = "RabbitMQ: conceitos fundamentais"
date = "2025-01-12T11:59:49-03:00"
#dateFormat = "YYYY-MM-DD" # This value can be configured for per-post date formatting
author = "Lucas Silva"
authorTwitter = "" #do not include @
cover = ""
tags = ["rabbitmq", "system-design"]
keywords = ["", ""]
description = "Conceitos fundamentais para não fazer feio ao usar o RabbitMQ"
showFullContent = false
readingTime = false
hideComments = false
+++

O RabbitMQ é um dos message brokers mais populares, conhecido por sua flexibilidade e robustez no processamento de mensagens. Este artigo apresenta os principais conceitos do RabbitMQ, incluindo publishers, consumers, exchanges, filas e mecanismos de reconhecimento de mensagens.

![Fluxo básico de um sistema de mensageria](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/k8nuljmbh6sgc3lot1ec.png)

## Publisher/Producer

Uma aplicação **publisher** é responsável por publicar ou produzir mensagens. Além disso, uma aplicação que publica mensagens também pode consumi-las.  
No RabbitMQ, uma mensagem publicada precisa ser roteada para uma fila. Se houver consumidores online conectados à fila, a mensagem será enviada diretamente para eles.

## Consumer

Um **consumer** é uma aplicação que consome mensagens de uma fila e realiza o reconhecimento (acknowledgement) dessas mensagens. Da mesma forma que um publisher pode consumir mensagens, um consumer também pode publicá-las.

## Exchanges

No RabbitMQ, as mensagens não são enviadas diretamente para as filas. Os publishers enviam mensagens para um **exchange**, que é responsável por rotear as mensagens para filas específicas, com base nas ligações (*bindings*) e chaves de roteamento (*routing keys*).

### Tipos de Exchanges

Os exchanges são categorizados de acordo com a forma como realizam o roteamento de mensagens:

- **Direct**: As mensagens são roteadas para filas cuja ligação (*binding*) coincida exatamente com a chave de roteamento (*routing key*).

![Exemplo de direct exchange](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zf2ox2jyw1kl1tatvn7g.png)


- **Fanout**: Roteia mensagens para todas as filas que estejam conectadas a ele.

![Exemplo de exchange fanout](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/57caqfmlmb5rq06ppxol.png)


- **Topic**: Faz um *match* com base em padrões (*wildcards*) entre a chave de roteamento da mensagem e a chave de ligação das filas.

![Exemplo de exchange baseada em tópicos](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/iofrflnzwugjrhy3eksq.png)


- **Headers**: Roteia mensagens com base nos cabeçalhos especificados na mensagem.

![Exmplo de exchange baseada em headers](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vsjsi550nc2d9ybmugog.png)

## Queues

As **filas** no RabbitMQ são coleções ordenadas de mensagens, e elas seguem uma lógica de *First In, First Out* (FIFO) para o enfileiramento e desenfileiramento. Cada mensagem é processada em ordem, garantindo consistência no consumo.

## Acknowledgement

O **acknowledgement** é o mecanismo de confirmação de que uma mensagem foi entregue e/ou processada pelo consumidor. Ele permite que o RabbitMQ saiba quando pode marcar uma mensagem como concluída ou quando deve reenviar a mensagem.

### Delivery Acknowledgement

Existem diferentes formas de reconhecimento no RabbitMQ, que determinam quando uma mensagem é considerada processada com sucesso:

- **Reconhecimento Automático**: O RabbitMQ considera a mensagem processada assim que ela é entregue.
- **Reconhecimento Manual**: Requer que o consumidor informe explicitamente o status da mensagem. Isso pode ser feito de forma positiva ou negativa:
  - `basic.ack`: Usado para indicar que a mensagem foi processada com sucesso.
  - `basic.nack`: Usado para indicar que o processamento falhou.
  - `basic.reject`: Similar ao `basic.nack`, mas com limitações (não suporta múltiplas mensagens ao mesmo tempo).

Quando o reconhecimento negativo ocorre, a mensagem pode ser reenfileirada, roteada para um **dead letter exchange**, ou descartada, dependendo da configuração do campo `requeue`.

## Dead Letter

Mensagens podem ser marcadas como **dead-letter** e republicadas caso algum dos seguintes eventos ocorra:

1. A mensagem é negativamente reconhecida por um consumidor.
2. A mensagem expira devido ao TTL (*Time To Live*).
3. A mensagem excede o limite de tamanho configurado.
4. A mensagem ultrapassa o limite de tentativas de entrega.

Dead letters permitem lidar com falhas de forma estruturada, garantindo maior resiliência no sistema.

---

Compreender esses conceitos é essencial para projetar sistemas de mensageria eficientes e confiáveis. O RabbitMQ oferece flexibilidade para adaptar-se a diversas arquiteturas, tornando-o uma ferramenta poderosa no ecossistema de aplicações distribuídas.

## Referências
- [Documentação do RabbitMQ](https://www.rabbitmq.com/docs)