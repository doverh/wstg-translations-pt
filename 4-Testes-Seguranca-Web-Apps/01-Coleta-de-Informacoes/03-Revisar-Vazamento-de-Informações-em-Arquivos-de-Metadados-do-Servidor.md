# Revisar Vazamento de Informações em Arquivos de Metadados do Servidor

|ID          |
|------------|
|WSTG-INFO-03|

Resumo

Esta seção descreve como testar vários arquivos de metadados buscando o vazamento de informações sobre o(s) caminho(s) ou funcionalidade do aplicativo web. Além disso, a lista de diretórios que devem ser evitados por Spiders, Robots ou Crawlers também pode ser criada como uma dependência para [Mapear caminhos de execução através do aplicativo](07-Mappear-Caminhos-Execucação-do-Applicativo.md). Outras informações também podem ser coletadas para identificar a superfície de ataque, detalhes de tecnologia ou para uso em engenharia social.

Objetivos do teste

- Identificar caminhos e funcionalidades ocultas ou obfuscadas através da análise de arquivos de metadados.
- Extrair e mapear outras informações que possam levar a uma melhor compreensão dos sistemas em mãos.

Como testar

> Qualquer uma das ações executadas abaixo com `wget` também pode ser feita com `curl`. Muitas ferramentas de teste de segurança de aplicativos dinâmicos (DAST), como ZAP e Burp Suite, incluem verificações ou análise desses recursos como parte de sua funcionalidade de spider/crawler. Eles também podem ser identificados usando vários [Google Dorks](https://en.wikipedia.org/wiki/Google_hacking) ou alavancando recursos avançados de pesquisa, como `inurl:`.

Robots

Web Spiders, Robots ou Crawlers recuperam uma página da web e, em seguida, percorrem recursivamente os hiperlinks para recuperar mais conteúdo da web. Seu comportamento aceito é especificado pelo [Protocolo de Exclusão de Robôs](https://www.robotstxt.org) do arquivo [robots.txt](https://www.robotstxt.org/) no diretório raiz da web.

Como exemplo, o início do arquivo `robots.txt` do [Google](https://www.google.com/robots.txt) amostrado em 5 de maio de 2020 é citado abaixo:

```text
User-agent: *
Disallow: /search
Allow: /search/about
Allow: /search/static
Allow: /search/howsearchworks
Disallow: /sdch
...
```

A diretiva [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent) refere-se ao spider/robot/crawler da web específico. Por exemplo, `User-Agent: Googlebot` refere-se ao spider do Google, enquanto `User-Agent: bingbot` refere-se a um crawler da Microsoft. `User-Agent: *` no exemplo acima se aplica a todos os [spiders/robots/crawlers](https://support.google.com/webmasters/answer/6062608?visit_id=637173940975499736-3548411022&rd=1) da web.

A diretiva `Disallow` especifica quais recursos são proibidos pelos spiders/robots/crawlers. No exemplo acima, os seguintes recursos são proibidos:

```text
...
Disallow: /search
...
Disallow: /sdch
...
```

Spiders/robots/crawlers da web podem [ignorar intencionalmente](https://blog.isc2.org/isc2_blog/2008/07/the-attack-of-t.html) as diretivas `Disallow` especificadas em um arquivo `robots.txt`, como aquelas das [Redes Sociais](https://www.htbridge.com/news/social_networks_can_robots_violate_user_privacy.html) para garantir que os links compartilhados ainda sejam válidos. Portanto, o arquivo `robots.txt` não deve ser considerado como um mecanismo para impor restrições sobre como o conteúdo da web é acessado, armazenado ou republicado por terceiros.

O arquivo `robots.txt` é recuperado do diretório raiz da web do servidor. Por exemplo, para recuperar o `robots.txt` de `www.google.com` usando `wget` ou `curl`:

```bash
$ curl -O -Ss http://www.google.com/robots.txt && head -n5 robots.txt
User-agent: *
Disallow: /search
Allow: /search/about
Allow: /search/static
Allow: /search/howsearchworks
...
```

Analisar robots.txt usando as Ferramentas do Google para Webmasters

Os proprietários de sites podem usar a função do Google "Analisar robots.txt" para analisar o site como parte das [Ferramentas do Google para Webmasters](https://www.google.com/webmasters/tools). Essa ferramenta pode ajudar no teste e o procedimento é o seguinte:

1. Faça login nas Ferramentas do Google para Webmasters com uma conta do Google.
2. No painel, digite a URL do site a ser analisado.
3. Escolha entre os métodos disponíveis e siga as instruções na tela.

META Tags

As tags `<META>` são encontradas dentro da seção `HEAD` de cada documento HTML e devem ser consistentes em todo o site no caso do ponto de partida do spider/robot/crawler não começar a partir de um link de documento que não seja webroot, ou seja, um [link profundo](https://en.wikipedia.org/wiki/Deep_linking). A diretiva Robots também pode ser especificada através do uso de uma [tag META](https://www.robotstxt.org/meta.html) específica.

Robots META Tag

Se não houver entrada `<META NAME="ROBOTS" ... >`, então o "Protocolo de Exclusão de Robôs" assume os valores padrão `INDEX, FOLLOW` respectivamente. Portanto, as outras duas entradas válidas definidas pelo "Protocolo de Exclusão de Robôs" são prefixadas com `NO...` ou seja, `NOINDEX` e `NOFOLLOW`.

Com base nas diretivas Disallow listadas no arquivo `robots.txt` no diretório raiz, uma busca por <META NAME="ROBOTS"> é executada em cada página web. O resultado é então comparado ao arquivorobots.txt no diretório raiz.

#### Tags Meta de Informações Diversas

Organizações muitas vezes incorporam tags META informativas no conteúdo web para suportar várias tecnologias, como leitores de tela, visualizações de redes sociais, indexação de mecanismos de pesquisa, etc. Essas metainformações podem ser valiosas para testadores na identificação das tecnologias utilizadas e caminhos/funcionalidades adicionais a ser explorados e testados. As seguintes meta informações foram obtidas em `www.whitehouse.gov` usando "Ver Código Fonte da Página" em 05 de maio de 2020:

```html
...
<meta property="og:locale" content="en_US" />
<meta property="og:type" content="website" />
<meta property="og:title" content="The White House" />
<meta property="og:description" content="We, the citizens of America, are now joined in a great national effort to rebuild our country and to restore its promise for all. – President Donald Trump." />
<meta property="og:url" content="https://www.whitehouse.gov/" />
<meta property="og:site_name" content="The White House" />
<meta property="fb:app_id" content="1790466490985150" />
<meta property="og:image" content="https://www.whitehouse.gov/wp-content/uploads/2017/12/wh.gov-share-img_03-1024x538.png" />
<meta property="og:image:secure_url" content="https://www.whitehouse.gov/wp-content/uploads/2017/12/wh.gov-share-img_03-1024x538.png" />
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:description" content="We, the citizens of America, are now joined in a great national effort to rebuild our country and to restore its promise for all. – President Donald Trump." />
<meta name="twitter:title" content="The White House" />
<meta name="twitter:site" content="@whitehouse" />
<meta name="twitter:image" content="https://www.whitehouse.gov/wp-content/uploads/2017/12/wh.gov-share-img_03-1024x538.png" />
<meta name="twitter:creator" content="@whitehouse" />
...
<meta name="apple-mobile-web-app-title" content="The White House">
<meta name="application-name" content="The White House">
<meta name="msapplication-TileColor" content="#0c2644">
<meta name="theme-color" content="#f5f5f5">
...
```

### Mapas do Site

Um mapa do site é um arquivo onde um desenvolvedor ou organização pode fornecer informações sobre as páginas, vídeos e outros arquivos oferecidos pelo site ou aplicativo, e a relação entre eles. Mecanismos de pesquisa podem usar esse arquivo para explorar seu site de forma mais inteligente. Testadores podem usar arquivos `sitemap.xml` para aprender mais sobre o site ou aplicativo e explorá-lo mais completamente.

O seguinte trecho é do mapa do site principal do Google obtido em 05 de maio de 2020.

```bash
$ wget --no-verbose https://www.google.com/sitemap.xml && head -n8 sitemap.xml
2020-05-05 12:23:30 URL:https://www.google.com/sitemap.xml [2049] -> "sitemap.xml" [1]

<?xml version="1.0" encoding="UTF-8"?>
<sitemapindex xmlns="http://www.google.com/schemas/sitemap/0.84">
  <sitemap>
    <loc>https://www.google.com/gmail/sitemap.xml</loc>
  </sitemap>
  <sitemap>
    <loc>https://www.google.com/forms/sitemaps.xml</loc>
  </sitemap>
...
```

Explorando a partir daí, um testador pode desejar recuperar o mapa do site do Gmail `https://www.google.com/gmail/sitemap.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9" xmlns:xhtml="http://www.w3.org/1999/xhtml">
  <url>
    <loc>https://www.google.com/intl/am/gmail/about/</loc>
    <xhtml:link href="https://www.google.com/gmail/about/" hreflang="x-default" rel="alternate"/>
    <xhtml:link href="https://www.google.com/intl/el/gmail/about/" hreflang="el" rel="alternate"/>
    <xhtml:link href="https://www.google.com/intl/it/gmail/about/" hreflang="it" rel="alternate"/>
    <xhtml:link href="https://www.google.com/intl/ar/gmail/about/" hreflang="ar" rel="alternate"/>
...
```

### Security TXT

`security.txt` é um padrão proposto que permite que sites definam políticas de segurança e detalhes de contato. Existem várias razões pelas quais isso pode ser interessante em cenários de teste, incluindo, mas não se limitando a:

- Identificar outros caminhos ou recursos para incluir em descoberta/análise.
- Coleta de inteligência de código aberto.
- Encontrar informações sobre Bug Bounties, etc.
- Engenharia social.

O arquivo pode estar presente na raiz do servidor da web ou no diretório `.well-known/`. Exemplo:

- `https://example.com/security.txt`
- `https://example.com/.well-known/security.txt`

Aqui está um exemplo do mundo real obtido do LinkedIn em 05 de maio de 2020:

```bash
$ wget --no-verbose https://www.linkedin.com/.well-known/security.txt && cat security.txt
2020-05-07 12:56:51 URL:https://www.linkedin.com/.well-known/security.txt [333/333] -> "security.txt" [1]
# Conforms to IETF `draft-foudil-securitytxt-07`
Contact: mailto:security@linkedin.com
Contact: https://www.linkedin.com/help/linkedin/answer/62924
Encryption: https://www.linkedin.com/help/linkedin/answer/79676
Canonical: https://www.linkedin.com/.well-known/security.txt
Policy: https://www.linkedin.com/help/linkedin/answer/62924
```

### Humans TXT

`humans.txt` é uma iniciativa para conhecer as pessoas por trás de um site. Ele é apresentado sob a forma de um arquivo de texto que contém informações sobre as diferentes pessoas que contribuíram para a construção do site. Veja [humanstxt](http://humanstxt.org/) para mais informações. Esse arquivo frequentemente (embora nem sempre) contém informações para carreiras ou sites/trajetos de empregos.

O exemplo a seguir foi obtido do Google em 05 de maio de 2020:

```bash
$ wget --no-verbose  https://www.google.com/humans.txt && cat humans.txt
2020-05-07 12:57:52 URL:https://www.google.com/humans.txt [286/286] -> "humans.txt" [1]
Google é construído por uma grande equipe de engenheiros, designers, pesquisadores, robôs e outros em vários locais ao redor do mundo. Ele é atualizado continuamente e construído com mais ferramentas e tecnologias do que podemos contar. Se você quiser nos ajudar, consulte careers.google.com.
```

### Outras Fontes de Informações .well-known

Existem outras RFCs (do inglês Request for Comment) e rascunhos da Internet que sugerem usos padronizados de arquivos no diretório `.well-known/`. Listas deles podem ser encontradas [aqui](https://en.wikipedia.org/wiki/List_of_/.well-known/_services_offered_by_webservers) ou [aqui](https://www.iana.org/assignments/well-known-uris/well-known-uris.xhtml).

Seria bastante simples para um testador revisar a RFC/rascunhos e criar uma lista a ser fornecida a um rastreador ou fuzzing, a fim de verificar a existência ou o conteúdo de tais arquivos.

## Ferramentas

- Navegador (funcionalidade Exibir Código-fonte ou Dev Tools)
- curl
- wget
- Burp Suite
- ZAP