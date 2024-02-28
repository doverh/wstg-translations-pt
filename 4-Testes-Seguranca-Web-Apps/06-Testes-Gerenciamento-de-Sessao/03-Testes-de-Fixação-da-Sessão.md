# Testes de Fixação da Sessão

|ID          |
|------------|
|WSTG-SESS-03|

## Resumo

A fixação de sessão é habilitada pela prática insegura de preservar o mesmo valor dos cookies de sessão antes e depois da autenticação. Isso geralmente ocorre quando os cookies de sessão são usados para armazenar informações de estado mesmo antes do login, por exemplo, para adicionar itens a um carrinho de compras antes da autenticação para o pagamento.

No exploit genérico de vulnerabilidades de fixação de sessão, um atacante pode obter um conjunto de cookies de sessão do site-alvo sem autenticação prévia. O atacante pode, então, forçar esses cookies no navegador da vítima usando diferentes técnicas. Se a vítima posteriormente se autenticar no site-alvo e os cookies não forem atualizados no login, a vítima será identificada pelos cookies de sessão escolhidos pelo atacante. O atacante pode então se passar pela vítima com esses cookies conhecidos.

Esse problema pode ser corrigido atualizando os cookies de sessão após o processo de autenticação. Alternativamente, o ataque pode ser prevenido garantindo a integridade dos cookies de sessão. Ao considerar atacantes de rede, ou seja, atacantes que controlam a rede usada pela vítima, use o [HSTS completo](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security) ou adicione o prefixo `__Host-` / `__Secure-` ao nome do cookie.

A adoção completa do HSTS ocorre quando um host ativa o HSTS para si e para todos os seus subdomínios. Isso é descrito em um artigo chamado *Testing for Integrity Flaws in Web Sessions* por Stefano Calzavara, Alvise Rabitti, Alessio Ragazzo e Michele Bugliesi.

## Objetivos do Teste

- Analisar o mecanismo de autenticação e seu fluxo.
- Forçar cookies e avaliar o impacto.

## Como Testar

Nesta seção, damos uma explicação da estratégia de teste que será mostrada na próxima seção.

O primeiro passo é fazer uma solicitação ao site a ser testado (por exemplo, `www.example.com`). Se o testador solicitar o seguinte:

```http
GET / HTTP/1.1
Host: www.example.com
```

Eles obterão a seguinte resposta:

```html
HTTP/1.1 200 OK
Date: Wed, 14 Aug 2008 08:45:11 GMT
Server: IBM_HTTP_Server
Set-Cookie: JSESSIONID=0000d8eyYq3L0z2fgq10m4v-rt4:-1; Path=/; secure
Cache-Control: no-cache="set-cookie,set-cookie2"
Expires: Thu, 01 Dec 1994 16:00:00 GMT
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html;charset=Cp1254
Content-Language: en-US
```

A aplicação define um novo identificador de sessão, `JSESSIONID=0000d8eyYq3L0z2fgq10m4v-rt4:-1`, para o cliente.

Em seguida, se o testador autenticar com sucesso na aplicação com o seguinte POST para `https://www.example.com/authentication.php`:

```http
POST /authentication.php HTTP/1.1
Host: www.example.com
[...]
Referer: http://www.example.com
Cookie: JSESSIONID=0000d8eyYq3L0z2fgq10m4v-rt4:-1
Content-Type: application/x-www-form-urlencoded
Content-length: 57

Name=Meucci&wpPassword=secret!&wpLoginattempt=Log+in
```

O testador observa a seguinte resposta do servidor:

```http
HTTP/1.1 200 OK
Date: Thu, 14 Aug 2008 14:52:58 GMT
Server: Apache/2.2.2 (Fedora)
X-Powered-By: PHP/5.1.6
Content-language: en
Cache-Control: private, must-revalidate, max-age=0
X-Content-Encoding: gzip
Content-length: 4090
Connection: close
Content-Type: text/html; charset=UTF-8
...
Dados HTML
...
```

Como nenhum novo cookie foi emitido após uma autenticação bem-sucedida, o testador sabe que é possível realizar a sequestro de sessão, a menos que a integridade do cookie de sessão seja garantida.

O testador pode enviar um identificador de sessão válido para um usuário (possivelmente usando um truque de engenharia social), aguardar que eles se autentiquem e, posteriormente, verificar se os privilégios foram atribuídos a este cookie.

### Teste com Cookies Forçados

Essa estratégia de teste é direcionada a atacantes de rede, portanto, só precisa ser aplicada a sites sem adoção completa de HSTS (sites com adoção completa de HSTS são seguros, pois todos os seus cookies têm integridade). Assumimos ter duas contas de teste no site em teste, uma para agir como vítima e outra para agir como atacante. Simulamos um cenário em que o atacante força no navegador da vítima todos os cookies que não são emitidos recentemente após o login e não têm integridade. Após o login da vítima, o atacante apresenta os cookies forçados ao site para acessar a conta da vítima: se forem suficientes para agir em nome da vítima, a fixação de sessão é possível.

Aqui estão as etapas para executar este teste:

1. Acesse a página de login do site.
2. Salve um instantâneo do cookie jar antes de fazer login, excluindo cookies que contenham o prefixo `__Host-` ou `__Secure-` em seus nomes.
3. Faça login no site como a vítima e alcance qualquer página que ofereça uma função segura que exija autenticação.
4. Defina o cookie jar para o instantâneo tirado na etapa 2.
5. Acione a função segura identificada na etapa 3.
6. Observe se a operação na etapa 5 foi realizada com sucesso. Se sim, o ataque foi bem-sucedido.
7. Limpe o cookie jar, faça login como atacante e alcance a página na etapa 3.
8. Escreva no cookie jar, um por um, os cookies salvos na etapa 2.


9. Acione novamente a função segura identificada na etapa 3.
10. Limpe o cookie jar e faça login novamente como a vítima.
11. Observe se a operação na etapa 9 foi realizada com sucesso na conta da vítima. Se sim, o ataque foi bem-sucedido; caso contrário, o site está protegido contra fixação de sessão.

Recomendamos usar duas máquinas ou navegadores diferentes para a vítima e o atacante. Isso permite diminuir o número de falsos positivos se a aplicação da web fizer fingerprinting para verificar o acesso habilitado a partir de um determinado cookie. Uma variante mais curta, mas menos precisa, da estratégia de teste requer apenas uma conta de teste. Segue as mesmas etapas, mas interrompe na etapa 6.

## Remediação

Implemente a renovação do token de sessão após um usuário autenticar com sucesso.

A aplicação deve sempre invalidar primeiro o ID de sessão existente antes de autenticar um usuário e, se a autenticação for bem-sucedida, fornecer outro ID de sessão.

## Ferramentas

- [OWASP ZAP](https://www.zaproxy.org)

## Referências

- [Fixação de Sessão](https://owasp.org/www-community/attacks/Session_fixation)
- [ACROS Security](https://www.acrossecurity.com/papers/session_fixation.pdf)
- [Chris Shiflett](http://shiflett.org/articles/session-fixation)
