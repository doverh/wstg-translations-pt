# Testar Métodos HTTP

|ID          |
|------------|
|WSTG-CONF-06|

## Resumo

O HTTP oferece vários métodos que podem ser usados para realizar ações no servidor web (o padrão HTTP 1.1 os refere como `métodos`, mas também são comumente descritos como `verbos`). Embora GET e POST sejam de longe os métodos mais comuns usados para acessar informações fornecidas por um servidor web, o HTTP permite vários outros métodos (e um pouco menos conhecidos). Alguns desses métodos podem ser usados para fins nefastos se o servidor web estiver mal configurado.

[RFC 7231 –  Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content](https://tools.ietf.org/html/rfc7231) define os seguintes métodos de solicitação HTTP válidos, ou verbos:

- [`GET`](https://tools.ietf.org/html/rfc7231#section-4.3.1)
- [`HEAD`](https://tools.ietf.org/html/rfc7231#section-4.3.2)
- [`POST`](https://tools.ietf.org/html/rfc7231#section-4.3.3)
- [`PUT`](https://tools.ietf.org/html/rfc7231#section-4.3.4)
- [`DELETE`](https://tools.ietf.org/html/rfc7231#section-4.3.5)
- [`CONNECT`](https://tools.ietf.org/html/rfc7231#section-4.3.6)
- [`OPTIONS`](https://tools.ietf.org/html/rfc7231#section-4.3.7)
- [`TRACE`](https://tools.ietf.org/html/rfc7231#section-4.3.8)

No entanto, a maioria das aplicações web só precisa responder a solicitações GET e POST, recebendo dados do usuário na string de consulta da URL ou anexados à solicitação, respectivamente. Links no estilo `<a href=""></a>` e formulários definidos sem um método acionam uma solicitação GET; dados de formulário enviados via `<form method='POST'></form>` acionam solicitações POST. Chamadas JavaScript e AJAX podem enviar métodos diferentes de GET e POST, mas geralmente não precisam fazer isso. Como os outros métodos são tão raramente usados, muitos desenvolvedores não sabem, ou deixam de considerar, como a implementação desses métodos pelo servidor web ou pelo framework de aplicativos impacta as características de segurança da aplicação.

## Objetivos do Teste

- Enumerar os métodos HTTP suportados.
- Testar a possibilidade de contornar o controle de acesso.
- Testar vulnerabilidades de Cross-Site Tracing (XST).
- Testar técnicas de substituição de métodos HTTP.

## Como Testar

### Descobrir os Métodos Suportados

Para realizar este teste, o testador precisa de alguma maneira de descobrir quais métodos HTTP são suportados pelo servidor web que está sendo examinado. Enquanto o método HTTP `OPTIONS` fornece uma maneira direta de fazer isso, verifique a resposta do servidor emitindo solicitações usando diferentes métodos. Isso pode ser feito por teste manual ou algo como o script Nmap [`http-methods`](https://nmap.org/nsedoc/scripts/http-methods.html).

Para usar o script Nmap `http-methods` para testar o ponto final `/index.php` no servidor `localhost` usando HTTPS, emita o seguinte comando:

```bash
nmap -p 443 --script http-methods --script-args http-methods.url-path='/index.php' localhost
```

Ao testar um aplicativo que precisa aceitar outros métodos, por exemplo, um Serviço Web RESTful, teste-o minuciosamente para garantir que todos os pontos de extremidade aceitem apenas os métodos necessários.

#### Testando o Método PUT

1. Capture a solicitação base do alvo com um proxy da web.
2. Altere o método da solicitação para `PUT` e adicione um arquivo `test.html` e envie a solicitação para o servidor de aplicativos.

   ```html
   PUT /test.html HTTP/1.1
   Host: website-de-teste

   <html>
   O Método HTTP PUT está Habilitado
   </html>
   ```

3. Se o servidor responder com códigos de sucesso 2XX ou redirecionamentos 3XX, confirme com uma solicitação `GET` para o arquivo `test.html`. A aplicação é vulnerável.

Se o método HTTP `PUT` não for permitido no URL ou na solicitação base, tente outros caminhos no sistema.

> OBSERVAÇÃO: Se você conseguir fazer upload de um shell da web, deve sobrescrevê-lo ou garantir que a equipe de segurança do alvo esteja ciente e remova o componente rapidamente após a prova de conceito.

Aproveitando o método `PUT` um invasor pode ser capaz de inserir conteúdo arbitrário e potencialmente malicioso no sistema, o que pode levar à execução remota de código, desfiguração do site ou negação de serviço.

### Testar Contorno de Controle de Acesso

Encontre uma página que tenha uma restrição de segurança, de modo que uma solicitação GET normalmente forçaria um redirecionamento 302 para uma página de login ou forçaria um login diretamente. Emita solicitações usando vários métodos, como HEAD, POST, PUT etc., bem como métodos arbitrariamente inventados, como BILBAO, FOOBAR, CATS, etc. Se a aplicação web responder com um `HTTP/1.1 200 OK` que não seja uma página de login, pode ser possível contornar a autenticação ou autorização. O exemplo a seguir usa o [`ncat`](https://nmap.org/ncat/) da Nmap.

```bash
$ n

cat www.example.com 80
HEAD /admin HTTP/1.1
Host: www.example.com

HTTP/1.1 200 OK
Date: Mon, 18 Aug 2008 22:44:11 GMT
Server: Apache
Set-Cookie: PHPSESSID=pKi...; path=/; HttpOnly
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Pragma: no-cache
Set-Cookie: adminOnlyCookie1=...; expires=Tue, 18-Aug-2009 22:44:31 GMT; domain=www.example.com
Set-Cookie: adminOnlyCookie2=...; expires=Mon, 18-Aug-2008 22:54:31 GMT; domain=www.example.com
Set-Cookie: adminOnlyCookie3=...; expires=Sun, 19-Aug-2007 22:44:30 GMT; domain=www.example.com
Content-Language: EN
Connection: close
Content-Type: text/html; charset=ISO-8859-1
```

Se o sistema parecer vulnerável, emita ataques semelhantes a CSRF, como os seguintes, para explorar mais completamente o problema:

- `HEAD /admin/createUser.php?member=myAdmin`
- `PUT /admin/changePw.php?member=myAdmin&passwd=foo123&confirm=foo123`
- `CATS /admin/groupEdit.php?group=Admins&member=myAdmin&action=add`

Usando os três comandos acima, modificados para atender à aplicação sob teste e aos requisitos de teste, um novo usuário seria criado, uma senha atribuída e o usuário feito administrador, tudo usando submissão cega de solicitação.

### Testando Potencial de Cross-Site Tracing (XST)

Nota: Para entender a lógica e os objetivos de um ataque de Cross-Site Tracing (XST), é necessário estar familiarizado com [ataques de cross-site scripting (XSS)](https://owasp.org/www-community/attacks/xss/).

O método `TRACE`, destinado a testes e depuração, instrui o servidor web a refletir a mensagem recebida de volta ao cliente. Esse método, embora aparentemente inofensivo, pode ser aproveitado com sucesso em alguns cenários para roubar credenciais legítimas de usuários. Essa técnica de ataque foi descoberta por Jeremiah Grossman em 2003, em uma tentativa de burlar o atributo [HttpOnly](https://owasp.org/www-community/HttpOnly) que visa proteger cookies contra acesso por JavaScript. No entanto, o método TRACE pode ser usado para contornar essa proteção e acessar o cookie mesmo quando esse atributo está definido.

Teste o potencial de Cross-Site Tracing emitindo uma solicitação como a seguinte:

```bash
$ ncat www.vitima.com 80
TRACE / HTTP/1.1
Host: www.vitima.com
Random: Header

HTTP/1.1 200 OK
Random: Header
...
```

O servidor web retornou um 200 e refletiu o cabeçalho aleatório que foi configurado. Para explorar ainda mais esse problema:

```bash
$ ncat www.vitima.com 80
TRACE / HTTP/1.1
Host: www.vitima.com
Ataque: <script>prompt()</script>
```

O exemplo acima funciona se a resposta estiver sendo refletida no contexto HTML.

Em navegadores mais antigos, os ataques eram realizados usando a tecnologia [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest), que vazava os cabeçalhos quando o servidor os refletia (*por exemplo,* Cookies, tokens de autorização, etc.) e contornava medidas de segurança como o atributo [HttpOnly](../06-Session_Management_Testing/02-Testing_for_Cookies_Attributes.md#httponly-attribute). Este ataque pode ser realizado em navegadores mais recentes apenas se a aplicação se integrar a tecnologias semelhantes ao Flash.

### Testando Substituição de Métodos HTTP

Alguns frameworks da web oferecem uma maneira de substituir o método HTTP real na solicitação, emulando os verbos HTTP ausentes passando alguns cabeçalhos personalizados nas solicitações. O principal objetivo disso é contornar algumas limitações de middleware (por exemplo, proxy, firewall) onde os métodos geralmente permitidos não abrangem verbos como `PUT` ou `DELETE`. Os seguintes cabeçalhos alternativos podem ser usados para fazer essa substituição de método HTTP:

- `X-HTTP-Method`
- `X-HTTP-Method-Override`
- `X-Method-Override`

Para testar isso, nos cenários em que verbos restritos, como PUT ou DELETE, retornam um "405 Método não permitido", reproduza a mesma solicitação com a adição dos cabeçalhos alternativos para substituição de método HTTP e observe como o sistema responde. A aplicação deve responder com um código de status diferente (*por exemplo,* 200) nos casos em que a substituição de método é suportada.

O servidor web no exemplo a seguir não permite o método `DELETE` e o bloqueia:

```bash
$ ncat www.exemplo.com 80
DELETE /recurso.html HTTP/1.1
Host: www.exemplo.com

HTTP/1.1 405 Método Não Permitido
Date: Sáb, 04 Abr 2020 18:26:53 GMT
Server: Apache
Allow: GET,HEAD,POST,OPTIONS
Content-Length: 320
Content-Type: text/html; charset=iso-8859-1
Vary: Accept-Encoding
```

Após adicionar o cabeçalho `X-HTTP-Header`, o servidor responde à solicitação com um 200:

```bash
$ ncat www.exemplo.com 80
DELETE /recurso.html HTTP/1.1
Host: www.exemplo.com
X-HTTP-Method: DELETE

HTTP/1.1 200 OK
Date: Sáb, 04 Abr 2020 19:26:01 GMT
Server: Apache
```

## Remediação

- Garanta que apenas os cabeçalhos necessários sejam permitidos e que os cabeçalhos permitidos estejam configurados corretamente.
- Certifique-se de que não existam soluções alternativas implementadas para contornar medidas de segurança implementadas por agentes de usuário, frameworks ou servidores web.

## Ferramentas

- [Ncat](https://nmap.org/ncat/)
- [cURL](https://curl.haxx.se/)
- [Script NSE http-methods do nmap](https://nmap.org/nsedoc/scripts/http-methods.html)
- [Plugin htaccess_methods do w3af](http://w3af.org/plugins/audit/htaccess_methods)

## Referências

- [RFC 2109](https://tools.ietf.org/html/rfc2109) e [RFC 2965](https://tools.ietf.org/html/rfc2965): "HTTP State Management Mechanism"
- [HTACCESS: Método BILBAO Exposto](https://web.archive.org/web/20160616172703/http://www.kernelpanik.org/docs/kernelpanik/bme.eng.pdf)
- [Amit Klein: "Variantes de ataque XS(T) que podem, em alguns casos, eliminar a necessidade de TRACE"](https://www.securityfocus.com/archive/107/308433)
- [Fortify - Substituição de Método HTTP frequentemente mal utilizada](https://vulncat.fortify.com/en/detail?id=desc.dynamic.xtended_preview.often_misused_http_method_override)
- [CAPEC-107: Cross Site Tracing](https://capec.mitre.org/data/definitions/107.html)