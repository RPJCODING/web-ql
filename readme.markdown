# Guia de Sintaxe da Linguagem WebQL v2

Este guia oferece uma vis�o detalhada da sintaxe e da sem�ntica da WebQL v2, uma linguagem de consulta poderosa, ideal para interagir com dados na web.

## Conte�dos

[Introdu��o](#introduction) <br/>
[Consulta Raiz](#query-root) <br/>
[Pagina��o com Limit e Offset](#paging) <br/>
[Filtragem com Where](#filtering) <br/>
[Ordena��o com Order](#ordering) <br/>
[Contexto de Express�es](#expression-context) <br/>
[Exemplos de Consulta](#examples) <br/>

## Introdu��o <a name="introduction">

WebQL v2 � uma linguagem de consulta robusta e vers�til, projetada para facilitar a manipula��o e o acesso a dados na web.

## Consulta Raiz <a name="query-root">

A consulta raiz em WebQL v2 pode incluir propriedades especiais com fun��es definidas:

### Pagina��o com Limit e Offset <a name="paging">

* limit: Define o n�mero m�ximo de itens a serem retornados.
* offset: Define a posi��o inicial para a consulta, usada em conjunto com limit.

### Filtragem com Where <a name="filtering">

* where: Define um escopo de filtragem, permitindo especificar condi��es detalhadas para os dados que deseja recuperar.

### Ordena��o com Order <a name="ordering">

* order: Especifica as propriedades pelas quais os dados devem ser ordenados, suportando m�ltiplas propriedades no estilo orderby seguido de thenby.

### Conceito de Contexto dentro de Express�es <a name="expression-context">

Em WebQL v2, o contexto dentro de express�es � um aspecto fundamental que define como as express�es s�o resolvidas e interpretadas. O contexto se refere ao tipo de dado ou � entidade sobre a qual a express�o opera.

#### Contexto Inicial: A Entidade Raiz

O contexto inicial em qualquer consulta WebQL v2 � sempre o tipo da entidade raiz, ou typeof(T).
Este contexto raiz define o escopo inicial de propriedades e m�todos dispon�veis para a express�o.

#### Nichos de Contexto: Acesso a Membros e Operadores de Itera��o

� medida que as express�es acessam membros de um objeto ou entidade, o contexto se ajusta para refletir o tipo do membro acessado.
Opera��es de acesso a membros mudam o contexto para o tipo da propriedade ou do membro espec�fico.

#### Exemplo de Mudan�a de Contexto com Acesso a Membros
```json
{
    "propriedade": {
        "subPropriedade": {
            /* O contexto aqui � o tipo de 'subPropriedade' */
        }
    }
}
```

#### Contexto e Operadores de Itera��o

Operadores como $any e $all podem alterar o contexto quando usados para iterar sobre propriedades iter�veis (arrays, listas, cole��es).
Neste caso, o contexto se torna o tipo dos elementos da cole��o.

#### Exemplo de Contexto com Operadores de Itera��o
```json
{
    "cole��o": {
        "$any": {
            "elemento": "valor"
            /* O contexto aqui � o tipo dos elementos dentro de 'cole��o' */
        }
    }
}
```

#### Import�ncia do Contexto

Compreender o contexto � essencial para formular express�es v�lidas e significativas em WebQL v2.
O contexto guia o usu�rio sobre quais propriedades e opera��es s�o poss�veis em cada ponto da consulta.

## Exemplos de Consulta <a name="examples">

A seguir, exemplos variados de consultas em WebQL v2 demonstram a flexibilidade e a pot�ncia da linguagem:

### Exemplo 1: Pagina��o Simples
```json
{
    "limit": 10,
    "offset": 20
}
```
Busca os pr�ximos 10 itens, come�ando do item 21.

### Exemplo 2: Filtragem Avan�ada
```json
{
    "where": {
        "categoria": "eletr�nicos",
        "pre�o": {
            "$greater": 100
        }
    }
}
```
Filtra itens na categoria 'eletr�nicos' com pre�o maior que 100.

### Exemplo 3: Ordena��o Composta
```json
{
    "order": {
        "dataCriacao": "descending",
        "nome": "ascending"
    }
}
```
Ordena os dados primeiro por 'dataCriacao' em ordem decrescente, e depois por 'nome' em ordem crescente.

### Exemplo 4: Filtragem com Subescopos
```json
{
    "where": {
        "marca": "XYZ",
        "especificacoes": {
            "cor": "azul",
            "tamanho": {
                "$any": ["M", "G"]
            }
        }
    }
}
```
Busca itens da marca 'XYZ' que sejam azuis e nos tamanhos 'M' ou 'G'.

### Exemplo 5: Consulta Complexa com Pagina��o e Ordena��o
```json
{
    "limit": 5,
    "offset": 10,
    "where": {
        "disponibilidade": "emEstoque",
        "avaliacao": {
            "$greaterEquals": 4
        }
    },
    "order": {
        "avaliacao": "descending"
    }
}
```

Busca os pr�ximos 5 itens em estoque com avalia��o maior ou igual a 4, come�ando do 11� item, ordenados por avalia��o em ordem decrescente.

# Operadores em WebQL v2

WebQL v2 oferece uma variedade de operadores que podem ser usados para construir express�es complexas em consultas. Aqui est� um guia para entender como cada operador funciona:

## Operadores de Compara��o Aritm�tica

Estes operadores s�o usados com valores num�ricos:

* LessThan ($less): Menor que. 
* LessOrEquals ($lessEquals): Menor ou igual a. 
* Greater ($greater): Maior que. 
* GreaterOrEquals ($greaterEquals): Maior ou igual a.

## Operadores de Compara��o de Igualdade

Estes operadores podem ser usados com um valor ou uma lista de valores:

* Equals ($equals): Igual a.
* NotEquals ($notEquals): N�o igual a.

### Uso com Arrays

Quando usados com arrays, representam v�rias express�es ligadas por um "OR":

```json
{
    "campo": {
        "$equals": ["valor1", "valor2"] 
    }
}
```

� poss�vel omitir o operador $equals. Quando isso acontece a linguagem infere o uso do operador.

```json
{
    "campo": ["valor1", "valor2"] 
}
```

## Operador Like

Usado exclusivamente com strings:
* Like ($like): Corresponde a um padr�o de string.

### Uso com Arrays

Pode ser usado para verificar m�ltiplos padr�es:

```json
{
    "campo": {
        "$like": ["padr�o1", "padr�o2"] 
    }
}
```

## Operadores L�gicos

* Any ($any): Usado para iterar sobre uma propriedade iter�vel ou combinar m�ltiplos escopos de express�o com um "OR".
* All ($all): Semelhante ao $any, mas usado para combinar com um "AND".

Repare que o 'right hand side' de uma express�o de operador '$or' ou '$and' s� pode ser um escopo se o contexto for de uma propriedade iter�vel. Caso contr�rio apenas um array de escopos ser� aceito.

Quando usados para iterar uma propriedade, o contexto � autom�ticamete definido como o tipo contido dentro da cole��o iter�vel.

### Uso com Propriedades Iter�veis, Array de Strings
```json
{
    "listaDeStrings": {
        "$any": [
            { "$like": "foo" },
            { "$like": "bar" },
        ]
    }
}
```

### Uso com Propriedades Iter�veis, Objeto Complexo
```json
{
    "propriedadeIter�vel": {
        "$any": {
            "subcampo": "valor"
        }
    }
}
```

Os operadores l�gicos '$or' e '$and' tamb�m podem ser usados em qualquer contexto, que n�o seja de uma propriedade iter�vel, para juntar conjuntos de escopo de express�es. Respectivamente usando 'OR' e 'AND' l�gicos.

### Uso com V�rios Escopos

Combina escopos com "OR" ($any) ou "AND" ($all):
```json
{
    "$any": [
        { "campo1": "valor1" },
        { "campo2": "valor2" }
    ]
}
```