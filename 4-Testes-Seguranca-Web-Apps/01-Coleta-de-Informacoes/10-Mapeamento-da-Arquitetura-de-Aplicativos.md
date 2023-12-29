# Mapeamento de Arquitetura de Aplicativos

|ID          |
|------------|
|WSTG-INFO-10|

## Resumo

Para testar efetivamente umo aplicativo e ser capaz de fornecer recomendações significativas sobre como abordar qualquer problema identificado, é importante entender o que está sendo testado. Além disso, pode ser útil determinar se componentes específicos devem ser considerados fora do escopo dos testes.

Os aplicativos web modernos podem variar significativamente em complexidade, desde um simples script em execução em um único servidor até umo aplicativo altamente complexo espalhada por dezenas de sistemas, linguagens e componentes diferentes. Também pode haver componentes de nível de rede adicionais, como firewalls ou sistemas de proteção contra invasões, que podem ter um impacto significativo nos testes.

## Objetivos dos Testes

- Compreender a arquitetura do aplicativo e as tecnologias em uso.

## Como Testar

Ao testar a partir de uma perspectiva de caixa-preta, é importante tentar criar uma imagem clara de como o aplicativo funciona e quais tecnologias e componentes estão em uso. Em alguns casos, é possível testar componentes específicos, como um firewall de aplicativo web, enquanto outros componentes podem ser identificados pela inspeção do comportamento do aplicativo.

As seções a seguir fornecem uma visão geral de alto nível dos componentes arquitetônicos comuns, juntamente com detalhes sobre como eles podem ser identificados.

### Componentes do aplicativo

#### Servidor Web

Aplicativos simples podem ser executados em um único servidor, que pode ser identificado usando as etapas discutidas na seção [Fingerprint Web Server](02-Fingerprint_Web_Server.md) do guia.

#### Plataforma como Serviço (PaaS)

Em um modelo de Plataforma como Serviço (PaaS), o servidor web e a infraestrutura subjacente são gerenciados pelo provedor de serviços, e o cliente é responsável apenas pelo aplicativo que está implantado neles. Do ponto de vista dos testes, existem duas principais diferenças:

- O proprietário do aplicativo não tem acesso à infraestrutura subjacente, o que significa que eles não poderão remediar diretamente quaisquer problemas.
- Os testes de infraestrutura provavelmente estarão fora do escopo de qualquer engajamento.

Em alguns casos, é possível identificar o uso do PaaS, pois o aplicativo pode usar um nome de domínio específico (por exemplo, os aplicativos implantados nos Serviços do Azure terão um domínio `*.azurewebsites.net` - embora também possam usar domínios personalizados). Em outros casos, é difícil determinar se o PaaS está em uso.

#### Sem Servidor (Serverless)

Em um modelo sem servidor (Serverless), os desenvolvedores fornecem código que é executado diretamente em uma plataforma de hospedagem como funções individuais, em vez de executar um aplicativo web maior, tradicionalmente implantado em um webroot. Isso o torna adequado para arquiteturas baseadas em microservices. Assim como em um ambiente PaaS, os testes de infraestrutura provavelmente estarão fora do escopo.

Em alguns casos, o uso de código sem servidor pode ser indicado pela presença de cabeçalhos HTTP específicos. Por exemplo, as funções da AWS Lambda normalmente retornam os seguintes cabeçalhos:

```http
X-Amz-Invocation-Type
X-Amz-Log-Type
X-Amz-Client-Context
```

As Funções do Azure são menos óbvias. Elas normalmente retornam o cabeçalho `Server: Kestrel` - mas isso por si só não é suficiente para determinar que é uma função do Azure App, pois pode ser algum outro código em execução no Kernel.

#### Microservices

Em uma arquitetura baseada em microservices, a API do aplicativo é composta por vários serviços discretos, em vez de ser executada como umo aplicativo monolítica. Os próprios serviços geralmente rodam em contêineres (geralmente com Kubernetes) e podem usar uma variedade de sistemas operacionais e linguagens diferentes. Embora normalmente estejam atrás de um único gateway e domínio da API, o uso de várias linguagens (geralmente indicadas em mensagens de erro detalhadas) pode sugerir que os microservices estão em uso.

