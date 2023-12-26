# Mapeamento da Arquitetura da Aplicação
Aqui está a tradução da seção "Map Application Architecture" do arquivo Markdown fornecido:

```markdown
# Mapeamento da Arquitetura da Aplicação

|ID          |
|------------|
|WSTG-INFO-10|

## Resumo

A complexidade da infraestrutura web interconectada e heterogênea pode incluir centenas de aplicações web e torna a gestão e revisão da configuração um passo fundamental no teste e implantação de cada aplicação individual. Na verdade, basta uma única vulnerabilidade para comprometer a segurança de toda a infraestrutura, e até mesmo problemas pequenos e aparentemente insignificantes podem evoluir para riscos graves para outra aplicação na mesma infraestrutura.

Para abordar esses problemas, é de extrema importância realizar uma revisão aprofundada da configuração e das questões de segurança conhecidas. Antes de realizar uma revisão detalhada, é necessário mapear a arquitetura da rede e da aplicação. Os diferentes elementos que compõem a infraestrutura precisam ser determinados para entender como interagem com uma aplicação web e como afetam a segurança.

## Objetivos do Teste

- Gerar um mapa da aplicação em questão com base na pesquisa realizada.

## Como Testar

### Mapear a Arquitetura da Aplicação

A arquitetura da aplicação precisa ser mapeada por meio de algum teste para determinar quais componentes diferentes são usados para construir a aplicação web. Em configurações pequenas, como uma simples aplicação PHP, pode ser usado um único servidor que atende à aplicação PHP e, talvez, também ao mecanismo de autenticação.

Em configurações mais complexas, como um sistema bancário online, vários servidores podem estar envolvidos. Isso pode incluir um proxy reverso, um servidor web de front-end, um servidor de aplicativos e um servidor de banco de dados ou servidor LDAP. Cada um desses servidores será usado para diferentes fins e até mesmo pode ser segregado em redes diferentes com firewalls entre eles. Isso cria diferentes zonas de rede, de modo que o acesso ao servidor web não necessariamente concederá a um usuário remoto acesso ao mecanismo de autenticação em si, e para que as violações dos diferentes elementos da arquitetura possam ser isoladas para que não comprometam toda a arquitetura.

Obter conhecimento da arquitetura da aplicação pode ser fácil se essa informação for fornecida à equipe de teste pelos desenvolvedores da aplicação em forma de documento ou por meio de entrevistas, mas também pode se mostrar muito difícil se estiver realizando um teste de penetração às cegas.

Nesse último caso, um testador começará com a suposição de que existe uma configuração simples (um único servidor). Em seguida, ele obterá informações de outros testes e derivará os diferentes elementos, questionará essa suposição e estenderá o mapa da arquitetura. O testador começará fazendo perguntas simples, como: "Há um firewall protegendo o servidor web?". Essa pergunta será respondida com base nos resultados de verificações de rede direcionadas ao servidor web e na análise de se as portas de rede do servidor web estão sendo filtradas na borda da rede (nenhuma resposta ou ICMP inatingível é recebido) ou se o servidor está conectado diretamente à Internet (ou seja, retorna pacotes RST para todas as portas não ouvidas). Essa análise pode ser aprimorada para determinar o tipo de firewall usado com base em testes de pacotes de rede. É um firewall com estado ou é um filtro de lista de acesso em um roteador? Como ele está configurado? Pode ser contornado? É um firewall de aplicação web completo?

Detectar um proxy reverso na frente do servidor web pode ser feito por meio da análise do banner do servidor web, que pode divulgar diretamente a existência de um proxy reverso. Também pode ser determinado obtendo as respostas dadas pelo servidor web às solicitações e comparando-as com as respostas esperadas. Por exemplo, alguns proxies reversos atuam como Sistemas de Prevenção de Intrusões (IPS) bloqueando ataques conhecidos direcionados ao servidor web. Se o servidor web for conhecido por responder com uma mensagem 404 a uma solicitação que visa uma página indisponível e retornar uma mensagem de erro diferente para alguns ataques web comuns, como os feitos por scanners de vulnerabilidade, isso pode ser uma indicação de um proxy reverso (ou um firewall de nível de aplicação) que está filtrando as solicitações e retornando uma página de erro diferente da esperada. Outro exemplo: se o servidor web retornar um conjunto de métodos HTTP disponíveis (incluindo TRACE), mas os métodos esperados retornarem erros, provavelmente há algo entre eles bloqueando-os.

Em alguns casos, até mesmo o sistema de proteção pode se denunciar. Aqui está um exemplo de identificação própria do mod_security:

![Exemplo de Página de Erro do mod_security](images/10_mod_security.jpg)\
*Figura 4.1.10-1: Exemplo de Página de Erro do mod_security*

Os proxies reversos também podem ser introduzidos como caches de proxy para acelerar o desempenho dos servidores de aplicativos nos bastidores. A detecção desses proxies pode ser feita com base no cabeçalho do servidor. Eles também podem ser detectados cronometrando solicitações que devem ser armazenadas em cache pelo servidor e comparando o tempo levado para atender à primeira solicitação com as solicitações subsequentes.

Outro elemento que pode ser detectado são os balanceadores de carga de rede. Normalmente, esses sistemas equilibram uma porta TCP/IP específica para vários servidores com base em diferentes algoritmos (round-robin, carga do servidor web, número de solicitações, etc.). Assim, a detecção desse elemento de arquitetura precisa ser feita examinando várias solicitações e comparando os resultados para determinar se as solicitações estão indo para os mesmos servidores web ou diferentes. Por exemplo, com base no cabeçalho Data se os relógios dos servidores não estiverem sincronizados. Em alguns casos, o processo de balanceamento de carga de rede pode injetar novas informações nos cabeçalhos que o tornarão nitidamente diferente, como o cookie com prefixo BIGipServer introduzido pelos balanceadores de carga F5 BIG-IP.

Os servidores web de aplicativos geralmente são fáceis de detectar. A solicitação de vários recursos é tratada pelo servidor de aplicativos em si (não

 pelo servidor web) e o cabeçalho de resposta variará significativamente (incluindo valores diferentes ou adicionais no cabeçalho de resposta). Outra forma de detectar esses servidores é verificar se o servidor web tenta definir cookies que indicam que um servidor de aplicativos web está sendo usado (como o JSESSIONID fornecido por vários servidores J2EE), ou para reescrever URLs automaticamente para rastreamento de sessão.

No entanto, os back-ends de autenticação (como diretórios LDAP, bancos de dados relacionais ou servidores RADIUS) não são tão fáceis de detectar de um ponto de vista externo de maneira imediata, pois serão ocultados pela própria aplicação.

O uso de um banco de dados de back-end pode ser determinado simplesmente navegando em uma aplicação. Se houver conteúdo altamente dinâmico gerado "na hora", provavelmente está sendo extraído de algum tipo de banco de dados pela própria aplicação. Às vezes, a forma como as informações são solicitadas pode dar uma ideia da existência de um banco de dados nos bastidores. Por exemplo, uma aplicação de compras online que usa identificadores numéricos (`id`) ao navegar pelos diferentes artigos na loja. No entanto, ao fazer um teste de aplicação às cegas, o conhecimento do banco de dados subjacente geralmente só está disponível quando uma vulnerabilidade surge na aplicação, como um tratamento inadequado de exceção ou susceptibilidade a injeções SQL.
```

