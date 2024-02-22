# Teste de Funcionalidade de Logout

|ID          |
|------------|
|WSTG-SESS-06|

## Resumo

A terminação de sessão é um aspecto crítico do ciclo de vida da sessão, desempenhando um papel fundamental na redução do risco de ataques de sequestro de sessão. A terminação segura de sessões atua como uma medida de controle contra vários ataques, como Cross-Site Scripting (XSS) e Cross-Site Request Forgery (CSRF), ambos frequentemente dependentes da presença de uma sessão autenticada. Não implementar a terminação segura de sessões aumenta a superfície de ataque para essas ameaças.

Um mecanismo robusto de terminação de sessões deve incluir os seguintes componentes:

- Controles de interface do usuário que permitem o logout manual.
- Terminação de sessão após um período definido de inatividade (tempo limite de sessão).
- Invalidez adequada do estado da sessão no lado do servidor.

Vários problemas podem prejudicar a terminação eficaz da sessão. Em uma aplicação web segura ideal, os usuários devem ter a capacidade de fazer logout a qualquer momento por meio de uma interface amigável. Cada página deve apresentar proeminentemente um botão de logout para acesso rápido, e a funcionalidade deve ser clara para os usuários, evitando ambiguidades que possam levar à desconfiança.

Um erro comum na terminação de sessões é redefinir o token de sessão do lado do cliente para um novo valor sem invalidar adequadamente o estado do lado do servidor. Às vezes, apenas uma mensagem de confirmação é exibida sem tomar nenhuma ação adicional, deixando o estado do lado do servidor ativo e potencialmente reutilizável.

Certos frameworks de aplicativos da web dependem exclusivamente do cookie de sessão para identificar o usuário autenticado, incorporando o ID do usuário no valor do cookie (criptografado). Ao fazer logout, o cookie de sessão é removido do navegador, mas, como a aplicação não rastreia sessões no lado do servidor, reutilizar um cookie de sessão pode conceder acesso não autorizado. A funcionalidade de Autenticação de Formulários no ASP.NET é um exemplo conhecido disso.

Os usuários da web frequentemente fecham o navegador ou uma aba sem fazer logout explicitamente. As aplicações web devem estar cientes desse comportamento e encerrar automaticamente as sessões no lado do servidor após um período definido de inatividade.

O uso de um sistema de Single Sign-On (SSO) pode resultar na coexistência de várias sessões que precisam ser encerradas separadamente. Por exemplo, encerrar a sessão específica da aplicação pode não encerrar a sessão no sistema SSO e vice-versa.

## Objetivos do Teste

- Avaliar a interface de logout.
- Analisar o tempo limite da sessão e verificar a terminação adequada após o logout.

## Como Testar

### Testando a Interface de Logout

1. **Presença e Visibilidade do Botão de Logout:**
   - Verifique a aparência e visibilidade da funcionalidade de logout na interface do usuário.
   - Verifique se há um botão de logout em todas as páginas da aplicação web.
   - Certifique-se de que o botão de logout seja rapidamente identificável para um usuário que deseja fazer logout.
   - Confirme se o botão de logout é visível sem rolar a página após o carregamento.
   - Idealmente, coloque o botão de logout em uma área fixa da página que permaneça visível na janela de visualização do navegador durante a rolagem.

### Testando a Terminação da Sessão no Lado do Servidor

1. **Capturar Cookies de Sessão:**
   - Antes de fazer logout, armazene os valores dos cookies usados para identificar uma sessão.

2. **Invocar a Função de Logout:**
   - Execute a função de logout e observe o comportamento da aplicação, especialmente em relação aos cookies de sessão.

3. **Verificar Páginas Autenticadas:**
   - Tente navegar para páginas visíveis apenas em uma sessão autenticada, por exemplo, usando o botão de voltar do navegador.
   - Se uma versão em cache da página for exibida, use o botão de recarregar para atualizar a página no servidor.

4. **Restaurar Cookies de Sessão:**
   - Se a função de logout causar a definição dos cookies de sessão para um novo valor, restaure os valores antigos dos cookies de sessão.

5. **Verificar Outras Páginas:**
   - Teste páginas adicionais da aplicação consideradas críticas para a segurança para garantir que a terminação adequada da sessão seja reconhecida em diferentes áreas.

### Testando o Tempo Limite da Sessão

1. **Determinar o Tempo Limite da Sessão:**
   - Execute solicitações a uma página autenticada com atrasos crescentes para determinar o tempo limite da sessão.
   - Observe se o comportamento de logout ocorre, indicando um valor de tempo limite correspondente.

2. **Considerar Segurança e Usabilidade:**
   - Avalie o valor do tempo limite da sessão com base no propósito da aplicação, equilibrando segurança e usabilidade.
   - Em aplicações bancárias, um tempo limite curto (por exemplo, 15 minutos) pode ser apropriado, enquanto fóruns ou wikis podem tolerar tempos limite mais longos.

### Testando a Terminação da Sessão em Ambientes de Single Sign-On (Logoff Único)

1. **Realizar Logout na Aplicação:**
   - Faça logout na aplicação testada.

2. **Verificar Portal Central ou Diretório de Aplicativos:**
   - Verifique se há um portal central ou diretório de aplicativos que permite ao usuário fazer login novamente na aplicação sem autenticação.

3. **Fazer Logout no Sistema SSO:**
   - Enquanto estiver logado na aplicação testada, faça logout no sistema SSO.

4. **Acessar Área Autenticada:**
   - Tente acessar uma área autenticada da aplicação testada após o logout no SSO.

## Ferramentas

- [Burp Suite - Repeater](https://portswigger.net/burp/documentation/desktop/tools/repeater)

## Referências

### Artigos

- [Cookie replay attacks in ASP.NET when using forms authentication](https://www.vanstechelman.eu/content/cookie-replay-attacks-in-aspnet-when-using-forms-authentication)

