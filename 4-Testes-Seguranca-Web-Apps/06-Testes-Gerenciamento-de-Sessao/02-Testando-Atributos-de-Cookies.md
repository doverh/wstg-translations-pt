# Testando Atributos de Cookies

|ID          |
|------------|
|WSTG-SESS-02|

## Resumo

Os Cookies da Web (aqui referidos como cookies) são frequentemente um vetor de ataque chave para usuários maliciosos (geralmente visando outros usuários), e a aplicação deve sempre tomar a devida diligência para proteger os cookies.

O HTTP é um protocolo sem estado, o que significa que não mantém nenhuma referência às solicitações enviadas pelo mesmo usuário. Para corrigir esse problema, as sessões foram criadas e anexadas às solicitações HTTP. Navegadores, conforme discutido em [teste de armazenamento do navegador](../11-Testes-a-Nivel-do-Cliente/12-Testando-o-Armazenamento-do-Navegador.md), contêm uma variedade de mecanismos de armazenamento. Nessa seção do guia, cada um é discutido detalhadamente.

O mecanismo de armazenamento de sessão mais utilizado em navegadores é o armazenamento de cookies. Cookies podem ser definidos pelo servidor, incluindo um cabeçalho [`Set-Cookie`](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Headers/Set-Cookie) na resposta HTTP ou via JavaScript. Cookies podem ser usados para uma variedade de razões, tais como:

- gerenciamento de sessão
- personalização
- rastreamento

Para proteger dados de cookies, a indústria desenvolveu meios para ajudar a restringir esses cookies e limitar sua superfície de ataque. Com o tempo, os cookies se tornaram um mecanismo de armazenamento preferido para aplicações web, pois permitem grande flexibilidade no uso e na proteção.

Os meios para proteger os cookies são:

- [Atributos de Cookie](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Cookies#Creating_cookies)
- [Prefixos de Cookie](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Cookies#Cookie_prefixes)

## Objetivos do Teste

- Garantir que a configuração de segurança adequada seja definida para os cookies.

## Como Testar

Abaixo, uma descrição de cada atributo e prefixo será discutida. O testador deve validar se estão sendo usados corretamente pela aplicação. Cookies podem ser revisados usando um [proxy de interceptação](#proxy-de-interceptação) ou revisando o repositório de cookies do navegador.

### Atributos de Cookie

#### Atributo Secure

O atributo [`Secure`](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Headers/Set-Cookie#Secure) instrui o navegador a enviar o cookie apenas se a solicitação estiver sendo enviada por meio de um canal seguro, como `HTTPS`. Isso ajudará a proteger o cookie contra ser enviado em solicitações não criptografadas. Se a aplicação puder ser acessada tanto por `HTTP` quanto por `HTTPS`, um atacante poderia redirecionar o usuário para enviar seu cookie como parte de solicitações não protegidas.

#### Atributo HttpOnly

O atributo [`HttpOnly`](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Headers/Set-Cookie#HttpOnly) é usado para ajudar a prevenir ataques, como vazamento de sessão, pois não permite que o cookie seja acessado por um script do lado do cliente, como JavaScript.

> Isso não limita toda a superfície de ataque de ataques XSS, pois um atacante ainda poderia enviar solicitações no lugar do usuário, mas limita imensamente o alcance dos vetores de ataque XSS.

#### Atributo Domain

O atributo [`Domain`](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Cookies#Scope_of_cookies) é usado para comparar o domínio do cookie com o domínio do servidor para o qual a solicitação HTTP está sendo feita. Se o domínio corresponder ou se for um subdomínio, então o atributo [`path`](#atributo-path) será verificado em seguida.

Note que apenas hosts que pertencem ao domínio especificado podem definir um cookie para esse domínio. Além disso, o atributo `domain` não pode ser um domínio de nível superior (como `.gov` ou `.com`) para evitar que servidores definam cookies arbitrários para outro domínio (como definir um cookie para `owasp.org`). Se o atributo de domínio não estiver definido, então o nome do host do servidor que gerou o cookie é usado como o valor padrão do `domain`.

Por exemplo, se um cookie for definido por uma aplicação em `app.meudominio.com` sem o atributo de domínio definido, então o cookie seria reenviado para todas as solicitações subsequentes para `app.meudominio.com` e seus subdomínios (como `hacker.app.meudominio.com`), mas não para `outraapp.meudominio.com`. Se um desenvolvedor quisesse afrouxar essa restrição, então ele poderia definir o atributo de domínio para `meudominio.com`. Neste caso, o cookie seria enviado para todas as solicitações para `app.meudominio.com` e subdomínios de `meudominio.com`, como `hacker.app.meudominio.com`, e até `banco.meudominio.com`. Se houvesse um servidor vulnerável em um subdomínio (por exemplo, `outraapp.meudominio.com`) e o atributo de domínio tivesse sido definido muito frouxamente (por exemplo, `meudominio.com`), então o servidor vulnerável poderia ser usado para coletar cookies (como tokens de sessão) em todo o escopo de `meudominio.com`.

#### Atributo Path

O atributo [`Path`](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Cookies#Scope_of_cookies) desempenha um papel importante na definição do escopo dos cookies em conjunto com o [`domínio`](#atributo-domain). Além do domínio, o caminho URL para o qual o cookie é válido pode ser especificado. Se o domínio e o caminho coincidirem, então o cookie será enviado na solicitação. Assim como o atributo de domínio, se o atributo de caminho for definido muito frouxamente, isso poderá deixar a aplicação vulnerável a ataques de outras aplicações no mesmo servidor. Por exemplo, se o atributo de caminho fosse definido como a raiz do servidor web `/`, então os

 cookies da aplicação seriam enviados para todas as aplicações dentro do mesmo domínio (se várias aplicações residirem sob o mesmo servidor). Alguns exemplos para múltiplas aplicações sob o mesmo servidor:

- `path=/banco`
- `path=/privado`
- `path=/docs`
- `path=/docs/admin`

#### Atributo Expires

O atributo [`Expires`](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Cookies#Permanent_cookies) é usado para:

- definir cookies persistentes
- limitar a vida útil se uma sessão durar muito tempo
- remover um cookie forçadamente definindo-o para uma data passada

Ao contrário dos [cookies de sessão](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Cookies#Session_cookies), cookies persistentes serão usados pelo navegador até que o cookie expire. Uma vez que a data de expiração ultrapassou o tempo definido, o navegador excluirá o cookie.

#### Atributo SameSite

O atributo [`SameSite`](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Cookies#SameSite_cookies) é usado para afirmar que um cookie não deve ser enviado junto com solicitações entre sites. Esse recurso permite que o servidor mitigue o risco de vazamento de informações entre organizações. Em alguns casos, também é usado como estratégia de redução de risco (ou mecanismo de defesa em profundidade) para evitar ataques de [falsificação de solicitação entre sites](05-Testando-CSRF.md). Esse atributo pode ser configurado em três modos diferentes:

- `Strict`
- `Lax`
- `None`

##### Valor Estrito

O valor `Strict` é o uso mais restritivo do `SameSite`, permitindo que o navegador envie o cookie apenas para o contexto da primeira parte sem navegação de nível superior. Em outras palavras, os dados associados ao cookie serão enviados apenas em solicitações que correspondam ao site atual mostrado na barra de URL do navegador. O cookie não será enviado em solicitações geradas por sites de terceiros. Esse valor é especialmente recomendado para ações realizadas no mesmo domínio. No entanto, pode ter algumas limitações com alguns sistemas de gerenciamento de sessões afetando negativamente a experiência de navegação do usuário. Como o navegador não enviaria o cookie em solicitações geradas a partir de um domínio ou e-mail de terceiros, o usuário seria obrigado a fazer login novamente, mesmo que já tenha uma sessão autenticada.

##### Valor Laxo

O valor `Lax` é menos restritivo do que o `Strict`. O cookie será enviado se a URL for igual ao domínio do cookie (primeira parte), mesmo que o link venha de um domínio de terceiros. Esse valor é considerado pela maioria dos navegadores como o comportamento padrão, pois oferece uma melhor experiência do usuário do que o valor `Strict`. Ele não é acionado para ativos, como imagens, onde cookies podem não ser necessários para acessá-los.

##### Valor Nenhum

O valor `None` especifica que o navegador enviará o cookie em solicitações entre sites (o comportamento normal antes da implementação de `SameSite`) somente se o atributo `Secure` também for usado, por exemplo, `SameSite=None; Secure`. É um valor recomendado, em vez de não especificar nenhum valor de `SameSite`, pois força o uso do [`atributo seguro`](#atributo-secure).

### Prefixos de Cookie

Por design, os cookies não têm a capacidade de garantir a integridade e confidencialidade das informações armazenadas neles. Essas limitações tornam impossível para um servidor ter confiança sobre como os atributos de um determinado cookie foram definidos na criação. Para dar aos servidores esses recursos de maneira compatível com versões anteriores, a indústria introduziu o conceito de [`Prefixos de Nome de Cookie`](https://tools.ietf.org/html/draft-ietf-httpbis-cookie-prefixes-00) para facilitar a passagem desses detalhes incorporados como parte do nome do cookie.

#### Prefixo de Host

O prefixo [`__Host-`](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Cookies#Cookie_prefixes) espera que os cookies atendam às seguintes condições:

1. O cookie deve ser definido com o [`atributo Secure`](#atributo-secure).
2. O cookie deve ser definido a partir de um URI considerado seguro pelo agente do usuário.
3. Enviado apenas para o host que def

iniu o cookie e NÃO DEVE incluir nenhum [`atributo de domínio`](#atributo-domain).
4. O cookie deve ser definido com o [`atributo de caminho`](#atributo-path) com um valor de `/` para ser enviado para todas as solicitações ao host.

Por esse motivo, o cookie `Set-Cookie: __Host-SID=12345; Secure; Path=/` seria aceito, enquanto qualquer um dos seguintes sempre seria rejeitado:
`Set-Cookie: __Host-SID=12345`
`Set-Cookie: __Host-SID=12345; Secure`
`Set-Cookie: __Host-SID=12345; Domain=site.exemplo`
`Set-Cookie: __Host-SID=12345; Domain=site.exemplo; Path=/`
`Set-Cookie: __Host-SID=12345; Secure; Domain=site.exemplo; Path=/`

#### Prefixo Seguro

O prefixo [`__Secure-`](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Cookies#Cookie_prefixes) é menos restritivo e pode ser introduzido adicionando a string sensível a maiúsculas e minúsculas `__Secure-` ao nome do cookie. Qualquer cookie que corresponda ao prefixo `__Secure-` deverá atender às seguintes condições:

1. O cookie deve ser definido com o atributo `Secure`.
2. O cookie deve ser definido a partir de um URI considerado seguro pelo agente do usuário.

### Práticas Sólidas

Com base nas necessidades da aplicação e em como o cookie deve funcionar, os atributos e prefixos devem ser aplicados. Quanto mais o cookie estiver protegido, melhor.

Juntando tudo isso, podemos definir a configuração de atributos de cookie mais segura como: `Set-Cookie: __Host-SID=<token de sessão>; path=/; Secure; HttpOnly; SameSite=Strict`.

## Ferramentas

### Proxy de Interceptação

- [OWASP Zed Attack Proxy Project](https://www.zaproxy.org)
- [Web Proxy Burp Suite](https://portswigger.net)

### Plug-in do Navegador

- [Tamper Data para FF Quantum](https://addons.mozilla.org/pt-BR/firefox/addon/tamper-data-for-ff-quantum/)
- ["FireSheep" para FireFox](https://github.com/codebutler/firesheep)
- ["EditThisCookie" para Chrome](https://chrome.google.com/webstore/detail/editthiscookie/fngmhnnpilhplaeedifhccceomclgfbg?hl=pt-BR)
- ["Cookiebro - Gerenciador de Cookies" para FireFox](https://addons.mozilla.org/pt-BR/firefox/addon/cookiebro/)

## Referências

- [RFC 2965 - Mecanismo de Gerenciamento de Estado HTTP](https://tools.ietf.org/html/rfc2965)
- [RFC 2616 - Protocolo de Transferência de Hipertexto - HTTP 1.1](https://tools.ietf.org/html/rfc2616)
- [Cookies Same-Site - rascunho-ietf-httpbis-cookie-same-site-00](https://tools.ietf.org/html/draft-ietf-httpbis-cookie-same-site-00)
- [O importante atributo "expires" do Set-Cookie](https://seckb.yehg.net/2012/02/important-expires-attribute-of-set.html)
- [ID de Sessão HttpOnly na URL e Corpo da Página](https://seckb.yehg.net/2012/06/httponly-session-id-in-url-and-page.html)