Esta é a tradução da seção "Map Application Architecture" do arquivo Markdown fornecido em português do Brasil.
|ID          |
|------------|
|WSTG-INFO-10|

## Resumo

A complexidade da infraestrutura web interconectada e heterogênea pode incluir centenas de aplicações web e torna a gestão e revisão da configuração um passo fundamental no teste e implantação de cada aplicação individual. Na verdade, basta uma única vulnerabilidade para comprometer a segurança de toda a infraestrutura, e até mesmo problemas pequenos e aparentemente insignificantes podem evoluir para riscos graves para outra aplicação na mesma infraestrutura.

Para abordar esses problemas, é de extrema importância realizar uma revisão aprofundada da configuração e das questões de segurança conhecidas. Antes de realizar uma revisão detalhada, é necessário mapear a arquitetura da rede e da aplicação. Os diferentes elementos que compõem a infraestrutura precisam ser determinados para entender como interagem com uma aplicação web e como afetam a segurança.

## Objetivos do Teste

- Gerar um mapa da aplicação em questão com base na pesquisa realizada.

## Como Testar

### Mapear a Arquitetura da Aplicação

A arquitetura da aplicação precisa ser mapeada por meio de algum teste para determinar quais componentes diferentes são usados para construir a aplicação web. Em configurações pequenas, como uma simples aplicação PHP, pode ser usado um único servidor que atende à aplicação PHP e, talvez, também ao mecanismo de autenticação.

Em configurações mais complexas, como um sistema bancário online, vários servidores podem estar envolvidos. Isso pode incluir um proxy reverso, um servidor web de front-end, um servidor de aplicativos e um servidor de banco de dados ou servidor LDAP. Cada um desses servidores será usado para diferentes fins e até mesmo pode ser segregado em redes diferentes com firewalls entre eles. Isso cria diferentes zonas de rede, de modo que o acesso ao servidor web não necessariamente concederá a um usuário remoto acesso ao mecanismo de autenticação em si, e para que as violações dos diferentes elementos da arquitetura possam ser isoladas para que não comprometam toda a arquitetura.

Obter conhecimento da arquitetura da aplicação pode ser fácil se essa informação for fornecida à equipe de teste pelos desenvolvedores da aplicação em forma de documento ou por meio de entrevistas, mas também pode se mostrar muito difícil se estiver realizando um teste de penetração às cegas.

