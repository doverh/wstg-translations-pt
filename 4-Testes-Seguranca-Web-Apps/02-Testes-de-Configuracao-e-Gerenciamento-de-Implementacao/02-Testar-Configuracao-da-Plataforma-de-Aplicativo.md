# Testar Configuração da Plataforma do Aplicativo
|ID          |
|------------|
|WSTG-CONF-02|

## Resumo

A configuração adequada dos elementos individuais que compõem a arquitetura de uma aplicação é importante para evitar erros que possam comprometer a segurança de toda a arquitetura.

A revisão e teste de configuração são tarefas críticas na criação e manutenção de uma arquitetura. Isso ocorre porque muitos sistemas diferentes geralmente são fornecidos com configurações genéricas que podem não ser adequadas para a tarefa que executarão no site específico em que estão instalados.

Embora a instalação típica de servidor web e aplicativo contenha muitas funcionalidades (como exemplos de aplicativos, documentação, páginas de teste), o que não é essencial deve ser removido antes da implantação para evitar exploração pós-instalação.

## Objetivos do Teste

- Garantir que padrões e arquivos conhecidos tenham sido removidos.
- Validar que nenhum código de depuração ou extensões sejam deixados nos ambientes de produção.
- Revisar os mecanismos de registro configurados para a aplicação.

## Como Testar

### Teste de Caixa Preta

#### Amostras e Arquivos e Diretórios Conhecidos

