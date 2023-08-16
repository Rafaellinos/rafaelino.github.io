---
title: Design Pattern Singleton
author: Rafael Veloso Lino
categories: [oop, Java, Design Patterns]
tags: [oop]
---

# Padrão Singleton

O padrão Singleton é um dos padrões de design mais conhecidos e utilizados. Ele pode ser considerado simples de entender, mas sua implementação pode ser um pouco mais complexa. Neste artigo, vamos explorar o que é o padrão Singleton, seus tipos e como implementá-lo em Java.

## O que é o Padrão Singleton?

O objetivo do padrão Singleton é garantir uma única instância de um objeto em toda a aplicação, mesmo entre escopos e threads diferentes. Um exemplo comum é utilizar um Singleton para guardar um único estado por toda a aplicação, como Logging, Fábrica de objetos (Factory) ou acesso a banco de dados. Esse Singleton geralmente é utilizado como único ponto de acesso a algum outro recurso.

Em Java, utilizamos o método construtor como privado para não permitir criações e expomos um único método para obter a instância.

## Tipos de Singleton

### Eager Initialization

- Cria um único componente o mais cedo possível; por se tratar de um campo estático, a criação ocorre assim que é carregada na JVM.
- É Thread Safe.

```java
package org.example;

public class SingletonEagerInitialization {

    private static final SingletonEagerInitialization singletonEagerInitialization = new SingletonEagerInitialization();

    private SingletonEagerInitialization() {
    }

    public static SingletonEagerInitialization getInstance() {
        return singletonEagerInitialization;
    }
}
```

### Static Block Initialization

- Utiliza o static block do Java para criar a instância.
- No Java, blocos estáticos são executados assim que carregados na JVM e apenas uma vez.
- É igual ao Eager Initialization, com a diferença que com o block estático é possível colocar uma lógica complexa, como try/catch.
- É Thread Safe.

```java
package org.example;

public class SingletonStaticBlock {

    private static SingletonStaticBlock instance;

    static {
        instance = new SingletonStaticBlock();
    }

    private SingletonStaticBlock() {
    }

    public static SingletonStaticBlock getInstance() {
        return instance;
    }
}
```

### Lazy Initialization

- Inicia o componente quando é necessário, não carregando em memória sem necessidade.
- Útil quando temos algum processo envolvendo sua criação, podendo ser postergado apenas quando necessário.
- Não é Thread Safe, pode haver problema de concorrência na criação do objeto (condição de corrida).

```java
package org.example;

public class SingletonLazy {

    private SingletonLazy() {
    }

    private static SingletonLazy singletonLazy;

    public static SingletonLazy getInstance() {
        if (singletonLazy == null) {
            singletonLazy = new SingletonLazy();
        }
        return singletonLazy;
    }
}
```

- Para garantir que o Lazy seja Thread Safe e evitar dores de cabeça, faça uso da keyword \`synchronized\` no Java.

```java
package org.example;

public class SingletonLazyThreadSafe {

    private static SingletonLazyThreadSafe instance;

    private SingletonLazyThreadSafe() {

    }

    public static synchronized SingletonLazyThreadSafe getInstance() {
        if (instance == null) {
            System.out.println("some processing thread safe");
            instance = new SingletonLazyThreadSafe();
        }
        return instance;
    }
}
```

### Bill Pugh Initialization

- É um método de criação da instância de uma forma Lazy (apenas quando requerido) sem necessitar da keyword \`synchronized\`, usado em código legado principalmente.
- Também pode ser usado de forma a evitar o overhead na sincronização das threads (bloqueante/synchronized).

```java
package org.example;

public class SingletonBillPugh {

    private SingletonBillPugh() {

    }

    private static class InnerStaticClass {
        private static final SingletonBillPugh instance = new SingletonBillPugh();
    }

    public static SingletonBillPugh getInstance() {
        return InnerStaticClass.instance;
    }
}
```

## Conclusão

O padrão Singleton é uma ferramenta poderosa e versátil que pode ser aplicada em várias situações. Com os exemplos acima, você deve estar bem equipado para implementar esse padrão em seus próprios projetos. Se você tiver alguma dúvida ou comentário, sinta-se à vontade para deixá-los abaixo!

[Respositorio](https://github.com/Rafaellinos/design_patterns/tree/main/creational/singleton)