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
| **Total de bugs corrigidos**       | 11 / 12    |
| **Total de ajustes de Clean Code** | \_\_\_ / 6 |

---

## Parte 1 — Bugs encontrados

> Uma linha por bug, na ordem em que você os encontrou. Use a numeração dos seus
> commits (`fix: bug01 ...`). Preencha TODAS as colunas — metade da nota está aqui.

| #     | Sintoma observado (o que fiz/vi)                                                                                                                                                               | Causa raiz (arquivo e linha aproximada)   | Correção aplicada                                                                                                                                           | Conceito da disciplina                                |
| ----- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------- |
| bug01 | calculo de promoção está causando aumento de preço                                                                                                                                             | Filme.java linha: 25                      | troca de 1.2 para 0.8                                                                                                                                       | regra de negócio                                      |
| bug02 | dados da série eram salvos errado                                                                                                                                                              | Serie.java linha: 17                      | adicionado o super para herdar corretamente                                                                                                                 | herança & constructors                                |
| bug03 | calculo de temporada incorreto                                                                                                                                                                 | Serie.java linha: 23 e 24                 | adicionado indicativo para sobrescrita                                                                                                                      | polimorfismo & @Override                              |
| bug04 | documentátio está sendo cobrado ondo contra a regra                                                                                                                                            | Documentario.java linha 19 a 22           | adicionada sobrescrita de preço                                                                                                                             | herança & polimorfismo                                |
| bug05 | cadastro de nome de usuário incorreto recebendo ele mesmo ai invés do atributo                                                                                                                 | Usuario.java linha 22                     | adicionado o this.nome                                                                                                                                      | atributos/parâmetros & this                           |
| bug06 | o usuário pode ser impedido de comprar mesmo tendo créditos                                                                                                                                    | Usuario.jada linha 28                     | correção da comparativa entre preço e créditos                                                                                                              | lógica                                                |
| bug07 | conteudos indisponíveis poderiam ser alugados                                                                                                                                                  | Usuario.java linha 38 a 41                | adicionada validação de título disponível antes de qualquer coisa para evitar validações desnecessárias                                                     | regra de negócio / exceções.                          |
| bug08 | Ao buscar um conteúdo com um ID inexistente, a API não retorna o 404 esperado. A exceção é capturada e o método termina retornando null.                                                       | ConteudoController.java linha 32 a 34     | Removido o try/catch desnecessário, permitindo que a ConteudoNaoEncontradoException seja propagada para o GlobalExceptionHandler, que retorna HTTP 404.     | Tratamento de exceções / exceções personalizadas      |
| bug09 | Ao buscar conteúdos por categoria, a comparação com == pode não encontrar corretamente as String com o mesmo conteúdo, retornando uma lista vazia mesmo quando existem conteúdos da categoria. | ConteudoController.js linha 40            | Substituído o loop com == pela chamada conteudoRepository.findByCategoria(categoria), utilizando o método já existente no repository para realizar a busca. | Comparação de Strings / Separação de responsabilidade |
| bug10 | permite cadastrar conteúdo com duração 0 ou negativa.                                                                                                                                          | Conteudo. java linha 24                   | adicionada validação no construtor de Conteudo, rejeitando valores <= 0.                                                                                    | encapsulamento / herança.                             |
| bug11 | exceção de classificação indicativa pode resultar em erro 500 por não possuir tratamento global.                                                                                               | GlobalExceptionHandler.java linha 28 a 35 | adicionado handler específico retornando 403 FORBIDDEN e a mensagem da exceção.                                                                             | tratamento global de exceções                         |
| bug12 |                                                                                                                                                                                                |                                           |                                                                                                                                                             |                                                       |

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
O Spring precisa gerenciar o ciclo de vida do `ConteudoRepository` porque ele não é
"apenas um objeto" — é um **bean gerenciado pelo container**, com implementação
gerada dinamicamente em tempo de execução (no caso de interfaces `JpaRepository`,
o Spring cria um _proxy_ que implementa todos os métodos de CRUD e consulta).

Se você fizesse `new ConteudoRepository()`:

- **Não existe implementação concreta para instanciar.** `ConteudoRepository` é uma
  interface. O `new` exige uma classe concreta; quem cria a classe concreta (o
  proxy dinâmico) é o próprio Spring Data JPA, usando reflexão/bytecode generation.
  Sem o container, não há como obter esse objeto.
- **Perda de gerenciamento de dependências transitivas.** O repository depende de
  um `EntityManager`/`DataSource` configurado (usuário, senha, pool de conexões,
  dialeto do banco). O Spring injeta tudo isso automaticamente a partir do
  `application.properties`. Com `new`, você teria que montar manualmente essa
  cadeia inteira toda vez.
- **Perda do escopo de bean (singleton).** Por padrão o Spring mantém uma única
  instância do repository reaproveitada por toda a aplicação. Com `new` dentro do
  controller, cada requisição (ou cada controller) criaria sua própria instância,
  desperdiçando conexões e memória.
- **Acoplamento forte.** Com `new ConteudoRepository()` dentro do
  `ConteudoController`, o controller passa a conhecer os detalhes de construção do
  repository. Isso quebra a Inversão de Controle (IoC): o objetivo do `@Autowired`
  é que o controller apenas **declare o que precisa** (uma dependência do tipo
  `ConteudoRepository`), e quem decide **como fornecer** isso é o container.
  Em resumo: o `@Autowired` funciona porque, na inicialização da aplicação, o Spring
  varre o contexto (`ApplicationContext`), identifica todos os beans (`@Repository`,
  `@Service`, `@Component` etc.), resolve as dependências entre eles e injeta a
  instância correta no campo/construtor marcado. Um `new` comum não participa desse
  processo, então não tem acesso a nada que o container configurou.

### 2. JDBC vs Spring Data JPA (Aulas 12 e 13)

Na Aula 12 escrevemos um `ProdutoDAO` na mão com `Connection`, `PreparedStatement` e
`ResultSet`. Aqui o `ConteudoRepository` tem 2 linhas e faz CRUD completo. Compare as
duas abordagens: o que o Spring Data JPA automatiza, o que o JDBC/DAO ainda resolve
melhor, e como o `findByCategoria` consegue funcionar sem implementação.

R:
| | `ProdutoDAO` (JDBC puro) | `ConteudoRepository` (Spring Data JPA) |
|---|---|---|
| Conexão | Você abre/fecha `Connection` manualmente | Gerenciada pelo `EntityManager`/pool de conexões |
| SQL | Escrito à mão em cada método (`SELECT`, `INSERT`...) | Gerado automaticamente a partir do nome do método ou da entidade |
| Mapeamento objeto-linha | Manual, lendo cada coluna do `ResultSet` | Automático via anotações `@Entity`, `@Column` |
| Tratamento de erros | `SQLException` tratada manualmente em cada método | Traduzida para exceções do Spring (`DataAccessException`) |
| Controle fino de performance | Total — você escreve exatamente o SQL que quer | Menor — depende do que o JPA/Hibernate gera |

**O que o Spring Data JPA automatiza:** todo o CRUD básico (`save`, `findById`,
`findAll`, `deleteById`) já vem pronto ao estender `JpaRepository<Conteudo, Long>`.
Também automatiza a tradução do modelo Java para tabelas (via JPA/Hibernate) e a
gestão de transações.

**O que o JDBC/DAO ainda resolve melhor:** consultas muito específicas, otimizadas
"na mão" (joins complexos, tuning de performance, uso de recursos específicos do
banco), ou quando você precisa de controle total sobre o SQL executado — coisas que
um `JpaRepository` genérico não cobre bem sem cair para `@Query` nativa.

**Como `findByCategoria` funciona sem implementação:** o Spring Data JPA usa
**Query Derivation** (derivação de consultas por nome de método). Em tempo de
inicialização, o Spring analisa a assinatura `findByCategoria(String categoria)`,
identifica que `Categoria` é um atributo da entidade `Conteudo` e monta
automaticamente a query equivalente a
`SELECT c FROM Conteudo c WHERE c.categoria = :categoria`. Não existe mágica: é
parsing do nome do método + reflexão sobre os atributos da entidade mapeada.

### 3. Exceções checked vs unchecked (Aula 11)