Muitos servidores web e aplicativos fornecem, em uma instalação padrão, aplicativos e arquivos de amostra para o benefício do desenvolvedor e para testar se o servidor está funcionando corretamente logo após a instalação. No entanto, muitos aplicativos padrão de servidor web foram posteriormente considerados vulneráveis. Esse foi o caso, por exemplo, do CVE-1999-0449 (Negação de Serviço no IIS quando o site de amostra Exair estava instalado), CAN-2002-1744 (Vulnerabilidade de travessia de diretórios no CodeBrws.asp no Microsoft IIS 5.0), CAN-2002-1630 (Uso do sendmail.jsp no Oracle 9iAS) ou CAN-2003-1172 (Travessia de diretórios na amostra view-source no Apache's Cocoon).

Scanners CGI incluem uma lista detalhada de arquivos conhecidos e amostras de diretórios fornecidos por diferentes servidores web ou de aplicativos e podem ser uma maneira rápida de determinar se esses arquivos estão presentes. No entanto, a única maneira de ter certeza é fazer uma revisão completa do conteúdo do servidor web ou do servidor de aplicativos e determinar se estão relacionados à aplicação ou não.

#### Revisão de Comentários

É muito comum os programadores adicionarem comentários ao desenvolver grandes aplicativos baseados na web. No entanto, comentários incluídos em linha no código HTML podem revelar informações internas que não deveriam estar disponíveis para um atacante. Às vezes, até mesmo o código-fonte é comentado, pois uma funcionalidade não é mais necessária, mas esse comentário é vazado para as páginas HTML retornadas aos usuários involuntariamente.

A revisão de comentários deve ser feita para determinar se há vazamento de informações por meio de comentários. Essa revisão só pode ser feita completamente por meio de uma análise do conteúdo estático e dinâmico do servidor web e por meio de buscas de arquivos. Pode ser útil navegar pelo site de maneira automática ou orientada e armazenar todo o conteúdo recuperado. Esse conteúdo recuperado pode então ser pesquisado para analisar quaisquer comentários HTML disponíveis no código.

#### Configuração do Sistema

Várias ferramentas, documentos ou listas de verificação podem ser usados para fornecer aos profissionais de TI e segurança uma avaliação detalhada da conformidade dos sistemas de destino com várias linhas de base ou benchmarks de configuração. Essas ferramentas incluem (mas não se limitam a):

- [CIS-CAT Lite](https://www.cisecurity.org/blog/introducing-cis-cat-lite/)
- [Microsoft's Attack Surface Analyzer](https://github.com/microsoft/AttackSurfaceAnalyzer)
- [Programa de Lista de Verificação Nacional do NIST](https://nvd.nist.gov/ncp/repository)

### Teste de Caixa Cinza

#### Revisão de Configuração

A configuração do servidor web ou do servidor de aplicativos desempenha um papel importante na proteção do conteúdo do site e deve ser cuidadosamente revisada para identificar erros comuns de configuração. Obviamente, a configuração recomendada varia dependendo da política do site e da funcionalidade que o software do servidor deve fornecer. Na maioria dos casos, no entanto, as diretrizes de configuração (fornecidas pelo fornecedor de software ou partes externas) devem ser seguidas para determinar se o servidor foi devidamente protegido.

É impossível dizer genericamente como um servidor deve ser configurado; no entanto, algumas diretrizes comuns devem ser consideradas:

- Ative apenas módulos do servidor (extensões ISAPI no caso do IIS) necessários para a aplicação. Isso reduz a superfície de ataque, pois o servidor é reduzido em tamanho e complexidade quando módulos de software são desativados. Também previne vulnerabilidades que podem surgir no software do fornecedor, caso estejam presentes apenas em módulos que já foram desativados.
- Trate erros do servidor (40x ou 50x) com páginas personalizadas em vez das páginas padrão do servidor web. Certifique-se especialmente de que quaisquer erros da aplicação não sejam retornados ao usuário final e que nenhum código seja vazado por meio desses erros, pois isso ajudará um invasor. É comum esquecer este ponto, pois os desenvolvedores precisam dessas informações em ambientes de pré-produção.
- Garanta que o software do servidor execute com privilégios mínimos no sistema operacional. Isso impede que um erro no software do servidor comprometa diretamente todo o sistema, embora um invasor possa elevar privilégios ao executar código como servidor web.
- Certifique-se de que o software do servidor registre corretamente o acesso legítimo e os erros.
- Garanta que o servidor esteja configurado para lidar adequadamente com sobrecargas e evitar ataques de Negação de Serviço. Certifique-se de que o servidor foi ajustado adequadamente em termos de desempenho.
- Nunca conceda a identidades

 não administrativas (com exceção de `NT SERVICE\WMSvc`) acesso a applicationHost.config, redirection.config e administration.config (leitura ou gravação). Isso inclui `Network Service`, `IIS_IUSRS`, `IUSR` ou qualquer identidade personalizada usada por pools de aplicativos do IIS. Os processos de trabalho do IIS não devem acessar diretamente nenhum desses arquivos.
- Não compartilhe applicationHost.config, redirection.config e administration.config na rede. Ao usar Configuração Compartilhada, prefira exportar applicationHost.config para outro local (consulte a seção intitulada "Configurar Permissões para Configuração Compartilhada").
- Lembre-se de que todos os usuários podem ler os arquivos .NET Framework `machine.config` e root `web.config` por padrão. Não armazene informações confidenciais nesses arquivos se deveriam ser apenas para os olhos do administrador.
- Criptografe informações sensíveis que devem ser lidas apenas pelos processos de trabalho do IIS e não por outros usuários na máquina.
- Não conceda acesso de gravação à identidade que o servidor Web usa para acessar o `applicationHost.config` compartilhado. Essa identidade deve ter apenas acesso de leitura.
- Use uma identidade separada para publicar applicationHost.config no compartilhamento. Não use essa identidade para configurar o acesso à configuração compartilhada nos servidores Web.
- Use uma senha forte ao exportar as chaves de criptografia para uso com a configuração compartilhada.
- Mantenha o acesso restrito ao compartilhamento que contém a configuração compartilhada e as chaves de criptografia. Se esse compartilhamento for comprometido, um invasor poderá ler e gravar qualquer configuração do IIS para seus servidores Web, redirecionar o tráfego do seu site para fontes maliciosas e, em alguns casos, obter controle de todos os servidores web carregando código arbitrário nos processos de trabalho do IIS.

#### Registro

O registro é um ativo importante para a segurança de uma arquitetura de aplicativo, pois pode ser usado para detectar falhas em aplicativos (usuários constantemente tentando recuperar um arquivo que realmente não existe) e ataques sustentados de usuários mal-intencionados. Os logs geralmente são gerados corretamente pelo software do servidor web e de outros servidores. Não é comum encontrar aplicativos que registram corretamente suas ações em um log e, quando o fazem, a principal intenção dos logs do aplicativo é produzir saída de depuração que poderia ser usada pelo programador para analisar um erro específico.

Em ambos os casos (logs do servidor e do aplicativo), várias questões devem ser testadas e analisadas com base no conteúdo do log:

1. Os logs contêm informações sensíveis?
2. Os logs são armazenados em um servidor dedicado?
3. O uso de logs pode gerar uma condição de Negação de Serviço?
4. Como são rotacionados? Os logs são mantidos pelo tempo suficiente?
5. Como os logs são revisados? Os administradores podem usar essas revisões para detectar ataques direcionados?
6. Como os backups de logs são preservados?
7. Os dados registrados são validados (comprimento mínimo/máximo, caracteres etc.) antes de serem registrados? 

##### Sensitive Information in Logs

Some applications might, for example, use GET requests to forward form data which will be seen in the server logs. This means that server logs might contain sensitive information (such as usernames as passwords, or bank account details). This sensitive information can be misused by an attacker if they obtained the logs, for example, through administrative interfaces or known web server vulnerabilities or misconfiguration (like the well-known `server-status` misconfiguration in Apache-based HTTP servers).

Event logs will often contain data that is useful to an attacker (information leakage) or can be used directly in exploits:

- Debug information
- Stack traces
- Usernames
- System component names
- Internal IP addresses
- Less sensitive personal data (e.g. email addresses, postal addresses and telephone numbers associated with named individuals)
- Business data

Also, in some jurisdictions, storing some sensitive information in log files, such as personal data, might oblige the enterprise to apply the data protection laws that they would apply to their back-end databases to log files too. And failure to do so, even unknowingly, might carry penalties under the data protection laws that apply.

A wider list of sensitive information is:

- Application source code
- Session identification values
- Access tokens
- Sensitive personal data and some forms of personally identifiable information (PII)
- Authentication passwords
- Database connection strings
- Encryption keys
- Bank account or payment card holder data
- Data of a higher security classification than the logging system is allowed to store
- Commercially-sensitive information
- Information it is illegal to collect in the relevant jurisdiction
- Information a user has opted out of collection, or not consented to e.g. use of do not track, or where consent to collect has expired

#### Log Location

Typically servers will generate local logs of their actions and errors, consuming the disk of the system the server is running on. However, if the server is compromised its logs can be wiped out by the intruder to clean up all the traces of its attack and methods. If this were to happen the system administrator would have no knowledge of how the attack occurred or where the attack source was located. Actually, most attacker tool kits include a ''log zapper '' that is capable of cleaning up any logs that hold given information (like the IP address of the attacker) and are routinely used in attacker’s system-level root kits.

Consequently, it is wiser to keep logs in a separate location and not in the web server itself. This also makes it easier to aggregate logs from different sources that refer to the same application (such as those of a web server farm) and it also makes it easier to do log analysis (which can be CPU intensive) without affecting the server itself.

#### Log Storage

Logs can introduce a Denial of Service condition if they are not properly stored. Any attacker with sufficient resources could be able to produce a sufficient number of requests that would fill up the allocated space to log files, if they are not specifically prevented from doing so. However, if the server is not properly configured, the log files will be stored in the same disk partition as the one used for the operating system software or the application itself. This means that if the disk were to be filled up the operating system or the application might fail because it is unable to write on disk.

Typically in UNIX systems logs will be located in /var (although some server installations might reside in /opt or /usr/local) and it is important to make sure that the directories in which logs are stored are in a separate partition. In some cases, and in order to prevent the system logs from being affected, the log directory of the server software itself (such as /var/log/apache in the Apache web server) should be stored in a dedicated partition.

This is not to say that logs should be allowed to grow to fill up the file system they reside in. Growth of server logs should be monitored in order to detect this condition since it may be indicative of an attack.

Testing this condition is as easy, and as dangerous in production environments, as firing off a sufficient and sustained number of requests to see if these requests are logged and if there is a possibility to fill up the log partition through these requests. In some environments where QUERY_STRING parameters are also logged regardless of whether they are produced through GET or POST requests, big queries can be simulated that will fill up the logs faster since, typically, a single request will cause only a small amount of data to be logged, such as date and time, source IP address, URI request, and server result.

#### Log Rotation

Most servers (but few custom applications) will rotate logs in order to prevent them from filling up the file system they reside on. The assumption when rotating logs is that the information in them is only necessary for a limited amount of time.

This feature should be tested in order to ensure that:

- Logs are kept for the time defined in the security policy, not more and not less.
- Logs are compressed once rotated (this is a convenience, since it will mean that more logs will be stored for the same available disk space).
- File system permission of rotated log files are the same (or stricter) that those of the log files itself. For example, web servers will need to write to the logs they use but they don’t actually need to write to rotated logs, which means that the permissions of the files can be changed upon rotation to prevent the web server process from modifying these.

Some servers might rotate logs when they reach a given size. If this happens, it must be ensured that an attacker cannot force logs to rotate in order to hide his tracks.

#### Log Access Control

Event log information should never be visible to end users. Even web administrators should not be able to see such logs since it breaks separation of duty controls. Ensure that any access control schema that is used to protect access to raw logs and any applications providing capabilities to view or search the logs is not linked with access control schemas for other application user roles. Neither should any log data be viewable by unauthenticated users.

#### Log Review

Review of logs can be used for more than extraction of usage statistics of files in the web servers (which is typically what most log-based application will focus on), but also to determine if attacks take place at the web server.

In order to analyze web server attacks the error log files of the server need to be analyzed. Review should concentrate on:

- 40x (not found) error messages. A large amount of these from the same source might be indicative of a CGI scanner tool being used against the web server
- 50x (server error) messages. These can be an indication of an attacker abusing parts of the application which fail unexpectedly. For example, the first phases of a SQL injection attack will produce these error message when the SQL query is not properly constructed and its execution fails on the back end database.

Log statistics or analysis should not be generated, nor stored, in the same server that produces the logs. Otherwise, an attacker might, through a web server vulnerability or improper configuration, gain access to them and retrieve similar information as would be disclosed by log files themselves.

## References

- Apache
  - Apache Security, by Ivan Ristic, O’reilly, March 2005.
  - [Apache Security Secrets: Revealed (Again), Mark Cox, November 2003](https://awe.com/mark/talks/apachecon2003us.html)
  - [Apache Security Secrets: Revealed, ApacheCon 2002, Las Vegas, Mark J Cox, October 2002](https://awe.com/mark/talks/apachecon2002us.html)
  - [Performance Tuning](https://httpd.apache.org/docs/current/misc/perf-tuning.html)
- Lotus Domino
  - Lotus Security Handbook, William Tworek et al., April 2004, available in the IBM Redbooks collection
  - Lotus Domino Security, an X-force white-paper, Internet Security Systems, December 2002
  - Hackproofing Lotus Domino Web Server, David Litchfield, October 2001
- Microsoft IIS
  - [Security Best Practices for IIS 8](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj635855(v=ws.11))
  - [CIS Microsoft IIS Benchmarks](https://www.cisecurity.org/benchmark/microsoft_iis/)
  - Securing Your Web Server (Patterns and Practices), Microsoft Corporation, January 2004
  - IIS Security and Programming Countermeasures, by Jason Coombs
  - From Blueprint to Fortress: A Guide to Securing IIS 5.0, by John Davis, Microsoft Corporation, June 2001
  - Secure Internet Information Services 5 Checklist, by Michael Howard, Microsoft Corporation, June 2000
- Red Hat’s (formerly Netscape’s) iPlanet
  - Guide to the Secure Configuration and Administration of iPlanet Web Server, Enterprise Edition 4.1, by James M Hayes, The Network Applications Team of the Systems and Network Attack Center (SNAC), NSA, January 2001
- WebSphere
  - IBM WebSphere V5.0 Security, WebSphere Handbook Series, by Peter Kovari et al., IBM, December 2002.
  - IBM WebSphere V4.0 Advanced Edition Security, by Peter Kovari et al., IBM, March 2002.
- General
  - [Logging Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html), OWASP
  - [SP 800-92](https://csrc.nist.gov/publications/detail/sp/800-92/final) Guide to Computer Security Log Management, NIST
  - [PCI DSS v3.2.1](https://www.pcisecuritystandards.org/document_library) Requirement 10 and PA-DSS v3.2 Requirement 4, PCI Security Standards Council

- Generic:
  - [CERT Security Improvement Modules: Securing Public Web Servers](https://resources.sei.cmu.edu/asset_files/SecurityImprovementModule/2000_006_001_13637.pdf)

  - [How To: Use IISLockdown.exe](https://support.microsoft.com/en-us/help/325864/how-to-install-and-use-the-iis-lockdown-wizard)

# Informações Sensíveis nos Registros

Algumas aplicações podem, por exemplo, usar solicitações GET para encaminhar dados de formulário que serão registrados nos logs do servidor. Isso significa que os logs do servidor podem conter informações sensíveis (como nomes de usuários, senhas ou detalhes de contas bancárias). Essas informações sensíveis podem ser mal utilizadas por um atacante se eles obtiverem os logs, por exemplo, por meio de interfaces administrativas ou vulnerabilidades conhecidas no servidor web ou má configuração (como a conhecida má configuração de `server-status` em servidores HTTP baseados no Apache).

Logs de eventos frequentemente conterão dados úteis para um atacante (vazamento de informações) ou podem ser usados diretamente em exploits:

- Informações de depuração
- Rastreamentos de pilha
- Nomes de usuários
- Nomes de componentes do sistema
- Endereços IP internos
- Dados pessoais menos sensíveis (por exemplo, endereços de e-mail, endereços postais e números de telefone associados a indivíduos nomeados)
- Dados comerciais

Além disso, em algumas jurisdições, armazenar informações sensíveis em arquivos de log, como dados pessoais, pode obrigar a empresa a aplicar as leis de proteção de dados que aplicariam aos seus bancos de dados também aos arquivos de log. E o não cumprimento, mesmo sem saber, pode acarretar penalidades sob as leis de proteção de dados aplicáveis.

Uma lista mais abrangente de informações sensíveis inclui:

- Código-fonte do aplicativo
- Valores de identificação de sessão
- Tokens de acesso
- Dados pessoais sensíveis e algumas formas de informações pessoalmente identificáveis (PII)
- Senhas de autenticação
- Cadeias de conexão do banco de dados
- Chaves de criptografia
- Dados de titular de conta bancária ou cartão de pagamento
- Dados de uma classificação de segurança mais alta do que o sistema de registro tem permissão para armazenar
- Informações comercialmente sensíveis
- Informações ilegais de se coletar na jurisdição relevante
- Informações das quais um usuário optou por não coletar ou não deu consentimento, por exemplo, uso de não rastrear ou quando o consentimento para coletar expirou

#### Localização do Log

Tipicamente, os servidores gerarão logs locais de suas ações e erros, consumindo o disco do sistema no qual o servidor está em execução. No entanto, se o servidor for comprometido, seus logs podem ser apagados pelo invasor para eliminar todas as pistas de seu ataque e métodos. Se isso acontecer, o administrador do sistema não terá conhecimento de como o ataque ocorreu ou onde estava localizada a fonte do ataque. Na verdade, a maioria dos kits de ferramentas de ataque inclui um "apagador de logs" que é capaz de limpar qualquer log que contenha informações específicas (como o endereço IP do atacante) e é rotineiramente usado em kits de raiz de sistema de invasores.

Consequentemente, é mais sensato manter logs em uma localização separada e não no próprio servidor web. Isso também facilita a agregação de logs de diferentes fontes que se referem à mesma aplicação (como aquelas de um conjunto de servidores web) e também facilita a análise de logs (que pode ser intensiva em CPU) sem afetar o próprio servidor.

#### Armazenamento de Logs

Logs podem introduzir uma condição de Negação de Serviço se não forem armazenados adequadamente. Qualquer atacante com recursos suficientes pode ser capaz de produzir um número suficiente de solicitações que preencham o espaço alocado para os arquivos de log, se não forem especificamente impedidos de fazer isso. No entanto, se o servidor não estiver configurado corretamente, os arquivos de log serão armazenados na mesma partição de disco que aquela usada para o software do sistema operacional ou a própria aplicação. Isso significa que, se o disco ficar cheio, o sistema operacional ou a aplicação podem falhar porque não conseguem gravar no disco.

Tipicamente, em sistemas UNIX, os logs estarão localizados em /var (embora algumas instalações de servidor possam residir em /opt ou /usr/local) e é importante garantir que os diretórios nos quais os logs são armazenados estejam em uma partição separada. Em alguns casos, e para evitar que os logs do sistema sejam afetados, o diretório de log do próprio software do servidor (como /var/log/apache no servidor web Apache) deve ser armazenado em uma partição dedicada.

Isso não significa que os logs devem ser permitidos a crescer para preencher o sistema de arquivos no qual residem. O crescimento dos logs do servidor deve ser monitorado para detectar essa condição, pois pode ser indicativo de um ataque.

Testar essa condição é tão fácil e perigoso em ambientes de produção quanto enviar um número suficiente e sustentado de solicitações para ver se essas solicitações são registradas e se há a possibilidade de preencher a partição de log por meio dessas solicitações. Em alguns ambientes nos quais os parâmetros QUERY_STRING também são registrados, independentemente de serem produzidos por solicitações GET ou POST, grandes consultas podem ser simuladas e preencher os logs mais rapidamente, uma vez que, normalmente, uma única solicitação causará apenas uma pequena quantidade de dados a ser registrada, como data e hora, endereço IP de origem, solicitação URI e resultado do servidor.

#### Rotação de Logs

A maioria dos servidores (mas poucas aplicações personalizadas) rotacionará os logs para evitar que eles preencham o sistema de arquivos no qual residem. A suposição ao rotacionar logs é que as informações neles são necessárias apenas por um período limitado.

Essa funcionalidade deve ser testada para garantir que:

- Os logs são mantidos pelo tempo definido na política de segurança, nem mais nem menos.
- Os logs são compactados após a rotação (isso é conveniente, pois significa que mais logs serão armazenados pelo mesmo espaço em disco disponível).
- As permissões do sistema de arquivos dos arquivos de log rotacionados são as mesmas (ou mais restritas) do que as dos próprios arquivos de log. Por exemplo, os servidores web precisarão gravar nos logs que usam, mas eles não precis

am realmente escrever nos logs rotacionados, o que significa que as permissões dos arquivos podem ser alteradas após a rotação para impedir que o processo do servidor web os modifique.

Alguns servidores podem rotacionar os logs quando atingem um determinado tamanho. Se isso acontecer, deve ser garantido que um atacante não pode forçar a rotação dos logs para ocultar seus rastros.

#### Controle de Acesso aos Logs

As informações do registro de eventos nunca devem ser visíveis para os usuários finais. Mesmo os administradores da web não devem poder ver esses logs, pois isso quebra os controles de separação de funções. Garanta que qualquer esquema de controle de acesso usado para proteger o acesso aos logs brutos e quaisquer aplicativos que forneçam capacidades para visualizar ou pesquisar os logs não estejam vinculados a esquemas de controle de acesso para outras funções de usuário do aplicativo. E nenhum dado de log deve ser visível por usuários não autenticados.

#### Revisão de Logs

A revisão de logs pode ser usada para mais do que a extração de estatísticas de uso de arquivos nos servidores web (que é tipicamente o foco da maioria das aplicações baseadas em logs), mas também para determinar se ataques estão ocorrendo no servidor web.

Para analisar ataques ao servidor web, os arquivos de log de erro do servidor precisam ser analisados. A revisão deve se concentrar em:

- Mensagens de erro 40x (não encontradas). Uma grande quantidade delas da mesma origem pode ser indicativa do uso de uma ferramenta de scanner CGI contra o servidor web.
- Mensagens de erro 50x (erro do servidor). Isso pode ser uma indicação de um atacante abusando de partes da aplicação que falham inesperadamente. Por exemplo, as primeiras fases de um ataque de injeção SQL produzirão essas mensagens de erro quando a consulta SQL não for construída corretamente e sua execução falhar no banco de dados.

Estatísticas ou análises de logs não devem ser geradas, nem armazenadas, no mesmo servidor que produz os logs. Caso contrário, um atacante pode, por meio de uma vulnerabilidade no servidor web ou configuração imprópria, ganhar acesso a eles e recuperar informações semelhantes às que seriam divulgadas pelos próprios arquivos de log.

## Referências

- Apache
  - Apache Security, de Ivan Ristic, O'reilly, março de 2005.
  - [Apache Security Secrets: Revealed (Again), Mark Cox, novembro de 2003](https://awe.com/mark/talks/apachecon2003us.html)
  - [Apache Security Secrets: Revealed, ApacheCon 2002, Las Vegas, Mark J Cox, outubro de 2002](https://awe.com/mark/talks/apachecon2002us.html)
  - [Performance Tuning](https://httpd.apache.org/docs/current/misc/perf-tuning.html)
- Lotus Domino
  - Lotus Security Handbook, William Tworek et al., abril de 2004, disponível na coleção IBM Redbooks
  - Lotus Domino Security, um white-paper X-force, Internet Security Systems, dezembro de 2002
  - Hackproofing Lotus Domino Web Server, David Litchfield, outubro de 2001
- Microsoft IIS
  - [Security Best Practices for IIS 8](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj635855(v=ws.11))
  - [CIS Microsoft IIS Benchmarks](https://www.cisecurity.org/benchmark/microsoft_iis/)
  - Securing Your Web Server (Patterns and Practices), Microsoft Corporation, janeiro de 2004
  - IIS Security and Programming Countermeasures, de Jason Coombs
  - From Blueprint to Fortress: A Guide to Securing IIS 5.0, de John Davis, Microsoft Corporation, junho de 2001
  - Secure Internet Information Services 5 Checklist, de Michael Howard, Microsoft Corporation, junho de 2000
- iPlanet da Red Hat (anteriormente Netscape)
  - Guia para a Configuração e Administração Seguras do iPlanet Web Server, Enterprise Edition 4.1, de James M Hayes, The Network Applications Team of the Systems and Network Attack Center (SNAC), NSA, janeiro de 2001
- WebSphere
  - IBM WebSphere V5.0 Security, WebSphere Handbook Series, de Peter Kovari et al., IBM, dezembro de 2002.
  - IBM WebSphere V4.0 Advanced Edition Security, de Peter Kovari et al., IBM, março de 2002.
- Geral
  - [Logging Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html), OWASP
  - [SP 800-92](https://csrc.nist.gov/publications/detail/sp/800-92/final) Guia para Gerenciamento de Logs de Segurança de Computadores, NIST
  - [PCI DSS v3.2.1](https://www.pcisecuritystandards.org/document_library) Requisito 10 e PA-DSS v3.2 Requisito 4, Conselho de Padrões de Segurança PCI

- Genérico:
  - [CERT Security Improvement Modules: Securing Public Web Servers](https://resources.sei.cmu.edu/asset_files/SecurityImprovementModule/2000_006_001_13637.pdf)

  - [How To: Use IISLockdown.exe](https://support.microsoft.com/en-us/help/325864/how-to-install-and-use-the-iis-lockdown-wizard)