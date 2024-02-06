Below is the translation of the provided text into Brazilian Portuguese, formatted in Markdown:

```markdown
# Teste de Configuração da Infraestrutura de Rede

|ID          |
|------------|
|WSTG-CONF-01|

## Resumo

A complexidade intrínseca de uma infraestrutura de servidor web interconectada e heterogênea, que pode incluir centenas de aplicações web, torna a gestão e revisão de configuração um passo fundamental no teste e implantação de cada aplicação. Basta uma única vulnerabilidade para comprometer a segurança de toda a infraestrutura, e até problemas pequenos e aparentemente insignificantes podem evoluir para riscos graves para outra aplicação no mesmo servidor. Para abordar esses problemas, é de extrema importância realizar uma revisão aprofundada da configuração e dos problemas de segurança conhecidos, após ter mapeado toda a arquitetura.

A gestão adequada da configuração da infraestrutura do servidor web é muito importante para preservar a segurança da própria aplicação. Se elementos como o software do servidor web, os servidores de banco de dados backend ou os servidores de autenticação não forem adequadamente revisados e protegidos, eles podem introduzir riscos indesejados ou novas vulnerabilidades que podem comprometer a própria aplicação.

Por exemplo, uma vulnerabilidade do servidor web que permita a um atacante remoto divulgar o código-fonte da própria aplicação (uma vulnerabilidade que surgiu várias vezes tanto em servidores web quanto em servidores de aplicação) poderia comprometer a aplicação, pois usuários anônimos poderiam usar as informações divulgadas no código-fonte para realizar ataques contra a aplicação ou seus usuários.

As seguintes etapas precisam ser realizadas para testar a infraestrutura de gestão de configuração:

- Os diferentes elementos que compõem a infraestrutura precisam ser determinados para entender como eles interagem com uma aplicação web e como afetam sua segurança.
- Todos os elementos da infraestrutura precisam ser revisados para garantir que não contenham vulnerabilidades conhecidas.
- Uma revisão precisa ser feita das ferramentas administrativas usadas para manter todos os diferentes elementos.
- Os sistemas de autenticação precisam ser revisados para garantir que atendam às necessidades da aplicação e que não possam ser manipulados por usuários externos para obter acesso.
- Uma lista de portas definidas que são necessárias para a aplicação deve ser mantida e controlada por mudanças.

Após ter mapeado os diferentes elementos que compõem a infraestrutura (ver [Mapa de Rede e Arquitetura de Aplicação](../01-Information_Gathering/10-Map_Application_Architecture.md)) é possível revisar a configuração de cada elemento encontrado e testar para quaisquer vulnerabilidades conhecidas.

## Objetivos do Teste

- Revisar as configurações das aplicações definidas na rede e validar que não são vulneráveis.
- Validar que os frameworks e sistemas usados são seguros e não suscetíveis a vulnerabilidades conhecidas devido a software não mantido ou configurações e credenciais padrão.

## Como Testar

### Vulnerabilidades de Servidor Conhecidas

Vulnerabilidades encontradas nas diferentes áreas da arquitetura da aplicação, seja no servidor web ou no banco de dados backend, podem comprometer seriamente a própria aplicação. Por exemplo, considere uma vulnerabilidade de servidor que permite a um usuário remoto não autenticado carregar arquivos no servidor web ou até substituir arquivos. Esta vulnerabilidade poderia comprometer a aplicação, já que um usuário mal-intencionado pode ser capaz de substituir a própria aplicação ou introduzir código que afetaria os servidores backend, já que seu código de aplicação seria executado como qualquer outra aplicação.

Revisar as vulnerabilidades do servidor pode ser difícil se o teste precisar ser feito através de um teste de penetração cego. Nestes casos, as vulnerabilidades precisam ser testadas a partir de um site remoto, tipicamente usando uma ferramenta automatizada. No entanto, testar algumas vulnerabilidades pode ter resultados imprevisíveis no servidor web, e testar outras (como aquelas diretamente envolvidas em ataques de negação de serviço) pode não ser possível devido ao tempo de inatividade do serviço envolvido se o teste for bem-sucedido.



Algumas ferramentas automatizadas sinalizarão vulnerabilidades com base na versão do servidor web recuperada. Isso leva a falsos positivos e falsos negativos. Por um lado, se a versão do servidor web foi removida ou obscurecida pelo administrador do site local, a ferramenta de varredura não marcará o servidor como vulnerável, mesmo que seja. Por outro lado, se o fornecedor que fornece o software não atualizar a versão do servidor web quando as vulnerabilidades forem corrigidas, a ferramenta de varredura marcará vulnerabilidades que não existem. O último caso é muito comum, pois alguns fornecedores de sistemas operacionais fazem backport de patches de vulnerabilidades de segurança para o software que fornecem no sistema operacional, mas não fazem upload completo para a última versão do software. Isso acontece na maioria das distribuições GNU/Linux, como Debian, Red Hat ou SuSE. Na maioria dos casos, a varredura de vulnerabilidade de uma arquitetura de aplicação só encontrará vulnerabilidades associadas aos elementos "expostos" da arquitetura (como o servidor web) e geralmente não será capaz de encontrar vulnerabilidades associadas a elementos que não estão diretamente expostos, como os backends de autenticação, o banco de dados backend ou proxies reversos em uso.

Finalmente, nem todos os fornecedores de software divulgam vulnerabilidades de forma pública, e, portanto, essas fraquezas não se tornam registradas em bancos de dados de vulnerabilidades conhecidos publicamente [2]. Essa informação é divulgada apenas para clientes ou publicada através de correções que não têm avisos acompanhantes. Isso reduz a utilidade das ferramentas de varredura de vulnerabilidades. Tipicamente, a cobertura de vulnerabilidade dessas ferramentas será muito boa para produtos comuns (como o servidor web Apache, o Internet Information Server da Microsoft ou o Lotus Domino da IBM) mas será deficiente para produtos menos conhecidos.

É por isso que revisar vulnerabilidades é melhor feito quando o testador é fornecido com informações internas do software usado, incluindo versões e lançamentos usados e patches aplicados ao software. Com essas informações, o testador pode recuperar a informação do próprio fornecedor e analisar quais vulnerabilidades podem estar presentes na arquitetura e como elas podem afetar a própria aplicação. Quando possível, essas vulnerabilidades podem ser testadas para determinar seus efeitos reais e detectar se pode haver quaisquer elementos externos (como sistemas de detecção ou prevenção de intrusão) que possam reduzir ou negar a possibilidade de exploração bem-sucedida. Os testadores podem até determinar, através de uma revisão de configuração, que a vulnerabilidade nem mesmo está presente, já que afeta um componente de software que não está em uso.

Também vale a pena notar que os fornecedores às vezes corrigem silenciosamente vulnerabilidades e disponibilizam as correções com novos lançamentos de software. Diferentes fornecedores terão diferentes ciclos de lançamento que determinam o suporte que podem fornecer para versões antigas. Um testador com informações detalhadas das versões de software usadas pela arquitetura pode analisar o risco associado ao uso de versões de software antigas que podem ser descontinuadas a curto prazo ou já não são mais suportadas. Isso é crítico, pois se uma vulnerabilidade surgir em uma versão antiga de software que não é mais suportada, o pessoal de sistemas pode não estar diretamente ciente disso. Nenhum patch será disponibilizado para ela e os avisos podem não listar essa versão como vulnerável, já que não é mais suportada. Mesmo no caso de estarem cientes de que a vulnerabilidade está presente e o sistema é vulnerável, eles precisarão fazer um upgrade completo para uma nova versão de software, o que pode introduzir tempo de inatividade significativo na arquitetura da aplicação ou pode forçar a aplicação a ser recodificada devido a incompatibilidades com a última versão do software.

### Ferramentas Administrativas

Qualquer infraestrutura de servidor web requer a existência de ferramentas administrativas para manter e atualizar as informações usadas pela aplicação. Essas informações incluem conteúdo estático (páginas web, arquivos gráficos), código-fonte da aplicação, bancos de dados de autenticação de usuários, etc. As ferramentas administrativas variam dependendo do site, da tecnologia ou do software utilizado. Por exemplo, alguns servidores web são gerenciados usando interfaces administrativas que são, elas próprias, servidores web (como o servidor web iPlanet) ou são administrados por arquivos de configuração em texto simples (no caso do Apache [3]) ou usam ferramentas de GUI do sistema operacional (quando usando o servidor IIS da Microsoft ou ASP.Net).

Na maioria dos casos, a configuração do servidor é feita usando diferentes ferramentas de manutenção de arquivos usadas pelo servidor web, que são gerenciadas por servidores FTP, WebDAV, sistemas de arquivos de rede (NFS, CIFS) ou outros mecanismos. Obviamente, o sistema operacional dos elementos que compõem a arquitetura da aplicação também será gerenciado usando outras ferramentas. As aplicações também podem ter interfaces administrativas embutidas nelas que são usadas para gerenciar os próprios dados da aplicação (usuários, conteúdo, etc.).

Após mapear as interfaces administrativas usadas para gerenciar as diferentes partes da arquitetura, é importante revisá-las, pois se um atacante ganhar acesso a qualquer uma delas, ele pode comprometer ou danificar a arquitetura da aplicação. Para fazer isso, é importante:

Determinar os mecanismos que controlam o acesso a essas interfaces e suas susceptibilidades associadas. Essa informação pode estar disponível online.
Alterar o nome de usuário e senha padrão.
Algumas empresas optam por não gerenciar todos os aspectos de suas aplicações de servidor web, mas podem ter terceiros gerenciando o conteúdo entregue pela aplicação web. Esta empresa externa pode fornecer apenas partes do conteúdo (atualizações de notícias ou promoções) ou pode gerenciar completamente o servidor web (incluindo conteúdo e código). É comum encontrar interfaces administrativas disponíveis na Internet nessas situações, já que usar a Internet é mais barato do que fornecer uma linha dedicada que conectará a empresa externa à infraestrutura da aplicação através de uma interface apenas de gerenciamento. Nesta situação, é muito importante testar se as interfaces administrativas podem ser vulneráveis a ataques.

Referências
[1] WebSEAL, também conhecido como Tivoli Authentication Manager, é um proxy reverso da IBM que faz parte do framework Tivoli.
[2] Como o Bugtraq da Symantec, o X-Force da ISS ou o National Vulnerability Database (NVD) do NIST.
[3] Existem algumas ferramentas de administração baseadas em GUI para o Apache (como o NetLoony), mas ainda não são amplamente utilizadas.