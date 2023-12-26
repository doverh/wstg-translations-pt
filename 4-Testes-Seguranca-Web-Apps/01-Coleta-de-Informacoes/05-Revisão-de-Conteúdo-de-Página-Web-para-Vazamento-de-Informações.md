# Revisão de Conteúdo de Página Web para Vazamento de Informações

|ID          |
|------------|
|WSTG-INFO-05|

## Resumo

É muito comum, e até mesmo recomendado, que os programadores incluam comentários detalhados e metadados em seu código-fonte. No entanto, comentários e metadados incluídos no código HTML podem revelar informações internas que não deveriam estar disponíveis para possíveis hackers. A revisão de comentários e metadados deve ser realizada para determinar se há vazamento de informações.

Para aplicativos web modernos, o uso de JavaScript do lado do cliente para o front-end está se tornando mais popular. Tecnologias populares de construção de front-end, como ReactJS, AngularJS ou Vue, utilizam JavaScript do lado do cliente. Assim como os comentários e metadados no código HTML, muitos programadores também incluem informações sensíveis em variáveis JavaScript no front-end. Essas informações sensíveis podem incluir (mas não se limitam a): Chaves de API privadas (por exemplo, uma chave irrestrita do Google Map API), endereços IP internos, rotas sensíveis (por exemplo, rota para páginas ou funcionalidades de administração ocultas) ou até credenciais. Essas informações sensíveis podem vazar do código JavaScript do front-end. Uma revisão deve ser realizada para determinar se há vazamento de informações sensíveis que poderiam ser usadas por atacantes para abuso.

Para aplicativos web grandes, problemas de desempenho são uma grande preocupação para os programadores. Os programadores têm usado diferentes métodos para otimizar o desempenho do front-end, incluindo Syntactically Awesome Style Sheets (SASS), Sassy CSS (SCSS), webpack, etc. O uso dessas tecnologias torna o código do front-end às vezes mais difícil de entender e depurar. Devido a isso, os programadores frequentemente implementam arquivos de mapa de origem para fins de depuração. Um "mapa de origem" é um arquivo especial que conecta uma versão minificada/uglify de um recurso (CSS ou JavaScript) à versão original autoral. Os programadores ainda estão debatendo se devem ou não trazer arquivos de mapa de origem para o ambiente de produção. No entanto, é inegável que arquivos de mapa de origem ou arquivos de depuração, se liberados para o ambiente de produção, tornarão seu código mais legível para humanos. Isso pode facilitar para os atacantes encontrar vulnerabilidades no front-end ou coletar informações sensíveis dele. A revisão do código JavaScript deve ser feita para determinar se há arquivos de depuração expostos no front-end. Dependendo do contexto e da sensibilidade do projeto, um especialista em segurança deve decidir se os arquivos devem existir no ambiente de produção ou não.

## Objetivos do Teste

- Revisar comentários e metadados da página da web para encontrar vazamentos de informações.
- Coletar arquivos JavaScript e revisar o código JS para entender melhor a aplicação e encontrar vazamentos de informações.
- Identificar se existem arquivos de mapa de origem ou outros arquivos de depuração do front-end.

## Como Testar

### Revisar comentários e metadados da página da web

Os comentários HTML são frequentemente usados pelos desenvolvedores para incluir informações de depuração sobre a aplicação. Às vezes, eles esquecem os comentários e os deixam nos ambientes de produção. Os testadores devem procurar por comentários HTML que começam com `<!--`.

Verificar o código-fonte HTML em busca de comentários que contenham informações sensíveis que possam ajudar o atacante a obter mais informações sobre a aplicação. Pode ser código SQL, nomes de usuário e senhas, endereços IP internos ou informações de depuração.

```html
...
<div class="table2">
  <div class="col1">1</div><div class="col2">Mary</div>
  <div class="col1">2</div><div class="col2">Peter</div>
  <div class="col1">3</div><div class="col2">Joe</div>

<!-- Consulta: SELECT id, name FROM app.users WHERE active='1' -->

</div>
...
```

O testador pode até encontrar algo como:

```html
<!-- Use a senha do administrador do banco de dados para teste: f@keP@a$$w0rD -->
```

