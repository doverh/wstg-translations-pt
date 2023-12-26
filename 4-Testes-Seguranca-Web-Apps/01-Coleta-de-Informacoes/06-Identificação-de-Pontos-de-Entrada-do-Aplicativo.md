# Identificação de Pontos de Entrada do aplicativo

|ID          |
|------------|
|WSTG-INFO-06|

## Resumo

Enumerar a aplicação e sua superfície de ataque é um precursor essencial antes que qualquer teste abrangente possa ser realizado, pois permite ao testador identificar áreas prováveis de fraqueza. Esta seção tem como objetivo ajudar a identificar e mapear áreas dentro do aplicativo que devem ser investigadas após a conclusão da enumeração e do mapeamento.

## Objetivos do Teste

- Identificar possíveis pontos de entrada e injeção de dados por meio da análise de solicitações e respostas.

## Como Testar

Antes de iniciar qualquer teste, o testador deve sempre obter uma boa compreensão do aplicativo e de como o usuário e o navegador se comunicam com ela. Conforme o testador percorre a aplicação, deve prestar atenção em todas as solicitações HTTP, bem como em cada parâmetro e campo de formulário que é enviado para a aplicação. Deve-se dar atenção especial ao uso de solicitações GET e POST para passar parâmetros para a aplicação. Além disso, é necessário prestar atenção quando outros métodos para serviços RESTful são usados.

