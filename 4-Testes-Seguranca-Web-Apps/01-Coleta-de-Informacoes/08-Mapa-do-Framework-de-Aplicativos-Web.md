# Mapa do Framework de Aplicativos Web

|ID          |
|------------|
|WSTG-INFO-08|

## Resumo

Não há nada de novo sob o sol, e quase toda aplicação web que se possa pensar em desenvolver já foi desenvolvida. Com o grande número de projetos de software gratuitos e de código aberto que são desenvolvidos e implantados ativamente em todo o mundo, é muito provável que um teste de segurança de aplicação enfrente um alvo que dependa inteira ou parcialmente dessas aplicações ou estruturas bem conhecidas (por exemplo, WordPress, phpBB, Mediawiki, etc). Conhecer os componentes da aplicação web que está sendo testada ajuda significativamente no processo de teste e também reduz drasticamente o esforço necessário durante o teste. Essas aplicações web bem conhecidas têm cabeçalhos HTML, cookies e estruturas de diretórios conhecidos que podem ser enumerados para identificar a aplicação. A maioria das estruturas web tem vários marcadores nesses locais que ajudam um atacante ou testador a reconhecê-las. Isso é basicamente o que todas as ferramentas automáticas fazem, elas procuram um marcador em uma localização predefinida e depois o comparam com o banco de dados de assinaturas conhecidas. Para obter maior precisão, vários marcadores são geralmente usados.

## Objetivos do Teste

- Identificar os componentes utilizados pelas aplicações web.

## Como Testar

### Teste de Caixa Preta

Existem vários locais comuns a serem considerados para identificar estruturas ou componentes:

- Cabeçalhos HTTP
- Cookies
- Código-fonte HTML
- Arquivos e pastas específicos
- Extensões de arquivo
- Mensagens de erro

#### Cabeçalhos HTTP

A forma mais básica de identificar uma estrutura web é olhar o campo `X-Powered-By` no cabeçalho de resposta HTTP. Muitas ferramentas podem ser usadas para identificar um alvo, a mais simples é o netcat.

Considere a seguinte Requisição-Resposta HTTP:

```html
$ nc 127.0.0.1 80
HEAD / HTTP/1.0

HTTP/1.1 200 OK
Server: nginx/1.0.14
[...]
X-Powered-By: Mono
```

A partir do campo `X-Powered-By`, podemos entender que a estrutura de aplicação web provavelmente é `Mono`. No entanto, embora esta abordagem seja simples e rápida, essa metodologia não funciona em 100% dos casos. É possível desativar facilmente o cabeçalho `X-Powered-By` por meio de uma configuração adequada. Também existem várias técnicas que permitem que um site oculte cabeçalhos HTTP (veja um exemplo na seção [Remediação](#Remediação)). No exemplo acima, também podemos observar uma versão específica do `nginx` sendo usada para servir o conteúdo.

Portanto, no mesmo exemplo, o testador pode perder o cabeçalho `X-Powered-By` ou obter uma resposta como a seguinte:

```html
HTTP/1.1 200 OK
Server: nginx/1.0.14
Date: Sáb, 07 Set 2013 08:19:15 GMT
Content-Type: text/html;charset=ISO-8859-1
Connection: close
Vary: Accept-Encoding
X-Powered-By: Blood, sweat and tears
```

Às vezes, existem mais cabeçalhos HTTP que apontam para uma determinada estrutura. No exemplo a seguir, de acordo com as informações da solicitação HTTP, pode-se ver que o cabeçalho `X-Powered-By` contém a versão do PHP. No entanto, o cabeçalho `X-Generator` aponta que a estrutura usada é na verdade `Swiftlet`, o que ajuda um testador de penetração a expandir seus vetores de ataque. Ao realizar a identificação, inspecione cuidadosamente cada cabeçalho HTTP em busca de vazamentos semelhantes.

```html
HTTP/1.1 200 OK
Server: nginx/1.4.1
Date: Sáb, 07 Set 2013 09:22:52 GMT
Content-Type: text/html
Connection: keep-alive
Vary: Accept-Encoding
X-Powered-By: PHP/5.4.16-1~dotdeb.1
Expires: Qui, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Pragma: no-cache
X-Generator: Swiftlet
```
```
Aqui está a tradução da seção "Cookies" do arquivo Markdown fornecido:

```markdown
#### Cookies

Outra maneira semelhante e um pouco mais confiável de determinar o framework web atual são os cookies específicos do framework.

Considere a seguinte solicitação HTTP:

![Cakephp HTTP Request](images/Cakephp_cookie.png)\
*Figura 4.1.8-7: Solicitação HTTP do Cakephp*

O cookie `CAKEPHP` foi automaticamente definido, fornecendo informações sobre o framework em uso. Uma lista de nomes comuns de cookies é apresentada na seção [Cookies](#Cookies). Limitações ainda existem ao depender deste mecanismo de identificação - é possível alterar o nome dos cookies. Por exemplo, para o framework `CakePHP` selecionado, isso pode ser feito por meio da seguinte configuração (trecho de `core.php`):

```php
/**
* O nome do cookie de sessão do CakePHP.
*
* Observe as diretrizes para nomes de sessão: "O nome da sessão faz referência
* ao ID da sessão em cookies e URLs. Deve conter apenas caracteres alfanuméricos."
* @link http://php.net/session_name
*/
Configure::write('Session.cookie', 'CAKEPHP');
```

No entanto, essas alterações são menos propensas a serem feitas do que alterações no cabeçalho `X-Powered-By`, portanto, essa abordagem de identificação pode ser considerada mais confiável.

#### Código-fonte HTML

Essa técnica é baseada na busca de determinados padrões no código-fonte da página HTML. Muitas vezes, é possível encontrar muitas informações que ajudam um testador a reconhecer um componente específico. Um dos marcadores comuns são comentários HTML que levam diretamente à revelação do framework. Com mais frequência, podem ser encontrados caminhos específicos do framework, ou seja, links para pastas de CSS ou JS específicas do framework. Por fim, variáveis de script específicas também podem apontar para um determinado framework.

A partir da captura de tela abaixo, é fácil aprender o framework utilizado e sua versão pelos marcadores mencionados. O comentário, os caminhos específicos e as variáveis de script podem ajudar um atacante a determinar rapidamente uma instância do framework ZK.

![Amostra de Código-fonte HTML do ZK Framework](images/Zk_html_source.png)\
*Figura 4.1.8-2: Amostra de Código-fonte HTML do Framework ZK*

Frequentemente, essas informações estão posicionadas na seção `<head>` das respostas HTTP, em tags `<meta>`, ou no final da página. No entanto, as respostas inteiras devem ser analisadas, pois podem ser úteis para outros fins, como a inspeção de outros comentários úteis e campos ocultos. Às vezes, os desenvolvedores web não se importam muito em ocultar informações sobre os frameworks ou componentes usados. Ainda é possível encontrar algo como o seguinte na parte inferior da página:

![Página Inferior do Banshee](images/Banshee_bottom_page.png)\
*Figura 4.1.8-3: Página Inferior do Banshee*

Referências

Whitepapers
Saumil Shah: "An Introduction to HTTP fingerprinting"
Anant Shrivastava : "Web Application Finger Printing"
```