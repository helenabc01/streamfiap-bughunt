# Checkpoint 4 — Bug Hunt StreamFIAP

## Identificação

**Grupo:** \_\_\_

| Integrante              | RM     | Turma |
| ----------------------- | ------ | ----- |
| Helena Barbosa Costa    | 562450 | 2ccpw |
| Bruna Marques e Queiroz | 565648 | 2ccpw |
| Pedro Henrique Lisboa   | 565722 | 2ccpw |

| Campo                              |            |
| ---------------------------------- | ---------- |
| **Total de bugs corrigidos**       | 5 / 12     |
| **Total de ajustes de Clean Code** | \_\_\_ / 6 |

---

## Parte 1 — Bugs encontrados

> Uma linha por bug, na ordem em que você os encontrou. Use a numeração dos seus
> commits (`fix: bug01 ...`). Preencha TODAS as colunas — metade da nota está aqui.

| #     | Sintoma observado (o que fiz/vi)                                               | Causa raiz (arquivo e linha aproximada) | Correção aplicada                           | Conceito da disciplina      |
| ----- | ------------------------------------------------------------------------------ | --------------------------------------- | ------------------------------------------- | --------------------------- |
| bug01 | calculo de promoção está causando aumento de preço                             | Filme.java linha: 25                    | troca de 1.2 para 0.8                       | regra de negócio            |
| bug02 | dados da série eram salvos errado                                              | Serie.java linha: 17                    | adicionado o super para herdar corretamente | herança & constructors      |
| bug03 | calculo de temporada incorreto                                                 | Serie.java linha: 23 e 24               | adicionado indicativo para sobrescrita      | polimorfismo & @Override    |
| bug04 | documentátio está sendo cobrado ondo contra a regra                            | Documentario.java linha 19 a 22         | adicionada sobrescrita de preço             | herança & polimorfismo      |
| bug05 | cadastro de nome de usuário incorreto recebendo ele mesmo ai invés do atributo | Usuario.java linha 22                   | adicionado o this.nome                      | atributos/parâmetros & this |
| bug06 |                                                                                |                                         |                                             |                             |
| bug07 |                                                                                |                                         |                                             |                             |
| bug08 |                                                                                |                                         |                                             |                             |
| bug09 |                                                                                |                                         |                                             |                             |
| bug10 |                                                                                |                                         |                                             |                             |
| bug11 |                                                                                |                                         |                                             |                             |
| bug12 |                                                                                |                                         |                                             |                             |

## Parte 2 — Ajustes de Clean Code

| #       | Onde estava | Qual princípio/boas práticas era violado | O que eu mudei |
| ------- | ----------- | ---------------------------------------- | -------------- |
| clean01 |             |                                          |                |
| clean02 |             |                                          |                |
| clean03 |             |                                          |                |
| clean04 |             |                                          |                |
| clean05 |             |                                          |                |
| clean06 |             |                                          |                |

---

## Parte 3 — Perguntas de reflexão

> Responda com suas palavras, 5 a 10 linhas cada, **usando o código real do projeto
> como exemplo**. Respostas genéricas de tutorial não pontuam.

### 1. Injeção de dependência (Aula 13)

Os controllers recebem os repositories via `@Autowired` (ex.: `ConteudoController`
usa `ConteudoRepository`). Explique por que o Spring precisa gerenciar esses objetos
em vez de criarmos com `new ConteudoRepository()`. O que exatamente o Spring faz ao
injetar um bean, e por que isso não funcionaria com um `new` comum?

### 2. JDBC vs Spring Data JPA (Aulas 12 e 13)

Na Aula 12 escrevemos um `ProdutoDAO` na mão com `Connection`, `PreparedStatement` e
`ResultSet`. Aqui o `ConteudoRepository` tem 2 linhas e faz CRUD completo. Compare as
duas abordagens: o que o Spring Data JPA automatiza, o que o JDBC/DAO ainda resolve
melhor, e como o `findByCategoria` consegue funcionar sem implementação.

### 3. Exceções checked vs unchecked (Aula 11)

A `ClassificacaoIndicativaException` estourava como um erro genérico do servidor,
sem mensagem útil para o cliente. Explique a diferença entre `extends Exception` e
`extends RuntimeException` no contexto desse bug, e como você fez a mensagem da
regra (classificação indicativa) chegar de forma clara ao cliente da API.

### 4. Sobrescrita vs sobrecarga (Aula 7)

Um dos bugs compilava sem nenhum erro: o método da `Serie` parecia sobrescrever
`calcularPrecoAluguel`, mas na verdade sobrecarregava. Explique a diferença entre
override e overload nesse caso e por que a anotação `@Override` teria impedido o bug.

### 5. Onde blindar o objeto? (Aulas 3, 4 e 13)

Vimos bugs de dados inválidos aceitos (duração negativa, créditos negativos, campos
nulos). Em quais lugares (construtor, setter, método do model) cada tipo de validação
deve ficar? Justifique usando os bugs que você encontrou e explique por que validar só
em um lugar não foi suficiente.

### 6. Abstração e interface (Aulas 8 e 9)

`Conteudo` é abstrata e `Promocionavel` é uma interface. Explique a diferença de
propósito entre as duas nesse projeto e o que mudaria no código se o Documentário
passasse a ter promoções — quais classes/linhas seriam tocadas e quais ficariam
intactas? O que isso diz sobre o design do sistema?

---

## Parte 4 — Espaço livre (opcional)

Alguma dificuldade, dúvida ou comentário sobre o checkpoint?

```

```
