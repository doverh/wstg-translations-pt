# Testando CSRF

|ID          |
|------------|
|WSTG-SESS-05|

## Resumo

Cross-Site Request Forgery ([CSRF](https://owasp.org/www-community/attacks/csrf)) é um ataque que força um usuário final a executar ações não intencionadas em uma aplicação web na qual estão autenticados. Com um pouco de ajuda de engenharia social (como enviar um link por e-mail ou chat), um atacante pode forçar os usuários de uma aplicação web a executar ações escolhidas pelo atacante. Um exploit CSRF bem-sucedido pode comprometer os dados e operações do usuário final quando direcionado a um usuário normal. Se o usuário final alvo for a conta de administrador, um ataque CSRF pode comprometer toda a aplicação web.

O CSRF depende de:

1. O comportamento do navegador da web em relação ao tratamento de informações relacionadas à sessão, como cookies e informações de autenticação HTTP.
2. O conhecimento de um atacante sobre URLs, solicitações ou funcionalidades válidas da aplicação web.
3. A gestão da sessão da aplicação baseando-se apenas em informações conhecidas pelo navegador.
4. A existência de tags HTML cuja presença cause acesso imediato a um recurso HTTP[S]; por exemplo, a tag de imagem `img`.

Os pontos 1, 2 e 3 são essenciais para a vulnerabilidade estar presente, enquanto o ponto 4 facilita a exploração real, mas não é estritamente necessário.

1. Os navegadores enviam automaticamente informações usadas para identificar uma sessão de usuário. Suponha que *site* seja um site hospedando uma aplicação web e o usuário *vítima* tenha acabado de se autenticar no *site*. Em resposta, *site* envia um cookie para *vítima* que identifica as solicitações enviadas por *vítima* como pertencentes à sessão autenticada de *vítima*. Quando o navegador recebe o cookie definido por *site*, ele o enviará automaticamente junto com qualquer outra solicitação direcionada a *site*.
2. Se a aplicação não utilizar informações relacionadas à sessão em URLs, então as URLs da aplicação, seus parâmetros e valores legítimos podem ser identificados. Isso pode ser feito por análise de código ou acessando a aplicação e observando os formulários e URLs incorporados no HTML ou JavaScript.
3. "Conhecido pelo navegador" refere-se a informações como cookies ou credenciais de autenticação HTTP (como Autenticação Básica e não autenticação baseada em formulário), que são armazenadas pelo navegador e subsequentemente apresentadas em cada solicitação direcionada a uma área da aplicação que exige autenticação. As vulnerabilidades discutidas a seguir se aplicam a aplicações que dependem inteiramente desse tipo de informação para identificar uma sessão de usuário.

Para simplificar, considere URLs acessíveis por GET (embora a discussão se aplique também a solicitações POST). Se *vítima* já estiver autenticada, enviar outra solicitação fará com que o cookie seja automaticamente enviado com ela. A figura abaixo ilustra o usuário acessando uma aplicação em `www.example.com`.

![Session Riding](images/Session_riding.GIF)\
*Figura 4.6.5-1: Session Riding*

A solicitação GET pode ser enviada pelo usuário de várias maneiras diferentes:

- Usando a aplicação web
- Digitando a URL diretamente no navegador
- Seguindo um link externo que aponta para a URL

Essas invocações são indistinguíveis para a aplicação. Em particular, a terceira pode ser bastante perigosa. Existem várias técnicas e vulnerabilidades que podem disfarçar as propriedades reais de um link. O link pode estar incorporado em uma mensagem de e-mail, aparecer em um site malicioso para o qual o usuário é atraído, ou aparecer em conteúdo hospedado por terceiros (como outro site ou e-mail HTML) e apontar para um recurso da aplicação. Se o usuário clicar no link, como eles já estão autenticados na aplicação web no *site*, o navegador emitirá uma solicitação GET para a aplicação web, acompanhada das informações de autenticação (o cookie de ID de sessão). Isso resulta em uma operação válida sendo realizada na aplicação web que o usuário não espera; por exemplo, uma transferência de fundos em um aplicativo bancário web.

Usando uma tag como `img`, conforme especificado no ponto 4 acima, nem mesmo é necessário que o usuário siga um link específico. Suponha que o atacante envie ao usuário um e-mail induzindo-os a visitar uma URL que se refere a uma página contendo o seguinte HTML (simplificado).

```html
<html>
    <body>
...
<img src="https://www.company.example/action" width="0" height="0">
...
    </body>
</html>
```

Quando o navegador exibe esta página, ele tentará exibir a imagem especificada de dimensão zero (portanto, invisível) de `https://www.company.example` também. Isso resulta em uma solicitação sendo automaticamente enviada para a aplicação web hospedada no *site*. Não é importante que a URL da imagem não se refira a uma imagem adequada, pois sua presença acionará a solicitação `action` especificada no campo `src` de qualquer maneira. Isso acontece desde que o download de imagens não seja desativado no navegador. A maioria dos navegadores não desativa o download de imagens, pois isso prejudicaria a usabilidade da maioria das aplicações web.

O problema aqui é uma consequência de:

- Tags HTML na página resultando na execução automática de solicitações HTTP (`img` sendo uma delas).
- O navegador não tendo como saber que o recurso referenciado por `img` não é uma imagem legítima.
- O carregamento da imagem acontecendo independentemente da localização da suposta origem da imagem, ou seja, o formulário e a imagem em si não precisam estar no mesmo host ou mesmo no mesmo domínio.

O fato de o conteúdo HTML não relacionado à aplicação web poder se referir a componentes na aplicação, e o fato de o navegador compor automaticamente uma solicitação válida para a aplicação, permite esse tipo de ataque. Não há como proibir esse comportamento, a menos que seja impossível para o atacante interagir com a funcionalidade da aplicação.

Em ambientes de correio/navegador integrados, simplesmente exibir uma mensagem

 de e-mail contendo a referência da imagem resultaria na execução da solicitação para a aplicação web com o cookie do navegador associado. Mensagens de e-mail podem se referir a URLs de imagem aparentemente válidas, como:

```html
<img src="https://[atacante]/picture.gif" width="0" height="0">
```

Neste exemplo, `[atacante]` é um site controlado pelo atacante. Utilizando um mecanismo de redirecionamento, o site malicioso pode usar `http://[atacante]/picture.gif` para direcionar a vítima para `http://[terceiro]/action` e acionar o `action`.

Cookies não são o único exemplo envolvido nesse tipo de vulnerabilidade. Aplicações web cujas informações de sessão são inteiramente fornecidas pelo navegador também são vulneráveis. Isso inclui aplicações que dependem apenas de mecanismos de autenticação HTTP, já que as informações de autenticação são conhecidas pelo navegador e são enviadas automaticamente a cada solicitação. Isso não inclui autenticação baseada em formulário, que ocorre apenas uma vez e gera algum tipo de informação relacionada à sessão, geralmente um cookie.

Suponhamos que a vítima esteja autenticada em um console de gerenciamento web de firewall. Para fazer login, um usuário precisa se autenticar e as informações de sessão são armazenadas em um cookie.

Suponhamos que o console de gerenciamento web do firewall tenha uma função que permite a um usuário autenticado excluir uma regra especificada pelo seu ID numérico, ou todas as regras na configuração se o usuário especificar `*` (um recurso perigoso na realidade, mas que torna o exemplo mais interessante). A página de exclusão é mostrada abaixo. Suponhamos que o formulário - para simplificar - emite uma solicitação GET. Para excluir a regra número um:

```text
https://[alvo]/fwmgt/delete?rule=1
```

Para excluir todas as regras:

```text
https://[alvo]/fwmgt/delete?rule=*
```

Este exemplo é intencionalmente ingênuo, mas mostra de maneira simplificada os perigos do CSRF.

![Session Riding Firewall Management](images/Session_Riding_Firewall_Management.gif)\
*Figura 4.6.5-2: Session Riding Firewall Management*

Usando o formulário mostrado na figura acima, inserir o valor `*` e clicar no botão Excluir enviará a seguinte solicitação GET:

```text
https://www.company.example/fwmgt/delete?rule=*
```

Isso excluiria todas as regras do firewall.

![Session Riding Firewall Management 2](images/Session_Riding_Firewall_Management_2.gif)\
*Figura 4.6.5-3: Session Riding Firewall Management 2*

O usuário também poderia ter alcançado os mesmos resultados enviando manualmente a URL:

```text
https://[alvo]/fwmgt/delete?rule=*
```

Ou seguindo um link que aponta, diretamente ou por meio de uma redireção, para a URL acima. Ou, novamente, acessando uma página HTML com uma tag `img` incorporada apontando para a mesma URL.

Em todos esses casos, se o usuário estiver autenticado no momento na aplicação de gerenciamento de firewall, a solicitação terá sucesso e modificará a configuração do firewall. Pode-se imaginar ataques direcionados a aplicações sensíveis, como lances automáticos em leilões, transferências de dinheiro, pedidos, alterações na configuração de componentes de software críticos, etc.

Uma coisa interessante é que essas vulnerabilidades podem ser exploradas por trás de um firewall; ou seja, é suficiente que o link sendo atacado seja alcançável pela vítima e não diretamente pelo atacante. Em particular, pode ser qualquer servidor web em uma intranet; por exemplo, no cenário de gerenciamento de firewall mencionado anteriormente, que é improvável de ser exposto à Internet.

Aplicações auto-vulneráveis, ou seja, aplicações que são usadas tanto como vetor de ataque quanto como alvo (como aplicações de webmail), pioram as coisas. Como os usuários estão autenticados ao lerem suas mensagens de e-mail, uma aplicação vulnerável desse tipo pode permitir que atacantes realizem ações como excluir mensagens ou enviar mensagens que parecem originar-se da vítima.

## Objetivos do Teste

- Determinar se é possível iniciar solicitações em nome de um usuário que não foram iniciadas pelo próprio usuário.

## Como Testar

Audite a aplicação para determinar se sua gestão de sessão é vulnerável. Se a gestão de sessão depender apenas de valores do lado do cliente (informações disponíveis para o navegador), então a aplicação é vulnerável. "Valores do lado do cliente" referem-se a cookies e credenciais de autenticação HTTP (Autenticação Básica e outras formas de autenticação HTTP; não autenticação baseada em formulário, que é uma autenticação de nível de aplicação).

Recursos acessíveis por solicitações HTTP GET são facilmente vulneráveis, embora solicitações POST possam ser automatizadas por meio de JavaScript e também sejam vulneráveis; portanto, o uso apenas de POST não é suficiente para corrigir a ocorrência de vulnerabilidades CSRF.

No caso de POST, o seguinte exemplo pode ser usado.

1. Crie uma página HTML semelhante à mostrada abaixo.
2. Hospede o HTML em um site malicioso ou de terceiros.
3. Envie o link da página para a vítima(s) e a induza a clicar nele.

```html
<html>
<body onload='document.CSRF.submit()'>

<form action='http://targetWebsite/Authenticate.jsp' method='POST' name='CSRF'>
    <input type='hidden' name='name' value='Hacked'>
    <input type='hidden' name='password' value='Hacked'>
</form>

</body>
</html>
```

No caso de aplicações web em que os desenvolvedores estão utilizando JSON para comunicação entre navegador e servidor, pode surgir um problema com o fato de não haver parâmetros de consulta com o formato JSON, que são necessários com formulários de envio automático. Para contornar esse caso, podemos usar um formulário de envio automático com payloads JSON incluindo uma entrada oculta para explorar CSRF. Teremos que alterar o tipo de codificação (`enctype`) para `text/plain` para garantir que o

 payload seja entregue como está. O código de exploração será semelhante ao seguinte:

```html
<html>
 <body>
  <script>history.pushState('', '', '/')</script>
   <form action='http://victimsite.com' method='POST' enctype='text/plain'>
     <input type='hidden' name='{"name":"hacked","password":"hacked","padding":"'value='something"}' />
     <input type='submit' value='Submit request' />
   </form>
 </body>
</html>
```

A solicitação POST será a seguinte:

```http
POST / HTTP/1.1
Host: victimsite.com
Content-Type: text/plain

{"name":"hacked","password":"hacked","padding":"=something"}
```

Quando esses dados são enviados como uma solicitação POST, o servidor aceitará alegremente os campos de nome e senha e ignorará o campo com o nome de preenchimento, pois não precisa dele.

## Remediação

- Consulte a [OWASP CSRF Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html) para medidas preventivas.

## Ferramentas

- [OWASP ZAP](https://www.zaproxy.org/)
- [CSRF Tester](https://wiki.owasp.org/index.php/Category:OWASP_CSRFTester_Project)
- [Pinata-csrf-tool](https://code.google.com/archive/p/pinata-csrf-tool/)

## Referências

- [Peter W: "Cross-Site Request Forgeries"](https://web.archive.org/web/20160303230910/http://www.tux.org/~peterw/csrf.txt)
- [Thomas Schreiber: "Session Riding"](https://web.archive.org/web/20160304001446/http://www.securenet.de/papers/Session_Riding.pdf)
- [Oldest known post](https://web.archive.org/web/20000622042229/http://www.zope.org/Members/jim/ZopeSecurity/ClientSideTrojan)
- [Cross-site Request Forgery FAQ](https://www.cgisecurity.com/csrf-faq.html)
- [A Most-Neglected Fact About Cross Site Request Forgery (CSRF)](http://yehg.net/lab/pr0js/view.php/A_Most-Neglected_Fact_About_CSRF.pdf)
- [Multi-POST CSRF](https://www.lanmaster53.com/2013/07/17/multi-post-csrf/)
- [SANS Pen Test Webcast: Complete Application pwnage via Multi POST XSRF](https://www.youtube.com/watch?v=EOs5PZiiwug)
```