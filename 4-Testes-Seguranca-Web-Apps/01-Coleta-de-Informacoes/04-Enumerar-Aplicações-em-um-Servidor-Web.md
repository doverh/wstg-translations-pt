# Enumerar Aplicações em um Servidor Web

|ID          |
|------------|
|WSTG-INFO-04|

## Resumo

Um passo fundamental para testar vulnerabilidades de aplicativos web é descobrir quais aplicativos específicos estão hospedados em um servidor web. Muitos aplicativos têm vulnerabilidades conhecidas e estratégias de ataque conhecidas que podem ser exploradas para obter controle remoto ou explorar dados. Além disso, muitos aplicativos geralmente estão mal configurados ou desatualizados, devido à percepção de que eles são usados apenas "internamente" e, portanto, não representam uma ameaça.
Com a proliferação de servidores web virtuais, a relação tradicional 1:1 entre um endereço IP e um servidor web está perdendo grande parte de seu significado original. Não é incomum ter vários sites ou aplicativos web cujos nomes simbólicos se resolvem para o mesmo endereço IP. Essa situação não se limita a ambientes de hospedagem, mas também se aplica a ambientes corporativos comuns.

Profissionais de segurança podem receber um conjunto de endereços IP como alvo para teste. Pode-se argumentar que esse cenário está mais relacionado a um teste de invasão, mas, de qualquer forma, espera-se que essa atribuição teste todos os aplicativos web acessíveis por meio desse alvo. O problema é que o endereço IP fornecido hospeda um serviço HTTP na porta 80, mas se um testador tentar acessá-lo especificando o endereço IP (que é tudo o que eles sabem), será exibida a mensagem "Nenhum servidor web configurado neste endereço" ou mensagem semelhante. Mas esse sistema pode "esconder" vários aplicativos web, associados a nomes simbólicos (DNS) não relacionados. Obviamente, a extensão da análise é profundamente afetada pela forma como o testador testa, se todos os aplicativos ou apenas aqueles de que têm conhecimento.

Às vezes, a especificação do alvo é mais detalhada. O testador pode receber uma lista de endereços IP e seus nomes simbólicos correspondentes. No entanto, essa lista pode transmitir informações parciais, ou seja, pode omitir alguns nomes simbólicos e o cliente pode nem mesmo estar ciente disso (isso é mais provável de acontecer em grandes organizações).

Outros problemas que afetam o escopo da avaliação são representados por aplicativos web publicados em URLs não óbvias (por exemplo, `http://www.example.com/some-strange-URL`), que não são referenciados em nenhum outro lugar. Isso pode acontecer tanto por erro (devido a configurações incorretas) quanto intencionalmente (por exemplo, interfaces administrativas não divulgadas).

Para lidar com esses problemas, é necessário realizar a descoberta de aplicativos web.

## Objetivos do Teste

- Enumerar os aplicativos dentro do escopo que existem em um servidor da web.

## Como Testar

A descoberta de aplicativos web é um processo destinado a identificar os aplicativos em uma determinada infraestrutura. Esta última geralmente é especificada como um conjunto de endereços IP (talvez um bloco de rede), mas pode consistir em um conjunto de nomes simbólicos DNS ou uma combinação dos dois. Essas informações são fornecidas antes da execução de uma avaliação, seja um teste de penetração no estilo clássico ou uma avaliação focada em aplicativos. Em ambos os casos, a menos que as regras de engajamento especifiquem o contrário (por exemplo, teste apenas o aplicativo localizado na URL `http://www.example.com/`), a avaliação deve se esforçar para ser a mais abrangente possível em termos de escopo, ou seja, deve identificar todos os aplicativos acessíveis por meio do alvo fornecido. Os exemplos a seguir analisam algumas técnicas que podem ser empregadas para atingir esse objetivo.

> Algumas das técnicas a seguir se aplicam a servidores web voltados para a Internet, ou seja, serviços de pesquisa on-line baseados em DNS e IP reverso e o uso de mecanismos de busca. Os exemplos usam endereços IP privados (como `192.168.1.100`), que, a menos que indicado de outra forma, representam endereços IP *genéricos* e são usados apenas para fins de anonimato.

Existem três fatores que influenciam quantos aplicativos estão relacionados a um determinado nome de DNS (ou um endereço IP):

