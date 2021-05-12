# Revisa arquivos de Meta-Dados do Servidor Web para o Vazamento de Informações


|ID          |
|------------|
|WSTG-INFO-03|

## Resumo

Esta seção descreve como testar diversos arquivos de meta-dados para o vazamento de informações sobre o caminho(s) ou funcionalidades de aplicativos web. Além disso, a lista de diretórios que devem ser evitados por Spider, Robots or Crawlers pode também ser criada como uma dependência [Mapa de execução de caminhos do aplicativo] (07-Map_Execution_Paths_Through_Application.md). Outras informações também podem ser coletadas para ataques de superfície de identidade, detalhes tecnológicos, ou para engajamento em engenharia social. 


## Objetivos do Teste 

- Identificar caminhos e funcionalidades escondidos ou ofuscados através da análise de arquivos de meta-dados. 
- Extrair e mapear outras informações que podem levar a um melhor entendimento do sistema manuseado.

## Como Testar

Qualquer das ações abaixo executadas com ‘wget’ podem também ser realizadas através do uso de ‘curl’. Muitas ferramentas de testes dinâmicos de segurança de aplicação (DAST) tais como ZAP e Burp Suite, incluem verificações ou análise para esses recursos como parte da funcionalidade de varredura spider/crawler. Eles podem também ser identificados usando vários [Google Dorks](https://en.wikipedia.org/wiki/Google_hacking)  ou fazendo-se valer de funcionalidades de busca avançadas tais como ‘inurl‘. 

### Robôs

Web Spiders, Robôs ou Crawlers retornam uma página web e recursivamente analisar hiperlinks para recuperar conteúdos web a fundo. O comportamento aceito é especificado pela o protocolo robô de exclusão [Robots Exclusion Protocol](https://www.robotstxt.org) of the [robots.txt](https://www.robotstxt.org/)  no diretório web raiz. 

Em um exemplo, o começo do arquivo `robots.txt`[Google](https://www.google.com/robots.txt) extraído em 5 de maio de 2020 como demonstrado abaixo:

```text
User-agent: *
Disallow: /search
Allow: /search/about
Allow: /search/static
Allow: /search/howsearchworks
Disallow: /sdch
...
```

O [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent) directive refere-se ao  web spider/robot/crawler específico. Por exemplo, o `User-Agent: Googlebot` refere-se ao spider Google enquanto o `User-Agent: bingbot` refere-se ao crawler da Microsoft. `User-Agent: *` no exemplo acima aplicasse a todos	 [web spiders/robots/crawlers](https://support.google.com/webmasters/answer/6062608?visit_id=637173940975499736-3548411022&rd=1).

The `Disallow` directive specifies which resources are prohibited by spiders/robots/crawlers. In the example above, the following are prohibited:

```text
...
Disallow: /search
...
Disallow: /sdch
...
```

Web spiders/robots/crawlers can [intentionally ignore](https://blog.isc2.org/isc2_blog/2008/07/the-attack-of-t.html) the `Disallow` directives specified in a `robots.txt` file, such as those from [Social Networks](https://www.htbridge.com/news/social_networks_can_robots_violate_user_privacy.html) to ensure that shared linked are still valid. Hence, `robots.txt` should not be considered as a mechanism to enforce restrictions on how web content is accessed, stored, or republished by third parties.

The `robots.txt` file is retrieved from the web root directory of the web server. For example, to retrieve the `robots.txt` from `www.google.com` using `wget` or `curl`:

```bash
$ curl -O -Ss http://www.google.com/robots.txt && head -n5 robots.txt
User-agent: *
Disallow: /search
Allow: /search/about
Allow: /search/static
Allow: /search/howsearchworks
...
```

#### Analyze robots.txt Using Google Webmaster Tools

Web site owners can use the Google "Analyze robots.txt" function to analyze the website as part of its [Google Webmaster Tools](https://www.google.com/webmasters/tools). This tool can assist with testing and the procedure is as follows:

1. Sign into Google Webmaster Tools with a Google account.
2. On the dashboard, enter the URL for the site to be analyzed.
3. Choose between the available methods and follow the on screen instruction.

### META Tags

`<META>` tags are located within the `HEAD` section of each HTML document and should be consistent across a web site in the event that the robot/spider/crawler start point does not begin from a document link other than webroot i.e. a [deep link](https://en.wikipedia.org/wiki/Deep_linking). Robots directive can also be specified through use of a specific [META tag](https://www.robotstxt.org/meta.html).

#### Robots META Tag

If there is no `<META NAME="ROBOTS" ... >` entry then the "Robots Exclusion Protocol" defaults to `INDEX,FOLLOW` respectively. Therefore, the other two valid entries defined by the "Robots Exclusion Protocol" are prefixed with `NO...` i.e. `NOINDEX` and `NOFOLLOW`.

Based on the Disallow directive(s) listed within the `robots.txt` file in webroot, a regular expression search for `<META NAME="ROBOTS"` within each web page is undertaken and the result compared to the `robots.txt` file in webroot.

#### Miscellaneous META Information Tags

Organizations often embed informational META tags in web content to support various technologies such as screen readers, social networking previews, search engine indexing, etc. Such meta-information can be of value to testers in identifying technologies used, and additional paths/functionality to explore and test. The following meta information was retrieved from `www.whitehouse.gov` via View Page Source on 2020 May 05:

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

### Sitemaps

A sitemap is a file where a developer or organization can provide information about the pages, videos, and other files offered by the site or application, and the relationship between them. Search engines can use this file to more intelligently explore your site. Testers can use `sitemap.xml` files to learn more about the site or application to explore it more completely.

The following excerpt is from Google's primary sitemap retrieved 2020 May 05.

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

Exploring from there a tester may wish to retrieve the gmail sitemap `https://www.google.com/gmail/sitemap.xml`:

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

`security.txt` is a [proposed standard](https://securitytxt.org/) which allows websites to define security policies and contact details. There are multiple reasons this might be of interest in testing scenarios, including but not limited to:

- Identifying further paths or resources to include in discovery/analysis.
- Open Source intelligence gathering.
- Finding information on Bug Bounties, etc.
- Social Engineering.

The file may be present either in the root of the webserver or in the `.well-known/` directory. Ex:

- `https://example.com/security.txt`
- `https://example.com/.well-known/security.txt`

Here is a real world example retrieved from LinkedIn 2020 May 05:

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

`humans.txt` is an initiative for knowing the people behind a website. It takes the form of a text file that contains information about the different people who have contributed to building the website. See [humanstxt](http://humanstxt.org/) for more info. This file often (though not always) contains information for career or job sites/paths.

The following example was retrieved from Google 2020 May 05:

```bash
$ wget --no-verbose  https://www.google.com/humans.txt && cat humans.txt
2020-05-07 12:57:52 URL:https://www.google.com/humans.txt [286/286] -> "humans.txt" [1]
Google is built by a large team of engineers, designers, researchers, robots, and others in many different sites across the globe. It is updated continuously, and built with more tools and technologies than we can shake a stick at. If you'd like to help us out, see careers.google.com.
```

### Other .well-known Information Sources

There are other RFCs and Internet drafts which suggest standardized uses of files within the `.well-known/` directory. Lists of which can be found [here](https://en.wikipedia.org/wiki/List_of_/.well-known/_services_offered_by_webservers) or [here](https://www.iana.org/assignments/well-known-uris/well-known-uris.xhtml).

It would be fairly simple for a tester to review the RFC/drafts are create a list to be supplied to a crawler or fuzzer, in order to verify the existence or content of such files.

## Ferramentas

- Browser (View Source or Dev Tools functionality)
- curl
- wget
- Burp Suite
- ZAP
