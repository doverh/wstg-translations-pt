# Revisar Backups Antigos e Arquivos Não Referenciados para Informações Sensíveis

|ID          |
|------------|
|WSTG-CONF-04|

## Resumo

Embora a maioria dos arquivos em um servidor web seja gerenciada diretamente pelo próprio servidor, não é incomum encontrar arquivos não referenciados ou esquecidos que podem ser usados para obter informações importantes sobre a infraestrutura ou credenciais.

Cenários mais comuns incluem a presença de versões antigas renomeadas de arquivos modificados, arquivos de inclusão que são carregados na linguagem de escolha e podem ser baixados como código-fonte, ou até mesmo backups automáticos ou manuais em forma de arquivos compactados. Arquivos de backup também podem ser gerados automaticamente pelo sistema de arquivos subjacente no qual a aplicação está hospedada, uma funcionalidade geralmente chamada de "snapshots".

Todos esses arquivos podem conceder ao testador acesso às operações internas, portas dos fundos, interfaces administrativas ou até mesmo credenciais para se conectar à interface administrativa ou ao servidor de banco de dados.

Uma fonte importante de vulnerabilidade está em arquivos que não têm relação com a aplicação, mas são criados como consequência da edição de arquivos da aplicação, ou após a criação de cópias de backup improvisadas, ou ao deixar no sistema web arquivos antigos ou não referenciados. Realizar edições no local ou outras ações administrativas em servidores web de produção pode inadvertidamente deixar cópias de backup, seja geradas automaticamente pelo editor ao editar arquivos, ou pelo administrador que está compactando um conjunto de arquivos para criar um backup.

É fácil esquecer tais arquivos, e isso pode representar uma séria ameaça à segurança da aplicação. Isso acontece porque cópias de backup podem ser geradas com extensões de arquivo diferentes das dos arquivos originais. Um arquivo de arquivamento `.tar`, `.zip` ou `.gz` que geramos (e esquecemos...) tem obviamente uma extensão diferente, o mesmo acontece com cópias automáticas criadas por muitos editores (por exemplo, o emacs gera uma cópia de backup chamada `file~` ao editar `file`). Fazer uma cópia manualmente pode produzir o mesmo efeito (pense em copiar `file` para `file.old`). O sistema de arquivos subjacente no qual a aplicação está pode estar fazendo `snapshots` da sua aplicação em diferentes momentos sem o seu conhecimento, os quais também podem ser acessíveis via web, representando uma ameaça semelhante, mas diferente, ao estilo de `arquivo de backup` para a sua aplicação.

Como resultado, essas atividades geram arquivos desnecessários para a aplicação e que podem ser tratados de maneira diferente pelo servidor web em comparação com o arquivo original. Por exemplo, se fizermos uma cópia de `login.asp` chamada `login.asp.old`, estamos permitindo que os usuários baixem o código-fonte de `login.asp`. Isso ocorre porque `login.asp.old` geralmente será servido como texto ou plano, em vez de ser executado devido à sua extensão. Em outras palavras, acessar `login.asp` causa a execução do código no lado do servidor de `login.asp`, enquanto acessar `login.asp.old` faz com que o conteúdo de `login.asp.old` (que também é código no lado do servidor) seja simplesmente retornado ao usuário e exibido no navegador. Isso pode representar riscos de segurança, pois informações sensíveis podem ser reveladas.

Geralmente, expor código no lado do servidor é uma má ideia. Não apenas você está expondo desnecessariamente a lógica de negócios, mas também pode estar revelando inadvertidamente informações relacionadas à aplicação que podem ajudar um invasor (nomes de caminhos, estruturas de dados, etc.). Sem mencionar o fato de que existem muitos scripts com nome de usuário e senha incorporados em texto simples (o que é uma prática descuidada e muito perigosa).

Outras causas de arquivos não referenciados ocorrem devido a escolhas de design ou configuração que permitem diversos tipos de arquivos relacionados à aplicação, como arquivos de dados, arquivos de configuração, arquivos de log, serem armazenados em diretórios do sistema de arquivos que podem ser acessados pelo servidor web. Esses arquivos normalmente não têm motivo para estar em um espaço de sistema de arquivos que pode ser acessado via web, pois devem ser acessados apenas no nível da aplicação, pela aplicação em si (e não pelo usuário casual navegando).