Observe que, para ver os parâmetros enviados no corpo de solicitações, como uma solicitação POST, o testador pode querer usar uma ferramenta como um proxy de interceptação (consulte [ferramentas](#ferramentas)). Dentro da solicitação POST, o testador também deve fazer uma nota especial de quaisquer campos de formulário ocultos que estão sendo enviados para a aplicação, pois esses geralmente contêm informações sensíveis, como informações de estado, quantidade de itens, preço de itens, que o desenvolvedor nunca pretendia que qualquer pessoa visse ou alterasse.

Na experiência do autor, tem sido muito útil usar um proxy de interceptação e uma planilha para esta etapa de teste. O proxy manterá o controle de cada solicitação e resposta entre o testador e a aplicação enquanto a exploram. Além disso, nesse ponto, os testadores geralmente capturam todas as solicitações e respostas para que possam ver exatamente cada cabeçalho, parâmetro, etc. que está sendo enviado para a aplicação e o que está sendo retornado. Isso pode ser bastante tedioso às vezes, especialmente em sites interativos grandes (pense em uma aplicação bancária). No entanto, a experiência mostrará o que procurar, e esta fase pode ser significativamente reduzida.

Conforme o testador percorre a aplicação, deve fazer uma nota de quaisquer parâmetros interessantes na URL, cabeçalhos personalizados ou no corpo das solicitações/respostas e salvá-los em uma planilha. A planilha deve incluir a página solicitada (pode ser bom também adicionar o número da solicitação do proxy, para referência futura), os parâmetros interessantes, o tipo de solicitação (GET, POST, etc.), se o acesso é autenticado/não autenticado, se o TLS é usado, se faz parte de um processo de vários passos, se os WebSockets são usados e quaisquer outras notas relevantes. Uma vez que todas as áreas do aplicativo estejam mapeadas, então o testador pode percorrer a aplicação e testar cada uma das áreas identificadas e fazer anotações sobre o que funcionou e o que não funcionou. O restante deste guia identificará como testar cada uma dessas áreas de interesse, mas esta seção deve ser realizada antes que qualquer teste real possa começar.

Abaixo estão alguns pontos de interesse para todas as solicitações e respostas. Na seção de solicitações, concentre-se nos métodos GET e POST, pois esses são os mais comuns. Note que outros métodos, como PUT e DELETE, podem ser usados. Muitas vezes, essas solicitações menos comuns, se permitidas, podem expor vulnerabilidades. Há uma seção especial neste guia dedicada para testar esses métodos HTTP.

### Solicitações

- Identificar onde são usados GETs e onde são usados POSTs.
- Identificar todos os parâmetros usados em uma solicitação POST (esses estão no corpo da solicitação).
- Dentro da solicitação POST, prestar atenção especial a quaisquer parâmetros ocultos. Quando um POST é enviado, todos os campos do formulário (incluindo parâmetros ocultos) serão enviados no corpo da mensagem HTTP para a aplicação. Geralmente, esses não são vistos a menos que um proxy ou a visualização do código-fonte HTML seja usada. Além disso, a próxima página exibida, seus dados e o nível de acesso podem ser diferentes dependendo do valor do(s) parâmetro(s) oculto(s).
- Identificar todos os parâmetros usados em uma solicitação GET (ou seja, URL), especialmente a string de consulta (geralmente após um ?).
- Identificar todos os parâmetros da string de consulta. Estes geralmente estão em um formato de par, como `foo=bar`. Também observe que muitos parâmetros podem estar em uma única string de consulta, separados por `&`, `\~`, `:`, ou qualquer outro caractere especial ou codificação.
- Uma nota especial quando se trata de identificar vários parâmetros em uma única string ou dentro de uma solicitação POST é que alguns ou todos os parâmetros serão necessários para executar os ataques. O testador precisa identificar todos os parâmetros (mesmo que codificados ou criptografados) e identificar quais são processados pela aplicação. Seções posteriores do guia identificarão como testar esses parâmetros. Neste ponto, certifique-se apenas de que cada um deles seja identificado.
- Prestar atenção também a quaisquer cabeçalhos adicionais ou de tipo personalizado que normalmente não são vistos (como `debug: false`).

### Respostas

- Identificar onde novos cookies são definidos (cabeçalho `Set-Cookie`), modificados ou adicionados.
- Identificar onde há redirecionamentos (código de status HTTP 3xx), códigos de status 400, em particular 403 Forbidden, e erros internos do servidor 500 durante respostas normais (ou seja, solicitações não modificadas).
- Também observar onde são usados quaisquer cabeçalhos interessantes. Por exemplo, `Server: BIG-IP` indica que o site está

 balanceado. Assim, se um site estiver balanceado e um servidor estiver configurado incorretamente, o testador pode ter que fazer várias solicitações para acessar o servidor vulnerável, dependendo do tipo de balanceamento de carga usado.

### Teste de Caixa Preta

#### Testando Pontos de Entrada do aplicativo

A seguir estão dois exemplos de como verificar os pontos de entrada do aplicativo.

#### Exemplo 1

Este exemplo mostra uma solicitação GET que compraria um item de uma aplicação de compras online.

```http
GET /shoppingApp/buyme.asp?CUSTOMERID=100&ITEM=z101a&PRICE=62.50&IP=x.x.x.x HTTP/1.1
Host: x.x.x.x
Cookie: SESSIONID=Z29vZCBqb2IgcGFkYXdhIG15IHVzZXJuYW1lIGlzIGZvbyBhbmQgcGFzc3dvcmQgaXMgYmFy
```

> Aqui, o testador notaria todos os parâmetros da solicitação, como CUSTOMERID, ITEM, PRICE, IP, e o Cookie (que poderia ser apenas parâmetros codificados ou usados para o estado da sessão).

#### Exemplo 2

Este exemplo mostra uma solicitação POST que o logaria em uma aplicação.

```http
POST /KevinNotSoGoodApp/authenticate.asp?service=login HTTP/1.1
Host: x.x.x.x
Cookie: SESSIONID=dGhpcyBpcyBhIGJhZCBhcHAgdGhhdCBzZXRzIHByZWRpY3RhYmxlIGNvb2tpZXMgYW5kIG1pbmUgaXMgMTIzNA==;CustomCookie=00my00trusted00ip00is00x.x.x.x00

user=admin&pass=pass123&debug=true&fromtrustIP=true
```

> Neste exemplo, o testador notaria todos os parâmetros como antes, no entanto, a maioria dos parâmetros é passada no corpo da solicitação e não na URL. Além disso, observe que há um cabeçalho HTTP personalizado (`CustomCookie`) sendo usado.

### Teste de Caixa Cinza

Testar os pontos de entrada do aplicativo por meio de uma metodologia de caixa cinza consistiria em tudo o que já foi identificado acima com uma adição. Nos casos em que há fontes externas das quais a aplicação recebe dados e os processa (como armadilhas SNMP, mensagens syslog, SMTP ou mensagens SOAP de outros servidores), uma reunião com os desenvolvedores do aplicativo pode identificar quaisquer funções que aceitariam ou esperariam a entrada do usuário e como elas são formatadas. Por exemplo, o desenvolvedor poderia ajudar a entender como formular uma solicitação SOAP correta que a aplicação aceitaria e onde o serviço da web reside (se o serviço da web ou qualquer outra função ainda não foi identificado durante o teste de caixa preta).

#### OWASP Attack Surface Detector

A ferramenta Attack Surface Detector (ASD) investiga o código-fonte e descobre os pontos finais de uma aplicação web, os parâmetros que esses pontos finais aceitam e o tipo de dados desses parâmetros. Isso inclui os pontos finais não vinculados que uma aranha não será capaz de encontrar ou parâmetros opcionais totalmente não utilizados no código do lado do cliente. Ele também tem a capacidade de calcular as mudanças na superfície de ataque entre duas versões de uma aplicação.

O Attack Surface Detector está disponível como um plugin tanto para o ZAP quanto para o Burp Suite, e uma ferramenta de linha de comando também está disponível. O arquivo jar da linha de comando está disponível para download em [https://github.com/secdec/attack-surface-detector-cli/releases](https://github.com/secdec/attack-surface-detector-cli/releases).

Você pode executar o seguinte comando para o ASD identificar os pontos finais no código-fonte do aplicativo web alvo.

`java -jar attack-surface-detector-cli-1.3.5.jar <caminho-do-codigo-fonte> [flags]`

Aqui está um exemplo de execução do comando no [OWASP RailsGoat](https://github.com/OWASP/railsgoat).

```text
$ java -jar attack-surface-detector-cli-1.3.5.jar railsgoat/
Beginning endpoint detection for '<...>/railsgoat' with 1 framework types
Using framework=RAILS
[0] GET: /login (0 variants): PARAMETERS={url=name=url, paramType=QUERY_STRING, dataType=STRING}; FILE=/app/controllers/sessions_contro
ller.rb (lines '6'-'9')
[1] GET: /logout (0 variants): PARAMETERS={}; FILE=/app/controllers/sessions_controller.rb (lines '33'-'37')
[2] POST: /forgot_password (0 variants): PARAMETERS={email=name=email, paramType=QUERY_STRING, dataType=STRING}; FILE=/app/controllers/
password_resets_controller.rb (lines '29'-'38')
[3] GET: /password_resets (0 variants): PARAMETERS={token=name=token, paramType=QUERY_STRING, dataType=STRING}; FILE=/app/controllers/p
assword_resets_controller.rb (lines '19'-'27')
[4] POST: /password_resets (0 variants): PARAMETERS={password=name=password, paramType=QUERY_STRING, dataType=STRING, user=name=user, paramType=QUERY_STRING, dataType=STRING, confirm_password=name=confirm_password, paramType=QUERY_STRING, dataType=STRING}; FILE=/app/controllers/password_resets_controller.rb (lines '5'-'17')
[5] GET: /sessions/new (0 variants): PARAMETERS={url=name=url, paramType=QUERY_STRING, dataType=STRING}; FILE=/app/controllers/sessions_controller.rb (lines '6'-'9')
[6] POST: /sessions (0 variants): PARAMETERS={password=name=password, paramType=QUERY_STRING, dataType=STRING, user_id=name=user_id, paramType=SESSION, dataType=STRING, remember_me=name=remember_me, paramType=QUERY_STRING, dataType=STRING, url=name=url, paramType=QUERY_STRING, dataType=STRING, email=name=email, paramType=QUERY_STRING, dataType=STRING}; FILE=/app/controllers/sessions_controller.rb (lines '11'-'31')
[7] DELETE: /sessions/{id} (0 variants): PARAMETERS={}; FILE=/app/controllers/sessions_controller.rb (lines '33'-'37')
[8] GET: /users (0 variants): PARAMETERS={}; FILE=/app/controllers/api/v1/users_controller.rb (lines '9'-'11')
[9] GET: /users/{id} (0 variants): PARAMETERS={}; FILE=/app/controllers/api/v1/users_controller.rb (lines '13'-'15')
... snipped ...
[38] GET: /api/v1/mobile/{id} (0 variants): PARAMETERS={id=name=id, paramType=QUERY_STRING, dataType=STRING, class=name=class, paramType=QUERY_STRING, dataType=STRING}; FILE=/app/controllers/api/v1/mobile_controller.rb (lines '8'-'13')
[39] GET: / (0 variants): PARAMETERS={url=name=url, paramType=QUERY_STRING, dataType=STRING}; FILE=/app/controllers/sessions_controller.rb (lines '6'-'9')
Generated 40 distinct endpoints with 0 variants for a total of 40 endpoints
Successfully validated serialization for these endpoints
0 endpoints were missing code start line
0 endpoints were missing code end line
0 endpoints had the same code start and end line
Generated 36 distinct parameters
Generated 36 total parameters
- 36/36 have their data type
- 0/36 have a list of accepted values
- 36/36 have their parameter type
--- QUERY_STRING: 35
--- SESSION: 1
Finished endpoint detection for '<...>/railsgoat'
----------
-- DONE --
0 projects had duplicate endpoints
Generated 40 distinct endpoints
Generated 40 total endpoints
Generated 36 distinct parameters
Generated 36 total parameters
1/1 projects had endpoints generated
To enable logging include the -debug argument
```

Certainly! Here is the translated text in Portuguese (Brazilian) along with a Markdown file:

```md
# Você também pode gerar um arquivo de saída JSON usando a opção `-json`, que pode ser usado pelo plugin tanto para o ZAP quanto para o Burp Suite. Consulte os seguintes links para obter mais detalhes.

- [Página inicial do Plugin ASD para OWASP ZAP](https://github.com/secdec/attack-surface-detector-zap/wiki)
- [Página inicial do Plugin ASD para PortSwigger Burp](https://github.com/secdec/attack-surface-detector-burp/wiki)

## Ferramentas

- [OWASP Zed Attack Proxy (ZAP)](https://www.zaproxy.org/)
- [Burp Suite](https://www.portswigger.net/burp/)
- [Fiddler](https://www.telerik.com/fiddler)

## Referências

- [RFC 2616 – Protocolo de Transferência de Hipertexto – HTTP 1.1](https://tools.ietf.org/html/rfc2616)
- [OWASP Attack Surface Detector](https://owasp.org/www-project-attack-surface-detector/)
```
