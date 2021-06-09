## Resumo

Esta seção descreve como testar diversos arquivos de meta-dados para o vazamento de informações sobre o caminho(s) ou funcionalidades de aplicativos web. Além disso, a lista de diretórios que devem ser evitados por Spider, Robots or Crawlers pode também ser criada como uma dependência [Mapa de execução de caminhos do aplicativo] (07-Map_Execution_Paths_Through_Application.md). Outras informações também podem ser coletadas para ataques de superfície de identidade, detalhes tecnológicos, ou para engajamento em engenharia social. 


## Objetivos do Teste 

- Identificar caminhos e funcionalidades escondidos ou ofuscados através da análise de arquivos de meta-dados. 
- Extrair e mapear outras informações que podem levar a um melhor entendimento do sistema manuseado.

## Como Testar

Qualquer das ações abaixo executadas com ‘wget’ podem também ser realizadas através do uso de ‘curl’. Muitas ferramentas de testes dinâmicos de segurança de aplicação (DAST) tais como ZAP e Burp Suite, incluem verificações ou análise para esses recursos como parte da funcionalidade de varredura spider/crawler. Eles podem também ser identificados usando vários [Google Dorks](https://en.wikipedia.org/wiki/Google_hacking)  ou fazendo-se valer de funcionalidades de busca avançadas tais como ‘inurl‘. 

### Robôs

Web Spiders, Robôs ou Crawlers retornam uma página web e recursivamente analisam hiperlinks para recuperar conteúdos web em detalhes. O comportamento aceito é especificado pelo protocolo de exclusão [Robots Exclusion Protocol](https://www.robotstxt.org) do [robots.txt](https://www.robotstxt.org/) no diretório web raiz. 

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

O diretório [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent) refere-se ao web spider/robot/crawler específico. Por exemplo, o `User-Agent: Googlebot` refere-se ao spider Google enquanto o `User-Agent: bingbot` refere-se ao crawler da Microsoft. `User-Agent: *` no exemplo acima aplicasse a todos	[web spiders/robots/crawlers](https://support.google.com/webmasters/answer/6062608?visit_id=637173940975499736-3548411022&rd=1).

A instrução `Disallow` especifíca quais recursos são proibidos pelos spiders/robots/crawlers. No exemplo acima os seguintes recursos são proibidos:

```text
...
Disallow: /search
...
Disallow: /sdch
...
```

Web spiders/robots/crawlers podem [ignorar intencionalmente] (https://blog.isc2.org/isc2_blog/2008/07/the-attack-of-t.html) o `Disallow` de recursos específicos no arquivo `robots.txt`, tais como aqueles de [Mídias Socias](https://www.htbridge.com/news/social_networks_can_robots_violate_user_privacy.html) para assegurar que os links compartilhados são ainda válidos. Por essa razão, `robots.txt` não deveriam ser considerados como mecanismos que impõem restrições em como o conteúdo web é acessado, armazenado, ou repostado por terceiros.

O arquivo é recuperado do diretório raiz de servidor web. Por exemplo, para recuperar o arquivo `robots.txt` de `www.google.com` usando `wget` ou `curl`:

```bash
$ curl -O -Ss http://www.google.com/robots.txt && head -n5 robots.txt
User-agent: *
Disallow: /search
Allow: /search/about
Allow: /search/static
Allow: /search/howsearchworks
...
```

#### Analise robots.txt usando ferramentas Google Webmaster

Proprietários de site podem usar função Google "Analyze robots.txt" para analisar o site como parte da ferramenta [Google Webmaster Tools](https://www.google.com/webmasters/tools). Essa ferramenta pode auxiliar com testes e procedimentos como se segue:

1. Acesse a Google web Masters tools com uma conta Google.
2. No dashboard, insira a URL do site a ser analisado.
3. Escolha entre os métodos disponíveis e siga as instruções da tela.

### META Tags

`<META>` tags estão localizadas dentro da sessão `HEAD` de cada documento HTML e devem ser consideradas por todo o site em um evento no qual o ponto de partida não inicia a partir de um documento outro senão o raiz, isto é, um 
[deep link](https://en.wikipedia.org/wiki/Deep_linking). Instruções Robots podem ser também especificadas através do uso específico de [META Tag](https://www.robotstxt.org/meta.html).

#### Robots META Tag

Se não há uma entrada`<META NAME="ROBOTS" ... >` então o "Robots Exclusion Protocol" é padronizado para `INDEX,FOLLOW` respectivamente. Entretanto, as duas outras entradas válidas definidas pelo "Robots Exclusion Protocol" são prefixadas com `NO...`, isto é,  `NOINDEX` e `NOFOLLOW`.

Baseadas na(s) diretiva(s) Disallow listadas dentro do arquivo ‘robot.txt’ do diretório web raiz, a expressão regular de busca `<META NAME="ROBOTS"` em cada página web é empreendida e o resultado comparado ao arquivo `robots.txt` do diretório web raiz.
 
#### Informações variadas de META Tags

Organizações comumente inserem informações de META tags para conteúdo web afim de suportar várias tecnologias tais como leitores de tela (acessibilidade), visualizadores de mídias sociais, mecanismos de busca etc.
Tais meta-informações podem ser valiosas a testadores na identificação de tecnologias utilizadas, bem como funcionalidades e caminhos para se explorar e testar.
A seguinte informação de META tags foi obtida de `www.whitehouse.gov` através da funcionalidade ver arquivo fonte em 5 de maio de 2020:

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

Um sitemap é um arquivo onde o desenvolvedor ou a organização podem prover informações sobre as páginas, vídeos, e outros arquivos oferecidos pelo site ou aplicativo, bem como a relação entre eles. Buscadores podem usar esse arquivo para explorar de maneira mais inteligente seu site. Testadores podem usar o arquivo `sitemap.xml` para aprender mais sobre o site ou aplicativo e explorá-lo mais detalhadamente.

O seguinte trecho foi retirado do sitemap primário do  Google em 5 de maio de 2020.

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

A partir do sitemap, um testador pode desejar extrair o sitemap do gmail em `https://www.google.com/gmail/sitemap.xml`:

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

`security.txt` é um [padrão proposto](https://securitytxt.org/) o qual permite web sites definir políticas de segurança e de detalhes de contatos. Existem múltiplas razões às quais isso possa ser interessante a cenários de testes, incluindo, mas não limitado a:

- Identificando caminhos adicionais ou recursos para inclusão na descoberta ou análise.
- Coleta de inteligência de Código Aberto.
- Coletando informações em Bug Bounties, etc.
- Engenharia Social.
 
O arquivo pode estar presente tanto na raiz do servidor web quanto no diretório `.well-known/`
Ex:

- `https://example.com/security.txt`
- `https://example.com/.well-known/security.txt`
Aqui está um exemplo real retirado do  LinkedIn em 5 de maio de 2020:

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

O `humans.txt` é uma iniciativa para se conhecer as pessoas por trás do website. Assume a forma de um arquivo de texto que contém informações sobre diferentes pessoas que tem contribuído para construir website. Acesse [humanstxt](http://humanstxt.org/) para mais informações. Esse arquivo comumente, embora não sempre, contém informações para sites de empregos ou carreira.

O exemplo a seguir foi extraído do Google em 5 de maio de 2020:

```bash
$ wget --no-verbose  https://www.google.com/humans.txt && cat humans.txt
2020-05-07 12:57:52 URL:https://www.google.com/humans.txt [286/286] -> "humans.txt" [1]
Google is built by a large team of engineers, designers, researchers, robots, and others in many different sites across the globe. It is updated continuously, and built with more tools and technologies than we can shake a stick at. If you'd like to help us out, see careers.google.com.
```

### Outras fontes de informação well-known 


Existem outras RFCs(Request for Comments) e rascunhos de os quais sugerem padronização no uso de arquivos dentro do diretório. Essas listas podem ser encontradas[aqui] https://en.wikipedia.org/wiki/List_of_/.well-known/_services_offered_by_webservers) ou [aqui](https://www.iana.org/assignments/well-known-uris/well-known-uris.xhtml). Seria consideravelmente  simples para um testador revisar ou criar uma lista para fornecer ao crawler ou ao fuzzer, afim de verificar a existência ou conteúdo de tais arquivos.

## Ferramentas

- Browser (View Source or Dev Tools functionality)
- curl
- wget
- Burp Suite
- ZAP
