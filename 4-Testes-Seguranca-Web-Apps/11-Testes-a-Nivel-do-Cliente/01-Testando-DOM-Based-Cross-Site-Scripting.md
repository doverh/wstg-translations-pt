# Testando DOM-Based Cross Site Scripting

|ID          |
|------------|
|WSTG-CLNT-01|


## Resumo


[DOM-based cross-site scripting](https://owasp.org/www-community/attacks/DOM_Based_XSS) é o nome de facto para [XSS](https://owasp.org/www-community/attacks/xss/) defeitos que são o resultado de conteúdo ativo do navegador em uma página tipicamente JavaScript, obtendo dados de entrada do usuário através da [fonte](https://github.com/wisec/domxsswiki/wiki/sources) e usando em um [sink](https://github.com/wisec/domxsswiki/wiki/Sinks)*, levando a execução de injeção de código. Este documento discute apenas defeitos de Java Script que levam ao XSS ou cross-site scripting. 
*[Nota do Tradutor] – sink é referencia a água de uma fonte que chega à torneira de uma pia, assim também a entrada de dados vir de uma fonte desconhecida. Não há como se conhecer a fonte da água que chegou a pia apenas abrinfo a torneira, do mesmo modo não há como se conhecer a origem dos dados apenas olhando o código fonte.


O DOM, ou [Document Object Model](https://en.wikipedia.org/wiki/Document_Object_Model), é um formato estrutural usado para representar documentos em um navegador. O DOM permite que scripts dinâmicos, como JavaScript, façam referência a componentes do documento, como um campo de formulário ou um cookie de sessão. O DOM também é usado pelo navegador para segurança - por exemplo, para limitar que scripts em diferentes domínios possam obter cookies de sessão para outros domínios. Uma vulnerabilidade XSS baseada em DOM pode ocorrer quando o conteúdo ativo, como uma função JavaScript, é modificado por uma solicitação especialmente criada de forma que um elemento DOM possa ser controlado por um invasor.


Nem todos os defeitos de XSS requerem que o invasor controle o conteúdo retornado do servidor, mas, em vez disso, podem abusar de práticas de codificação JavaScript inadequadas para obter os mesmos resultados. As consequências são as mesmas de uma falha XSS típica, apenas o meio de entrega é diferente.


Comparadas a outros tipos de vulnerabilidades de cross site scripting ([refletidas e armazenadas] (https://owasp.org/www-community/attacks/xss/), onde um parâmetro não higienizado é passado pelo servidor e depois devolvido ao usuário e executado no contexto do navegador, uma vulnerabilidade XSS baseada em DOM controla o fluxo do código usando elementos do DOM junto com o código criado pelo invasor para alterar o fluxo.


Devido à sua natureza, as vulnerabilidades XSS baseadas em DOM podem ser executadas em muitas instâncias sem que o servidor seja capaz de determinar o que está realmente sendo executado. Isso pode tornar muitas das técnicas gerais de filtragem e detecção de XSS impotentes para tais ataques.
Um exemplo hipotético usa o seguinte código, lado de cliente:



```html
<script>
              document.write("Site is at: " + document.location.href + ".");
</script>
```


An attacker may append `#<script>alert('xss')</script>` to the affected page URL which would, when executed, display the alert box. In this instance, the appended code would not be sent to the server as everything after the `#` character is not treated as part of the query by the browser, but as a fragment. In this example, the code is immediately executed and an alert of "xss" is displayed by the page. Unlike the more common types of cross site scripting ([reflected and stored](https://owasp.org/www-community/attacks/xss/) in which the code is sent to the server and then back to the browser, this is executed directly in the user's browser without server contact.


Um invasor pode anexar `#<script>alert('xss')</script>` ao URL da página afetada que, quando executado, exibirá a caixa de alerta. Nesse caso, o código anexado não seria enviado ao servidor, pois tudo após o `#` não é tratado como parte da consulta pelo navegador, mas como um fragmento – e portanto faz referencia à própria página . Neste exemplo, o código é executado imediatamente e um alerta de "xss" é exibido pela página. Ao contrário dos tipos mais comuns de cross site scripting ([refletido e armazenado](https://owasp.org/www-community/attacks/xss/) no qual o código é enviado ao servidor e depois de volta ao navegador, ele é executado diretamente no navegador do usuário, sem contato com o servidor.


As [consequências](https://owasp.org/www-community/attacks/xss/)
das falhas de XSS baseadas em DOM são tão amplas quanto aquelas vistas em formas mais conhecidas, incluindo recuperação de cookies, injeção de script adicional malicioso, etc., e devem, portanto, ser tratadas com a mesma severidade.


## Objetivos de Testes


- Identificar “DOM sinks”.
- Construir payloads que pertençam a cada tipo de DOM sink.


## Como testar


Os aplicativos JavaScript diferem significativamente de outros tipos de aplicativos porque geralmente são gerados dinamicamente pelo servidor. Para entender qual código está sendo executado, o site que está sendo testado precisa ser rastreado para determinar todas as instâncias de JavaScript que estão sendo executadas e onde a entrada do usuário é aceita. Muitos sites dependem de grandes bibliotecas de funções, que muitas vezes se estendem por centenas de milhares de linhas de código e não foram desenvolvidas internamente. Nesses casos, o teste de cima para baixo geralmente se torna a única opção viável, uma vez que muitas funções de nível inferior nunca são usadas, e analisá-las para determinar quais são os coletores consumirá mais tempo do que o normalmente disponível. O mesmo pode ser dito para o teste de cima para baixo se as entradas ou a falta delas não forem identificadas no início.


Dados de entrada dos usuários são dadas de dois modos:


- Entradas inseridas na página pelo servidor e que não permitem XSS diretamente, e  
- Entradas obtidas por objetos JavaScript do lado do cliente.


Aqui estão dois exemplos de como o servidor pode inserir dados no JavaScript:


```js
var data = "<escaped data from the server>";

```
Aqui estão dois exemplos de dados de objetos JavaScript do lado do cliente:



```js
var data = window.location;
var result = someFunction(window.referrer);
```


Embora exista pouca diferença em como o código JavaScript é recuperado, é importante observar que, quando a entrada é recebida por meio do servidor, o servidor pode aplicar qualquer permutação desejada aos dados. Por outro lado, as permutações realizadas por objetos JavaScript são mais bem compreendidas e documentadas. Se `someFunction` no exemplo acima fosse um coletor, então a utilização no primeiro caso dependeria da filtragem feita pelo servidor, ao passo que no último caso dependeria da codificação feita pelo navegador no objeto `window.referrer. Stefano Di Paulo escreveu um excelente artigo sobre o que os navegadores retornam quando solicitados pelos vários elementos de uma [URL usando os atributos de documento e localização](https://github.com/wisec/domxsswiki/wiki/location,-documentURI-and-URL-sources).


Além disso, o JavaScript é frequentemente executado fora dos blocos de `<script>`, como evidenciado pelos muitos vetores que levaram a desvios de filtro XSS no passado. Ao rastrear o aplicativo, é importante observar o uso de scripts em locais tais como manipuladores de eventos e blocos CSS com atributos de expressão. Além disso, observe que qualquer CSS externo ou objetos de script precisará ser avaliado para determinar qual código está sendo executado.


Portanto, o teste manual deve ser realizado e pode ser feito examinando-se as áreas do código onde os parâmetros são mencionados úteis para um invasor. Exemplos de tais áreas incluem locais onde o código é escrito dinamicamente na página e em outros locais onde o DOM é modificado ou mesmo onde os scripts são executados diretamente.


```html
<script>
var pos=document.URL.indexOf("message=")+5;
document.write(document.URL.substring(pos,document.URL.length));
</script>
```
Contudo, isso não pode ser detectado no seguinte caso:



```html
<script>

	@@ -85,15 +85,16 @@ else
</script>
```


Por esse motivo, o teste automatizado não detectará áreas que podem ser suscetíveis a XSS baseado em DOM, a menos que a ferramenta de testes possa realizar análises adicionais do código do lado do cliente.




Portanto, o teste manual deve ser realizado examinando-se as áreas do código onde os parâmetros são mencionados e que podem ser úteis para um invasor. Exemplos de tais áreas incluem locais onde o código é escrito dinamicamente na página e em locais onde o DOM é modificado ou mesmo onde os scripts são executados diretamente.


## Remediação
Para medidas de prevenção de XSS baseado em DOM, consulte a [folha de dicas de prevenção de XSS baseada em DOM] 
(https://cheatsheetseries.owasp.org/cheatsheets/DOM_based_XSS_Prevention_Cheat_Sheet.html).


## Referências


- [Dom XSS Wiki](https://github.com/wisec/domxsswiki/wiki/)
- [DOM XSS 
![image](https://user-images.githubusercontent.com/25070780/114122257-e5687080-98bd-11eb-9ed5-dbce6136902a.png)