---
title: Filtrar resultado Json de um output Pipe bash com Python
author: Rafael Veloso Lino
categories: [Python, Linux]
tags: [python, bash, linux]
---

## Como filtrar a saída Json de um output bash com Python

<p>Esses dias me deparei com a seguinte situação: Precisei filtrar informações de uma saida de um programa CLI com resultado JSON. O programa em sí era escrito em PERL e como não teria a possibilidade.</p>
<p>Na mesma hora pense: Óbviamente existe um jeito de se fazer em Python! A Primeira coisa que tinha que fazer, era ler a saída stdin. Pra quem não sabe, no Linux é possível conectar a saída de um comando com a entrada de um outro software.</p>
<p>Ex: É muito comum em diretórios com muitos arquivos, utilizarmos o grep (comando de busca) para mostrar apenas os resultado que queremos:</p>

```console
foo@bar:~$ ls -la | grep .js
-rw-rw-r--  1 foo foo  267 dez 18 22:01 app.js
-rw-rw-r--  1 foo foo 1506 dez 18 22:01 sw.js
```

<p>Para simular uma saída json, iremos utilizar a PokeAPI, que nos retornara um JSON.</p>

```console
foo@bar:~$ curl -X GET https://pokeapi.co/api/v2/pokemon/gyarados
```

<p>Com o curl, fazer um get para objeter os dados de um <strike>o melhor</strike> pokemon. O retorno será enorme e nosso objetivo será obter apenas o game_index do versão gold.</p>
<p>Primeira coisa que devemos fazer, é capturar o stdin (saída) com Python:</p>

```python
#!/usr/bin/env python3
import sys

data = sys.stdin.read()
print(data)
```

<p>Agora, vamos executar o curl e ligar com a saída do python. Para quem não sabe, a primeira linha do script (com #!) se chama hashbang, utilizado para identificar será o interpretador, nesse caso, python.</p>

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
<p>Apesar de pequeno, existem muitos conceitos por traz do desse script, como Dicionário, lista, generator etc que não caberia explicar aqui, mas basicamente, o filter recebe uma função anônima (lambda), que será usada para iterar em cima da lista passada pela chave "game_indices", e vai verificar entre os indices, se existe algum dicionário com a chave "version" e, dentro do dict a chave "name" igual à 'gold'.</p>
<p>O script pode ser melhorar, utilizando a args ou parse.args para que o usuário possa apenas passar o parâmetro ao script para o filter</p>

```python
#!/usr/bin/env python3
import sys
import json

version_name = sys.argv[1] # obter o argumento

data = sys.stdin.read() # ler entrada de dados
data = json.loads(data) # parse dos dados para objeto python

data = list(filter(lambda x: x['version']['name'] == version_name, data.get('game_indices'))) 
# Filtrando dados para obter o game index do gyarados na versão gold

print(data)
print(data[0]['game_index'])
```

```console
foo@bar:~$ curl -X GET https://pokeapi.co/api/v2/pokemon/gyarados | ./python_output.py gold
```

<p>O comando acima trará o mesmo resultado, mas agora é possível filtrar de acordo com o parâmetro passado ao script!</p>
<p>Graças ao closure e ao fato que todas as funções no python são Firs-Class, incluindo lambdas, é possível definir funções e passa-las por parâmetro com uma variável definida no scopo atual.</p>
<p>Python é uma linguagem incrível e com ela é possível fazer muito em poucas linhas, recomendo muito pra quem precisa escrever scripts.</p>

