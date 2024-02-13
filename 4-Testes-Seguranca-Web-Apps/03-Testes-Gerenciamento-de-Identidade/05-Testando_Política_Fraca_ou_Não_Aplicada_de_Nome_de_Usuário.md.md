# Testando Políticas Fraca ou Não Aplicada de Nome de Usuário

|ID          |
|------------|
|WSTG-IDNT-05|

## Resumo

Os nomes de contas de usuário frequentemente seguem uma estrutura altamente padronizada (por exemplo, o nome de Joe Bloggs é jbloggs e o nome de Fred Nurks é fnurks) e nomes de contas válidos podem ser facilmente adivinhados.

## Objetivos do Teste

- Determinar se uma estrutura consistente de nomes de conta torna a aplicação vulnerável à enumeração de contas.
- Determinar se as mensagens de erro da aplicação permitem a enumeração de contas.

## Como Testar

- Determinar a estrutura dos nomes de conta.
- Avaliar a resposta da aplicação a nomes de conta válidos e inválidos.
- Usar respostas diferentes para nomes de conta válidos e inválidos para enumerar nomes de conta válidos.
- Usar dicionários de nomes de conta para enumerar nomes de conta válidos.

## Remediação

Garanta que a aplicação retorne mensagens de erro genéricas consistentes em resposta a nomes de conta inválidos, senhas ou outras credenciais de usuário inseridas durante o processo de login.