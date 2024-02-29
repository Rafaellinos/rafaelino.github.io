---
title: Garantias de entregas de mensageria
author: Rafael Lino
categories: [software]
tags: [kafka]
---

# Garantias de entrega de mensagens

Sistemas de mensageria como Kafka, RabbitMq e SQS (AWS) estão presentes há tempos no mercado, oferecendo a vantagem de 
desacoplamento entre sistemas e processamento assíncrono, além de outras características 

Distinguir entre os sistemas de mensageria e suas garantias é crucial para sua arquitetura, seja ela qual for, podendo depender 
de requisitos funcionais ou não.

Nesse artigo, abordarei sistemas de mensageria, em geral, sem aprofundar em nenhuma deles, pois, se trata de um resumo e introdução para entender as garantias de entregas de mensagens. O íntuito é nívelar a leitura, facilitando o entendo do conteúdo. 

## Terminologias

Abaixo algumas terminologias comuns em sistemas de mensageria:

- Terminologias:
  - Publisher/Producer: Sistema que produz uma mensagem
  - Subscriber/Consumer: Sistema que consumirá a mensagem
  - Broker: Message brokers é basicamente o "local" onde a mensagem ficará armazenada até ser deletada. Pode carregar significados diferentes a depender do software, Kafka, por exemplo, se refere a um unico servidor com partições e tópicos. 
  - Topic: Um tópico é o destino da mensagem para armazenamento, usualmente, termo utilizando em Pub/Subs. Tópicos são mais granulares que brokers, que podem conter vários topicos.
  - Partitions: Em sistemas de mensageria onde os dados são distribuídos, como Kafka, um tópico pode ter mensagens espalhadas em várias partições. Alguns sistemas, como Kinises, podem chamar Partitions de Shards, mas conceitualmente são o mesmo.
  - FIFO: Acrônimo de `First in, First out`. Sistemas FIFO são usados para manter a cronologia para a leitura das mensagens. Pense em uma fila de pessoas, onde o primeiro a entrar, será o primeiro a sair (__Ser atendido?__). 

## Tipos de Sistemas de mensageria

- Point-to-Point:
  - Descrição: Tipo mais simples de mensageria. Envolve enviar uma mesangem e apenas um consumidor a receberá, comumente chamado de `Message Queue`
  - Exemplos: AWS SQS, RabbitMQ, ActiveMQ
- Pub/Sub
  - Descrição: O modelo Publish e Subscribe consiste no envio de mensagens  