Nesse último caso, um testador começará com a suposição de que existe uma configuração simples (um único servidor). Em seguida, ele obterá informações de outros testes e derivará os diferentes elementos, questionará essa suposição e estenderá o mapa da arquitetura. O testador começará fazendo perguntas simples, como: "Há um firewall protegendo o servidor web?". Essa pergunta será respondida com base nos resultados de verificações de rede direcionadas ao servidor web e na análise de se as portas de rede do servidor web estão sendo filtradas na borda da rede (nenhuma resposta ou ICMP inatingível é recebido) ou se o servidor está conectado diretamente à Internet (ou seja, retorna pacotes RST para todas as portas não ouvidas). Essa análise pode ser aprimorada para determinar o tipo de firewall usado com base em testes de pacotes de rede. É um firewall com estado ou é um filtro de lista de acesso em um roteador? Como ele está configurado? Pode ser contornado? É um firewall de aplicação web completo?

Detectar um proxy reverso na frente do servidor web pode ser feito por meio da análise do banner do servidor web, que pode divulgar diretamente a existência de um proxy reverso. Também pode ser determinado obtendo as respostas dadas pelo servidor web às solicitações e comparando-as com as respostas esperadas. Por exemplo, alguns proxies reversos atuam como Sistemas de Prevenção de Intrusões (IPS) bloqueando ataques conhecidos direcionados ao servidor web. Se o servidor web for conhecido por responder com uma mensagem 404 a uma solicitação que visa uma página indisponível e retornar uma mensagem de erro diferente para alguns ataques web comuns, como os feitos por scanners de vulnerabilidade, isso pode ser uma indicação de um proxy reverso (ou um firewall de nível de aplicação) que está filtrando as solicitações e retornando uma página de erro diferente da esperada. Outro exemplo: se o servidor web retornar um conjunto de métodos HTTP disponíveis (incluindo TRACE), mas os métodos esperados retornarem erros, provavelmente há algo entre eles bloqueando-os.

Em alguns casos, até mesmo o sistema de proteção pode se denunciar. Aqui está um exemplo de identificação própria do mod_security:

![Exemplo de Página de Erro do mod_security](images/10_mod_security.jpg)\
*Figura 4.1.10-1: Exemplo de Página de Erro do mod_security*

Os proxies reversos também podem ser introduzidos como caches de proxy para acelerar o desempenho dos servidores de aplicativos nos bastidores. A detecção desses proxies pode ser feita com base no cabeçalho do servidor. Eles também podem ser detectados cronometrando solicitações que devem ser armazenadas em cache pelo servidor e comparando o tempo levado para atender à primeira solicitação com as solicitações subsequentes.

Outro elemento que pode ser detectado são os balanceadores de carga de rede. Normalmente, esses sistemas equilibram uma porta TCP/IP específica para vários servidores com base em diferentes algoritmos (round-robin, carga do servidor web, número de solicitações, etc.). Assim, a detecção desse elemento de arquitetura precisa ser feita examinando várias solicitações e comparando os resultados para determinar se as solicitações estão indo para os mesmos servidores web ou diferentes. Por exemplo, com base no cabeçalho Data se os relógios dos servidores não estiverem sincronizados. Em alguns casos, o processo de balanceamento de carga de rede pode injetar novas informações nos cabeçalhos que o tornarão nitidamente diferente, como o cookie com prefixo BIGipServer introduzido pelos balanceadores de carga F5 BIG-IP.

Os servidores web de aplicativos geralmente são fáceis de detectar. A solicitação de vários recursos é tratada pelo servidor de aplicativos em si (não

 pelo servidor web) e o cabeçalho de resposta variará significativamente (incluindo valores diferentes ou adicionais no cabeçalho de resposta). Outra forma de detectar esses servidores é verificar se o servidor web tenta definir cookies que indicam que um servidor de aplicativos web está sendo usado (como o JSESSIONID fornecido por vários servidores J2EE), ou para reescrever URLs automaticamente para rastreamento de sessão.

No entanto, os back-ends de autenticação (como diretórios LDAP, bancos de dados relacionais ou servidores RADIUS) não são tão fáceis de detectar de um ponto de vista externo de maneira imediata, pois serão ocultados pela própria aplicação.

O uso de um banco de dados de back-end pode ser determinado simplesmente navegando em uma aplicação. Se houver conteúdo altamente dinâmico gerado "na hora", provavelmente está sendo extraído de algum tipo de banco de dados pela própria aplicação. Às vezes, a forma como as informações são solicitadas pode dar uma ideia da existência de um banco de dados nos bastidores. Por exemplo, uma aplicação de compras online que usa identificadores numéricos (`id`) ao navegar pelos diferentes artigos na loja. No entanto, ao fazer um teste de aplicação às cegas, o conhecimento do banco de dados subjacente geralmente só está disponível quando uma vulnerabilidade surge na aplicação, como um tratamento inadequado de exceção ou susceptibilidade a injeções SQL.
```
