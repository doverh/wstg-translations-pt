# Testando a Exposição de Variáveis de Sessão

|ID          |
|------------|
|WSTG-SESS-04|

## Resumo

Os Tokens de Sessão (Cookie, SessionID, Campo Oculto), se expostos, geralmente permitem que um atacante se faça passar por uma vítima e acesse a aplicação ilegitimamente. É importante protegê-los contra espionagem o tempo todo, principalmente durante a transmissão entre o navegador do cliente e os servidores da aplicação.

As informações aqui se referem a como a segurança de transporte se aplica à transferência de dados sensíveis do ID de Sessão, em vez de dados em geral, e pode ser mais rigorosa do que as políticas de armazenamento em cache e transporte aplicadas aos dados servidos pelo site.

Usando um proxy pessoal, é possível obter as seguintes informações sobre cada solicitação e resposta:

- Protocolo usado (por exemplo, HTTP vs HTTPS)
- Cabeçalhos HTTP
- Corpo da mensagem (por exemplo, POST ou conteúdo da página)

Cada vez que os dados do ID de Sessão são passados entre o cliente e o servidor, o protocolo, as diretivas de cache e privacidade e o corpo devem ser examinados. A segurança de transporte aqui se refere a IDs de Sessão passados em solicitações GET ou POST, corpos de mensagem ou outros meios por meio de solicitações HTTP válidas.

## Objetivos do Teste

- Garantir que a criptografia adequada seja implementada.
- Revisar a configuração de armazenamento em cache.
- Avaliar a segurança do canal e dos métodos.

## Como Testar

### Testando Vulnerabilidades de Criptografia e Reutilização de Tokens de Sessão

A proteção contra espionagem é frequentemente fornecida pela criptografia SSL, mas pode incorporar outros métodos de tunelamento ou criptografia. Deve-se observar que a criptografia ou o hash criptográfico do ID de Sessão deve ser considerado separadamente da criptografia de transporte, pois o próprio ID de Sessão está sendo protegido, não os dados que podem ser representados por ele.

Se o ID de Sessão puder ser apresentado por um atacante à aplicação para obter acesso, ele deve ser protegido em trânsito para mitigar esse risco. Portanto, deve-se garantir que a criptografia seja tanto a padrão quanto aplicada para qualquer solicitação ou resposta em que o ID de Sessão seja transmitido, independentemente do mecanismo usado (por exemplo, um campo de formulário oculto). Verificações simples, como substituir `https://` por `http://` durante a interação com a aplicação, devem ser realizadas, juntamente com a modificação de postagens de formulários para determinar se há uma segregação adequada entre os sites seguros e não seguros.

Observe que se houver também um elemento no site em que o usuário é rastreado com IDs de Sessão, mas a segurança não está presente (por exemplo, observando quais documentos públicos um usuário registrado baixa), é essencial que um ID de Sessão diferente seja usado. Portanto, o ID de Sessão deve ser monitorado à medida que o cliente muda de elementos seguros para não seguros para garantir que um diferente seja usado.

> Toda vez que a autenticação for bem-sucedida, o usuário deve esperar receber:
>
> - Um token de sessão diferente
> - Um token enviado por canal criptografado sempre que fizer uma solicitação HTTP

### Testando Vulnerabilidades de Proxys e Armazenamento em Cache

Proxys também devem ser considerados ao revisar a segurança da aplicação. Em muitos casos, os clientes acessarão a aplicação por meio de proxies corporativos, de ISP ou de outros proxies ou gateways de protocolo conscientes (por exemplo, firewalls). O protocolo HTTP fornece diretivas para controlar o comportamento dos proxies downstream, e a implementação correta dessas diretivas também deve ser avaliada.

Em geral, o ID de Sessão nunca deve ser enviado por transporte não criptografado e nunca deve ser armazenado em cache. A aplicação deve ser examinada para garantir que as comunicações criptografadas sejam tanto o padrão quanto aplicadas para qualquer transferência de IDs de Sessão. Além disso, sempre que o ID de Sessão for transmitido, devem ser implementadas diretivas para evitar seu armazenamento em cache por caches intermediários e até mesmo caches locais.

