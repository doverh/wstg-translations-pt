# Testando a Sobrecarga de Variáveis da Sessão

|ID          |
|------------|
|WSTG-SESS-08|

## Resumo

A Sobrecarga de Variáveis de Sessão (também conhecida como Session Puzzling) é uma vulnerabilidade de nível de aplicativo que pode permitir que um atacante execute várias ações maliciosas, incluindo, mas não se limitando a:

- Burlar mecanismos eficientes de autenticação e se passar por usuários legítimos.
- Elevar os privilégios de uma conta de usuário malicioso, em um ambiente que, de outra forma, seria considerado à prova de falhas.
- Pular fases qualificatórias em processos de várias fases, mesmo se o processo incluir todas as restrições de código comumente recomendadas.
- Manipular valores no lado do servidor por métodos indiretos que não podem ser previstos ou detectados.
- Executar ataques tradicionais em locais anteriormente inatingíveis ou considerados seguros.

Essa vulnerabilidade ocorre quando uma aplicação utiliza a mesma variável de sessão para mais de um propósito. Um atacante pode potencialmente acessar páginas em uma ordem não antecipada pelos desenvolvedores, de modo que a variável de sessão seja definida em um contexto e, em seguida, utilizada em outro.

Por exemplo, um atacante poderia usar a sobrecarga de variáveis de sessão para burlar mecanismos eficientes de autenticação de aplicações que exigem autenticação validando a existência de variáveis de sessão que contêm valores relacionados à identidade, os quais geralmente são armazenados na sessão após um processo de autenticação bem-sucedido. Isso significa que um atacante primeiro acessa uma localização na aplicação que define o contexto da sessão e, em seguida, acessa localizações privilegiadas que examinam esse contexto.

Por exemplo, um vetor de ataque de bypass de autenticação poderia ser executado acessando um ponto de entrada de acesso público (por exemplo, uma página de recuperação de senha) que preenche a sessão com uma variável de sessão idêntica, com base em valores fixos ou em entrada originada pelo usuário.

## Objetivos do Teste

- Identificar todas as variáveis de sessão.
- Interromper o fluxo lógico da geração de sessão.

## Como Testar

### Teste de Caixa Preta

Essa vulnerabilidade pode ser detectada e explorada enumerando todas as variáveis de sessão usadas pela aplicação e em qual contexto elas são válidas. Em particular, isso é possível acessando uma sequência de pontos de entrada e, em seguida, examinando os pontos de saída. No caso de teste de caixa preta, esse procedimento é difícil e requer alguma sorte, já que cada sequência diferente pode levar a um resultado diferente.

#### Exemplos

Um exemplo muito simples poderia ser a funcionalidade de redefinição de senha que, no ponto de entrada, poderia solicitar ao usuário que forneça algumas informações de identificação, como o nome de usuário ou o endereço de e-mail. Essa página pode então popular a sessão com esses valores de identificação, que são recebidos diretamente do lado do cliente ou obtidos de consultas ou cálculos com base na entrada recebida. Neste ponto, pode haver algumas páginas na aplicação que mostram dados privados com base nesse objeto de sessão. Dessa forma, o atacante poderia burlar o processo de autenticação.

### Teste de Caixa Cinza

A maneira mais eficaz de detectar essas vulnerabilidades é por meio de uma revisão de código-fonte.

## Remediação

As variáveis de sessão devem ser usadas apenas para um único propósito consistente.

## Referências

- [Session Puzzles](https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/puzzlemall/Session%20Puzzles%20-%20Indirect%20Application%20Attack%20Vectors%20-%20May%202011%20-%20Whitepaper.pdf)
- [Session Puzzling and Session Race Conditions](http://sectooladdict.blogspot.com/2011/09/session-puzzling-and-session-race.html)