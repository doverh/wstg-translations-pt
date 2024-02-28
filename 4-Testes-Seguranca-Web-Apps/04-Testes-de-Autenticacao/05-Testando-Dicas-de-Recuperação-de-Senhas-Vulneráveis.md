# Testando Dicas de Recuperação de Senhas Vulneráveis

|ID          |
|------------|
|WSTG-ATHN-05|

## Resumo

Credenciais são a tecnologia de autenticação mais amplamente utilizada. Devido ao amplo uso de pares de nome de usuário e senha, os usuários não conseguem mais gerenciar adequadamente suas credenciais em várias aplicações usadas.

Para ajudar os usuários com suas credenciais, surgiram várias tecnologias:

- Aplicações fornecem uma funcionalidade de *lembrar-me* que permite que o usuário permaneça autenticado por longos períodos de tempo, sem pedir novamente as credenciais do usuário.
- Gerenciadores de senhas - incluindo gerenciadores de senhas de navegadores - que permitem ao usuário armazenar suas credenciais de maneira segura e depois injetá-las em formulários de usuário sem qualquer intervenção do usuário.

## Objetivos do Teste

- Validar que a sessão gerada é gerenciada de forma segura e não coloca as credenciais do usuário em perigo.

## Como Testar

Como esses métodos proporcionam uma melhor experiência do usuário e permitem que o usuário esqueça completamente suas credenciais, eles aumentam a superfície de ataque. Algumas aplicações:

- Armazenam as credenciais de maneira codificada nos mecanismos de armazenamento do navegador, o que pode ser verificado seguindo o [cenário de teste de armazenamento na web](../11-Client-side_Testing/12-Testing_Browser_Storage.md) e passando pelos cenários de [análise de sessão](../06-Session_Management_Testing/01-Testing_for_Session_Management_Schema.md#session-analysis). As credenciais não devem ser armazenadas de forma alguma no lado do cliente e devem ser substituídas por tokens gerados no lado do servidor.
- Injetam automaticamente as credenciais do usuário que podem ser abusadas por:
  - Ataques de [ClickJacking](../11-Client-side_Testing/09-Testing_for_Clickjacking.md).
  - Ataques [CSRF](../06-Session_Management_Testing/05-Testing_for_Cross_Site_Request_Forgery.md).
- Tokens devem ser analisados em termos de tempo de vida do token, onde alguns tokens nunca expiram e colocam os usuários em perigo se esses tokens forem roubados. Certifique-se de seguir o cenário de teste [timeout da sessão](../06-Session_Management_Testing/07-Testing_Session_Timeout.md).

## Remediação

- Siga as boas práticas de [gerenciamento de sessão](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html).
- Garanta que nenhuma credencial seja armazenada em texto claro ou seja facilmente recuperável em formas codificadas ou criptografadas nos mecanismos de armazenamento do navegador; elas devem ser armazenadas no lado do servidor e seguir boas práticas de [armazenamento de senha](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html).