#### Armazenamento Estático

Muitos aplicativos armazenam conteúdo estático em plataformas de armazenamento dedicadas, em vez de hospedá-lo diretamente no servidor web principal. As duas plataformas mais comuns são os Buckets da Amazon S3 e as Contas de Armazenamento da Azure, e podem ser facilmente identificadas pelos nomes de domínio:

- `BUCKET.s3.amazonaws.com` ou `s3.REGION.amazonaws.com/BUCKET` para Buckets da Amazon S3
- `ACCOUNT.blob.core.windows.net` para Contas de Armazenamento da Azure

Essas contas de armazenamento frequentemente expõem arquivos sensíveis, conforme discutido na seção [Testing Cloud Storage Guide](../02-Configuration_and_Deployment_Management_Testing/11-Test_Cloud_Storage.md).

#### Banco de Dados

A maioria dos aplicativos web não triviais usa algum tipo de banco de dados para armazenar conteúdo dinâmico. Em alguns casos, é possível determinar o banco de dados. Isso geralmente pode ser feito por:

- Fazer uma varredura de portas no servidor e procurar por portas abertas associadas a bancos de dados específicos
- Desencadear mensagens de erro relacionadas a SQL (ou NoSQL) (ou encontrar erros existentes em um [motor de busca](../01-Information_Gathering/01-Conduct_Search_Engine_Discovery_Reconnaissance_for_Information_Leakage.md)

Quando não é possível determinar conclusivamente o banco de dados, o testador frequentemente pode fazer uma suposição educada com base em outros aspectos do aplicativo:

- O Windows, IIS e ASP.NET geralmente usam o Microsoft SQL Server
- Sistemas embarcados geralmente usam o SQLite
- O PHP geralmente usa o MySQL ou o PostgreSQL
- O APEX geralmente usa o Oracle

Essas não são regras rígidas, mas podem certamente dar um ponto de partida razoável se não houver informações melhores disponíveis.

#### Autenticação

A maioria dos aplicativos tem autenticação de usuário. Existem várias fontes de autenticação que podem ser usadas,

 como:

- Configuração do servidor web (incluindo arquivos `.htaccess`) ou senha codificada em scripts
    - Normalmente aparece como autenticação básica HTTP, indicada por um pop-up no navegador e um cabeçalho HTTP `WWW-Authenticate: Basic`
- Contas de usuário locais em um banco de dados
    - Normalmente integrado em um formulário ou ponto de extremidade de API no aplicativo
- Uma fonte de autenticação central existente, como o Active Directory ou um servidor LDAP
    - Pode usar a autenticação NTLM, indicada por um cabeçalho HTTP `WWW-Authenticate: NTLM`
    - Pode ser integrado no aplicativo web em um formulário
    - Pode exigir que o nome de usuário seja inserido no formato "DOMÍNIO\usuário" ou pode fornecer um menu suspenso de domínios disponíveis
- Logon único (SSO) com um provedor interno ou externo
    - Normalmente usa OAuth, OpenID Connect ou SAML

os aplicativos podem oferecer várias opções para o usuário autenticar (como registrar uma conta local ou usar sua conta do Facebook existente) e podem usar mecanismos diferentes para usuários normais e administradores.

#### Serviços e APIs de Terceiros

Quase todas os aplicativos web incluem recursos de terceiros que são carregados ou com os quais o cliente interage. Isso pode incluir:

- [Conteúdo ativo](https://developer.mozilla.org/pt-BR/docs/Web/Security/Mixed_content#mixed_active_content) (como scripts, folhas de estilo, fontes e iframes)
- [Conteúdo passivo](https://developer.mozilla.org/pt-BR/docs/Web/Security/Mixed_content#mixed_passivedisplay_content) (como imagens e vídeos)
- APIs externas
- Botões de mídia social
- Redes de publicidade
- Plataformas de pagamento

Esses recursos são solicitados diretamente pelo navegador do usuário, facilitando sua identificação usando as ferramentas do desenvolvedor ou um proxy de interceptação. Embora seja importante identificá-los (pois podem afetar a segurança do aplicativo), lembre-se de que *eles geralmente estão fora do escopo dos testes*, pois pertencem a terceiros.

Aqui está o texto traduzido para um arquivo Markdown (md) em português do Brasil (pt-BR):

```markdown
### Componentes de Rede

#### Proxy Reverso

Um proxy reverso fica na frente de um ou mais servidores backend e redireciona solicitações para o destino apropriado. Eles podem ser usados para implementar várias funcionalidades, como:

- Atuar como um [balanceador de carga](#balanceador-de-carga) ou [firewall de aplicativo web](#firewall-de-aplicação-web-waf)
- Permitir que múltiplos aplicativos sejam hospedadas em um único endereço IP ou domínio (em subpastas)
- Implementar filtragem de IP ou outras restrições
- Armazenar em cache conteúdo do backend para melhorar o desempenho

Não é sempre possível detectar um proxy reverso (especialmente se houver apenas uma único aplicativo por trás dele), mas você às vezes pode identificá-lo por:

- Uma incompatibilidade entre o servidor frontend e o aplicativo backend (como um cabeçalho `Server: nginx` com umo aplicativo ASP.NET)
    - Isso às vezes pode levar a [vulnerabilidades de smuggling de requisições](https://portswigger.net/web-security/request-smuggling)
- Cabeçalhos duplicados (especialmente o cabeçalho `Server`)
- Múltiplos aplicativos hospedadas no mesmo endereço IP ou domínio (especialmente se elas usarem linguagens diferentes)

#### Balanceador de Carga

Um balanceador de carga fica na frente de vários servidores backend e distribui solicitações entre eles para fornecer maior redundância e capacidade de processamento para o aplicativo.

Balanceadores de carga podem ser difíceis de detectar, mas às vezes podem ser identificados fazendo várias solicitações e examinando as respostas em busca de diferenças, como:

- Horários do sistema inconsistentes
- Diferentes endereços IP internos ou nomes de host em mensagens de erro detalhadas
- Diferentes endereços retornados de [Server-Side Request Forgery (SSRF)](../07-Input_Validation_Testing/19-Testing_for_Server-Side_Request_Forgery.md)

Eles também podem ser indicados pela presença de cookies específicos (por exemplo, os balanceadores de carga F5 BIG-IP criam um cookie chamado `BIGipServer`).

#### Rede de Entrega de Conteúdo (CDN)

Uma Rede de Entrega de Conteúdo (CDN) é um conjunto distribuído geograficamente de servidores proxy de cache projetados para melhorar o desempenho do site.

Normalmente, ela é configurada apontando o domínio de frente pública para os servidores da CDN e, em seguida, configurando a CDN para se conectar aos servidores backend corretos (às vezes conhecidos como "origem").

A maneira mais fácil de detectar uma CDN é realizar uma pesquisa WHOIS para os endereços IP que o domínio resolve. Se eles pertencerem a uma empresa de CDN (como Akamai, Cloudflare ou Fastly - consulte a [Wikipedia](https://en.wikipedia.org/wiki/Content_delivery_network#Notable_content_delivery_service_providers) para uma lista mais completa), é provável que uma CDN esteja em uso.

Ao testar um site atrás de uma CDN, você deve considerar os seguintes pontos:

- Os endereços IP e servidores pertencem ao provedor de CDN e provavelmente estão fora do escopo dos testes de infraestrutura
- Muitas CDNs também incluem recursos como detecção de bots, limitação de taxa e firewalls de aplicação web
- CDNs geralmente armazenam em cache o conteúdo. Portanto, as alterações feitas no backend podem não aparecer imediatamente no site.

Se o site estiver atrás de uma CDN, pode ser útil identificar os servidores backend. Se o controle de acesso adequado não for aplicado, o testador poderá contornar a CDN (e quaisquer proteções que ela ofereça) acessando diretamente os servidores backend. Existem vários métodos diferentes que podem permitir identificar o sistema backend:

- Emails enviados pelo aplicativo podem vir diretamente do servidor backend, o que pode revelar seu endereço IP
- Pesquisas DNS, transferências de zona ou listas de transparência de certificados para um domínio podem revelá-lo em um subdomínio
- Escanear as faixas de IP conhecidas usadas pela empresa pode ajudar a identificar o servidor backend
- Explorar [Server-Side Request Forgery (SSRF)](../07-Input_Validation_Testing/19-Testing_for_Server-Side_Request_Forgery.md) pode revelar o endereço IP
- Mensagens de erro detalhadas do aplicativo podem expor endereços IP ou nomes de host

### Componentes de Segurança

#### Firewall de Rede

A maioria dos servidores web será protegida por um firewall de filtragem de pacotes ou firewall de inspeção stateful, que bloqueia qualquer tráfego de rede que não seja necessário. Para detectar isso, realize uma varredura de portas no servidor e examine os resultados.

Se a maioria das portas aparecer como "fechadas" (ou seja, elas retornam um pacote `RST` em resposta ao pacote `SYN` inicial), isso sugere que o servidor pode não estar protegido por um firewall. Se as portas aparecerem como "filtradas" (ou seja, nenhuma resposta for recebida ao enviar um pacote `SYN` para uma porta não utilizada), então é muito provável que um firewall esteja em funcionamento.

Além disso, se serviços inadequados estiverem expostos ao mundo (como SMTP, IMAP, MySQL, etc), isso sugere que não há firewall em funcionamento ou que o firewall está mal configurado.

#### Sistema de Detecção e Prevenção de Intrusões de Rede

Um Sistema de Detecção de Intrusões de Rede (IDS) é projetado para detectar atividades suspeitas ou maliciosas no nível da rede, como varreduras de portas ou vulnerabilidades, e gerar alertas. Um Sistema de Prevenção de Intrusões (IPS) funciona de forma semelhante, mas também toma medidas para impedir a atividade, geralmente bloqueando o endereço IP de origem.

Um IPS geralmente pode ser detectado executando ferramentas de varredura automatizadas (como um scanner de portas) contra o alvo e verificando se o endereço IP de origem está bloqueado. No entanto, muitas ferramentas de nível de aplicação podem não ser detectadas por um IPS (es

pecialmente se ele não descriptografar TLS).

#### Firewall de Aplicação Web (WAF)

Um Firewall de Aplicação Web (WAF) inspeciona o conteúdo das solicitações HTTP e bloqueia aquelas que parecem suspeitas ou maliciosas. Eles também podem ser usados para aplicar dinamicamente outros controles, como CAPTCHA ou limitação de taxa. Normalmente, eles utilizam um conjunto de assinaturas ruins conhecidas e expressões regulares, como o [OWASP Core Rule Set](https://owasp.org/www-project-modsecurity-core-rule-set/), para identificar tráfego malicioso. WAFs podem ser eficazes na proteção contra certos tipos de ataques, como injeção SQL ou cross-site scripting, mas são menos eficazes contra outros tipos, como problemas de controle de acesso ou lógica de negócios.

Um WAF pode ser implantado em várias localizações, incluindo:

- No próprio servidor web
- Em uma máquina virtual ou dispositivo de hardware separado
- Na nuvem, na frente do servidor backend

Como um WAF bloqueia solicitações maliciosas, ele pode ser detectado adicionando strings de ataques comuns aos parâmetros e observando se eles são bloqueados. Por exemplo, tente adicionar um parâmetro chamado `foo` com um valor como `' UNION SELECT 1` ou `><script>alert(1)</script>`. Se essas solicitações forem bloqueadas, é provável que haja um WAF em funcionamento. Além disso, o conteúdo das páginas de bloqueio pode fornecer informações sobre a tecnologia específica em uso. Por fim, alguns WAFs podem adicionar cookies ou cabeçalhos HTTP às respostas que podem revelar sua presença.

Se um WAF baseado na nuvem estiver em uso, pode ser possível contorná-lo acessando diretamente o servidor backend, usando os mesmos métodos discutidos na seção [Rede de Entrega de Conteúdo](#rede-de-entrega-de-conteúdo-cdn).
```