---
title: O que são Design Patterns
author: Rafael Lino
categories: [oop, Java, Design Patterns]
tags: [oop]
---

# Design Patterns in Java

Design patterns, ou padrões de projeto, são soluções comuns para problemas frequentemente encontrados na programação, especialmente no paradigma da orientação a objetos. Eles são essenciais para facilitar testes, coesão e escalabilidade do código, e têm benefícios a longo prazo.

O famoso livro "Design Patterns: Elements of Reusable Object-Oriented Software" ou Gang of Four (GOF) categoriza 23 padrões de projetos organizados em 3 categorias: Criacional, Estrutural e Comportamental.

## Tipos de Design Patterns

### Criacional (Creational)

Relacionado à criação de objetos. Exemplos incluem:

- **Singleton**: Garante que uma classe tenha apenas uma instância e fornece um ponto global de acesso a ela.
- **Factory**: Uma fabrica de objetos, abstrai a lógica de criação e devolve o concreto solicitado, geralmente relacionado a uma inferface com um contrato fixo.
- **Builder**: Permite a construção de um objeto complexo passo a passo.

### Estrutural (Structural)

Focado na estruturação dos componentes e como eles se relacionam, auxiliando no desacoplamento. Exemplos incluem:

- **Adapter**: Permite que interfaces incompatíveis trabalhem juntas.
- **Facade**: Fornece uma interface unificada para um conjunto de interfaces em um subsistema.
- **Proxy**: Fornece um substituto ou espaço reservado para outro objeto controlar o acesso a ele.

### Comportamental (Behavioural)

Voltado para a interação, comunicação e relação entre objetos em geral. Exemplos incluem:

- **Command**: Encapsula uma solicitação como um objeto, permitindo parametrização e enfileiramento de solicitações.
- **Strategy**: Define uma família de algoritmos, encapsula cada um deles e os torna intercambiáveis.
- **Mediator**: Define um objeto que encapsula como um conjunto de objetos interage.

## Conclusão

<p>
Os padrões de design são ferramentas poderosas que podem ajudar os desenvolvedores a criar código mais limpo, mais eficiente e mais fácil de manter. Compreender esses padrões e saber quando e como aplicá-los é uma habilidade essencial para qualquer desenvolvedor Java sério.
</p>

<p>Em breve farei uma série discutingo e exemplificando cada um dos patterns</p>
