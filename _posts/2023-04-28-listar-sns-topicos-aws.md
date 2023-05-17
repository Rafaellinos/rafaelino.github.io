---
title: Como Listar SNS tópicos na AWS
author: Rafael Veloso Lino
categories: [AWS, Java]
tags: [aws, java]
---

<h1>Como listar SNS tópicos na AWS</h1>

Esse post é um tutorial de como listar os tópicos SNS na AWS utilizando o SDK Java.

<h2>Pré-requisitos</h2>

- Java 8+
- Maven 3.6.3+
- AWS CLI 2.0.0+
- AWS CLI configurado

<h2>Exemplo de código básico para listar:</h2>

```java
package org.example;
import software.amazon.awssdk.auth.credentials.ProfileCredentialsProvider;
import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.sns.SnsClient;
import software.amazon.awssdk.services.sns.model.ListTopicsRequest;
import software.amazon.awssdk.services.sns.model.ListTopicsResponse;
import software.amazon.awssdk.services.sns.model.SnsException;

public class Main {

    public static void listSNSTopics(SnsClient snsClient) {

        try {
            ListTopicsRequest request = ListTopicsRequest.builder().build();

            ListTopicsResponse result = snsClient.listTopics(request);
            Integer statusCode = result.sdkHttpResponse().statusCode();
            if (statusCode == 200) {
                System.out.println("Topics:");
                result.topics().forEach(topic -> System.out.println(topic.topicArn()));
            } else {
                System.out.println("Error: " + statusCode);
            }
        } catch (SnsException e) {
            System.err.println(e.awsErrorDetails().errorMessage());
            System.exit(1);
        }
    }


    public static void main(String[] args) {

        SnsClient snsClient = SnsClient.builder()
                .region(Region.US_EAST_1)
                .credentialsProvider(ProfileCredentialsProvider.create())
                .build();
        listSNSTopics(snsClient);
    }
}
```

<a href="https://github.com/Rafaellinos/aws-list-topics">Projecto no github</a>

<p>Não se esqueça de dar permissão para listar os tópicos, no caso no SNS, é necessário ter permissão para listar 
todos, caso contrário, provavelmente você irá receber um 401 ou 403.</p>

<h3>Exemplo de JSON para Role ter permissão </h3>

```json
{
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "SNS:ListTopics"
      ],
      "Resource": [
        "*" // necessário para listar todos os tópicos
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "SNS:GetTopicAttributes",
        "SNS:SetTopicAttributes",
        "SNS:AddPermission",
        "SNS:RemovePermission",
        "SNS:DeleteTopic",
        "SNS:Subscribe",
        "SNS:ListSubscriptionsByTopic",
        "SNS:Publish"
      ],
      "Resource": [
        "arn:aws:sns:us-east-1:<ACCOUNT-ID>:<TOPIC-NAME>"
      ]
    }
  ]
}
```