### Ameaças

Arquivos antigos, de backup e não referenciados apresentam várias ameaças à segurança de uma aplicação web:

- Arquivos não referenciados podem divulgar informações sensíveis que facilitam um ataque direcionado contra a aplicação; por exemplo, arquivos de inclusão contendo credenciais de banco de dados, arquivos de configuração contendo referências a outros conteúdos ocultos, caminhos de arquivos absolutos, etc.
- Páginas não referenciadas podem conter funcionalidades poderosas que podem ser usadas para atacar a aplicação; por exemplo, uma página de administração que não está vinculada a conteúdo publicado, mas pode ser acessada por qualquer usuário que saiba onde encontrá-la.
- Arquivos antigos e de backup podem conter vulnerabilidades que foram corrigidas em versões mais recentes; por exemplo, `viewdoc.old.jsp` pode conter uma vulnerabilidade de travessia de diretórios que foi corrigida em `viewdoc.jsp`, mas ainda pode ser explorada por quem encontrar a versão antiga.
- Arquivos de backup podem divulgar o código-fonte de páginas projetadas para serem executadas no servidor; por exemplo, solicitar `viewdoc.bak` pode retornar o código-fonte de `viewdoc.jsp`, que pode ser revisado em busca de vulnerabilidades que podem ser difíceis de encontrar fazendo solicitações cegas à página executável. Embora essa ameaça se aplique obviamente a linguagens de script, como Perl, PHP, ASP, scripts shell, JSP, etc., ela não se limita a elas, como mostrado no exemplo fornecido na próxima seção.
- Arquivos de backup podem conter cópias de todos os arquivos dentro (ou mesmo fora) da raiz do webserver. Isso permite que um invasor enumere rapidamente toda a aplicação, incluindo páginas não referenciadas, código-fonte

, arquivos de inclusão, etc. Por exemplo, se você esquecer um arquivo chamado `myservlets.jar.old` que contém (uma cópia de backup) das suas classes de implementação de servlet, você está expondo muitas informações sensíveis suscetíveis a descompilação e engenharia reversa.
- Em alguns casos, copiar ou editar um arquivo não modifica a extensão do arquivo, mas modifica o nome do arquivo. Isso acontece, por exemplo, em ambientes Windows, onde operações de cópia de arquivos geram nomes de arquivo prefixados com "Copy of " ou versões localizadas dessa string. Como a extensão do arquivo não é alterada, esse não é um caso em que um arquivo executável é retornado como texto simples pelo servidor web e, portanto, não é um caso de divulgação de código-fonte. No entanto, esses arquivos também são perigosos porque há a chance de incluírem lógica obsoleta e incorreta que, quando invocada, pode acionar erros na aplicação, o que pode fornecer informações valiosas a um invasor, se a exibição de mensagens de diagnóstico estiver habilitada.
- Arquivos de log podem conter informações sensíveis sobre as atividades dos usuários da aplicação, por exemplo, dados sensíveis transmitidos em parâmetros de URL, IDs de sessão, URLs visitados (que podem revelar conteúdo adicional não referenciado), etc. Outros arquivos de log (por exemplo, logs de FTP) podem conter informações sensíveis sobre a manutenção da aplicação por administradores de sistema.
- Snapshots do sistema de arquivos podem conter cópias do código que contêm vulnerabilidades que foram corrigidas em versões mais recentes. Por exemplo, `/.snapshot/monthly.1/view.php` pode conter uma vulnerabilidade de travessia de diretórios que foi corrigida em `/view.php`, mas ainda pode ser explorada por quem encontrar a versão antiga.

## Objetivos do Teste

- Encontrar e analisar arquivos não referenciados que podem conter informações sensíveis.

## Como Testar

### Teste de Caixa Preta

O teste de arquivos não referenciados usa técnicas automatizadas e manuais, envolvendo tipicamente uma combinação dos seguintes métodos:

#### Inferência do Esquema de Nomenclatura Usado para Conteúdo Publicado

