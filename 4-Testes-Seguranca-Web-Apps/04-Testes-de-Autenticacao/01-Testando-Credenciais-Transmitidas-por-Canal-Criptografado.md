# Testando Credenciais Transmitidas por Canal Criptografado

|ID          |
|------------|
|WSTG-ATHN-01|

## Resumo

O teste para transporte de credenciais verifica se as aplicações web criptografam dados de autenticação em trânsito. Essa criptografia impede que atacantes assumam contas por meio de [sniffing de tráfego de rede](https://owasp.org/www-community/attacks/Man-in-the-middle_attack). Aplicações web utilizam [HTTPS](https://tools.ietf.org/html/rfc2818) para criptografar informações em trânsito para comunicações tanto do cliente para o servidor quanto do servidor para o cliente. Um cliente pode enviar ou receber dados de autenticação durante as interações a seguir:

- Um cliente envia uma credencial para solicitar login.
- O servidor responde a um login bem-sucedido com um [token de sessão](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html#session-id-properties).
- Um cliente autenticado envia um token de sessão para solicitar informações sensíveis do site.
- Um cliente envia um token para o site se esqueceu sua senha.

A falha em criptografar qualquer uma dessas credenciais em trânsito pode permitir que atacantes com ferramentas de sniffer de rede visualizem as credenciais e, possivelmente, as usem para roubar uma conta de usuário. O atacante pode capturar o tráfego diretamente usando o [Wireshark](https://www.wireshark.org) ou ferramentas semelhantes, ou pode configurar um proxy para capturar solicitações HTTP. Dados sensíveis devem ser criptografados em trânsito para evitar isso.

O fato de o tráfego ser criptografado não significa necessariamente que é completamente seguro. A segurança também depende do algoritmo de criptografia usado e da robustez das chaves que a aplicação está usando. Consulte [Testando para Segurança Fraca na Camada de Transporte](../09-Testing_for_Weak_Cryptography/01-Testing_for_Weak_Transport_Layer_Security.md) para verificar se o algoritmo de criptografia é suficiente.

## Objetivos do Teste

- Avaliar se algum caso de uso do site ou aplicação faz com que o servidor ou o cliente troquem credenciais sem criptografia.

## Como Testar

Para testar o transporte de credenciais, capture o tráfego entre um cliente e o servidor da aplicação web que requer credenciais. Verifique se há transferência de credenciais durante o login e durante o uso da aplicação com uma sessão válida. Para configurar o teste:

1. Configure e inicie uma ferramenta para capturar tráfego, como:
   - As [ferramentas de desenvolvedor](https://developer.mozilla.org/en-US/docs/Tools) do navegador da web.
   - Um proxy como o [OWASP ZAP](https://owasp.org/www-project-zap/).
2. Desative quaisquer recursos ou plugins que façam o navegador da web favorecer o HTTPS. Alguns navegadores ou extensões, como o [HTTPS Everywhere](https://www.eff.org/https-everywhere), combaterão o [browsing forçado](https://owasp.org/www-community/attacks/Forced_browsing) redirecionando solicitações HTTP para HTTPS.

No tráfego capturado, procure por dados sensíveis, incluindo os seguintes:

- Senhas, geralmente dentro de um [corpo da mensagem](https://www.w3.org/Protocols/rfc2616/rfc2616-sec4.html).
- Tokens, geralmente dentro de [cookies](https://tools.ietf.org/html/rfc6265#section-4.2).
- Códigos de redefinição de conta ou senha.

Para qualquer mensagem contendo esses dados sensíveis, verifique se a troca ocorreu usando HTTPS (e não HTTP). Os exemplos a seguir mostram dados capturados que indicam testes bem-sucedidos ou falhos, em que a aplicação web está em um servidor chamado `www.exemplo.org`.

### Login

Encontre o endereço da página de login e tente trocar o protocolo para HTTP. Por exemplo, a URL para o browsing forçado pode se parecer com o seguinte: `http://www.exemplo.org/login`.

Se a página de login for normalmente HTTPS, tente remover o "S" para ver se a página de login carrega como HTTP.

Faça login usando uma conta válida enquanto tenta forçar o uso de HTTP não criptografado. Em um teste bem-sucedido, a solicitação de login deve ser HTTPS:

```http
Request URL: https://www.exemplo.org/j_acegi_security_check
Request method: POST
...
Response headers:
HTTP/1.1 302 Found
Server: nginx/1.19.2
Date: Tue, 29 Sep 2020 00:59:04 GMT
Transfer-Encoding: chunked
Connection: keep-alive
X-Content-Type-Options: nosniff
Expires: Thu, 01 Jan 1970 00:00:00 GMT
Set-Cookie: JSESSIONID.a7731d09=node01ai3by8hip0g71kh3ced41pmqf4.node0; Path=/; Secure; HttpOnly
ACEGI_SECURITY_HASHED_REMEMBER_ME_COOKIE=dXNlcmFiYzoxNjAyNTUwNzQ0NDU3OjFmNDlmYTZhOGI1YTZkEtcétera...
Location: https://www.exemplo.org/
...
POST data:
j_username=userabc
j_password=Minha-Senha-Protegida-452
from=/
Submit=Sign in
```

- No login, as credenciais são criptografadas devido à URL de solicitação HTTPS.
- Se o servidor retornar informações de cookie para um token de sessão, o cookie também deve incluir o atributo [`Secure`](https://owasp.org/www-community/controls/SecureFlag) para evitar que o cliente exponha o cookie por meio de canais não criptografados posteriormente. Procure a palavra-chave `Secure` no cabeçalho de

 resposta.

O teste falha se qualquer login transferir uma credencial por HTTP, semelhante ao seguinte:

```http
Request URL: http://www.exemplo.org/j_acegi_security_check
Request method: POST
...
POST data:
j_username=userabc
j_password=Minha-Senha-Protegida-452
from=/
Submit=Sign in
```

Neste exemplo de teste falho:

- A URL de fetch é `http://`, expondo `j_username` e `j_password` em texto simples através dos dados de postagem.
- Neste caso, como o teste já mostra dados de POST expondo todas as credenciais, não faz sentido verificar cabeçalhos de resposta (que também provavelmente exporiam um token de sessão ou cookie).

### Criação de Conta

Para testar a criação de conta não criptografada, tente forçar a navegação até a versão HTTP da criação de conta e crie uma conta, por exemplo: `http://www.exemplo.org/securityRealm/createAccount`

O teste passa se, mesmo após a navegação forçada, o cliente ainda enviar a solicitação de nova conta por meio de HTTPS:

```http
Request URL: https://www.exemplo.org/securityRealm/createAccount
Request method: POST
...
Response headers:
HTTP/1.1 200 OK
Server: nginx/1.19.2
Date: Tue, 29 Sep 2020 01:11:50 GMT
Content-Type: text/html;charset=utf-8
Content-Length: 3139
Connection: keep-alive
X-Content-Type-Options: nosniff
Set-Cookie: JSESSIONID.a7731d09=node011yew1ltrsh1x1k3m6g6b44tip8.node0; Path=/; Secure; HttpOnly
Expires: 0
Cache-Control: no-cache,no-store,must-revalidate
Etcétera...
POST data:
username=user456
fullname=User 456
password1=Minha-Senha-Protegida-808
password2=Minha-Senha-Protegida-808
Submit=Create account
Jenkins-Crumb=58e6f084fd29ea4fe570c31f1d89436a0578ef4d282c1bbe03ffac0e8ad8efd6
```

- Semelhante a um login, a maioria das aplicações web fornece automaticamente um token de sessão em uma criação de conta bem-sucedida. Se houver um `Set-Cookie:`, verifique se ele tem um atributo `Secure;` também.

O teste falha se o cliente enviar uma solicitação de nova conta com HTTP não criptografado:

```http
Request URL: http://www.exemplo.org/securityRealm/createAccount
Request method: POST
...
POST data:
username=user456
fullname=User 456
password1=Minha-Senha-Protegida-808
password2=Minha-Senha-Protegida-808
Submit=Create account
Jenkins-Crumb=8c96276321420cdbe032c6de141ef556cab03d91b25ba60be8fd3d034549cdd3
```

- Este formulário de criação de usuário do Jenkins expôs todos os detalhes do novo usuário (nome, nome completo e senha) nos dados de POST para a página de criação de conta HTTP.

### Redefinição de Senha, Mudança de Senha ou Outra Manipulação de Conta

Assim como no login e na criação de conta, se a aplicação web tiver recursos que permitam a um usuário alterar uma conta ou chamar um serviço diferente com credenciais, verifique se todas essas interações são feitas por HTTPS. As interações a serem testadas incluem:

- Formulários que permitem que os usuários lidem com uma senha esquecida ou outra credencial.
- Formulários que permitem que os usuários editem credenciais.
- Formulários que exigem que o usuário se autentique com outro provedor (por exemplo, processamento de pagamento).

### Acessar Recursos Após o Login

Após fazer login, acesse todos os recursos da aplicação, incluindo recursos públicos que não exigem necessariamente um login para acessar. Faça browsing forçado para a versão HTTP do site para ver se o cliente vaza credenciais.

O teste passa se todas as interações enviarem o token de sessão por HTTPS, semelhante ao exemplo a seguir:

```http
Request URL:http://www.exemplo.org/
Request method:GET
...
Request headers:
GET / HTTP/1.1
Host: www.exemplo.org
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
DNT: 1
Connection: keep-alive
Cookie: JSESSIONID.a7731d09=node01ai3by8hip0g71kh3ced41pmqf4.node0; ACEGI_SECURITY_HASHED_REMEMBER_ME_COOKIE=dXNlcmFiYzoxNjAyNTUwNzQ0NDU3OjFmNDlmYTZhOGI1YTZkEtcétera...
Upgrade-Insecure-Requests: 1
```

- O token de sessão no cookie é criptografado, pois a URL de solicitação é HTTPS.

O teste falha se o navegador enviar um token de sessão por HTTP em qualquer parte do site, mesmo que seja necessário forçar o browsing para acionar esse caso:

```http
Request URL:http://www.exemplo.org/
Request method:GET
...
Request headers:
GET / HTTP/1.1
Host: www.exemplo.org
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Cookie: language=en; welcomebanner_status=dismiss; cookieconsent_status=dismiss; screenResolution=1920x1200; JSESSIONID.c1e7b45b=node01warjbpki6icgxkn0arjbivo84.node0
Upgrade-Insecure-Requests: 1
```

- A solicitação GET expôs o token de sessão `JSESSIONID` (do navegador para o servidor) na URL de solicitação `http://www.exemplo.org/`.

## Remediação

Use HTTPS para todo o site. Implemente

 [HSTS](https://tools.ietf.org/html/rfc6797) e redirecione qualquer tráfego HTTP para HTTPS. O site ganha os seguintes benefícios ao usar HTTPS para todos os seus recursos:

- Impede que atacantes modifiquem interações com o servidor web (incluindo a inserção de malware JavaScript por meio de um [roteador comprometido](https://www.trendmicro.com/vinfo/us/security/news/cybercrime-and-digital-threats/over-200-000-mikrotik-routers-compromised-in-cryptojacking-campaign)).
- Evita perder clientes para avisos de site inseguro. Navegadores mais recentes [marcam sites baseados em HTTP como inseguros](https://www.blog.google/products/chrome/milestone-chrome-security-marking-http-not-secure/).
- Facilita a escrita de certos aplicativos. Por exemplo, APIs do Android [precisam de substituições](https://developer.android.com/training/articles/security-config#CleartextTrafficPermitted) para se conectar a qualquer coisa via HTTP.

Se for difícil mudar para HTTPS, priorize o HTTPS para operações sensíveis. A médio prazo, planeje converter toda a aplicação para HTTPS para evitar a perda de clientes devido a comprometimento ou aos avisos de HTTP sendo inseguro. Se a organização ainda não compra certificados para HTTPS, considere [Let's Encrypt](https://letsencrypt.org) ou outras autoridades de certificação gratuitas no servidor.