Verificar as informações da versão HTML quanto a números de versão válidos e URLs de Definição de Tipo de Dados (DTD).

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
```

- `strict.dtd` -- DTD estrito padrão
- `loose.dtd` -- DTD flexível
- `frameset.dtd` -- DTD para documentos de frameset

Algumas tags `META` não fornecem vetores de ataque ativos, mas permitem que um atacante profile uma aplicação:

```html
<META name="Author" content="Andrew Muller">
```

Uma tag `META` comum (mas não [WCAG](https://www.w3.org/WAI/standards-guidelines/wcag/) compatível) é [Refresh](https://en.wikipedia.org/wiki/Meta_refresh).

```html
<META http-equiv="Refresh" content="15;URL=https://www.owasp.org/index.html">
```

Um uso comum para a tag `META` é especificar palavras-chave que um mecanismo de busca pode usar para melhorar a qualidade dos resultados da pesquisa.

```html
<META name="keywords" lang="en-us" content="OWASP, security, sunshine, lollipops">
```

Embora a maioria dos servidores web gerencie a indexação de mecanismos de busca por meio do arquivo `robots.txt`, também pode ser gerenciada por tags `META`. A tag abaixo aconselhará robôs a não indexar e não seguir links na página HTML contendo a tag.

```html
<META name="robots" content="none">
```

A [Plataforma para Seleção de Conteúdo na Internet (PICS)](https://www.w3.org/PICS/) e o [Protocolo para Recursos de Descrição na Web (POWDER)](https://www.w3.org/2007/powder/) fornecem infraestrutura para associar metadados ao conteúdo da Internet.

### Identificar Código JavaScript e Coletar Arquivos JavaScript

Os programadores frequentemente

 incluem informações sensíveis em variáveis JavaScript no front-end. Os testadores devem verificar o código-fonte HTML e procurar por código JavaScript entre as tags `<script>` e `</script>`. Os testadores também devem identificar arquivos JavaScript externos para revisar o código (os arquivos JavaScript têm a extensão de arquivo `.js` e o nome do arquivo JavaScript geralmente é colocado no atributo `src` (source) de uma tag `<script>`).

Verificar o código JavaScript em busca de vazamentos de informações sensíveis que podem ser usadas por atacantes para abusar ou manipular o sistema. Procurar por valores como: chaves de API, endereços IP internos, rotas sensíveis ou credenciais. Por exemplo:

```javascript
const myS3Credentials = {
  accessKeyId: config('AWSS3AccessKeyID'),
  secretAcccessKey: config('AWSS3SecretAccessKey'),
};
```

O testador pode até encontrar algo como:

```javascript
var conString = "tcp://postgres:1234@localhost/postgres";
```

Quando uma Chave de API é encontrada, os testadores podem verificar se as restrições da Chave de API estão definidas por serviço ou por IP, HTTP referrer, aplicação, SDK, etc.

Por exemplo, se os testadores encontrarem uma Chave de API do Google Map, podem verificar se esta Chave de API está restrita por IP ou restrita apenas pelos APIs do Google Map. Se a Chave de API do Google estiver restrita apenas pelos APIs do Google Map, os atacantes ainda podem usá-la para consultar APIs do Google Map não restritos e o proprietário da aplicação deve pagar por isso.

```html
<script type="application/json">
...
{"GOOGLE_MAP_API_KEY":"AIzaSyDUEBnKgwiqMNpDplT6ozE4Z0XxuAbqDi4", "RECAPTCHA_KEY":"6LcPscEUiAAAAHOwwM3fGvIx9rsPYUq62uRhGjJ0"}
...
</script>
```

Em alguns casos, os testadores podem encontrar rotas sensíveis no código JavaScript, como links para páginas de administração internas ou ocultas.

```html
<script type="application/json">
...
"runtimeConfig":{"BASE_URL_VOUCHER_API":"https://staging-voucher.victim.net/api", "BASE_BACKOFFICE_API":"https://10.10.10.2/api", "ADMIN_PAGE":"/hidden_administrator"}
...
</script>
```

### Identificar Arquivos de Mapa de Origem

Os arquivos de mapa de origem geralmente serão carregados quando as DevTools estiverem abertas. Os testadores também podem encontrar arquivos de mapa de origem adicionando a extensão ".map" após a extensão de cada arquivo JavaScript externo. Por exemplo, se um testador vir um arquivo `/static/js/main.chunk.js`, eles podem então verificar se há um arquivo de mapa de origem visitando `/static/js/main.chunk.js.map`.

#### Teste de Caixa Preta

Verificar os arquivos de mapa de origem em busca de informações sensíveis que possam ajudar o atacante a obter mais informações sobre a aplicação. Por exemplo:

```json
{
  "version": 3,
  "file": "static/js/main.chunk.js",
  "sources": [
    "/home/sysadmin/cashsystem/src/actions/index.js",
    "/home/sysadmin/cashsystem/src/actions/reportAction.js",
    "/home/sysadmin/cashsystem/src/actions/cashoutAction.js",
    "/home/sysadmin/cashsystem/src/actions/userAction.js",
    "..."
  ],
  "..."
}
```

Quando os sites carregam arquivos de mapa de origem, o código-fonte do front-end se tornará legível e mais fácil de depurar.

## Ferramentas

- [Wget](https://www.gnu.org/software/wget/wget.html)
- Função "ver código-fonte" do navegador
- Olhos
- [Curl](https://curl.haxx.se/)
- [Burp Suite](https://portswigger.net/burp)
- [Waybackurls](https://github.com/tomnomnom/waybackurls)
- [Google Maps API Scanner](https://github.com/ozguralp/gmapsapiscanner/)

## Referências

- [KeyHacks](https://github.com/streaak/keyhacks)

### Artigos Técnicos

- [HTML versão 4.01](https://www.w3.org/TR/1999/REC-html401-19991224)
- [XHTML](https://www.w3.org/TR/2010/REC-xhtml-basic-20101123/)
- [HTML versão 5](https://www.w3.org/TR/html5/)
```