Enumere todas as páginas e funcionalidades da aplicação. Isso pode ser feito manualmente usando um navegador ou usando uma ferramenta de spidering de aplicação. A maioria das aplicações usa um esquema de nomenclatura reconhecível e organiza recursos em páginas e diretórios usando palavras que descrevem sua função. A partir do esquema de nomenclatura usado para o conteúdo publicado, muitas vezes é possível inferir o nome e a localização de páginas não referenciadas. Por exemplo, se uma página `viewuser.asp` for encontrada, procure também por `edituser.asp`, `adduser.asp` e `deleteuser.asp`. Se um diretório `/app/user` for encontrado, procure também por `/app/admin` e `/app/manager`.

#### Outras Pistas no Conteúdo Publicado

Muitas aplicações web deixam pistas em conteúdos publicados que podem levar à descoberta de páginas e funcionalidades ocultas. Essas pistas frequentemente aparecem no código-fonte de arquivos HTML e JavaScript. O código-fonte de todo o conteúdo publicado deve ser revisado manualmente para identificar pistas sobre outras páginas e funcionalidades. Por exemplo:

Comentários de programadores e seções comentadas do código-fonte podem se referir a conteúdo oculto:

```html
<!-- <A HREF="uploadfile.jsp">Enviar um documento para o servidor</A> -->
<!-- Link removido enquanto falhas em uploadfile.jsp são corrigidas -->
```

JavaScript pode conter links para páginas que são renderizados apenas na GUI do usuário sob certas circunstâncias:

```javascript
var adminUser=false;
if (adminUser) menu.add (new menuItem ("Manter usuários", "/admin/useradmin.jsp"));
```

Páginas HTML podem conter FORMs que foram ocultados desabilitando o elemento SUBMIT:

```html
<form action="forgotPassword.jsp" method="post">
    <input type="hidden" name="userID" value="123">
    <!-- <input type="submit" value="Esqueci a Senha"> -->
</form>
```

Outra fonte de pistas sobre diretórios não referenciados é o arquivo `/robots.txt` usado para fornecer instruções a robôs da web:

```html
User-agent: *
Disallow: /Admin
Disallow: /uploads
Disallow: /backup
Disallow: /~jbloggs
Disallow: /include
```

#### Adivinhação às Cegas

Em sua forma mais simples, isso envolve executar uma lista de nomes de arquivos comuns por meio de um mecanismo de solicitação na tentativa de adivinhar arquivos e diretórios que existem no servidor. O seguinte script de invólucro netcat lerá uma lista de palavras do stdin e realizará um ataque básico de adivinhação:

```bash
#!/bin/bash

servidor=example.org
porta=80

while read url
do
echo -ne "$url\t"
echo -e "GET /$url HTTP/1.0\nHost: $servidor\n" | netcat $servidor $porta | head -1
done | tee arquivo_saida
```

Dependendo do servidor, GET pode ser substituído por HEAD para obter resultados mais rápidos. O arquivo de saída especificado pode ser filtrado por códigos de resposta "interessantes". O código de resposta 200 (OK) geralmente indica que um recurso válido foi encontrado (desde que o servidor não forneça uma página de "não encontrado" personalizada usando o código 200). Mas também fique atento a 301 (Movido), 302 (Encontrado), 401 (Não autorizado), 403 (Proibido) e 500 (Erro interno), que também podem indicar recursos ou diretórios que merecem investigação adicional.

O ataque básico de adivinhação deve ser executado contra a raiz do webserver e também contra todos os diretórios identificados por outras técnicas de enumeração. Ataques de adivinhação mais avançados/eficazes podem ser realizados da seguinte forma:

- Identificar as extensões de arquivo em uso dentro de áreas conhecidas da aplicação (por exemplo, jsp, aspx, html) e usar uma lista básica de palavras anexada a cada uma dess

as extensões (ou usar uma lista mais longa de extensões comuns se os recursos permitirem).
- Para cada arquivo identificado por outras técnicas de enumeração, criar uma lista de palavras personalizada derivada desse nome de arquivo. Obter uma lista de extensões de arquivo comuns (incluindo ~, bak, txt, src, dev, old, inc, orig, copy, tmp, swp, etc.) e usar cada extensão antes, depois e no lugar da extensão do arquivo real.

