---
title: Filtrar resultado Json de um output Pipe bash com Python
author: Rafael Veloso Lino
categories: [Python, Linux]
tags: [python, bash, linux]
---

## Como filtrar a saída Json de um output bash com Python

<p>Esses dias me deparei com a seguinte situação: Precisei filtrar informações de uma saida de um programa CLI com resultado JSON. O programa em sí era escrito em PERL e não teria como modifica-lo (não conheço PERL).</p>
<p>Na mesma hora pense: Óbviamente existe um jeito de se fazer em Python! A Primeira coisa que tinha que fazer, era ler a saída stdin. Pra quem não sabe, no Linux é possível conectar a saída de um comando com a entrada de outro.</p>
<p>Ex: É muito comum em diretórios com muitos arquivos, utilizarmos o grep (comando de busca) para mostrar apenas os resultado que queremos:</p>

```console
foo@bar:~$ ls -la | grep .js
-rw-rw-r--  1 foo foo  267 dez 18 22:01 app.js
-rw-rw-r--  1 foo foo 1506 dez 18 22:01 sw.js
```

<p>No exemplo acima, a saída do comando ls -la (lista tudo do repositório atual) foi ligado com grep para listar apenas arquivos com ".js".</p>

<p>Para simular uma saída em JSON, iremos utilizar a PokeAPI, que nos retornara um JSON.</p>

```console
foo@bar:~$ curl -X GET https://pokeapi.co/api/v2/pokemon/gyarados
```

<p>Com o curl, fazer um get para obter os dados de um <strike>o melhor</strike> pokemon. O retorno será enorme e nosso objetivo será obter apenas o game_index da versão gold. O game index é o número do pokemon na pokédex e a versão é qual game estamos nos referindo.</p>
<p>Primeira coisa que devemos fazer, é capturar o stdin (saída) com Python:</p>

```python
#!/usr/bin/env python3
import sys

data = sys.stdin.read()
print(data)
```

<p>Agora, vamos executar o curl e ligar com a saída do python. Para quem não sabe, a primeira linha do script (com '#!') se chama hashbang, utilizado para identificar qual será o interpretador, nesse caso, python.</p>

```console
foo@bar:~$ chmod +x seu_script_python.py
foo@bar:~$ curl -X GET https://pokeapi.co/api/v2/pokemon/gyarados | ./seu_script_python.py
```

<p>Se tudo correr bem, você terá o mesmo output, só que dessa vez gerado pelo print do python. Agora, vamos filtro o dado que precisamos</p>

```python
#!/usr/bin/env python3
import sys
import json

data = sys.stdin.read() # ler entrada de dados
data = json.loads(data) # parse dos dados para objeto python

data = list(filter(lambda x: x['version']['name'] == 'gold', data.get('game_indices'))) 
# Filtrando dados para obter o game index do gyarados na versão gold

print(data)
print(data[0]['game_index'])
```

<p>O script acima foi feito para demonstrar o poder do python. Provavelmente conseguiríamos atingir o mesmo objetivo com praticamente qualquer linguagem. Mesmo com um bash script relativamente simples, mas não com a mesma velocidade e simplicidade do python.</p>
<p>Apesar de pequeno, existem muitos conceitos por traz desse script, como Dicionário, lista, generator etc que não caberia explicar aqui, mas basicamente, o filter recebe uma função anônima (lambda), que será usada para iterar sobre a lista retornada com a chave "game_indices", e com isso, será executado a lambda em cima de cada item, comparando a versão/nome com a string 'gold'.</p>
<p>O script pode ser melhorar, utilizando a args ou parse.args para que o usuário possa apenas passar o parâmetro ao script.</p>

```python
#!/usr/bin/env python3
import sys
import json

version_name = sys.argv[1] # obter o argumento

data = sys.stdin.read() # ler entrada de dados
data = json.loads(data) # parse dos dados para objeto python

# Usando filter para obter o game index do gyarados na versão gold
data = list(filter(lambda x: x['version']['name'] == version_name, data.get('game_indices', []))) 

# Usando list comprehension para obter game index do gyarados na versão gold
data = [
    d for d in data.get('game_indices', [])
    if d['version']['name'] == version_name
]

print(data)
print(data[0]['game_index'])
```

```console
foo@bar:~$ curl -X GET https://pokeapi.co/api/v2/pokemon/gyarados | ./python_output.py gold
```

<p>O comando acima trará o mesmo resultado, mas agora é possível filtrar de acordo com o parâmetro passado ao script! A legibilidade do nosso pequeno script é baixa e poderíamos utilizar um simples for-loop para resolver nosso problema, mas não seria tão rápido de fazer.</p>
<p>Graças ao closure e ao fato que todas as funções no python são Firs-Class, incluindo lambdas, é possível definir funções e passa-las por parâmetro com uma variável definida no scopo atual.</p>
<p>Python é uma linguagem incrível e com ela é possível fazer muito em poucas linhas, recomendo pra quem precisa escrever scripts Linux.</p>