A aplicação também deve ser configurada para proteger dados em caches tanto sobre HTTP/1.0 quanto sobre HTTP/1.1 – o RFC 2616 discute os controles apropriados com referência ao HTTP. O HTTP/1.1 fornece vários mecanismos de controle de cache. `Cache-Control: no-cache` indica que um proxy não deve reutilizar nenhum dado. Embora `Cache-Control: Private` pareça ser uma diretiva adequada, isso ainda permite que um proxy não compartilhado armazene em cache dados. No caso de cyber cafés ou outros sistemas compartilhados, isso apresenta um risco claro. Mesmo com estações de trabalho de usuário único, o ID de Sessão armazenado em cache pode ser exposto por meio de uma violação do sistema de arquivos ou quando são usados armazenamentos de rede. Caches HTTP/1.0 não reconhecem a diretiva `Cache-Control: no-cache`.

> As diretivas `Expires: 0` e `Cache-Control: max-age=0` devem ser usadas para garantir ainda mais que os caches não exponham os dados. Cada solicitação/resposta que passa dados do ID de Sessão deve ser examinada para garantir que as diretivas de cache apropriadas estejam em uso.

### Testando Vulnerabilidades de GET e POST

Em geral, as solicitações GET não devem ser usadas, pois o ID de Sessão pode ser exposto em logs de proxies ou firewalls. Além disso, elas são muito mais facilmente manipuladas do que outros tipos de transporte, embora deva ser observado que quase qualquer mecanismo pode ser manipulado pelo cliente com as ferramentas certas. Além disso, os ataques [Cross-site Scripting (XSS)](https://owasp.org/www-community/attacks/xss/) são mais facilmente explorados ao enviar um link especialmente construído para a vítima. Isso é muito menos provável se os dados são enviados do cliente como POSTs.

Todo o código do lado do servidor que recebe dados de solicitações POST deve ser testado

 para garantir que não aceita os dados se enviados como GET. Por exemplo, considere a seguinte solicitação POST (`http://owaspapp.com/login.asp`) gerada por uma página de login.

```http
POST /login.asp HTTP/1.1
Host: owaspapp.com
[...]
Cookie: ASPSESSIONIDABCDEFG=ASKLJDLKJRELKHJG
Content-Length: 51

Login=Username&password=Password&SessionID=12345678
```

Se login.asp for implementado de maneira inadequada, pode ser possível fazer login usando a seguinte URL: `http://owaspapp.com/login.asp?Login=Username&password=Password&SessionID=12345678`

Scripts do lado do servidor potencialmente inseguros podem ser identificados verificando cada POST dessa maneira.

### Testando Vulnerabilidades de Transporte

Toda interação entre o Cliente e a Aplicação deve ser testada pelo menos com base nos seguintes critérios.

- Como os IDs de Sessão são transferidos? Por exemplo, GET, POST, Campo de Formulário (incluindo campos ocultos)
- Os IDs de Sessão são sempre enviados por transporte criptografado por padrão?
- É possível manipular a aplicação para enviar IDs de Sessão não criptografados? Por exemplo, alterando HTTP para HTTPS?
- Quais diretrizes de controle de cache são aplicadas a solicitações/respostas que passam IDs de Sessão?
- Essas diretrizes estão sempre presentes? Se não, onde estão as exceções?
- Solicitações GET que incorporam o ID de Sessão são usadas?
- Se POST for usado, pode ser trocado por GET?

## Referências

### Artigos

- [RFCs 2109 & 2965 – Mecanismo de Gerenciamento de Estado HTTP [D. Kristol, L. Montulli]](https://www.ietf.org/rfc/rfc2965.txt)
- [RFC 2616 – Protocolo de Transferência de Hipertexto - HTTP/1.1](https://www.ietf.org/rfc/rfc2616.txt)
