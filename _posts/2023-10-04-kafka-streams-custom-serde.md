---
title: Kafka Streams Custom Serde
author: Rafael Lino
categories: [software]
tags: [kafka, java]
---

# Como criar um custom serde para Kafka Streams

<p>Se você não está familiarizado, o Kafka Streams é uma biblioteca fornecida pela Apache para interagir com o Kafka usando streams - pequenos pedaços de dados que são entregues ao longo do tempo e em tempo real.</p>

<p>Embora este post não ensine Kafka, aqui está uma breve introdução: no Kafka Streams, você pode construir "topologias", que são essencialmente o fluxo de transformação, processamento e filtragem de dados que você pode realizar usando KStreams e KTables.</p>

![kafka_simple_topology](/assets/img/kafka_example.svg){:class="img-responsive"}

<p>Na topologia acima, recebemos três mensagens de uma fonte (neste caso, um tópico), agrupamos por chave, agregamos em uma única mensagem e enviamos para um destino (outro tópico).</p>

<p>Para simplificar o trabalho com JSON, vamos trabalhar em HashMap. Vamos criar um Serde personalizado, incluindo serializadores e deserializadores, para ajudar na manipulação de JSON em formato de HashMap.</p>

> Serde significa "Serialization and Deserialization" (Serialização e Desserialização) e é responsável por traduzir os bytes do valor da mensagem para algum que consigamos trabalhar. Kafka possui alguns Serde built-in, como Serdes.String()

<p>Vamos usar o ObjectMapper da biblioteca Jackson JSON para simplificar as operações.</p>

## Serializador


```java
public class MapSerializer implements Serializer<HashMap<String, String>> {
    @Override
    public byte[] serialize(String s, HashMap<String, String> stringStringHashMap) {
        try {
            return new ObjectMapper().writeValueAsBytes(stringStringHashMap);
        } catch (JsonProcessingException ex) {
            throw new RuntimeException(ex);
        }
    }
}
```

## Deserializador

```java
public class MapDeserializer implements Deserializer<HashMap<String, String>> {

    @Override
    public HashMap<String, String> deserialize(String s, byte[] bytes) {
        try {
            return new ObjectMapper().readValue(
                    new String(bytes, StandardCharsets.UTF_8),
                    HashMap.class
            );
        } catch (JsonProcessingException ex) {
            throw new RuntimeException(ex);
        }
    }
}
```

<p>Por fim, nosso serde:</p>

## Serde HashMap

```java
public class MapSerde implements Serde<HashMap<String, String>> {

    private final MapDeserializer mapDeserializer = new MapDeserializer();
    private final MapSerializer mapSerializer = new MapSerializer();

    @Override
    public Serializer<HashMap<String, String>> serializer() {
        return mapSerializer;
    }

    @Override
    public Deserializer<HashMap<String, String>> deserializer() {
        return mapDeserializer;
    }
}
```

<p>Agora que temos o Serde, vamos aplicar na nossa topologia e processar o dados:</p>

```java
@Configuration
@RequiredArgsConstructor
public class ConsumerStream {
  
  private final StreamsBuilderFactoryBean streamsBuilderFactoryBean;

  @Value("${topic.name.consumer}")
  private String replyTopic;

  @PostConstruct
  public void init() {
    StreamsBuilder streamsBuilder = null;
    try {
      streamsBuilder = streamsBuilderFactoryBean.getObject();
    } catch (Exception e) {
      throw new RuntimeException("error to get stream builder", e);
    }
    if (streamsBuilder == null) {
      throw new RuntimeException("No builder for streams");
    }

    MapSerde mapSerde = new MapSerde();
    KStream<String, HashMap<String, String>> replyStream = streamsBuilder.stream(
        replyTopic, Consumed.with(Serdes.String(), mapSerde)
    );

    replyStream
        .groupByKey() // agrupar por chave
        .aggregate(
            HashMap::new,
            (key, value, aggregate) -> {
              aggregate.putAll(value); // agregar resultados
              return aggregate;
            },
            Named.as("data-grouped"),
            Materialized.with(Serdes.String(), mapSerde)
        )
        .toStream()
        .mapValues((k, v) -> {
          try {
            // transformaos em String para postar no sink e manter a mensagem no mesmo formato
            return new ObjectMapper().writeValueAsString(v); 
          } catch (JsonProcessingException ex) {
            throw new RuntimeException(ex);
          }
        })
        .to(replyTopic, Produced.with(Serdes.String(), Serdes.String()));
  }

}
```

Este post fornece um guia sobre como criar um Serde personalizado para o Kafka Streams, permitindo que você processe dados de forma eficiente em tempo real usando o Kafka. Se você tiver alguma dúvida ou encontrar informações incorretas sobre o Kafka, por favor, avise-me, e ficarei feliz em fazer as correções necessárias.