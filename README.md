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
| **Total de bugs corrigidos**       | 12 / 12    |
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
| bug12 | Ao cadastrar usuário via POST /api/usuarios ocorre erro de persistência JPA por ausência de geração de chave primária (IdentifierGenerationException).                                         | Usuario.java linha 12                     | Adicionada a anotação @GeneratedValue(strategy = GenerationType.IDENTITY) sobre o campo id.                                                                 | Mapeamento JPA / Entidades (Aula 13)                  |

## Parte 2 — Ajustes de Clean Code

| #       | Onde estava   | Qual princípio/boas práticas era violado       | O que eu mudei |
| ------- | ------------- | ---------------------------------------------- | -------------- |
| clean01 | Conteudo.java | erro no encapsulamento, o campo estava público | campo privado  |
| clean02 |               |                                                |                |
| clean03 |               |                                                |                |
| clean04 |               |                                                |                |
| clean05 |               |                                                |                |
| clean06 |               |                                                |                |

---

## Parte 3 — Perguntas de reflexão

    > Responda com suas palavras, 5 a 10 linhas cada, **usando o código real do projeto
    > como exemplo**. Respostas genéricas de tutorial não pontuam.

    ### 1. Injeção de dependência (Aula 13)

    Os controllers recebem os repositories via `@Autowired` (ex.: `ConteudoController`
    usa `ConteudoRepository`). Explique por que o Spring precisa gerenciar esses objetos
    em vez de criarmos com `new ConteudoRepository()`. O que exatamente o Spring faz ao
    injetar um bean, e por que isso não funcionaria com um `new` comum?
    R:
    No projeto, o ConteudoController recebe um ConteudoRepository por meio do @Autowired.
    O Spring gerencia esse objeto porque o ConteudoRepository é um bean e depende da infraestrutura do Spring Data JPA.
    Ao injetá-lo, o Spring cria a implementação necessária do repository e a disponibiliza para o controller.
    Por isso, não faria sentido fazer new ConteudoRepository(), pois ele é uma interface e não possui implementação concreta para ser instanciada diretamente.
    Além disso, o objeto criado pelo Spring participa do ciclo de vida e da configuração do JPA, incluindo acesso ao banco.
    Assim, no ConteudoController, basta chamar conteudoRepository.findAll() ou findById() sem criar nem configurar a conexão manualmente.

    ### 2. JDBC vs Spring Data JPA (Aulas 12 e 13)

    Na Aula 12 escrevemos um `ProdutoDAO` na mão com `Connection`, `PreparedStatement` e
    `ResultSet`. Aqui o `ConteudoRepository` tem 2 linhas e faz CRUD completo. Compare as
    duas abordagens: o que o Spring Data JPA automatiza, o que o JDBC/DAO ainda resolve
    melhor, e como o `findByCategoria` consegue funcionar sem implementação.

    R:
    No JDBC da Aula 12, o ProdutoDAO precisava controlar manualmente Connection, PreparedStatement e ResultSet.
    No projeto, o ConteudoRepository possui apenas extends JpaRepository<Conteudo, Long> e já recebe operações de CRUD prontas.
    O Spring Data JPA automatiza consultas, persistência, atualização e remoção usando o mapeamento das entidades JPA.
    Além disso, findByCategoria(String categoria) funciona pelo mecanismo de query derivation do Spring Data.
    O Spring interpreta o nome findByCategoria e cria uma consulta baseada no atributo categoria da entidade Conteudo.
    O JDBC/DAO ainda pode ser melhor quando precisamos de SQL muito específico, controle fino da consulta ou operações que não se encaixam bem no modelo JPA.

    ### 3. Exceções checked vs unchecked (Aula 11)

    A `ClassificacaoIndicativaException` estourava como um erro genérico do servidor,
    sem mensagem útil para o cliente. Explique a diferença entre `extends Exception` e
    `extends RuntimeException` no contexto desse bug, e como você fez a mensagem da
    regra (classificação indicativa) chegar de forma clara ao cliente da API.
    R:
    A ClassificacaoIndicativaException do projeto foi criada com extends Exception, portanto é uma exceção checked.
    Isso faz com que o método Usuario.alugar() precise declarar throws ClassificacaoIndicativaException, e o controller também declara essa exceção.
    Já ConteudoIndisponivelException e CreditosInsuficientesException usam RuntimeException, sendo unchecked.
    O problema da classificação era que ela podia chegar ao Spring sem um tratamento específico no GlobalExceptionHandler.
    Para corrigir, foi adicionado um @ExceptionHandler(ClassificacaoIndicativaException.class) que retorna e.getMessage() no campo "erro".
    Assim, a mensagem criada na regra de Usuario.alugar() chega claramente ao cliente em vez de aparecer apenas como um erro genérico.

    ### 4. Sobrescrita vs sobrecarga (Aula 7)

    Um dos bugs compilava sem nenhum erro: o método da `Serie` parecia sobrescrever
    `calcularPrecoAluguel`, mas na verdade sobrecarregava. Explique a diferença entre
    override e overload nesse caso e por que a anotação `@Override` teria impedido o bug.
    R:
    Em Conteudo, existe public double calcularPrecoAluguel() sem parâmetros.
    Na Serie, o método estava como calcularPrecoAluguel(double desconto), portanto possuía uma assinatura diferente.
    Isso caracteriza overload (sobrecarga), e não override (sobrescrita), mesmo que a intenção fosse substituir o comportamento da classe pai.
    Por isso, quando Usuario.alugar() executava c.calcularPrecoAluguel(), o método da Conteudo podia ser utilizado.
    A correção foi mudar o método da Serie para public double calcularPrecoAluguel() e calcular 4.90 \* numeroTemporadas.
    Se tivéssemos colocado @Override no método original, o compilador identificaria imediatamente que aquela assinatura não correspondia ao método da classe pai.

    ### 5. Onde blindar o objeto? (Aulas 3, 4 e 13)

    Vimos bugs de dados inválidos aceitos (duração negativa, créditos negativos, campos
    nulos). Em quais lugares (construtor, setter, método do model) cada tipo de validação
    deve ficar? Justifique usando os bugs que você encontrou e explique por que validar só
    em um lugar não foi suficiente.
    R:
    A validação deve ficar próxima da regra que ela protege, para impedir que o objeto entre ou permaneça em um estado inválido.
    No projeto, regras como duracaoMinutos <= 0 devem ser verificadas na criação do Conteudo, evitando conteúdos inválidos desde o início.
    Já a regra de créditos deve ser protegida em Usuario, especialmente em temCreditosSuficientes() e debitarCreditos(), para impedir saldo negativo.
    A classificação indicativa também pertence ao model, e por isso já é verificada dentro de Usuario.alugar().
    Além disso, a disponibilidade precisa ser verificada no próprio fluxo de aluguel antes de executar o débito.
    Validar somente no controller não seria suficiente, porque outros pontos do sistema poderiam chamar os métodos do model diretamente e criar estados inválidos.

    ### 6. Abstração e interface (Aulas 8 e 9)

    `Conteudo` é abstrata e `Promocionavel` é uma interface. Explique a diferença de
    propósito entre as duas nesse projeto e o que mudaria no código se o Documentário
    passasse a ter promoções — quais classes/linhas seriam tocadas e quais ficariam
    intactas? O que isso diz sobre o design do sistema?
    R:
    Conteudo é uma classe abstrata porque representa a estrutura comum dos conteúdos, como titulo, categoria, duracaoMinutos e classificacaoEtaria.

    Filme, Serie e Documentario herdam dessa estrutura e podem fornecer comportamentos específicos, como o preço.
    Já Promocionavel representa uma capacidade, definida pelo método aplicarPromocao(double preco), que pode ser implementada por diferentes tipos de conteúdo.
    Atualmente, Filme e Serie implementam essa interface, enquanto Documentario não implementa.
    Se o documentário passasse a ter promoção, bastaria alterar Documentario para extends Conteudo implements Promocionavel e implementar aplicarPromocao().
    A lógica geral de Conteudo.calcularPrecoPromocional() poderia permanecer intacta, mostrando que a interface permite adicionar esse comportamento sem alterar as outras classes.

---
