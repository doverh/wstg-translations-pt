# Conduza Buscas de Reconhecimento sobre o Vazamentos de Informações

|ID          |
|------------|
|WSTG-INFO-01|

## Resumo

Para que os mecanismos de pesquisa funcionem, programas de computador (ou `robôs`) buscam regularmente dados (conhecido como [rastreamento](https://en.wikipedia.org/wiki/Web_crawler)) de bilhões de páginas na web. Esses programas encontram conteúdo da web e funcionalidades seguindo links de outras páginas ou olhando sitemaps. Se um site usa um arquivo especial chamado `robots.txt` para listar páginas que não deseja que os mecanismos de pesquisa busquem, então as páginas listadas ali serão ignoradas. Esse é um resumo básico - o Google oferece uma explicação mais detalhada de [como um mecanismo de pesquisa funciona](https://support.google.com/webmasters/answer/70897?hl=en).

Testadores podem usar mecanismos de pesquisa para realizar um reconhecimento em sites e aplicações web. Existem elementos diretos e indiretos na descoberta e no reconhecimento de mecanismos de pesquisa: métodos diretos estão relacionados à busca nos índices e no conteúdo associado a partir de caches, enquanto métodos indiretos estão relacionados à obtenção de informações sensíveis de design e configuração por meio de pesquisas em fóruns, newsgroups e sites de licitação.

Após o término do rastreamento por um robô de mecanismo de pesquisa, ele começa a indexar o conteúdo da web com base em tags e atributos associados, como `<TITLE>`, para retornar resultados de pesquisa relevantes. Se o arquivo `robots.txt` não for atualizado durante a vida útil do site e tags meta HTML em linha que instruam os robôs a não indexar o conteúdo não tiverem sido usadas, então é possível que os índices contenham conteúdo da web não pretendido pelos proprietários. Proprietários de sites podem usar o arquivo `robots.txt` mencionado anteriormente, tags meta HTML, autenticação e ferramentas fornecidas pelos mecanismos de pesquisa para remover tal conteúdo.

## Objetivos do Teste

- Identificar quais informações sensíveis de design e configuração do aplicativo, sistema ou organização são expostas diretamente (no site da organização) ou indiretamente (por meio de serviços de terceiros).

## Como Testar

Use um mecanismo de pesquisa para buscar informações potencialmente sensíveis. Isso pode incluir:

- diagramas de rede e configurações;
- mensagens e e-mails arquivados por administradores ou outros funcionários-chave;
- procedimentos de logon e formatos de nome de usuário;
- nomes de usuário, senhas e chaves privadas;
- arquivos de configuração de serviços de terceiros ou de nuvem;
- conteúdo de mensagens de erro revelador; e
- versões de desenvolvimento, teste, teste de aceitação do usuário (UAT) e estágio de sites.

### Mecanismos de Pesquisa

Não limite os testes a apenas um provedor de mecanismo de pesquisa, pois diferentes mecanismos de pesquisa podem gerar resultados diferentes. Os resultados dos mecanismos de pesquisa podem variar de algumas maneiras, dependendo de quando o mecanismo rastreou pela última vez o conteúdo e do algoritmo que o mecanismo usa para determinar as páginas relevantes. Considere usar os seguintes mecanismos de pesquisa (listados em ordem alfabética):

- [Baidu](https://www.baidu.com/), o mecanismo de pesquisa mais popular da [China](https://en.wikipedia.org/wiki/Web_search_engine#Market_share).
- [Bing](https://www.bing.com/), um mecanismo de pesquisa de propriedade e operado pela Microsoft, o segundo mais popular no [mundo](https://en.wikipedia.org/wiki/Web_search_engine#Market_share). Suporta [palavras-chave de pesquisa avançadas](http://help.bing.microsoft.com/#apex/18/en-US/10001/-1).
- [binsearch.info](https://binsearch.info/), um mecanismo de pesquisa para novos grupos binários Usenet.
- [Common Crawl](https://commoncrawl.org/), "um repositório aberto de dados de rastreamento da web que pode ser acessado e analisado por qualquer pessoa."
- [DuckDuckGo](https://duckduckgo.com/), um mecanismo de pesquisa focado em privacidade que compila resultados de muitas [fontes](https://help.duckduckgo.com/results/sources/). Suporta [sintaxe de busca](https://help.duckduckgo.com/duckduckgo-help-pages/results/syntax/).
- [Google](https://www.google.com/), que oferece o mecanismo de pesquisa mais popular do [mundo](https://en.wikipedia.org/wiki/Web_search_engine#Market_share) e utiliza um sistema de classificação para tentar retornar os resultados mais relevantes. Suporta [operadores de busca](https://support.google.com/websearch/answer/2466433).
- [Internet Archive Wayback Machine](https://archive.org/web/), "construindo uma biblioteca digital de sites da Internet e outros artefatos culturais em formato digital."
- [Startpage](https://www.startpage.com/), um mecanismo de pesquisa que usa os resultados do Google sem coletar informações pessoais por meio de rastreadores e logs. Suporta [operadores de busca](https://support.startpage.com/index.php?/Knowledgebase/Article/View/989/0/advanced-search-which-search-operators-are-supported-by-startpagecom).
- [Shodan](https://www.shodan.io/), um serviço de busca por dispositivos e serviços conectados à Internet. As opções de uso incluem um plano gratuito limitado, bem como planos de assinatura pagos.

Tanto o DuckDuckGo quanto o Startpage oferecem maior privacidade aos usuários, não utilizando rastreadores ou mantendo logs. Isso pode reduzir os vazamentos de informações sobre o testador.

### Operadores de Busca

Um operador de busca é uma palavra-chave ou sintaxe especial que estende as capacidades das consultas de busca regulares e ajuda a obter resultados mais específicos. Eles geralmente têm a forma de `operador:consulta`. Aqui estão alguns operadores de busca comumente suportados:

- `site:` irá limitar a busca ao domínio fornecido.
- `inurl:` irá retornar apenas resultados que incluam a palavra-chave na URL.
- `intitle:` irá retornar apenas resultados que tenham a palavra-chave no título da página.
- `intext:` ou `inbody:` irá pesquisar a palavra-chave apenas no corpo das páginas.
- `filetype:` irá corresponder apenas a um tipo de arquivo específico, como png ou php.

Por exemplo, para encontrar o conteúdo da web de owasp.org indexado por um mecanismo de pesquisa típico, a sintaxe necessária é:

```text
site:owasp.org
```

![Exemplo de Resultado de Busca com Operação Site do Google](images/Google_site_Operator_Search_Results_Example_20200406.png)\
*Figura 4.1.1-1: Exemplo de Resultado de Busca com Operação Site do Google*

### Visualizando Conteúdo em Cache

Para buscar conteúdo que foi previamente indexado, use o operador `cache:`. Isso é útil para visualizar conteúdo que pode ter mudado desde o momento em que foi indexado ou que pode não estar mais disponível. Nem todos os mecanismos de pesquisa fornecem conteúdo em cache para busca; a fonte mais útil no momento da escrita é o Google.

Para visualizar `owasp.org` conforme está em cache, a sintaxe é:

```text
cache:owasp.org
```

![Exemplo de Resultado de Busca com Operação Cache do Google](images/Google_cache_Operator_Search_Results_Example_20200406.png)\
*Figura 4.1.1-2: Exemplo de Resultado de Busca com Operação Cache do Google*

### Google Hacking ou Dorking

Fazer buscas com operadores pode ser uma técnica de descoberta muito eficaz quando combinada com a criatividade do testador. Operadores podem ser encadeados para descobrir efetivamente tipos específicos de arquivos e informações sensíveis. Essa técnica, chamada de [Google hacking](https://en.wikipedia.org/wiki/Google_hacking) ou Dorking, também é possível com outros mecanismos de pesquisa, desde que os operadores de busca sejam suportados.

Um banco de dados de dorks, como [Google Hacking Database](https://www.exploit-db.com/google-hacking-database), é uma ferramenta útil que pode ajudar a descobrir informações específicas. Algumas categorias de dorks disponíveis nesse banco de dados incluem:

- Pontos de entrada
- Arquivos contendo nomes de usuários
- Diretórios sensíveis
- Detecção de servidor web
- Arquivos vulneráveis
- Servidores vulneráveis
- Mensagens de erro
- Arquivos contendo informações valiosas
- Arquivos contendo senhas
- Informações sigilosas de compras online  

Bancos de dados para outros mecanismos de pesquisa, como Bing e Shodan, estão disponíveis em recursos como o [Google Hacking Diggity Project](https://resources.bishopfox.com/resources/tools/google-hacking-diggity/) da Bishop Fox.

## Remediação

Considere cuidadosamente a sensibilidade das informações de design e configuração antes de publicá-las online.

Revise periodicamente a sensibilidade das informações de design e configuração existentes que foram publicadas online.