A `ClassificacaoIndicativaException` estourava como um erro genérico do servidor,
sem mensagem útil para o cliente. Explique a diferença entre `extends Exception` e
`extends RuntimeException` no contexto desse bug, e como você fez a mensagem da
regra (classificação indicativa) chegar de forma clara ao cliente da API.

R:
extends Exception (checked): o compilador obriga quem chama o método a tratar a exceção, seja com try/catch, seja propagando com throws. É boa para erros "esperados" e recuperáveis, dos quais quem chamou precisa necessariamente saber (ex.: leitura de arquivo, conexão externa).
extends RuntimeException (unchecked): não obriga tratamento explícito. É ideal para representar violações de regra de negócio — o erro pode "subir" na pilha de chamadas até um ponto central de tratamento, sem poluir todos os métodos intermediários com throws.

O bug ocorria porque, sem um @ExceptionHandler/@ControllerAdvice capturando a ClassificacaoIndicativaException, o Spring não sabia que aquilo era um erro de regra de negócio — ele tratava como uma exceção não mapeada e devolvia um 500 Internal Server Error genérico, sem a mensagem real.

A correção típica combina duas coisas:

Fazer ClassificacaoIndicativaException extends RuntimeException, já que é um erro de regra de domínio (não é algo que o chamador precise ser forçado a tratar em todo lugar).
Criar um handler dedicado (@ExceptionHandler(ClassificacaoIndicativaException.class), normalmente dentro de uma classe @ControllerAdvice) que capture essa exceção especificamente e devolva um status apropriado (ex.: 400 Bad Request) com o corpo contendo a mensagem da regra (ex.: "Conteúdo não permitido para essa classificação indicativa"), em vez de deixar o Spring cair no tratamento padrão de erro 500.

### 4. Sobrescrita vs sobrecarga (Aula 7)

Um dos bugs compilava sem nenhum erro: o método da `Serie` parecia sobrescrever
`calcularPrecoAluguel`, mas na verdade sobrecarregava. Explique a diferença entre
override e overload nesse caso e por que a anotação `@Override` teria impedido o bug.

R:

- Override (sobrescrita): a subclasse redefine um método com a mesma assinatura (mesmo nome, mesmos tipos e quantidade de parâmetros) da superclasse. O comportamento é resolvido em tempo de execução (polimorfismo dinâmico) — quando você chama calcularPrecoAluguel() em uma referência do tipo Conteudo que na verdade aponta para uma Serie, o método da Serie é executado.
- Overload (sobrecarga): métodos com o mesmo nome, mas assinatura diferente (parâmetros diferentes em tipo/quantidade). São métodos totalmente distintos do ponto de vista do compilador — a escolha de qual será chamado é feita em tempo de compilação, com base nos argumentos passados.

_No bug_: o método em Serie tinha o nome certo, mas parâmetros diferentes (ou tipo de retorno incompatível de forma que não conta como override) do método declarado em Conteudo. O compilador não acusou erro porque, tecnicamente, criar um método novo com o mesmo nome e assinatura diferente é uma sobrecarga válida — não uma tentativa (falha) de sobrescrita. O problema é que, ao chamar conteudo.calcularPrecoAluguel() (referência polimórfica), o método executado continuava sendo o da superclasse Conteudo, e não o comportamento específico da Serie que você esperava.

A anotação @Override teria evitado o bug porque ela instrui o compilador a verificar se aquele método realmente sobrescreve um método da superclasse/interface. Se a assinatura não bater exatamente, o @Override gera erro de compilação, forçando você a perceber a divergência imediatamente, em vez de descobrir o problema só em tempo de execução.

### 5. Onde blindar o objeto? (Aulas 3, 4 e 13)

Vimos bugs de dados inválidos aceitos (duração negativa, créditos negativos, campos
nulos). Em quais lugares (construtor, setter, método do model) cada tipo de validação
deve ficar? Justifique usando os bugs que você encontrou e explique por que validar só
em um lugar não foi suficiente.

