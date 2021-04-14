# Teste de Configuração da Infraestrutura da Rede

|ID          |
|------------|
|WSTG-CONF-01|

## Resumo 

A complexidade intrínseca da infraestrutura conectada e heterogênea do servidor de rede internet conectada e heterogênea, as quais podem incluir centenas de aplicativos web, torna o gerenciamento e revisão da configuração um passo fundamental no teste e instalação de cada aplicativo. Basta apenas uma única vulnerabilidade para comprometer a segurança de toda a infraestrutura, e mesmo um problema aparentemente irrelevante pode converter-se em múltiplos riscos para outros aplicativos no mesmo servidor. A fim de tratar esses problemas, é de extrema importância realizar uma revisão profunda das configurações e de problemas de segurança conhecidos, após o mapeamento da arquitetura por completo.  

O gerenciamento adequado da configuração do servidor de infraestrutura de rede é muito importante no sentido de preservar a segurança do aplicativo em si. Se elementos tais quais o software de servidor de rede, os servidores de bancos de dados de back-end, ou servidores de autenticação não são propriamente revisados e seguros, eles poderão introduzir riscos indesejáveis ou novas vulnerabilidades que podem comprometer o aplicativo em si. 

Por exemplo, as vulnerabilidades do servidor de rede que permitam que um ataque remoto exponha o código-fonte do aplicativo (a vulnerabilidade que pode ter surgido inúmeras vezes nos servidores de rede ou nos servidores do aplicativo) poderia comprometer o aplicativo quando usuários anônimos poderiam usar a informação exposta pelo código fonte para promover ataques contra o aplicativo e seus usuários.  

Os passos a seguir podem ser tomados de gerenciamento de configuração da infraestrutura:
- Os diferentes elementos que compõem a infraestrutura web precisa ser determinados para que se entenda como interagem com o aplicativo web e como afetam a segurança.
- Todos os elementos da infraestrutura precisam ser revisados parece que tem a certeza porque eles não possuam nenhuma vulnerabilidade conhecida.
- Uma revisão não precisa ser feita nas ferramentas administrativas usadas para manter os diferentes elementos.  
- Os sistemas de autenticação precisam ser revisados para garantir que servem às necessidades do aplicativo e que não possam ser manipulados por usuários externos para que obtenham acesso. 
- A lista de definição de portas que são requeridas para o aplicativo deveria ser mantida e preservada pela gerência de mudanças. 

Após ter mapeado diferentes elementos que compõem a infraestrutura (veja [Mapa da Arquitetura de Rede e do Aplicativo](../01-Information_Gathering/10-Map_Application_Architecture.md))  é possível revisar a configuração de cada elemento encontrado e testar as vulnerabilidades conhecidas. 

## Objetivo dos testes 

- Revisar a configuração dos aplicativos através da rede e validar que não são vulneráveis. 
- Validar se os frameworks e sistemas utilizados são seguros e não suscetíveis a vulnerabilidades conhecidas devido a falta de manutenção do software ou pela utilização de configurações e credenciais padrões.

## Como testar

### Vulnerabilidades conhecidas de Servidores

Vulnerabilidades encontradas em diferentes áreas da arquitetura da aplicação, seja no servidor web ou no banco de dados de backend, pode comprometer severamente o aplicativo em si.
Por exemplo, considerando um servidor vulnerável que permite conexão remota, usuários sem autenticação podem carregar arquivos no servidor web ou mesmo substituir arquivos. Esta vulnerabilidade pode comprometer o aplicativo, já que um usuário poderia substituir o aplicativo em si ou introduzir código que afetaria os servidores de backend, visto que o código do aplicativo poderia rodar como qualquer outro programa. 