Observação: Operações de cópia de arquivos no Windows geram nomes de arquivos prefixados com "Copy of " ou versões localizadas dessa string, portanto, não alteram as extensões de arquivo. Embora os arquivos "Copy of " geralmente não revelem o código-fonte quando acessados, podem fornecer informações valiosas caso causem erros quando invocados.

#### Informações Obtidas por Meio de Vulnerabilidades e Má Configuração do Servidor

A maneira mais óbvia pela qual um servidor mal configurado pode divulgar páginas não referenciadas é por meio da listagem de diretórios. Solicite todos os diretórios enumerados para identificar qualquer um que forneça uma lista de diretórios.

Numerosas vulnerabilidades foram encontradas em servidores web individuais que permitem a um invasor enumerar conteúdo não referenciado, por exemplo:

- Vulnerabilidade de listagem de diretórios Apache ?M=D.
- Várias vulnerabilidades de divulgação de código-fonte de scripts IIS.
- Vulnerabilidades de listagem de diretórios IIS WebDAV.

#### Uso de Informações Publicamente Disponíveis

Páginas e funcionalidades em aplicações web voltadas para a Internet que não são referenciadas de dentro da aplicação em si podem ser referenciadas por outras fontes de domínio público. Existem várias fontes dessas referências:

- Páginas que costumavam ser referenciadas ainda podem aparecer nos arquivos de busca de motores de busca na Internet. Por exemplo, `1998results.asp` pode não estar mais vinculado ao site de uma empresa, mas ainda pode permanecer no servidor e nos bancos de dados dos motores de busca. Esse script antigo pode conter vulnerabilidades que poderiam ser usadas para comprometer todo o site. O operador de pesquisa `site:` do Google pode ser usado para executar uma consulta apenas contra o domínio de escolha, como em: `site:www.example.com`. O uso de motores de busca dessa maneira levou a uma ampla variedade de técnicas que você pode achar úteis e que são descritas na seção `Google Hacking` deste Guia. Confira para aprimorar suas habilidades de teste via Google. Arquivos de backup não são propensos a serem referenciados por outros arquivos e, portanto, podem não ter sido indexados pelo Google, mas se estiverem em diretórios navegáveis, o mecanismo de busca pode estar ciente deles.
- Além disso, o Google e o Yahoo mantêm versões em cache de páginas encontradas por seus robôs. Mesmo que `1998results.asp` tenha sido removido do servidor de destino, uma versão de sua saída ainda pode estar armazenada por esses motores de busca. A versão em cache pode conter referências ou pistas sobre conteúdo oculto adicional que ainda permanece no servidor.
- Conteúdo que não é referenciado de dentro de uma aplicação de destino pode ser vinculado por sites de terceiros. Por exemplo, uma aplicação que processa pagamentos online em nome de comerciantes de terceiros pode conter uma variedade de funcionalidades personalizadas que normalmente só podem ser encontradas seguindo links nos sites de seus clientes.

#### Desvio de Filtro de Nome de Arquivo

Como os filtros de lista negra são baseados em expressões regulares, às vezes é possível tirar vantagem de recursos obscuros de expansão de nome de arquivo do sistema operacional de maneiras que o desenvolvedor não esperava. O testador pode explorar diferenças nas formas como os nomes de arquivo são interpretados pela aplicação, servidor web e sistema operacional subjacente, e nas convenções de nomenclatura de arquivo.

Exemplo: Expansão de nome de arquivo do Windows 8.3 `c:\\program files` se torna `C:\\PROGRA\~1`

- Remover caracteres incompatíveis.
- Converter espaços em sublinhados.
- Pegar os primeiros seis caracteres do nome do arquivo base.
- Adicionar `~<dígito>`, que é usado para distinguir arquivos com nomes que usam os mesmos seis caracteres iniciais.
- Essa convenção muda após as primeiras 3 colisões de nome.
- Truncar a extensão do arquivo para três caracteres.
- Tornar todos os caracteres maiúsculos.

### Teste de Caixa Cinza