1. **URL base diferente**

    O ponto de entrada óbvio para um aplicativo da web é `www.example.com`, ou seja, com essa notação abreviada, pensamos no aplicativo da web originando em `http://www.example.com/` (o mesmo se aplica a https). No entanto, mesmo que essa seja a situação mais comum, não há nada que obrigue o aplicativo a iniciar em `/`.

    Por exemplo, o mesmo nome simbólico pode estar associado a três aplicativos web, como: `http://www.example.com/url1` `http://www.example.com/url2` `http://www.example.com/url3`

    Nesse caso, a URL `http://www.example.com/` não estaria associada a uma página significativa, e os três aplicativos estariam **ocultos**, a menos que o testador saiba explicitamente como acessá-los, ou seja, o testador conhece *url1*, *url2* ou *url3*. Normalmente, não há necessidade de publicar aplicativos web dessa maneira, a menos que o proprietário não queira que eles sejam acessíveis de forma padrão e esteja preparado para informar os usuários sobre sua localização exata. Isso não significa que esses aplicativos sejam secretos, apenas que sua existência e localização não são explicitamente divulgadas.

2. **Portas não padrão**

    Embora os aplicativos web normalmente funcionem nas portas 80 (http) e 443 (https), não há nada de especial nessas portas. De fato, os aplicativos web podem estar associados a outras portas TCP e podem ser acessados especificando o número da porta da seguinte forma: `http[s]://www.example.com:port/`. Por exemplo, `http://www.example.com:20000/`.