Revisar vulnerabilidades de servidores pode ser difícil se os testes precisam ser feitos utilizando um teste de penetração às escuras. Nestes casos, vulnerabilidades precisam ser testadas de um local remoto, tipicamente usando ferramentas de automação. Contudo, testar por uma vulnerabilidade qualquer pode resultar em resultados imprevisíveis no servidor web, e testar por outras (como aquelas diretamente envolvidas em ataques de negação dos serviços (DoS), pode ser impossível devido a indisponibilidade do serviço se mesmo teste tenha sido bem 
sucedido. 

Algumas ferramentas de automação irão indicar vulnerabilidades baseadas no servidor web de versão antiga.
Isso pode levar ambos a resultados de falso positivo ou falso negativo. Se por um lado, a versão do servidor web for removida ou escondida pelo administrador local a ferramenta de escaneamento não irá indicar ao servidor as vulnerabilidades mesmo que elas existam,  por outro lado, se o fornecedor do software não atualizar o servidor web quando as vulnerabilidades são corrigidas, a ferramenta de escaneamento irá acusar vulnerabilidades que não existem. 

O último caso é muito comum já que alguns fornecedores de sistemas operacionais liberam correções (patches) de vulnerabilidades aos softwares incluídos no sistema, mas não fazem atualização (upload) por completo da última versão do software. Isso acontece comumente em distribuições GNULinux tais como Debian, Red Hat or SuSE. Em muitos casos o escaneamento de vulnerabilidades da arquitetura de um aplicativo irá apenas encontrar àquelas associadas com os elementos expostos na arquitetura, tais como o servidor web. Serão incapazes de encontrar vulnerabilidades associadas a elementos não diretamente expostos, tais como a autenticação de backends, o banco de dados de backend, ou proxies reversos em uso. 

Finalmente, nem todos os fornecedores de software tornaram públicas as vulnerabilidades e, portanto, essas ameaças não serão registradas nos bancos de dados de vulnerabilidades. Essa informação é apenas tornada pública aos clientes ou publicada através de correções sem um acompanhamento de conselheiros. Isso reduz a utilidade de ferramentas de escaneamento de vulnerabilidades. Tipicamente a cobertura de vulnerabilidade dessas ferramentas é muito eficaz para produtos comuns, tais como o servidor web Apache, o servidor de informações da Microsoft, ou o Lotus Domino da IBM, mas frágil na detecção de vulnerabilidades em produtos menos conhecidas. 

Isso demonstra porque a revisão de vulnerabilidades é mais bem feita quando é fornecido ao testador as informações internas sobre a utilização do software, incluindo versões usadas e correções aplicadas ao software. Com essas informações, o testador pode acessar as informações do fornecedor por si e analisar quais vulnerabilidades podem estar presentes na arquitetura e como elas podem afetar o aplicativo em si. Quando possível, essas vulnerabilidades podem ser testadas para determinar o real efeito e também para detectar se existem elementos externos (tais como a detecção de intrusões ou sistemas preventivos) que podem reduzir ou negar a possibilidade de invasão com sucesso. Testadores podem inclusive determinar, através da previsão da configuração, que vulnerabilidades não estão presentes, já que elas afetam componentes de um software que não estão em uso. 

Também é valido notar que fornecedores não irão divulgar vulnerabilidades e enviarão correções dentro de novas versões do software. Diferentes fornecedores terão diferentes ciclos de atualização das versões que determina o suporte que oferecerão para versões antigas. Um testador que possui detalhes sobre a versão do software utilizado pela arquitetura, pode analisar o risco associado ao uso de versões antigas, que podem perder suporte em breve ou já não serem suportadas. 
Isso se torna crítico uma vez que vulnerabilidade pode recair sobre uma versão do software que não é tem mais suporte e a equipe de sistemas pode não estar diretamente a par.
Nenhum patch será disponibilidade e experts podem não listar a versão como vulnerável já que ela não é suportada.

Mesmo que tomem conhecimento da existência da vulnerabilidade e da vulnerabilidade do sistema, eles precisam fazer um upgrade completo da nova versão do software, o que pode introduzir uma lentidão significativa na arquitetura do aplicativo ou forçá-lo sua reescrita devido a incompatibilidade com a última versão do software.

### Ferramentas administrativas

Qualquer servidor de infraestrutura requer a existência de ferramentas administrativas e para manter e atualizar as informações usadas pelo aplicativo. 
Essas informações incluem conteúdo estático (páginas web e arquivos gráficos), código fonte de aplicativos, banco de dados de autenticação, etc. Ferramentas administrativas serão diferentes dependendo do site, tecnologia ou site utilizado.
Alguns servidores web, por exemplo, serão gerenciados através do uso de interfaces administrativas as quais são elas mesmo servidores web (tais como o servidor web iPlanet), ou serão gerenciadas por meio de arquivos de configuração de texto puro (no caso do Apache [3]), ou ainda através do uso de ferramentas de interface (GUI) de sistemas operacionais (quando do uso de servidores Microsoft IIS ou ASP.Net)

Na maioria dos casos, a configuração do servidor será feita por meio de diferentes ferramentas de manutenção de arquivos usadas pelo servidor web, que são gerenciadas por meio de servidores FTP, WebDAV, sistemas de arquivos de rede (NFS, CIFS) ou outros mecanismos. Obviamente, o sistema operacional dos elementos que compõem a arquitetura da aplicação também será gerenciado por outras ferramentas. Os aplicativos também podem ter interfaces administrativas incorporadas que são usadas para gerenciar os próprios dados do aplicativo (usuários, conteúdo, etc.). 

Após ter mapeado as interfaces administrativas usadas no gerenciamento de diferentes partes da arquitetura é importante revisar se o invasor ganhou acesso a pode comprometer o danificar arquitetura do aplicativo. Para isso é importante:
- Determinar os mecanismos de controle de acesso dessas interfaces e suas prováveis as associações. Essa informação pode estar disponível online. 
- Modificar o usuário e a senha. 

Algumas empresas optam por não gerenciar todos os aspectos de seus aplicativos de servidores web, mas podem ter outras entidades gerenciando o conteúdo fornecido pelo aplicativo web. Essas entidades externas podem fornecer apenas partes do conteúdo (atualizações de notícias ou promoções) ou podem gerenciar o servidor web por completo (incluindo conteúdo e código). É comum encontrar interfaces administrativas disponíveis na Internet nessas situações, pois usar a Internet é mais barato do que fornecer uma linha dedicada que conectará a empresa externa à infraestrutura do aplicativo por meio de uma interface apenas de gerenciamento. Nessa situação, é muito importante testar se as interfaces administrativas podem estar vulneráveis a ataques. 


## Referências 

- [1] WebSEAL, também conhecidas como Gerenciador de Autenticação Tivoli, é um proxy reverso da IBM, o qual é parte do framework Tivoli.
- [2] Tal como o Bugtraq da Symantec, ISS' X-Force, ou NIST Base Nacional de Vulnerabilidades (NVD).
- [3] Há algumas ferramentas administrativas baseadas em interfaces GUI para Apache (como a NetLoony) mas elas ainda não são amplamente usadas.