R:
Regra geral: \*\*validação de invariantes do domínio deve estar no model (construtor
setters), e validação de formato/entrada de API pode reforçar na borda (DTO/ controller), mas nunca substituir a validação do model.\*\*
Construtor: garante que nenhum objeto inválido chegue a existir. Ex.: se duracao ou creditos negativos nunca fazem sentido para o domínio, o construtor deve lançar exceção (IllegalArgumentException ou uma exceção de domínio) assim que o objeto é criado com esses valores. Isso é o que impede o bug de "duração negativa aceita" na raiz.
Setters: garantem que o objeto não fique inválido depois de já existir. Se alguém chama setDuracao(-10) depois de o objeto criado, sem validação no setter o objeto passa a violar a mesma regra que o construtor tentou proteger. Por isso a mesma checagem de negativos/nulos precisa se repetir lá — não porque é redundante sem sentido, mas porque construtor e setter são portas de entrada diferentes para o mesmo estado.
Métodos do model (regras de negócio): validações que dependem de lógica de domínio, não só do valor isolado do campo — por exemplo, a regra de classificação indicativa (não é só "campo nulo", é "esse valor combinado com esse outro não é permitido"). Essas ficam em métodos específicos do model (ex.: validarClassificacao()), porque exigem conhecimento de mais de um atributo/contexto.
Camada de API (Controller/DTO): útil para validar formato de entrada (JSON mal formado, tipo errado, @NotNull/@Valid em DTOs) e devolver mensagens de erro amigáveis antes mesmo de tentar construir o objeto de domínio. Mas essa camada não substitui a validação no model, porque o model pode ser usado por outros caminhos além da API (testes, jobs internos, outro service) — se a validação existir só no controller, esses outros caminhos ficam desprotegidos.

Por que validar em um único lugar não foi suficiente: cada ponto de entrada (construtor, setter, camada de API) representa um caminho diferente pelo qual um valor inválido pode entrar no sistema. Validar só no construtor não impede um set inválido depois; validar só no controller não impede que o mesmo model seja mal utilizado em outro contexto da aplicação. A blindagem precisa existir em todas as portas de entrada do estado, não em apenas uma.

### 6. Abstração e interface (Aulas 8 e 9)

`Conteudo` é abstrata e `Promocionavel` é uma interface. Explique a diferença de
propósito entre as duas nesse projeto e o que mudaria no código se o Documentário
passasse a ter promoções — quais classes/linhas seriam tocadas e quais ficariam
intactas? O que isso diz sobre o design do sistema?

R:
Conteudo (classe abstrata): representa o que um Conteúdo é, no sentido de identidade/hierarquia. Define atributos e comportamentos comuns a todo conteúdo (título, duração, categoria, talvez um calcularPrecoAluguel() com implementação padrão) e força as subclasses (Filme, Serie, Documentario) a se especializarem. É usada para modelar uma relação "é um" com estado e comportamento compartilhado.
Promocionavel (interface): representa uma capacidade/comportamento opcional que uma classe pode ou não ter, independente de sua posição na hierarquia de Conteudo. É um contrato: "quem implementa isso, sabe aplicar/ calcular uma promoção". Não carrega estado nem hierarquia de "é um tipo de"; carrega apenas a garantia de que certos métodos existem.

Essa separação existe porque nem todo conteúdo é promocionável, e a capacidade de ter promoção não é uma característica que varia com a hierarquia de tipos de conteúdo — é ortogonal a ela. Se a promoção fosse modelada dentro de Conteudo (por exemplo, um método abstrato lá), todo subtipo seria obrigado a implementar promoção, mesmo os que nunca terão.

Se Documentario passasse a ter promoções:

Tocado: a declaração da classe Documentario, que passaria a implements Promocionavel, e a implementação dos métodos exigidos pela interface (ex.: aplicarPromocao(), calcularPrecoComDesconto()) dentro dela.
Intocado: Conteudo (a superclasse) não muda nada — ela nunca soube nem precisa saber quem é promocionável. Filme, Serie e qualquer outra classe que já implementa Promocionavel continuam exatamente iguais. Qualquer código que processa uma lista de Promocionavel (ex.: um serviço que aplica descontos em lote) também não muda — ele já é genérico o suficiente para aceitar qualquer novo tipo que implemente a interface, incluindo o Documentario agora.

---