3. **Hosts virtuais**

    O DNS permite que um único endereço IP seja associado a um ou mais nomes simbólicos. Por exemplo, o endereço IP `192.168.1.100` pode ser associado aos nomes DNS `www.example.com`, `helpdesk.example.com`, `webmail.example.com`. Não é necessário que todos os nomes pertençam ao mesmo domínio DNS. Essa relação de 1-para-N pode ser refletida para fornecer conteúdos diferentes usando os chamados hosts virtuais. As informações que especificam o host virtual ao qual estamos nos referindo estão incorporadas no cabeçalho HTTP 1.1 [Host](https://tools.ietf.org/html/rfc2616#section-14.23).

    Ninguém suspeitaria da existência de outros aplicativos web além do óbvio `www.example.com`, a menos que conhecessem `helpdesk.example.com` e `webmail.example.com`.

### Abordagens para tratar a questão 1 - URLs não padrão

Não há como confirmar completamente a existência de aplicativos web com nomes não padrão. Por serem não padrão, não há critérios fixos que regulem a convenção de nomenclatura, no entanto, existem várias técnicas que o testador pode usar para obter algumas informações adicionais.

Primeiro, se o servidor web estiver mal configurado e permitir a navegação de diretórios, pode ser possível identificar esses aplicativos. Ferramentas de varredura de vulnerabilidades podem ajudar nesse aspecto.

Segundo, esses aplicativos podem ser referenciados por outras páginas da web e há uma chance de que tenham sido rastreados e indexados por mecanismos de busca. Se os testadores suspeitarem da existência desses aplicativos "ocultos" em `www.example.com`, eles podem pesquisar usando o operador *site* e examinando o resultado de uma busca por `site: www.example.com`. Entre os URLs retornados, pode haver um apontando para um aplicativo não óbvio.

Outra opção é investigar URLs que possivelmente sejam candidatos a aplicativos não publicados. Por exemplo, um front-end de correio da web pode ser acessível através de URLs como `https://www.example.com/webmail`, `https://webmail.example.com/`, ou `https://mail.example.com/`. O mesmo vale para interfaces administrativas, que podem ser publicadas em URLs ocultas (por exemplo, uma interface administrativa do Tomcat), sem serem referenciadas em nenhum lugar. Portanto, realizar uma busca estilo dicionário (ou "adivinhação inteligente") pode revelar alguns resultados. Ferramentas de varredura de vulnerabilidades podem ajudar nesse aspecto.

### Abordagens para tratar a questão 2 - Portas não padrão

É fácil verificar a existência de aplicativos web em portas não padrão. Um scanner de portas, como o nmap, é capaz de realizar o reconhecimento de serviços por meio da opção `-sV` e identificará serviços http[s] em portas arbitrárias. O que é necessário é uma varredura completa de todo o espaço de endereços de porta TCP de 64k.

Por exemplo, o seguinte comando pesquisará, com uma varredura de conexão TCP, todas as portas abertas no IP `192.168.1.100` e tentará determinar quais serviços estão associados a elas (apenas as opções *essenciais* estão mostradas - o nmap possui um amplo conjunto de opções, cuja discussão está fora do escopo):

`nmap –Pn –sT –sV –p0-65535 192.168.1.100`

É suficiente examinar a saída e procurar por http ou indicação de serviços com SSL (que devem ser verificados para confirmar se são https). Por exemplo, a saída do comando anterior pode ser semelhante a esta:

```bash
Portas interessantes em 192.168.1.100:
(As 65527 portas verificadas e não mostradas abaixo estão no estado: fechadas)
PORTA     ESTADO SERVIÇO      VERSÃO
22/tcp    aberta ssh          OpenSSH 3.5p1 (protocolo 1.99)
80/tcp    aberta http         Apache httpd 2.0.40 ((Red Hat Linux))
443/tcp   aberta ssl          OpenSSL
901/tcp   aberta http         Samba SWAT servidor de administração
1241/tcp  aberta ssl          Nessus security scanner
3690/tcp  aberta desconhecido
8000/tcp  aberta http-alt?
8080/tcp  aberta http         Apache Tomcat/Coyote JSP engine 1.1
```

A partir deste exemplo, pode-se ver que:

- Há um servidor Apache HTTP em execução na porta 80.
- Parece haver um servidor HTTPS na porta 443 (mas isso precisa ser confirmado, por exemplo, visitando `https://192.168.1.100` com um navegador).
- Na porta 901, há uma interface web do Samba SWAT.
- O serviço na porta 1241 não é HTTPS, mas é o daemon Nessus com SSL.
- A porta 3690 possui um serviço não especificado (o nmap retorna sua *impressão digital* - aqui omitida por questões de clareza - juntamente com instruções "para submetê-lo para incorporação no banco de dados de impressões digitais do nmap, desde que você saiba qual serviço ele representa).
- Outro serviço não especificado na porta 8000; isso pode possivelmente ser HTTP, já que não é incomum encontrar servidores HTTP nesta porta. Vamos examinar essa questão:

```bash
$ telnet 192.168.10.100 8000
Trying 192.168.1.100...
Connected to 192.168.1.100.
Escape character is '^]'.
GET / HTTP/1.0

HTTP/1.0 200 OK
pragma: no-cache
Content-Type: text/html
Server: MX4J-HTTPD/1.0
expires: now
Cache-Control: no-cache

<html>
...
```

Isso confirma que de fato é um servidor HTTP. Alternativamente, os testadores poderiam ter visitado a URL com um navegador da web; ou usado os comandos Perl GET ou HEAD, que imitam interações HTTP como a mostrada acima (no entanto, as solicitações HEAD podem não ser atendidas por todos os servidores).

- Apache Tomcat sendo executado na porta 8080.

A mesma tarefa pode ser realizada por scanners de vulnerabilidades, mas primeiro verifique se o scanner escolhido é capaz de identificar serviços HTTP[S] em portas não-padrão. Por exemplo, o Nessus é capaz de identificá-los em portas arbitrárias (desde que seja instruído a escanear todas as portas) e fornecerá, em relação ao nmap, um número de testes em vulnerabilidades conhecidas de servidores web, bem como na configuração SSL de serviços HTTPS. Como mencionado anteriormente, o Nessus também é capaz de identificar aplicativos populares ou interfaces da web que poderiam passar despercebidos (por exemplo, uma interface administrativa do Tomcat).

### Abordagens para Lidar com a Questão 3 - Virtual Hosts

Existem várias técnicas que podem ser usadas para identificar nomes DNS associados a um determinado endereço IP `x.y.z.t`.

#### Transferências de Zona DNS

Esta técnica tem uso limitado atualmente, dado o fato de que as transferências de zona geralmente não são honradas pelos servidores DNS. No entanto, pode valer a pena tentar. Em primeiro lugar, os testadores devem determinar os servidores de nomes que atendem `x.y.z.t`. Se um nome simbólico é conhecido para `x.y.z.t` (vamos chamá-lo de `www.example.com`), seus servidores de nomes podem ser determinados por meio de ferramentas como `nslookup`, `host` ou `dig`, solicitando registros NS DNS.

Se nenhum nome simbólico for conhecido para `x.y.z.t`, mas a definição do alvo contiver pelo menos um nome simbólico, os testadores podem tentar aplicar o mesmo processo e consultar o servidor de nomes desse nome (esperando que `x.y.z.t` também seja atendido por esse servidor de nomes). Por exemplo, se o alvo consiste no endereço IP `x.y.z.t` e no nome `mail.example.com`, determine os servidores de nomes para o domínio `example.com`.

O exemplo a seguir mostra como identificar os servidores de nomes para `www.owasp.org` usando o comando `host`:

```bash
$ host -t ns www.owasp.org
www.owasp.org is an alias for owasp.org.
owasp.org name server ns1.secure.net.
owasp.org name server ns2.secure.net.
```

Agora uma transferência de zona pode ser solicitada aos servidores de nomes para o domínio `example.com`. Se os testadores tiverem sorte, eles receberão de volta uma lista de entradas DNS para esse domínio. Isso incluirá o óbvio `www.example.com` e o não tão óbvio `helpdesk.example.com` e `webmail.example.com` (e possivelmente outros). Verifique todos os nomes retornados pela transferência de zona e considere todos aqueles que estão relacionados com o alvo em avaliação.

Tentando solicitar uma transferência de zona para `owasp.org` a partir de um dos seus servidores de nomes:

```bash
$ host -l www.owasp.org ns1.secure.net
Using domain server:
Name: ns1.secure.net
Address: 192.220.124.10#53
Aliases:

Host www.owasp.org not found: 5(REFUSED)
; Transfer failed.
```

#### Consultas Inversas DNS

Este processo é semelhante ao anterior, mas baseia-se em registros DNS inversos (PTR). Em vez de solicitar uma transferência de zona, tente definir o tipo de registro como PTR e emitir uma consulta para o endereço IP fornecido. Se os testadores tiverem sorte, poderão obter uma entrada de nome DNS. Essa técnica depende da existência de mapas de nomes simbólicos para IP, o que não é garantido.

#### Pesquisas DNS Baseadas na Web

Esse tipo de busca é semelhante à transferência de zona DNS, mas baseia-se em serviços baseados na web que permitem pesquisas baseadas em nomes em DNS. Um desses serviços é o serviço [Netcraft Search DNS](https://searchdns.netcraft.com/?host). O testador pode pesquisar por uma lista de nomes pertencentes ao domínio de escolha, como `example.com`. Em seguida, eles verificarão se os nomes obtidos são relevantes para o alvo em avaliação.

#### Serviços de IP Reverso

Serviços de IP reverso são semelhantes às consultas inversas de DNS, com a diferença de que os testadores consultam um aplicativo baseado na web em vez de um servidor de nomes. Existem vários desses serviços disponíveis. Como eles tendem a retornar resultados parciais (e muitas vezes diferentes), é melhor usar vários serviços para obter uma análise mais abrangente.

[Ferramentas de IP reverso do Domain Tools](https://www.domaintools.com/reverse-ip/) (requer associação gratuita)

[Bing](https://bing.com), sintaxe: `ip:x.x.x.x`

[Webhosting Info](http://whois.webhosting.info/), sintaxe: `http://whois.webhosting.info/x.x.x.x`

[DNSstuff](https://www.dnsstuff.com/) (vários serviços disponíveis)

[Net Square](https://web.archive.org/web/20190515092354/http://www.net-square.com/mspawn.html) (várias consultas em domínios e endereços IP, requer instalação)

O exemplo a seguir mostra o resultado de uma consulta a um dos serviços de IP reverso mencionados acima para `216.48.3.18`, o endereço IP de www.owasp.org. Três nomes simbólicos adicionais não óbvios, mapeando para o mesmo endereço, foram revelados.

![Informações de Whois OWASP](images/Owasp-Info.jpg)\
*Figura 4.1.4-1: Informações de Whois OWASP*

#### Googling

Após a coleta de informações das técnicas anteriores, os testadores podem contar com mecanismos de busca para refinar e incrementar possivelmente sua análise. Isso pode revelar evidências de nomes simbólicos adicionais pertencentes ao alvo, ou de aplicativos acessíveis por meio de URLs não óbvias.

Por exemplo, considerando o exemplo anterior referente a `www.owasp.org`, o testador poderia pesquisar no Google e em outros mecanismos de busca informações (ou seja, nomes DNS) relacionadas aos domínios recém-descobertos de `webgoat.org`, `webscarab.com` e `webscarab.net`.

As técnicas de pesquisa no Google são explicadas em [Teste: Spiders, Robots e Crawlers](01-Conduct_Search_Engine_Discovery_Reconnaissance_for_Information_Leakage.md).

## Ferramentas

- Ferramentas de consulta DNS como `nslookup`, `dig` e similares.
- Mecanismos de pesquisa (Google, Bing e outros mecanismos de pesquisa principais).
- Serviço de pesquisa baseado na web relacionado ao DNS: consulte o texto.
- [Nmap](https://nmap.org/)
- [Scanner de Vulnerabilidades Nessus](https://www.tenable.com/products/nessus)
- [Nikto](https://www.cirt.net/nikto2)"