Realizar testes de caixa cinza contra arquivos antigos e de backup requer examinar os arquivos contidos nos diretórios pertencentes ao conjunto de diretórios da web servidos pelos servidor(es) web da infraestrutura da aplicação web. Teoricamente, o exame deve ser feito manualmente para ser abrangente. No entanto, como na maioria dos casos cópias de arquivos ou arquivos de backup tendem a ser criados usando as mesmas convenções de nomenclatura, a busca pode ser facilmente automatizada. Por exemplo, editores deixam para trás cópias de backup nomeando-as com uma extensão ou final reconhecível, e humanos tendem a deixar para trás arquivos com uma extensão previsível como `.old`. Uma boa estratégia é a de periodicamente agendar um trabalho em segundo plano para verificar arquivos com extensões que provavelmente representam cópias de arquivos de backup, renomeados ou antigos, em todos os diretórios web (por exemplo, verificando todos os diretórios web para arquivos que correspondam a `*~` ou `*.bak` ou `*.old`, etc.).

# Medidas de Remediação

Para garantir uma estratégia eficaz de proteção, os testes devem ser complementados por uma política de segurança que proíba claramente práticas perigosas, tais como:

- Editar arquivos no local nos sistemas de arquivos do servidor web ou do servidor de aplicativos. Este é um hábito especialmente ruim, pois é provável que gere arquivos de backup ou temporários pelos editores. É surpreendente ver com que frequência isso é feito, mesmo em organizações grandes. Se você realmente precisa editar arquivos em um sistema de produção, certifique-se de não deixar para trás nada que não seja explicitamente pretendido, e considere que está fazendo isso por sua própria conta e risco.
- Verificar cuidadosamente qualquer outra atividade realizada nos sistemas de arquivos expostos pelo servidor web, como atividades de administração pontual. Por exemplo, se você ocasionalmente precisar fazer um snapshot de alguns diretórios (o que você não deve fazer em um sistema de produção), pode ser tentador compactá-los primeiro. Tenha cuidado para não deixar para trás esses arquivos de arquivo.
- Políticas apropriadas de gerenciamento de configuração devem ajudar a prevenir arquivos obsoletos e não referenciados.
- As aplicações devem ser projetadas para não criar (ou depender de) arquivos armazenados sob as árvores de diretórios web servidas pelo servidor web. Arquivos de dados, arquivos de log, arquivos de configuração, etc., devem ser armazenados em diretórios não acessíveis pelo servidor web, para combater a possibilidade de divulgação de informações (sem mencionar a modificação de dados se as permissões do diretório web permitirem gravação).
- Os snapshots do sistema de arquivos não devem ser acessíveis via web se o document root estiver em um sistema de arquivos que usa essa tecnologia. Configure seu servidor web para negar acesso a esses diretórios, por exemplo, no Apache, uma diretiva de localização como esta deve ser usada:

```xml
<Location ~ ".snapshot">
    Order deny,allow
    Deny from all
</Location>
```

## Ferramentas

Ferramentas de avaliação de vulnerabilidades tendem a incluir verificações para identificar diretórios web com nomes padrão (como "admin", "test", "backup", etc.) e relatar qualquer diretório web que permita indexação. Se você não conseguir obter nenhuma listagem de diretório, deve tentar verificar as extensões prováveis de backup. Verifique, por exemplo:

- [Nessus](https://www.tenable.com/products/nessus)
- [Nikto2](https://cirt.net/Nikto2)

Ferramentas de spidering web:

- [wget](https://www.gnu.org/software/wget/)
- [Wget para Windows](http://www.interlog.com/~tcharron/wgetwin.html)
- [Sam Spade](https://web.archive.org/web/20090926061558/http://preview.samspade.org/ssw/download.html)
- [Spike proxy inclui uma função de rastreador de site](https://www.spikeproxy.com/)
- [Xenu](http://home.snafu.de/tilman/xenulink.html)
- [curl](https://curl.haxx.se)

Alguns deles também estão incluídos em distribuições padrão do Linux. Ferramentas de desenvolvimento web geralmente incluem recursos para identificar links quebrados e arquivos não referenciados.