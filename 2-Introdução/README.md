
# Introdução

## O Projeto de Testes OWASP
  
O Projeto de Testes OWASP está em desenvolvimento há muitos anos. O objetivo do projeto é ajudar pessoas a entender o *quê*, *por que*, *quando*, *onde* e *como* testar aplicativos web. O projeto criou um modelo de testes completo, um framework, e não apenas uma lista simples de itens ou diagnóstico de problemas que devem ser remediados. Os leitores podem usar essa estrutura como um modelo para construir seus próprios programas de teste ou ainda qualificar processos de outras pessoas. O guia de testes descreve em detalhes o modelo geral de testes e as técnicas necessárias para implementá-lo na prática.

Escrever o guia de testes tem sido uma tarefa árdua. Foi um desafio obter consenso e desenvolver conteúdos que permitam aplicar os conceitos sugeridos pelo guia, ao mesmo tempo em que permitam a adaptação a ambientes culturais específicos. Também foi um desafio modificar o foco dos testes de aplicativos web, de testes de intrusão (do inglês "Penetration Test" ou pentest"), para testes de integração, ou seja, testes integrados no ciclo de vida do desenvolvimento de software.

No entanto, o grupo está muito satisfeito com os resultados do projeto. Muitos especialistas e profissionais de segurança da informação, alguns dos quais responsáveis pela segurança de software em algumas das maiores empresas do mundo, estão validando esse modelo de testes. Esse modelo tem ajudado organizações a testar seus aplicativos web e construir softwares mais confiáveis e seguros. Embora seja um dos resulados de muitas listas de guia OWASP, esse framework não enfatiza simplesmente as áreas mais sensíveis. Decisões delicadas devem ser tomadas sobre a adequação do uso de certas técnicas e tecnologias de testes. O grupo entende perfeitamente que nem todos concordarão com todas as decisões tomadas. Contudo, a renomada posição da OWASP permite a mudança de cultura a longo prazo por meio da conscientização e educação, construindo consenso através de experiências.

O restante deste guia está organizado da seguinte forma: esta introdução cobre os pré-requisitos de teste de aplicativos web e o escopo de testes. Ela também cobre os princípios e técnicas de teste executados com sucesso, práticas recomendadas para relatórios e casos de negócios para testes de segurança. O capítulo 3 apresenta o modelo de testes OWASP e explica suas técnicas e tarefas em relação às várias fases do ciclo de vida de desenvolvimento de software. O capítulo 4 apresenta como testar vulnerabilidades específicas (por exemplo, injeção de SQL), inspeção de código e teste de intrusão.

### Medindo a segurança: dados economicos de um software inseguro
Um princípio básico da engenharia de software é resumido em uma citação de Tom DeMarco em [Controlling Software Projects: Management, Measurement, and Estimates](https://isbnsearch.org/isbn/9780131717114):

 > Você não pode controlar aquilo que você não pode medir.
    
Os testes de segurança não são diferentes. Infelizmente, medir segurança é um processo notoriamente complexo.

Um aspecto que deve ser enfatizado é de que as métricas de segurança tratam de questões técnicas específicas (por exemplo, quão predominante uma determinada vulnerabilidade é) e como estas questões impactam os custos do software. A maioria daqueles que possuem conhecimento técnico irão ao menos entender os problemas básicos, ou poderão compreender mais profundamente as vulnerabilidades. Infelizmente, poucos são capazes de traduzir esse conhecimento técnico em termos monetários e quantificar o impacto financeiro que essas vulnerabilidades terão sobre o negócio do proprietário do aplicativo. Até que isso aconteça, os diretores de tecnologia não serão capazes de prever um retorno preciso sobre o investimento em segurança e, consequentemente, dimensionar orçamentos apropriados para segurança de software.

Embora estimar o custo de um software inseguro possa parecer uma tarefa desencorajadoura, uma significativa quantidade de trabalho tem sido empregado nessa direção. Em 2018, o Consórcio para Qualidade de Software de TI [resumiu](https://www.it-cisq.org/the-cost-of-poor-quality-software-in-the-us-a-2018-report/The-Cost-of-Poor-Quality-Software-in-the-US-2018-Report.pdf):

>... o custo de software de baixa qualidade nos EUA em 2018 foi de aproximadamente U$ 2,84 trilhões ...
    
O modelo descrito neste documento incentiva as pessoas a medir a segurança durante todo o processo de desenvolvimento. Eles podem então relacionar o custo de softwares não seguros ao impacto que ele tem no negócio e, em consequência, desenvolver processos de negócios apropriados, atribuindo recursos para gerenciar os riscos. Lembre-se de que medir e testar aplicativos da web é ainda mais crítico do que qualquer outro software, uma vez que os aplicativos web são expostos a milhões de usuários através da Internet.

Muitas coisas precisam ser testadas durante o ciclo de vida do desenvolvimento de um aplicativo web, mas o que testes realmente significam? O Dicionário define "teste" como:

>***teste(substantivo)***: um procedimento destinado a estabelecer a qualidade, o desempenho ou a confiabilidade de algo, especialmente antes de que seja amplamente utilizado.
    

Para os fins deste documento, teste é um processo de comparação do estado de um sistema ou aplicativo perante um conjunto de critérios estabelecidos. No setor de segurança da informação, as pessoas frequentemente testam utilizando-se de um conjunto de critérios mentais que não são nem bem definidos nem completos. Como resultado disso, pessoas de fora do meio consideram testes de segurança uma arte obscura. O objetivo deste documento é mudar essa percepção, tornando mais fácil que pessoas sem conhecimento específicode segurança da informação possam fazer diferença ao testar.

### Por que testar?

Este documento foi criado para ajudar organizações a entender a abrangência de um programa de testes, e ajudá-las a identificar as etapas que devem ser assumidas para se construir e operar um programa de testes de aplicativos web modernos. O guia fornece uma visão ampla dos elementos necessários a um compreensivo programa de segurança de aplicativos web. Este guia pode ser usado como referência e como metodologia na identificação de lacunas entre as práticas existentes e as recomendadas. Este guia permite que as organizações se comparem aos seus pares no setor, para entender a magnitude dos recursos requeridos para testar e manter o software ou ainda se preparar para uma auditoria. Este capítulo não traz detalhes técnicos de como testar um aplicativo, pois a intenção é fornecer um modelo de segurança típico a uma organização. Os detalhes técnicos sobre como testar um aplicativo, como parte de um teste de invasão ou revisão de código, serão cobertos nas partes restantes deste documento.

### Quando testar?

A maioria das pessoas hoje em dia não testa o software até que ele já tenha sido criado e esteja na fase de implantação de seu ciclo de vida ( isto é, o código foi criado e instanciado em um aplicativo web em funcionamento). Esta é geralmente uma prática ineficaz e com custos proibitivos. Um dos melhores métodos para evitar que bugs de segurança apareçam em aplicativos já em ambiente de produção é melhorar o Ciclo de Vida de Desenvolvimento de Software (SDLC), incluindo segurança em cada uma de suas fases. Um SDLC é uma estrutura imposta ao desenvolvimento de artefatos de software. Se o SDLC não estiver sendo usado em seus projetos, é hora de escolher um! A figura a seguir mostra um modelo SDLC genérico, bem como o custo progressivo (estimado) para a correção de bugs de segurança em tal modelo.

![Generic SDLC Model](images/SDLC.jpg)\
*Figura 2-1: Modelo SDLC Genérico*

    
As empresas devem revisar seu ciclo SDLC para garantir que a segurança seja parte integrante do processo de desenvolvimento. Os SDLCs devem incluir testes de segurança para garantir que a segurança seja adequadamente coberta e os controles sejam eficazes durante todo o processo de desenvolvimento.

### O que testar?

É útil pensar no desenvolvimento de software como uma combinação de pessoas, processos e tecnologia. Se esses são os fatores que "criam" o software, é lógico que devam ser esses os fatores a se testar. Hoje porém, a maioria das pessoas testa a tecnologia ou o próprio software.

Um plano de testes efetivo deveria ter os seguinte componentes:
- **Pessoas** –  para garantir que haja educação e conscientização adequadas;
- **Processos** - para garantir que existem políticas e procedimentos adequados e que as pessoas sabem como segui-los;
- **Tecnologia** - para garantir que o processo tenha sido eficiente em sua implementação.

A menos que uma abordagem holística, que busca compreender o todo e não apenas as partes, seja adotada, testar apenas a implementação técnica de um aplicativo não revelará vulnerabilidades operacionais ou de gerenciamento que possam estar presentes. Ao testar pessoas, políticas e processos, uma organização pode detectar problemas que mais tarde se manifestariam em defeitos no produto, erradicando assim os bugs precocemente e identificando as raízes dos defeitos. Da mesma forma, testar apenas alguns dos problemas técnicos resultará em uma avaliação incompleta e imprecisa do estado de segurança do sistema.

Denis Verdon, Chefe de Segurança da Informação da [Fidelity National Financial](https://www.fnf.com/) , apresentou uma excelente analogia para essa concepção equivocada na Conferência OWASP AppSec 2004 em Nova York:

>Se os carros fossem construídos como aplicativos ... os testes de segurança considerariam apenas os impactos frontais. Os carros não seriam testados       em relação ao rolamento ou quanto à estabilidade em manobras de emergência, eficácia do freio, impacto lateral e resistência a roubos.
        
<h2>Como fazer referência a cenários WSTG?</h2>

Cada cenário tem um identificador no formato <code>WSTG-<categoria>-<número></code> , onde: 'categoria' é uma string de 4 caracteres maiúsculos que identifica o tipo de teste ou fragilidade, e 'número' é um valor numérico preenchido com zeros que vai de 01 a 99. Por exemplo: <code>WSTG-INFO-02</code> é o segundo teste de coleta de informações.
  
Os identificadores podem mudar entre as versões, portanto, é preferível que outros documentos, relatórios ou ferramentas usem o formato: <code>WSTG-<versão>-<categoria>-<número></code> , onde: 'versão' é a marca de versão com pontuação removida. Por exemplo: <code>WSTG-v42-INFO-02</code> seria entendido especificamente como o segundo teste de coleta de informações da versão 4.2. 
  
Se os identificadores forem usados sem incluir o <code><versão></code> , eles devem ser considerados como referindo-se ao conteúdo mais recente do Guia de Testes de Segurança Web. Obviamente, conforme o guia cresce e muda, isso se torna problemático, e é por isso que escritores ou desenvolvedores devem incluir o número preciso da versão.
  
### Lincando

Referencias a cenários do Guia de Testes de Segurança Web devem ser feitos usando links com versão não <code>estável</code> ou <code>última</code> que definitivamente mudará com o tempo. No entanto, é intenção da equipe do projeto que os links com versão não mudem. Por exemplo: <code>https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/01-Information_Gathering/02-Fingerprint_Web_Server.html</code>. Observação: a <code>v42</code> se refere à versão 4.2.


### Feedback e comentários

Como em todos os projetos OWASP, agradecemos comentários e feedback. Gostamos especialmente de saber se nosso trabalho está sendo usado e que é eficaz e preciso.

### Princípios de Testes

Há conceitos equivocados quando se busca desenvolver uma metodologia de testes para encontrar bugs de segurança em software. Este capítulo cobre alguns dos princípios básicos os quais profissionais devem levar em consideração ao realizar testes de segurança em software.

### Não há solução mágica
Embora seja tentador pensar que um scanner de segurança ou um firewall fornecerá inúmeras defesas contra ataques e identificará uma infinidade de problemas. Na realidade não existe solução mágica para o problema de softwares inseguros. O software de avaliação de segurança de aplicativos, embora útil como um primeiro passo para encontrar frutas que estão ao alcance da mão, é geralmente imaturo e ineficaz em avaliações aprofundadas. Não fornecem cobertura de testes adequada. Lembre-se de que segurança é um processo e não um produto.
  
### Pense estrategicamente, não taticamente

Os profissionais de segurança perceberam a falácia do modelo patch-and-penetrate, difundido na segurança da informação durante a década de 1990. O modelo patch-and-penetrate envolve a correção de um bug reportado, mas sem a investigação adequada da causa raiz. Este modelo geralmente está associado à janela de vulnerabilidade, também conhecida como janela de exposição, mostrada na figura abaixo. A evolução das vulnerabilidades em softwares comuns usados em todo o mundo tem mostrado a inefetividade desse modelo. Para obter mais informações sobre as janelas de exposição, consulte [Schneier sobre Segurança].
(https://www.schneier.com/crypto-gram/archives/2000/0915.html)

Estudos de vulnerabilidade, como o [Internet Security Threat Report da Symantec](https://www.symantec.com/security-center/threat-report), mostraram que com o tempo de reação dos invasores em todo o mundo, a janela típica de vulnerabilidade não fornece tempo suficiente para a instalação do patch de correção. O tempo entre a descoberta de uma vulnerabilidade  e o desenvolvimento e lançamento de um ataque automatizado contra ela está diminuindo a cada ano.

Existem várias suposições incorretas no modelo patch-and-penetrate. Muitos usuários acreditam que os patches interferem nas operações normais ou podem interromper os aplicativos existentes. Também é incorreto presumir que todos os usuários estão cientes dos patches lançados recentemente. Conseqüentemente, nem todos os usuários de um produto aplicarão patches, seja porque acreditam que o patch pode interferir no funcionamento do software ou porque não têm conhecimento da existência do patch.

![Window of Vulnerability](images/WindowExposure.png)\
<p aling="center">Figura 2-2: Janela de Vulnerabilidade</p>

É essencial incluir segurança no Ciclo de Vida de Desenvolvimento de Software (SDLC) para evitar problemas de segurança recorrentes em um aplicativo. Os desenvolvedores podem agregar segurança no SDLC, desenvolvendo padrões, políticas e diretrizes que se encaixam e funcionam dentro da metodologia de desenvolvimento. A modelagem de ameaças e outras técnicas devem ser usadas para ajudar a designar recursos apropriados para as áreas do software mais vulneráveis.  


### SDLC é soberano

O SDLC é um processo bem conhecido entre os desenvolvedores. Ao integrar a segurança à cada fase do SDLC, possibilita-se uma abordagem holística à segurança do aplicativo que impulsiona os procedimentos já implementados na organização. Fique ciente de que, embora os nomes das várias fases possam mudar dependendo do modelo SDLC usado pela organização, cada fase conceitual do arquétipo SDLC será usada para desenvolver o aplicativo (ou seja, definir, projetar, desenvolver, implementar, manter). Cada fase tem considerações de segurança que devem se tornar parte do processo existente, para garantir um programa de segurança abrangente e economicamente viável.

Existem mútiplos modelos de segurança SDLC que fornecem conselhos descritivos e princíos normativos. O fato de uma pessoa aceitar conselhos descritivos ou princípios normativos, vai depender da maturidade do processo SDLC. Essencialmente, o principio normativo mostra como o SDLC de segurança deve funcionar. Já o conselho descritivo, mostra como ele é usado no mundo real. Ambos tem seu lugar. Por exemplo, se você não sabe por onde começar, um modelo normativo pode fornecer um menu de controles de segurança potenciais que podem ser aplicados no SDLC. O conselho descritivo pode então ajudar a conduzir o processo de decisão, apresentando o que funcionou bem quando aplicado por outras organizações. SDLCs de segurança descritivos incluem BSIMM; e os SDLCs de segurança normativos incluem o [Open Software Assurance Maturity Model](https://www.opensamm.org/) (OpenSAMM), and [ISO/IEC 27034](https://www.iso27001security.com/html/27034.html) Partes 1-7, todas publicadas (exceto a parte 4).


### Test desde o início e teste frequentemente

Quando um bug é detectado no início do SDLC, ele pode ser resolvido mais rapidamente e a um custo menor. Nesse sentido, um bug de segurança não é diferente de um bug funcional ou baseado de performance. Uma etapa importante para tornar isso possível é educar as equipes de desenvolvimento e controle de qualidade sobre problemas comuns de segurança e as maneiras de detectá-los e previní-los. Embora novas bibliotecas, ferramentas ou linguagens possam ajudar a projetar programas que reduzem bugs de segurança, novas ameaças surgem continuamente. Desenvolvedores devem estar cientes das ameaças que afetam o software que estão desenvolvendo. A educação em testes de segurança também ajuda os desenvolvedores a adquirir a mentalidade apropriada para testar um aplicativo assumindo a perspectiva de um invasor. Isso permite que cada organização considere as questões de segurança como parte de suas responsabilidades existentes.

### Automação de Testes

Em metodologias de desenvolvimento modernas, como (mas não se limitando a): ágil, devops / devsecops ou desenvolvimento rápido de aplicativos (RAD), deve-se considerar a integração de testes de segurança à integração / implantação contínua (CI / CD), afim de manter informações / análises em uma linha de base segura e identificar fraquezas do tipo quanto mais fácil melhor.  Isso pode impulsionado por meio de testes de segurança de aplicativos dinâmicos (DAST), testes de segurança de aplicativos estáticos (SAST), e análise de composição de software (SCA) ou ferramentas de rastreamento de dependência - durante fluxos de  implementações automatizadas padrões  ou em com frequências regulares agendadas.


### Entendendo o escopo de segurança

É importante saber o quão seguro um determinado projeto será. Os recursos a serem protegidos devem receber uma classificação que estabeleça o tratamento dado (por exemplo, confidencial, secreto ou ultra-secreto). Discussões com o conselho jurídico devem garantir que quaisquer requisitos de segurança específicos sejam atendidos. Nos EUA, os requisitos podem vir de leis federais, como o
[Gramm-Leach-Bliley Act](https://www.ftc.gov/tips-advice/business-center/privacy-and-security/gramm-leach-bliley-act), ou estaduais, tais como a da Califórnia [California SB-1386](https://leginfo.legislature.ca.gov/faces/billTextClient.xhtml?bill_id=200120020SB1386). Para organizações baseadas na União Européia, regulações específicas de países bem como da União Européia podem ser aplicadas. For example, [Directive 96/46/EC4](https://ec.europa.eu/info/policies/justice-and-fundamental-rights_en) e [Regulation (EU) 2016/679 (General Data Protection Regulation)](https://gdpr-info.eu/) torna obrigatório o cuidado no tratamento de dados pessoais independentemente da aplicação. (Nota do Tradutor: No Brasil, a lei geral de proteção de dados, LGPD, é orientada pela [Lei 13709 de 14 de agosto de 2018](https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm))   

### Desenvolva uma mentalidade adequada

O sucesso de testes de falhas de segurança em um aplicativo requer uma mentalidade fora dos padrões convencionais. Os casos de uso normais testarão o comportamento normal do aplicativo quando um usuário o estiver usando da maneira esperada. Testes de segurança adequados exigem que se vá além do esperado e se pense como um invasor que está tentando violar o aplicativo. O pensamento criativo pode ajudar a determinar quais dados inesperados podem fazer com que um aplicativo tenha falhas de segurança. Também pode ajudar a encontrar quaisquer suposições feitas por desenvolvedores web que nem sempre são verdadeiras e como essas podem ser subvertidas. Um dos motivos pelos quais as ferramentas automatizadas não funcionam bem no teste de vulnerabilidades é justamente porque maquinas não são capazes de pensar de modo criativo. O pensamento criativo deve ser feito caso a caso, pois a maioria dos aplicativos da web está sendo desenvolvida de uma maneira única (mesmo quando usam frameworks comuns).

### Entenda o contexto

Uma das principais iniciativas em qualquer programa de segurança é a necessidade de uma documentação adequada do aplicativo. A arquitetura, os diagramas de fluxo de dados, os casos de uso, etc, devem ser registrados em documentos formais e disponibilizados para revisão. A especificação técnica e os documentos do aplicativo devem incluir informações que listem não apenas os casos de uso desejados, mas também aqueles que devem ser evitados. Por último, é importante se ter pelo menos uma infraestrutura de segurança básica, que permita o monitoramento e padrões de ataques contra os aplicativos e a rede de uma organização (por exemplo, sistemas de detecção de intrusão).

### Use as ferramentas adequadas

Mesmo que tenhamos defendido que não há uma solução mágica, ferramentas tem sim um papel fundamental no programa de segurança como um todo. Existe uma ampla gama de ferramentas comerciais e de código aberto que podem automatizar muitas das rotinas de segurança. Essas ferramentas podem simplificar e acelerar o processo de segurança dando assistencia ao pessoal de segurança em suas tarefas. Entretanto, é importante entender exatamente o que essas ferramentas podem e não podem fazer, para que não se espere mais do que possam oferecer ou sejam usadas incorretamente. 

### O perigo mora nos detalhes

É fundamental não realizar uma análise superficial da segurança de um aplicativo e considerá-la completa. Isso poderá incutir uma falsa sensação de confiança, tão perigosa quanto como se não fosse feita análise alguma. É vital revisar cuidadosamente as descobertas e eliminar quaisquer falsos positivos que possam permanecer no relatório. Relatar uma descoberta de segurança incorreta pode frequentemente prejudicar a mensagem válida do restante de um relatório de segurança. Deve-se tomar cuidado para verificar se todas as caminhos lógicos possíveis do aplicativo foram testadas e se todos os cenários de caso de uso foram explorados para possíveis vulnerabilidades.

### Use o código fonte quando disponível

Embora os resultados de testes de invasão estilo black-box caixa preta pareçam impressionar e seja úteis para demonstrar como as vulnerabilidades são expostas em um ambiente de produção, eles não são a maneira mais eficaz ou eficiente de proteger um aplicativo. Utilizando o teste dinâmico é dificil testar toda a base de código, especialmente se existirem muitas instruções condicionais agrupadas. Se o código-fonte do aplicativo estiver disponível, ele deve ser fornecido à equipe de segurança para ajudá-los durante a revisão. É possível descobrir vulnerabilidades na origem do aplicativo,  que seriam perdidas durante um abordagem black-box.

### Estabeleça métricas

Parte importante de um bom programa de segurança é a capacidade de determinar se as coisas estão melhorando. É importante acompanhar os resultados dos esforços de testes e desenvolver métricas que revelarão as tendências de segurança do aplicativo dentro da organização.

Boas métricas irão demonstrar:

- se mais educação e treinamentos são necessarios;
- se há algum mecanismo de segurança que não está claro para o time de desenvolvimento;
- se o número total de problemas de segurança encontrados está diminuindo.

Métricas consistentes, que podem ser desenvolvidas de maneira automática a partir do código fonte disponível, ajudarão a organização a avaliar com efetividade dos mecanismos introduzidos para reduzir falhas de segurança no desenvolvimento de software. Metricas não são facilmente estabelecidas, portanto, o uso de um padrão, tais como o providos por norma [IEEE](https://ieeexplore.ieee.org/document/237006) é um bom começo.

### Documente os resultados de testes

Para concluir o processo de testes, é importante produzir um registro formal de quais ações de teste foram realizadas, por quem, quando e quais detalhes foram descobertos durante os testes. É aconselhável que se chegue a um acordo sobre um formato aceitável, para o relatório que seja útil para todas as partes interessadas, o que pode incluir desenvolvedores, gerenciamento de projetos, donos do negócio, departamento de TI, auditoria e jurídico.

O relatório deve identificar claramente para o proprietário da empresa onde existem riscos materiais, e fazê-lo de modo suficiente a obter seu apoio para ações de mitigação subsequentes. O relatório também deve ser claro para o desenvolvedor e, ao utilizar uma linguagem que o desenvolvedor entenda, apontar a função exata que é afetada pela vulnerabilidade e as recomendações associadas para resolver problemas. O relatório também deve permitir que outro testador de segurança reproduza os resultados. Escrever o relatório não deve ser excessivamente oneroso para o próprio testador de segurança. Os testadores de segurança, que não são destacados por sua habilidades de redação criativa, não devem incorrer  no erro de gerar um relatório complexo, pois assim levariam a resultados de teste sem a devida documentação. Usar um modelo de relatório de testes de segurança pode economizar tempo e garantir que os resultados sejam documentados de forma precisa, consistente e em um formato adequado para o público.

### Técnicas de teste explicadas

Esta seção apresenta uma visão geral de alto nível de várias técnicas de teste que podem ser empregadas ao construir um programa de testes. Não apresenta metodologias específicas para essas técnicas, já que essas informações são abordadas no capítulo III. Esta seção fornece contexto para a estrutura apresentada no próximo capítulo e destaca as vantagens ou desvantagens de algumas das técnicas que devem ser consideradas . Em particular, cobre:

- Inspeção manual e revisões
- Modelagem de ameaças
- Revisão de código
- Testes de invasão

## Inspeção manual e revisões

### Panorama geral

Manual inspections are human reviews that typically test the security implications of people, policies, and processes. Manual inspections can also include inspection of technology decisions such as architectural designs. They are usually conducted by analyzing documentation or performing interviews with the designers or system owners.

Inspeções manuais são revisões humanas que normalmente testam as implicações de segurança assumidas por pessoas, políticas e processos. As inspeções manuais também podem incluir a revisão de decisões de tecnologia, como os projetos de arquitetura. Elas geralmente são conduzidos analisando a documentação ou realizando entrevistas com os designers ou com os proprietários do sistema.

Embora o conceito de inspeções manuais e revisões humanas possa parecer simples, estão entre as técnicas mais poderosas e eficazes disponíveis. Ao perguntar como algo funciona e por que foi implementado daquele modo, o testador pode determinar rapidamente se alguma das preocupações de segurança esta evidenciada. As inspeções e revisões manuais são uma das poucas maneiras de testar o processo de ciclo de vida de desenvolvimento de software em si e de garantir que haja uma política adequada ou conjunto de habilidades em curso.

Como muitas coisas na vida, ao se conduzir inspeções manuais e revisões humanas, é recomendado que o modelo "confie, mas verifique" seja adotado. Nem tudo que aos testadores é mostrado ou reladado será exato. Revisões manuais são particularmente boas para testar o quanto as pessoas entendem do processo de segurança, se estão cientes das políticas, e possuem as habilidades adequadas para projetar ou implementar aplicações seguras.   

Outras atividades, incluindo a revisão manual da documentação, políticas de codigo seguro, requisitos de segurança e projetos de arquitetura, devem ser realizadas por meio de inspeções manuais.

### Vantagens

- Não requer suporte tecnológico
- Pode ser aplicado a uma variedade de situações
- Flexivel
- Promove o trabalho em equipe (teamwork)
- Implementado no início do SDLC

### Desvantagens

- Pode requerer muito tempo 
- Material de apoio nem sempre disponível
- Requer considerável esforço intelectual e habilidades para ser efetivado

## Modelagem de Ameaças

### Panorama Geral

Modelagem de ameaças têm se tornado uma técnica popular para ajudar arquitetos de sistemas a pensar quais ameaças suas aplicações e sistemas poderão enfrentar. Portanto, a modelagem de ameaças pode ser vista como uma avaliação de risco para aplicativos. Ele permite que o designer desenvolva estratégias de mitigação para vulnerabilidades em potencial e os ajuda a focar, seus innevitaveis escasos recursos e atenção, nas partes do sistema que mais vulneráveis. É recomendável que todos os aplicativos tenham um modelo de ameaça desenvolvido e documentado. Os modelos de ameaça devem ser criados o mais cedo possível no SDLC e devem ser revisados conforme o aplicativo evolui e o desenvolvimento avança.

Para desenvolver um modelo de ameaças recomendamos que seja seguido uma abordagem simples que conforme instrui a norma para avaliação de riscos [NIST 800-30](https://csrc.nist.gov/publications/detail/sp/800-30/rev-1/final). Essa abordagem envolve:

- Decompondo o aplicativo - use um processo de inspeção manual para entender como o aplicativo funciona, seus recursos, funcionalidades e conectividade.
- Definição e classificação dos recursos - classifique os ativos em ativos tangíveis e intangíveis e classifique-os de acordo com a importância de negócio.
- Explorando potenciais vulnerabilidades - sejam elas técnicas, operacionais ou gerenciais.
- Explorando potenciais ameaças - desenvolva uma visão realista dos vetores de ataque em potencial a partir da perspectiva de um invasor, usando para isso cenários de ameaças ou árvores de ataque.
- Criação de estratégias de mitigação - desenvolva controles de mitigação para cada uma das ameaças possíveis.

O resultado de um modelo de ameaça em si pode variar, mas normalmente é uma coleção de listas e diagramas. Vários projetos de código aberto e produtos comerciais oferecem suporte a metodologias de modelagem de ameaças de aplicativos. Esses podem ser usados como referência para testar possíveis falhas de segurança no design do aplicativo. Não existe uma maneira certa ou errada de desenvolver modelos de ameaças e realizar avaliações de risco de informações em aplicativos.

### Vantagens

- Visão do sistema a partir da pesrspectiva do invasor
- Flexivel
- Implementado no início do SDLC

### Desvantagens

- Boa modelagem de ameaças não se traduz automaticamente em bom sotware

## Revisão de Código-Fonte

### Visão geral

A revisão do código-fonte é o processo de verificação manual do código-fonte de um aplicativo web focado em problemas de segurança. Muitas vulnerabilidades de segurança graves não podem ser detectadas com qualquer outra forma de análise ou teste. Como diz o ditado popular, "se você quiser saber o que realmente está acontecendo, vá direto à fonte". Quase todos os especialistas em segurança concordam que não há substituto para o exame do código. Todas as informações para identificar problemas de segurança estão em algum lugar no código. Ao contrário do teste do teste de sistemas fechados, tais como um sistema operacional, ao testar aplicativos web o código-fonte deve ser disponibilizado para fins de teste, especialmente se eles foram desenvolvidos internamente.

Muitos problemas de segurança não intencionais, mas significativos, são extremamente difíceis de descobrir com outras formas de análise ou teste, como o teste de invasão. Isso converte a análise do código-fonte na técnica de escolha para testes técnicos. Com o código-fonte, um testador pode determinar com precisão o que está acontecendo (ou deveria estar acontecendo) e remover as suposições do teste de caixa preta.


Exemplos de problemas propícios a serem encontrados por meio de revisões de código-fonte incluem problemas de simultaneidade, lógica de negócios com falhas, problemas de controle de acesso e fragilidades criptográficas, bem como backdoors, trojans, Easter eggs, bombas-relógio, bombas lógicas e outras formas de código malicioso. Esses problemas geralmente se manifestam como as vulnerabilidades mais danosas em aplicativos web. A análise do código-fonte também pode ser extremamente eficiente para encontrar problemas de implementação, tais como locais onde a validação de valores não foi realizada ou onde procedimentos de controle aberto a falhas, onde o sistema continua operando mesmo em caso de falha, podem estar presentes. Os procedimentos operacionais também precisam ser revistos, uma vez que o código-fonte que está sendo implantado pode não ser o mesmo que está sendo analisado. {[O texto de Ken Thompson's, ganhador do prêmio Turing](https://ia600903.us.archive.org/11/items/pdfy-Qf4sZZSmHKQlHFfw/p761-thompson.pdf) descreve uma possível manifestação desse problema.

### Vantagens 

- Completude e efetividade
- Acurácia
- Rapidez (para revisores competentes)

### Desvantagens

- Requer desenvolvedores capacitados em segurança 
- Pode ignorar problemas em bibliotecas compiladas
- Não pode detectar facilmente erros em tempos de execução
- O código-fonte instalado pode ser difente daquele que está sendo analisado

Para mais informações sobre revisão de código, visite o [OWASP code review project](https://wiki.owasp.org/index.php/Category:OWASP_Code_Review_Project).

## Testes de Invasão

###  Visão geral

O teste de invasão tem sido uma técnica comum usada para testar a segurança da rede há décadas. Também conhecida como hacking ético, o teste de invasão é essencialmente a "arte" de testar um sistema ou aplicativo remotamente para encontrar vulnerabilidades de segurança, sem conhecer o funcionamento interno do próprio alvo, por isso também classificado como teste black box. Normalmente, a equipe de teste de invasão consegue acessar um aplicativo como se fosse um usuário. O testador atua como um invasor e tenta encontrar e explorar vulnerabilidades. Em muitos casos, o testador receberá uma ou mais contas válidas no sistema.

Embora o teste de invasão tenha se mostrado eficaz na segurança de redes, a técnica não se traduz naturalmente em aplicativos. Quando o teste de invasão é executado em redes e sistemas operacionais, a maior parte do trabalho envolvido é encontrar e explorar vulnerabilidades conhecidas em tecnologias específicas. Como os aplicativos web são quase exclusivamente feitos sob medida, os testes de penetração na área de aplicativos web se assemelham mais à pura pesquisa. Algumas ferramentas de teste de invasão automatizadas foram desenvolvidas, mas considerando a natureza personalizada dos aplicativos da web, sua eficácia em si pode ser limitada.

Muitas pessoas usam o teste de invasão de aplicativos web como sua principal técnica de teste de segurança. mesmo que tenha seu lugar em um programa de testes, não acreditamos que ele deva ser considerado a principal ou a única técnica de teste. Como Gary McGraw escreveu em [Software Penetration Testing](https://www.garymcgraw.com/wp-content/uploads/2015/11/bsi6-pentest.pdf), "Na prática, um teste de invasão pode identificar apenas uma pequena amostra representativa de todos os riscos de segurança possíveis em um sistema." No entanto, o teste de invasão focado (ou seja, o teste que tenta explorar vulnerabilidades detectadas em análises anteriores) pode ser útil para detectar se algumas vulnerabilidades específicas foram realmente corrigidas no código implementado.

### Vantagens

- Pode ser rápido (e por isso barato)
- Requer um relativo baixo conhecimento técnico comparado a revisão de código fonte
- Testa o código que está sendo exposto

### Desvantagens

- Ocorre no final do ciclo SDLC
- Cobre apenas testes de "impacto frontal", em uma referencia aos testes de batida frontal de automóveis. Ou seja, ignora ameaças internas e ou outros caminhos outros senão o abuso direto cometido pelo usuario.

## A necessidade de uma abordagem equilibrada

Com tantas técnicas e abordagemns para testar a segurança de aplicativos web, pode ser difícil entender quais técnicas usar e quando. Experiências demonstram que não há resposta certa ou errada a questão de quais técnicas deviriam ser usadas para se construir um modelo de testes. De fato, todas tecnicas deveriam ser usadas para testar todas áreas que precisam ser testadas.
  
Embora seja claro que não há uma técnica isolada que possa ser executada para efetivamente cobrir todo o teste de segurança e garantir que todos os problemas possam ser endereçados, muitas organizações adotam essa técnica. O teste de invasão tem sido essa técnica isolada historicamente usada. O teste de invasão quando usado não pode efetivamente endereçar muitos dos problemas que precisam ser testados. É simplesmente oferecer pouco tarde demais no SDLC   
A aboragem correta é uma equilibrada que inclui várias técnicas, desde revisões manuais até testes técnicos e testes integrados de CI / CD, (integrção e desenvolvimento contínuos). Uma abordagem equilibrada deve abranger o teste em todas as fases do SDLC. Essa abordagem aproveita as técnicas mais adequadas disponíveis, dependendo da fase atual do SDLC.

É evidente que há momentos e circunstâncias em que apenas uma técnica é possível. Por exemplo, considere um teste de um aplicativo web que já foi criado, mas onde a equipe de testes não tem acesso ao código-fonte. Nesse caso, o teste de invasão é claramente melhor do que nenhum teste. No entanto, as partes de teste devem ser encorajadas a desafiar as suposições, como, por exemplo, não ter acesso ao código-fonte, e explorar a possibilidade de testes mais completos.

Uma abordagem equilibrada varia dependendo de muitos fatores, como a maturidade do processo de teste e a cultura corporativa. Recomenda-se que uma estrutura de testes balanceada seja semelhante às representações mostradas na Figura 3 e na Figura 4. A figura a seguir mostra uma representação proporcional típica sobreposta ao SLDC. Em acordo com a pesquisa e a experiência, é essencial que as empresas dêem maior ênfase aos estágios iniciais de desenvolvimento.


![Proporção de Esforço de Tests no SDLC](images/ProportionSDLC.png)\
*Figura 2-3: Proporção de Esforço de Tests no SDLC*

A figura a seguir demonstra a representação proporcional típica sobreposta às tecnicas de teste

![Proporção de Esforço de Testes de acordo com a Técnica de Teste](images/ProportionTest.png)\
*Figura 2-4: Proporção de Esforço de Testes de acordo com a Técnica de Teste*

### Nota sobre scanners de aplicativos web
Muitas organizações passaram a usar scanners automatizados para aplicativos web. Mesmo que eles, indubitavelmente, tenham um lugar em um programa de testes, algumas questões fundamentais precisam ser destacadas sobre por que se acredita que a automação dos testes de caixa preta não é (nem nunca será) completamente eficaz. No entanto, destacar esses problemas não deve desencorajar o uso de scanners de aplicativos web. Em vez disso, o objetivo é garantir que as limitações sejam compreendidas e que os modelos de teste sejam planejadas de forma adequada.

É útil compreender a eficácia e as limitações das ferramentas automatizadas na detecção de vulnerabilidade. Para este fim, o [OWASP Benchmark Project](https://owasp.org/www-project-benchmark/) é um conjunto de testes projetado para avaliar a velocidade, cobertura e precisão de ferramentas e serviços automatizados de detecção de vulnerabilidades de software. O benchmarking pode ajudar a testar os recursos dessas ferramentas automatizadas e ajudar a tornar explícita sua utilidade.

Os exemplos a seguir mostram por que testes automatizados de caixa preta  podem não ser efetivos.

### Exemplo 1: Parâmetetros mágicos

Imagine um aplicativo web simples que aceita um par nome-valor "mágico" e, em seguida, o valor. Para simplificar, a solicitação GET pode ser: `http://www.host/application?magic=value`

Para simplificar ainda mais o exemplo, os valores neste caso só podem ser caracteres ASCII a - z (maiúsculas ou minúsculas) e números inteiros de 0 a 9.

Os designers desse aplicativo criaram uma backdoor administrativa durante o teste, mas a ofuscaram para evitar que o observador casual a descobrisse. Ao enviar o valor sf8g7sfjdsurtsdieerwqredsgnfg8d (30 caracteres), o usuário será então logado e apresentado a uma tela administrativa com total controle do aplicativo. A solicitação HTTP agora é: `http://www.host/application?magic=sf8g7sfjdsurtsdieerwqredsgnfg8d`

Dado que todos os outros parâmetros eram campos simples de dois e três caracteres, não é possível começar a adivinhar combinações a partir de aproximadamente 28 caracteres. Um scanner de aplicativo web precisará aplicar força bruta, adivinhando todo o espaço-chave de 30 caracteres. Isso é até 30 ^ 28 permutações, ou trilhões de solicitações HTTP. Isso é como um elétron em um palheiro digital.

O código para esta verificação do Parâmetro Mágico exemplar pode parecer com o seguinte:

```java
public void doPost( HttpServletRequest request, HttpServletResponse response) {
  String magic = "sf8g7sfjdsurtsdieerwqredsgnfg8d";
  boolean admin = magic.equals( request.getParameter("magic"));
  if (admin) doAdmin( request, response);
  else … // normal processing
}
```
Olhando o código, a vulnerabilidade praticamente salta da página como um problema potencial.

### Exemplo 2: Cryptography fraca

Cryptografia é amplamente utilizada em aplicativos web. Imagine que o desenvolvedor decidiu escrever um algoritimo de criptografia para acesso de usuário do site A para o site B automaticamente. Em sua sabedoria, o desenvolvedor decide que se o usuário estiver conectado no site A, então ele irá gerar uma chave usando uma função de hash MD5 que compreende: `Hash { username : date }`

Quando um usuário é transmitido para o site B, ele enviará a chave na string de consulta para o site B em um redirecionamento HTTP. O Site B calcula o hash independentemente e o compara com o hash transmitido na solicitação. Se corresponderem, o site B conecta o usuário como o usuário que afirma ser.

Conforme o esquema é explicado, as inadequações podem ser resolvidas. Qualquer um que descubra o esquema (ou seja informado como ele funciona, ou ainda baixe as informações do Bugtraq) pode acessar como qualquer usuário. A inspeção manual, como uma revisão ou inspeção de código, teria descoberto esse problema de segurança rapidamente. Um escâner de aplicativo web de caixa preta não teria descoberto a vulnerabilidade. Ele teria visto um hash de 128 bits que mudou com cada usuário e, pela natureza das funções de hash, não mudou de nenhum modo previsível.

### Uma nota sobre ferramentas de revisão de código-fonte estático
Muitas organizações começaram a usar escâners de código-fonte estático. Embora eles, sem dúvida, tenham um lugar em um programa de testes abrangente, é necessário destacar algumas questões fundamentais sobre o por que essa abordagem não é eficaz quando usada isoladamente. A análise estática do código-fonte por si só não consegue identificar problemas devido a falhas no design, uma vez que não consegue entender o contexto em que o código é construído. As ferramentas de análise de código-fonte são úteis em determinamados situações de segurança devido a erros de codificação; no entanto, é necessário um esforço manual significativo para validar as descobertas.

## Obtendo requisitos de testes seguros 

Para ter um programa de teste bem-sucedido, é preciso saber quais são os objetivos do teste. Esses objetivos são especificados pelos requisitos de segurança. Esta seção discute em detalhes como documentar requisitos para testes de segurança obtendo-os  a partir de normas e regulamentações aplicáveis, de requisitos positivos de aplicativo (especificando o que o aplicativo deve fazer) e de requisitos negativos de aplicativo  (especificando o que o aplicativo não deve fazer) . Também discute como os requisitos de segurança conduzem efetivamente os testes de segurança durante o SDLC e como os dados de teste de segurança podem ser usados para gerenciar com eficácia os riscos de segurança do software.

### Objectivos do teste

Um dos objetivos dos testes de segurança é validar se os controles de segurança operam conforme esperado. Isso é documentado por meio de `security requirements` que descrevem a funcionalidade do controle de segurança. Em um mais alto nível, isso significa provar a confidencialidade, integridade e disponibilidade dos dados, bem como do serviço. O outro objetivo é validar se os controles de segurança são implementados com poucas ou nenhuma vulnerabilidades. 

Essas são vulnerabilidades comuns, como o [OWASP Top Ten](https://owasp.org/www-project-top-ten/), bem como vulnerabilidades que foram previamente identificadas por avaliações de segurança durante o SDLC, tais como  modelagem de ameaças, análise de código-fonte e teste de invasão.

### Documento de requisitos de segurança 

A primeira etapa na documentação dos requisitos de segurança é entender os `business requirements` (documento de requisitos) . Um documento de requisitos de negócios pode fornecer informações iniciais de alto nível sobre a funcionalidade esperada do aplicativo. Por exemplo, o objetivo principal de um aplicativo pode ser fornecer serviços financeiros a clientes ou permitir que produtos sejam adquiridos de um catálogo on-line. A seção de segurança dos requisitos de negócios deve destacar a necessidade de proteger os dados do cliente, bem como estar em conformidade com documentação de segurança aplicável, tais como regulamentos, padrões e políticas.

Uma lista de verificação geral dos regulamentos, padrões e políticas aplicáveis é uma boa análise preliminar sobre a conformidade de segurança para aplicativos web. Por exemplo, os regulamentos de conformidade podem ser identificados verificando as informações sobre o setor de negócios e o país ou estado onde o aplicativo irá operar. Algumas dessas diretrizes e regulamentos de conformidade podem se traduzir em requisitos técnicos específicos para controles de segurança. Por exemplo, no caso de aplicativos financeiros, a conformidade com a Federal Financial Institutions Examination Council (FFIEC) [Cybersecurity Assessment Tool & Documentation](https://www.ffiec.gov/cyberassessmenttool.htm) exige que as instituições financeiras implementem aplicativos que atenuem os riscos de autenticação frágil com implementação de controles de segurança multicamadas e autenticação multifator.

Os padrões da indústria aplicáveis à segurança também devem ser capturados pela lista geral de verificação de requisitos de segurança. Por exemplo, no caso de aplicativos que manipulam dados de cartão de crédito do cliente, a conformidade com o PCI Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) Data Security Standard (DSS) proíbe o armazenamento de PINs e dados CVV2 e exige que o estabelecimento proteja os dados da fita magnética no armazenamento e na transmissão com criptografia e as exibiba com mascaramento. Esses requisitos de segurança do PCI DSS podem ser validados por meio da análise do código-fonte.

Outra seção da lista de verificação precisa impor que os requisitos gerais estejam em conformidade com os padrões e políticas de segurança da informação da organização. Da perspectiva dos requisitos funcionais, os requisitos para o controle de segurança precisam ser mapeados por uma seção específica dos padrões de segurança da informação. Um exemplo de tal requisito pode ser: "a complexidade de uma senha de dez caracteres alfanuméricos deve ser imposta pelos controles de autenticação usados pelo aplicativo." Quando os requisitos de segurança são vinculados as regras de conformidade, um teste de segurança pode validar a exposição dos riscos de conformidade. Se forem encontradas violações dos padrões e políticas de segurança da informação, isso resultará em um risco que pode ser documentado e que a empresa deve gerenciar ou solucionar. Como esses requisitos de conformidade de segurança são compulsórios, eles precisam ser bem documentados e validados com testes de segurança.

### Validação de requisitos de segurança

Do ponto de vista da funcionalidade, a validação dos requisitos de segurança é o principal objetivo dos testes de segurança. Do ponto de vista do gerenciamento de risco, a validação dos requisitos de segurança é o objetivo das avaliações de segurança da informação. Em um alto nível, o objetivo principal das avaliações de segurança da informação é a identificação de lacunas nos controles de segurança, como a falta de autenticação básica, autorização ou controles de criptografia. Examinado mais a fundo, o objetivo da avaliação de segurança é a análise de risco, como a identificação de possíveis pontos fracos nos controles de segurança responsáveis por garantir a confidencialidade, a integridade e a disponibilidade dos dados. Por exemplo, quando o aplicativo manipula informações de identificação pessoal (PII) e dados confidenciais, o requisito de segurança a ser validado é a conformidade com a política de segurança de informações da empresa que exige a criptografia de tais dados em trânsito e no armazenamento. Supondo que a criptografia seja usada para proteger os dados, os algoritmos de criptografia e os comprimentos de chaves precisam estar em conformidade com os padrões de criptografia da organização. Isso pode exigir que apenas determinados algoritmos e comprimentos de chaves sejam usados. Por exemplo, um requisito de segurança que pode ser testado é verificar se apenas criptogramas permitidos são usadas (por exemplo, SHA-256, RSA, AES) com comprimentos de chave mínimos permitidos (por exemplo, mais de 128 bits para criptografia simétrico e mais de 1024 para criptografia assimétrico).

Do ponto de vista da avaliação de segurança, os requisitos de segurança podem ser validados em diferentes fases do SDLC usando diferentes artefatos e metodologias de testes. Por exemplo, a modelagem de ameaças concentra-se na identificação de falhas de segurança durante o design; análises e revisões de código seguro se concentram na identificação de problemas de segurança no código-fonte durante o desenvolvimento; e o teste de invasão se concentra na identificação de vulnerabilidades no aplicativo durante o teste ou validação.

Os problemas de segurança que são identificados no início do SDLC podem ser documentados em um plano de testes, assim podem ser validados posteriormente com testes de segurança. Ao combinar os resultados de diferentes técnicas de testes, é possível gerar melhores casos de testes de segurança e aumentar o nível de garantia dos requisitos de segurança. Por exemplo, distinguir vulnerabilidades reais das não exploradas é possível quando os resultados dos testes de invasão e da análise do código-fonte são combinados. Considerando o teste de segurança para uma vulnerabilidade de injeção SQL, por exemplo, um teste de caixa preta pode primeiro envolver uma varredura do aplicativo para identificar a vulnerabilidade. A primeira evidência de uma potencial vulnerabilidade de injeção SQL que pode ser validada é a geração de uma exceção SQL. Uma validação adicional da vulnerabilidade SQL pode envolver a injeção manual de vetores de ataque para modificar a gramática de consulta SQL explorando assim a divulgação de informações. Isso pode envolver muitas análises de tentativa e erro antes que a consulta maliciosa seja executada. Supondo que o testador tenha acesso ao código-fonte, ele podem aprender diretamente com a análise do código-fonte como construir o vetor de ataque SQL que explorará com sucesso a vulnerabilidade (por exemplo, executar uma consulta maliciosa retornando dados confidenciais para o usuário não autorizado). Isso pode acelerar a validação da vulnerabilidade SQL.

### Sistema de classificação de ameaças e contramedidas

A `classificação de ameaças e contramedidas`, que leva em consideração causas raíz das vulnerabilidades, é o fator crítico para verificar se os controles de segurança são projetados, codificados e construídos para mitigar o impacto da exposição de tais vulnerabilidades. No caso de aplicativos web, a exposição dos controles de segurança a vulnerabilidades comuns, como o OWASP Top Ten, pode ser um bom ponto de partida para derivar os requisitos gerais de segurança. A [Lista de Verificação do Guia de Testes OWASP](https://github.com/OWASP/wstg/tree/master/checklist) é um recurso útil para orientar os testadores quanto a vulnerabilidades específicas e testes de validação.

O foco da categorização de ameaças e contramedidas é definir os requisitos de segurança em termos das ameaças e da causa raiz da vulnerabilidade. Uma ameaça pode ser categorizada usando [STRIDE](https://en.wikipedia.org/wiki/STRIDE_(security)) , um acrônimo para spoofing, adulteração, repúdio, divulgação de informações, negação de serviço e elevação de privilégio. A causa raiz pode ser categorizada como falha de segurança no design, um bug de segurança na codificação ou um problema devido à configuração insegura. Por exemplo, a causa raiz da vulnerabilidade de autenticação fraca pode ser a falta de autenticação mútua quando os dados cruzam um limite de confiança entre as camadas de cliente e servidor do aplicativo. Um requisito de segurança que captura a ameaça de não repúdio durante uma revisão do projeto de arquitetura permite a documentação do requisito para a contramedida (por exemplo, autenticação mútua) que pode ser validada posteriormente com testes de segurança.

Uma categorização de ameaças e contramedidas para vulnerabilidades também pode ser usada para documentar requisitos de segurança para codificação segura, como padrões de codificação seguros. Um exemplo de erro comum de codificação em controles de autenticação consiste em aplicar uma função hash para criptografar uma senha, sem aplicar uma chave de recuperação ao valor. Do ponto de vista da codificação segura, esta é uma vulnerabilidade que afeta a criptografia usada para autenticação tendo como causa raiz da vulnerabilidade um erro de codificação. Como a causa raiz é a codificação insegura, o requisito de segurança pode ser documentado em padrões de codificação segura e validado por meio de revisões de código seguro durante a fase de desenvolvimento do SDLC.

### Testes de segurança e análise de risco

Os requisitos de segurança precisam levar em consideração a severidade das vulnerabilidades para oferecer suporte a uma `estratégia de mitigação de riscos` . Supondo que a organização mantenha um repositório de vulnerabilidades encontradas em aplicativos (ou seja, uma base de conhecimento de vulnerabilidade), os problemas de segurança podem ser relatados por tipo, problema, mitigação, causa raiz e mapeadosa partir dos aplicativos onde são encontrados. Essa base de conhecimento de vulnerabilidade também pode ser usada para estabelecer métricas para analisar a eficácia dos testes de segurança em todo o SDLC.

Por exemplo, considere um problema de validação de entrada de dados, como a injeção SQL, que foi identificada por meio de análise de código-fonte e relatada com causa raiz o erro de codificação e tipo de vulnerabilidade de validação de entrada de dados. A exposição de tal vulnerabilidade pode ser avaliada por meio de um teste de invasão, investigando campos de entrada com vários vetores de ataque de injeção SQL. Este teste pode validar que os caracteres especiais são filtrados antes de atingir o banco de dados e atenuar a vulnerabilidade. Ao combinar os resultados da análise do código-fonte e do teste de penetração, é possível determinar a probabilidade de exposição e calcular a classificação de risco da vulnerabilidade. Ao relatar as classificações de risco de vulnerabilidade nas descobertas (por exemplo, relatório de testes), é possível decidir sobre a estratégia de mitigação. Por exemplo, vulnerabilidades de alto e médio risco podem ser priorizadas para correção, enquanto vulnerabilidades de baixo risco podem ser corrigidas em versões futuras.

Considerando os cenarios de riscos ao explorar vulnerabilidades comuns, é possivel identificar potenciais riscos os quais o controle de segurança do aplicativo precisa verificar usando testes de segurança. Por exemplo, as vulnerabilidades do OWASP Top Ten podem mapear attaques tais como pishing, violação de privacidade, roubo de identidade, comprometimento do sistema, alteração ou destruição de dados, perda financeira, e perda de reputação.
Tais problemas devem ser documentados como parte dos cenários de ameaça. Pensando em termos de ameaças e vulnerabilidades, é possível conceber uma bateria de testes que simulam esses cenários de ataque. Idealmente, a base de conhecimento de vulnerabilidade da organização pode ser usada para derivar casos de teste orientados a riscos de segurança, e validar os cenários de ataque mais prováveis. Por exemplo, se o roubo de identidade for considerado de alto risco, os cenários de teste negativos devem validar a mitigação dos impactos derivados da exploração de vulnerabilidades na autenticação, controles criptográficos, validação de entrada de dados e controles de autorização.

## Obtendo testes de requisitos funcionais e não-funcionais

### Requisitos funcionais  de segurança

De uma perspectiva de requisitos de segurança funcionais, as normas aplicáveis, políticas, e regulações orientam ambas a necessidade de um algum controle de segurança bem como o controle da funcionalidade. 
Esses requisitos são também referidos como “requisitos positivos”, já que eles implicam que a funcionalidade esperada pode ser validada através de testes de segurança. Exemplos de requisitos positivos são: “o aplicativo irá bloquear usuário depois de seis tentativas erradas de autenticação “ou “senhas precisam ter um mínimo de dez caracteres alfanuméricos”. A validação de requisitos positivos consiste na validação de funcionalidades esperadas e pode ser testada através da recriação das condições de teste e execução do teste de acordo com entradas de dados pré-definidas. Os resultados são apresentados. então como passados ou falhados. 

A fim de validar requisitos de segurança a partir de testes de segurança, requisitos de segurança precisam ser orientados por funções. Eles precisam enfatizar a funcionalidade esperada (o quê) e sugerir a implementação (o como). 

Exemplos de requisitos de design de segurança de alto nível para autenticação podem ser:
-	Proteger as credenciais e dados sigilosos do usuário quando da transmissão ou armazenamento.  
-       Mascarar a exibição de qualquer dado confidencial (por exemplo, senhas, e contas de usuário).
-	Bloquear a conta do usuário após um certo número de tentativas falhadas de autenticação.
- 	Não apresentar ao usuário nenhum erro de validação específico como resultado de tentativas falhadas de autenticação. 
-	Permitir apenas senhas que são alfanuméricas, incluem caracteres especiais, e tem no mínimo dez caracteres de extensão, para limitar os ataques de superfície.
-	Permitir a funcionalidade de troca da senha através da validação da senha antiga, da nova senha, e da resposta do usuário à pergunta de segurança, para prevenir ataques de força bruta por meio da troca de senha.  	
-	O formulário de revalidação da senha deve validar o nome de usuário e o e-mail registrado antes de mandar uma senha temporária ao usuário por e-mail. A senha temporária gerada deve ser uma senha de uso único. O link para a redefinição da senha será enviado ao usuário. A página de redefinição da senha deve validar a senha temporária do usuário, a nova senha, bem como a resposta do usuário a resposta à pergunta de segurança. 

### Requisitos de Segurança orientado a riscos

Testes de segurança precisam também ser orientados a riscos. Eles precisam validar o aplicativo a partir de comportamentos inesperados ou requisitos negativos.
Exemplos de requisitos negativos são:
•	O aplicativo não deve permitir que os dados sejam alterados ou destruídos. 
•	O aplicativo não deve comprometer ou usar de modo indevido autorizações financeiras iniciadas por usuário mal intencionado. 
Requisitos negativos são mais difíceis de serem testados pois não há um comportamento esperado a se comparar. Buscar comportamentos esperados que se adequem aos requisitos acima, pode requerer que a análise de ameaças forneça de modo irreal condições imprevisíveis de causas efeitos e dados de entrada. 
Portanto, testes de segurança precisam ser orientados por análise de riscos e modelagem de ameaças. A chave para isso está na documentação dos cenários de ameaças, e a funcionalidade de medidas preventivas para mitigar as ameaças. 
Por exemplo, no caso de controles de autenticação, os requisitos de segurança podem ser documentados a partir da perspectiva de ameaças e medidas preventivas do seguinte modo: 
•	Senhas criptografadas usando criptografia não reversível tais como Digest (HASH), e chaves para prevenir dicionários de ataques. 
•	Bloqueios de contas ao alcançarem o log de limite de falhas ou forçar a complexidade de senha para mitigar o risco de ataques de força bruta. 
•	Exibir mensagens genéricas de erro quando da validação de credenciais para mitigar o risco de coleta de contas ou contagem. 
•	Mutualmente autenticar cliente servidor para prevenir o não repúdio e o ataque ”Man In the Middle” (MiTM).
Ferramentas de modelagem de ameaças tais como as árvores de ameaça e as livrarias de ataque podem ser úteis para se se extrair os cenários de testes negativos. As árvores de ameaças assumirão o ataque base, por exemplo, o invasor pode estar apto a ler mensagens de outros usuários, e identificar diferentes vulnerabilidades de controle de segurança, por exemplo falhas de validação de dados causados pela vulnerabilidade de injeção SQL, e medidas preventivas necessárias (por exemplo, implementar validação de dados e parametrização de consultas SQL), que poderiam ser validadas e efetivamente mitigar tais ataques.

### Obtendo requisitos de testes de segurança através de casos de uso e uso indevido
	
Um pré-requisito para descrever o funcionamento do aplicativo é entender o que o aplicativo deve fazer e como. Isso pode ser descrito através de casos de uso. Os casos de uso, na forma gráfica comumente usada na engenharia de software, mostram as interações dos atores e suas interações. Eles ajudam a identificar os atores no aplicativo, seus relacionamentos, a sequência de ações pretendida para cada cenário, ações alternativas, requisitos especiais, pré-condições e pós-condições.

Semelhante aos casos de uso, os casos de uso indevido ou de abuso descrevem cenários de uso não intencionados e mal-intencionados do aplicativo. Esses casos de uso indevido fornecem uma maneira de descrever cenários que explicam como um invasor pode usar indevidamente o aplicativo. Ao percorrer os passos individuais em um cenário de uso e pensar sobre como ele pode ser explorado de forma mal-intencionada, é possível descobrir possíveis falhas ou aspectos do aplicativo que não estão bem definidos. A chave é descrever todos os cenários possíveis de uso ou uso mal indevido ou, pelo menos, os mais críticos.

Os cenários de uso indevido permitem a análise do aplicativo do ponto de vista do invasor e contribuem para a identificação de vulnerabilidades em potencial, bem como análise de medidas preventivas para mitigar o impacto causado pela exposição potencial a tais vulnerabilidades. Considerando todos os casos de uso e abuso, é importante analisá-los para determinar quais são os mais críticos e quais precisam ser documentados nos requisitos de segurança. A identificação dos casos mais críticos de uso indevido e abuso orientam a documentação dos requisitos de segurança e os controles necessários para que os riscos à segurança sejam mitigados.

Para derivar os requisitos de segurança de [casos de uso e mau uso] ](https://iacis.org/iis/2006/Damodaran.pdf) , é importante definir os cenários funcionais e os cenários negativos e colocá-los em forma gráfica. O exemplo a seguir é uma metodologia passo a passo de como derivar requisitos de segurança para autenticação.


### Passo 1: Descreve o cenário funcional 

O usuário se autentica fornecendo um nome de usuário e uma senha. O aplicativo concede acesso aos usuários com base na autenticação das credenciais do usuário pelo aplicativo e fornece erros específicos ao usuário quando a autenticação falha.

### Passo 2: Descreve o cenário negativo

O invasor quebra a autenticação por meio de um ataque de força bruta ou de dicionário de senhas, bem como através da coleta de contas vulneráveis no aplicativo. Os erros de validação fornecem informações específicas a um invasor que são usadas para adivinhar quais contas são registradas e válidas (nomes de usuário). O invasor então tenta usar força bruta na senha de uma conta válida. Um ataque de força bruta a senhas com comprimento mínimo de quatro dígitos pode ser bem-sucedido com um número limitado de tentativas (ou seja, 10 ^ 4, ou 10000 tentativas).

### Passo 3: Descreve cenários funcionais e negativos de caso de uso e uso indevido

O exemplo gráfico abaixo descreve a derivação dos requisitos de segurança por meio de casos de uso e uso indevido. O cenário funcional consiste nas ações do usuário (inserir um nome de usuário e senha) e nas ações do aplicativo (autenticar o usuário e fornecer uma mensagem de erro se a validação falhar). O caso de uso incorreto consiste nas ações do invasor, isto é, tentar quebrar a autenticação por força bruta da senha através de um ataque de dicionário e adivinhar nomes de usuário válidos a partir de mensagens de erro. Ao representar graficamente as ameaças às ações do usuário (uso indevido), é possível derivar medidas preventivas como as ações do aplicativo que mitigam tais ameaças. 

![Caso de uso e uso indevido](images/640px-UseAndMisuseCase.png)\
*Figura 2-5: Caso de Uso e Uso Indevido *

### Passo 4: Extraia os requisitos de segurança

Nesse caso os seguintes requisitos de segurança para autenticação são derivados:

1. Requisitos de senha precisam estar alinhados com os padrões suficientes de complexidade vigentes. 
2. Contas precisam ser bloqueadas após cinco tentativas frustradas de acesso. 
3. Mensagens de erro de acesso precisam ser genéricas. 

Esses requisitos de segurança precisam ser documentados e testados. 
  
## Testes de Segurança Integrados ao fluxo de Desenvolvimento e  Testes 

### Testes de segurança no fluxo(workflow) de desenvlvimento

Os testes de segurança durante a fase de desenvolvimento no SDLC, representam a primeira oportunidade para desenvolvedores garantir que componentes individuais do software, (desenvolvidos por eles), são testados, em termos de segurança, antes de serem integrados com a outros componentes do aplicativo. 
Componentes de software são compostos por artefatos tais como funções, métodos, e classes, assim como interface de programação de aplicativos (API), bibliotecas e arquivos executáveis Para testes de segurança, desenvolvedores podem apoiar-se nos resultados de análise de código fonte para verificar estatisticamente que o código fonte desenvolvido não inclui nenhuma vulnerabilidade potencial e está em conformidade com os padrões de segurança de código. Testes unitários de segurança podem além do mais verificar dinamicamente, isto é, em tempo de execução, que os componentes funcionam conforme esperado. Antes de integrar ambos os códigos modificados, novo e existente, no código do aplicativo, os resultados de análises estática e dinâmica devem ser revisados e validados.    

A validação do código fonte antes da integração no aplicativo é comumente responsabilidades dos desenvolvedores seniores. Desenvolvedores seniores são frequentemente os que entendem mais de segurança de software e sua tarefa é liderar um a revisão de segurança do código. Eles precisam decidir entre aceitar o código a ser instalado no aplicativo ou solicitar novas mudanças e testes. Esta revisão do fluxo de código seguro pode ser determinada via aceitação formal, bem como pela verificação na ferramenta de fluxo do projeto. Por exemplo, assumindo que o fluxo de gerenciamento comumente usado para defeitos funcionais, defeitos de segurança que foram corrigidos por um desenvolvedor podem ser reportados em um sistema de gerenciamento de defeitos ou mudanças. O repositório master  pode verificar o resultado de testes reportados por desenvolvedores na ferramenta e garantir a integração de códigos modificados no código da aplicação (“merge on máster”).   


### Testes de segurança no workflow de testes

Depois que componentes e alterações de código são testados pelos desenvolvedores e verificados na integração com o código do aplicativo, a próxima etapa mais provável no fluxo de trabalho(workflow) do processo de desenvolvimento de software é realizar testes no aplicativo como um todo (end-to-end). Este nível de testes é  também referido como teste integração e teste a nível de sistema. Quando os testes de segurança fazem parte dessas atividade,  eles podem ser usados para validar a funcionalidade de segurança do aplicativo como um todo, bem como a exposição de vulnerabilidades no nível do aplicativo. Esses testes de segurança no aplicativo incluem testes de caixa branca, como análise de código-fonte, e testes de caixa preta, como teste de invasão. Os testes também podem incluir testes de caixa cinza, nos quais se presume que o testador tenha algum conhecimento parcial sobre o aplicativo. Por exemplo, com algum conhecimento sobre o gerenciamento de sessão do aplicativo, o testador pode entender melhor se as funções de logout e tempo limite estão devidamente protegidas.

O alvo dos testes de segurança é todo o sistema que está vulnerável a ataques. Durante esta fase, os testadores de segurança podem determinar se as vulnerabilidades podem ser exploradas. Isso inclui vulnerabilidades comuns de aplicativos web, bem como problemas de segurança que foram identificados anteriormente no SDLC com outras atividades, tais como a modelagem de ameaças, a análise de código-fonte e  as revisões de segurança no código.

Normalmente, os engenheiros de testes, e não os desenvolvedores, realizam testes de segurança quando o aplicativo está na fase de testes de integração. Os engenheiros de teste têm conhecimento de vulnerabilidades de segurança de aplicativos web, técnicas de teste de caixa preta e caixa branca e são responsáveis pela validação dos requisitos de segurança nesta fase. Para realizar testes de segurança, é um pré-requisito que os casos de teste de segurança sejam documentados nas diretrizes e procedimentos de teste de segurança.

Um engenheiro de testes que valida a segurança do aplicativo no ambiente de sistema integrado pode liberar o aplicativo para testes no ambiente operacional (por exemplo, testes de aceitação do usuário). Nesse estágio do SDLC (ou seja, validação), o teste funcional do aplicativo geralmente é responsabilidade dos testadores do time de controle de qualidade, enquanto hackers de chapéu branco(white hat) ou consultores de segurança geralmente são responsáveis pelos testes de segurança. Algumas organizações contam com sua própria equipe especializada de hackers éticos para conduzir esses testes ao passo que uma avaliação de terceiros não é necessária (a não ser para fins de auditoria).

Como esses testes às vezes podem ser a última linha de defesa para correção de vulnerabilidades antes que o aplicativo seja lançado em ambiente de produção, é importante que os problemas sejam resolvidos conforme recomendado pela equipe de testes. As recomendações podem incluir alteração de código, design ou configuração. Nesse nível, os auditores de segurança e demais responsáveis de segurança da informação discutem os problemas relatados e analisam os riscos potenciais de acordo com os procedimentos de gerenciamento de riscos da informação. Esses procedimentos podem exigir que a equipe de desenvolvimento conserte todas as vulnerabilidades de alto risco antes que o aplicativo possa ser implantado, a menos que tais riscos sejam reconhecidos e aceitos.

## Testes de segurança do desenvolvedor

### Testes de segurança na fase de codificação: Testes Unitários

Do ponto de vista do desenvolvedor, o principal objetivo dos testes de segurança é validar se o código está sendo desenvolvido em conformidade com os requisitos dos padrões de codigo seguro. Os próprios artefatos de codificação dos desenvolvedores (como funções, métodos, classes, APIs e bibliotecas) precisam ser funcionalmente validados antes de serem integrados ao código do aplicativo.

Os requisitos de segurança que os desenvolvedores precisam seguir devem ser documentados em padrões de código seguro e validados com análises estáticas e dinâmicas. Se os testes unitários seguem uma revisão de código segura, os testes de unidade podem validar se as alterações exigidas por revisões de código seguro foram implementadas corretamente. Tanto as revisões seguras quanto a análise de código-fonte, por meio de ferramentas de análise de código-fonte, podem ajudar os desenvolvedores a identificar problemas de segurança no código-fonte, à medida que ele é desenvolvido. Usando testes de unidade e análise dinâmica (por exemplo, depuração), os desenvolvedores podem validar a segurança das funcionalidades, bem como verificar se as medidas preventivas sendo desenvolvidas endereçam quaisquer riscos de segurança previamente identificados por meio da modelagem de ameaças e análise de código-fonte.

Uma boa prática para desenvolvedores é construir casos de testes de segurança como um conjunto de testes genéricos que faz parte da estrutura de teste de unidade já existente. Um conjunto de testes de segurança genérico pode ser derivado de casos de uso e de casos uso indevido previamente definidos para funções, métodos e classes de teste de segurança. Um conjunto de testes de segurança genérico pode incluir casos de teste de segurança para validar tando requisitos positivos quanto negativos para controles de segurança, tais como:


- Identidade, autenticação e controle de acesso
- Validação de entrada de dados e máscaras
- Criptografia 
- Gerenciamento de Usuário e de sessão
- Tratamento de erros e de exceções
- Auditoria e controle de registros (logging)

Os desenvolvedores dotados de uma ferramenta de análise de código-fonte integrada ao seu IDE de desenvolvimento, bem como por padrões de codificação seguros e por uma estrutura de teste de unidade de segurança, podem avaliar e verificar a segurança dos componentes de software que estão sendo desenvolvidos. Casos de teste de segurança podem ser executados para identificar possíveis problemas de segurança que têm causas raízes no código-fonte: além da validação de entrada e saída de parâmetros que entram e saem dos componentes, esses problemas incluem verificações de autenticação e autorização feitas pelo componente, proteção dos dados do componente, exceção segura e tratamento de erros e ainda auditoria e controle de registros seguros. Estruturas de teste de unidade, como JUnit, NUnit e CUnit, podem ser adaptadas para verificar os requisitos de teste de segurança. No caso de testes funcionais de segurança, os testes unitários podem testar a funcionalidade dos controles de segurança no nível do componente de software, como funções, métodos ou classes. Por exemplo, um caso de teste pode validar a entrada e saída de dados (por exemplo, *variable sanitation*) e verificar valores limites para variáveis, validando a funcionalidade esperada do componente.

Os cenários de ameaças, identificados através de casos de uso e uso indevido, podem ser usados para documentar os procedimentos de testes de componentes de software. No caso de componentes de autenticação, por exemplo, os testes de unidade de segurança podem validar a funcionalidade de bloqueio de conta, bem como os dados de entrada do usuário não podem ser usados para contornar o bloqueio da conta (por exemplo, definindo o contador de bloqueio da conta para um número negativo).

No nível de componentes, os testes de unidade de segurança podem confirmar validações positivas, bem como negativas, como erros e tratamento de exceções. As exceções devem ser detectadas sem deixar o sistema exposto, tal como potencial negação de serviço (Denial of Service) causada por recursos não sendo desalocados (por exemplo, identificadores de conexão não fechados dentro de um bloco de instrução final). Também a potencial elevação de privilégios (por exemplo , privilégios mais altos adquiridos antes da exceção ser lançada e não redefinidos para o nível anterior antes da saída da função). O tratamento seguro de erros pode validar a divulgação de informações em potencial por meio de mensagens de erro informativas e  conteúdo de pilha (stack trace).

Casos de teste de segurança unitários podem ser desenvolvidos por um engenheiro de segurança que é o especialista no assunto. Também é o responsável por validar se os problemas de segurança no código-fonte foram corrigidos e podem ser verificados na construção do sistema integrado. Normalmente, o gerenciador das versões do aplicativo também garante que as bibliotecas de terceiros e os arquivos executáveis sejam avaliados quanto à segurança para possíveis vulnerabilidades antes de serem integrados a versão compilada do aplicativo.

Cenários de ameaças para vulnerabilidades comuns, que têm causas básicas em códigos vulnerável também podem ser documentados no guia de testes de segurança do desenvolvedor. Quando uma correção é implementada para um defeito de codificação identificado com a análise do código-fonte, por exemplo, os casos de teste de segurança podem verificar se a implementação da alteração de código segue os requisitos de codificação segura documentados nos padrões de código seguro.

A análise do código-fonte e os testes unitários podem validar se a alteração do código atenua a vulnerabilidade exposta pelo defeito de código identificado anteriormente. Os resultados da análise de código seguro automatizada também podem ser usados como portas de check-in automático para controle de versão, por exemplo, artefatos de software não podem ser verificados na versão de código com problemas de codificação de severidade alta ou média.

### Testes de segurança de testadores funcionais

#### Testes de segurança durante a fase de integração e validação: Testes de Integração e Operação  do Sistema 

O objetivo principal dos testes de integração do sistema é validar o conceito de "defesa em profundidade", ou seja, que a implementação de controles de segurança proporcione segurança em diferentes camadas. Por exemplo, a falta de validação de entrada de dados na chamada de um componente integrado ao aplicativo é frequentemente um fator que pode ser testado com o teste de integração.

O ambiente de testes do sistema de integração também é o primeiro ambiente onde os testadores podem simular cenários reais de ataque que podem ser executados por um usuário mal intencionado, externo ou interno do aplicativo. Os testes de segurança neste nível podem validar se as vulnerabilidades são reais e se podem ser exploradas por invasores. Por exemplo, uma vulnerabilidade potencial encontrada no código-fonte pode ser classificada como de alto risco por causa da exposição a usuários mal-intencionados em potencial, bem como por causa do impacto potencial (por exemplo, acesso a informações confidenciais).

Cenários reais de ataque podem ser testados ambos por técnicas de teste manual ou ferramentas de testes de invasão. Os testes de segurança desse tipo também são chamados de testes de hacking ético. Do ponto de vista dos testes de segurança, são testes orientados a riscos e têm o objetivo de testar a aplicação no ambiente operacional. O destino é a construção do aplicativo que representa a versão do aplicativo que está sendo implementado em produção.

Incluir testes de segurança na fase de integração e validação é fundamental para identificar vulnerabilidades devido à integração de componentes, assim como validar a exposição de tais vulnerabilidades. O teste de segurança de aplicativos requer um conjunto especializado de habilidades, incluindo conhecimento simultâneo  do software e de segurança, que não são típicas dos engenheiros de segurança. Como resultado, as organizações frequentemente precisam treinar seus desenvolvedores de software em técnicas de hacking ético, e também em procedimentos e ferramentas de avaliação de segurança. Um cenário realista é desenvolver esses recursos internamente e documentá-los em guias e procedimentos de testes de segurança que levam em consideração o conhecimento do desenvolvedor. A chamada "lista de verificação de casos de teste de segurança", por exemplo, pode fornecer casos de testes simples e vetores de ataque que podem ser usados por testadores para validar a exposição a vulnerabilidades comuns, como falsificação, divulgação de informações, estouro de buffer, strings de formato, injeção SQL e injeção de XSS, XML, SOAP, problemas de canonicalização, negação de serviço e código gerenciado e controles ActiveX (por exemplo, .NET). Uma primeira bateria desses testes pode ser realizada manualmente com um conhecimento muito básico de segurança de software.

O primeiro objetivo dos testes de segurança pode ser a validação de um conjunto de requisitos mínimos de segurança. Esses casos de teste de segurança podem consistir em forçar manualmente o aplicativo a erro e estados de exceção para obter conhecimento do comportamento seu comportamento. Por exemplo, as vulnerabilidades de injeção SQL podem ser testadas manualmente, injetando vetores de ataque por meio da entrada do usuário e verificando se as exceções de SQL são devolvidas ao usuário. A evidência de um erro de exceção SQL pode ser uma manifestação de uma vulnerabilidade a ser explorada.

Um teste de segurança mais profundado pode exigir o conhecimento de técnicas e ferramentas de teste especializadas pelo testador. Além da análise de código-fonte e testes de invasão, essas técnicas incluem, por exemplo: código-fonte e injeção de falhas binárias, análise de propagação de falhas e cobertura de código, testes de difusão e engenharia reversa. O guia de testes de segurança deve fornecer procedimentos e ferramentas recomendadas que podem ser usadas por testadores de segurança para realizar tais avaliações de segurança com maior profundidade.

O próximo nível de teste de segurança após os testes do sistema de integração é a realização de testes de segurança no ambiente de aceitação do usuário. Existem vantagens exclusivas em realizar testes de segurança no ambiente operacional. O ambiente de testes de aceitação do usuário (UAT) é o mais representativo da configuração da versão, com exceção dos dados (por exemplo, os dados de testes são usados no lugar dos dados reais). Uma característica dos testes de segurança em UAT é o teste de problemas de configuração de segurança. Em alguns casos, essas vulnerabilidades podem representar riscos elevados. Por exemplo, o servidor que hospeda o aplicativo web pode não estar configurado com privilégios mínimos, certificado SSL válido e configuração segura, serviços essenciais desabilitados e diretório web raiz limpo de páginas de teste e administrativas.

## Testes de Segurança - Análise de dados e relatórios

### Objetivos de métricas e medições para testes de segurança

Definir objetivos para as métricas e medições de testes de segurança é um pré-requisito para o uso de dados de testes de segurança para análise de risco e processos de gerenciamento. Por exemplo, uma medida, como o número total de vulnerabilidades encontradas a partir de testes de segurança, pode quantificar a abordagem de segurança do aplicativo. Essas medições também ajudam a identificar os objetivos de segurança para testes de segurança de software, por exemplo, reduzindo o número de vulnerabilidades a um número mínimo aceitável antes que o aplicativo seja implantado em produção.

Outro objetivo gerenciável pode ser comparar a postura de segurança do aplicativo em relação a uma linha base inicial,  para avaliar as melhorias nos processos de segurança do aplicativo. Por exemplo, a linha base das métricas de segurança pode consistir em um aplicativo que foi testado apenas com testes de invasão. Os dados de segurança obtidos de um aplicativo que também foi testado, do ponto de vista da  segurança, durante a codificação devem mostrar uma melhoria (por exemplo, menos vulnerabilidades) quando comparados a linha base.

Em testes de software tradicionais, o número de defeitos de software, como os bugs encontrados em um aplicativo, podem fornecer uma medida da qualidade do software. Da mesma forma, o teste de segurança pode fornecer uma medida de segurança do software. Do ponto de vista do gerenciamento de defeitos e relatórios, os testes de segurança e qualidade de software podem usar categorizações semelhantes para causa raiz e esforços de correção de defeitos. Do ponto de vista da causa raiz, um defeito de segurança pode ser devido a um erro no design (por exemplo, falhas de segurança) ou devido a um erro na codificação (por exemplo, bug de segurança). Da perspectiva do esforço necessário para corrigir um defeito, os defeitos de segurança e de qualidade podem ser medidos em termos de horas utilizadas pelo desenvolvedor para implementar a correção, as ferramentas e recursos necessários e o custo para implementar a correção.

Uma característica dos dados de testes de segurança, em comparação com os dados de qualidade, é a categorização em termos de ameaça, da exposição da vulnerabilidade e do impacto potencial da vulnerabilidade para determinar o risco. O teste de segurança dos aplicativos consiste em gerenciar riscos técnicos para garantir que as medidas preventivas do aplicativo atinjam níveis aceitáveis. Por esse motivo, os dados de testes de segurança precisam apoiar a estratégia de risco de segurança em pontos de verificação críticos durante o SDLC. Por exemplo, as vulnerabilidades encontradas com a análise do código-fonte representam uma medida inicial de risco. Uma medida de risco para a vulnerabilidade (por exemplo, alto, médio, baixo),  pode ser calculada determinando os fatores de exposição e probabilidade e validando a vulnerabilidade com testes de invasão. As métricas de risco, associadas às vulnerabilidades encontradas através de testes de segurança, capacitam o gerenciamento de negócios na tomamada de decisões de gerenciamento de risco,  como ao decidir se os riscos podem ser aceitos, mitigados ou transferidos em diferentes níveis dentro da organização (por exemplo, riscos comerciais ou técnicos).

Ao avaliar o nível de segurança de um aplicativo é importante levar em consideração certos fatores, tais como o tamanho do aplicativo sendo desenvolvido. Tamanho do aplicativo tem sido estatisticamente provado estar relacionado com o número de problemas encontrados durante o teste. Já que o teste reduz problemas, faz sentido que aplicativos maiores sejam testados mais frequentemente que aplicativos menores.

Quando os testes de segurança são realizados em várias fases do SDLC, os dados de teste podem provar a capacidade dos testes de segurança na detecção de vulnerabilidades assim que elas são introduzidas. Os dados de teste também podem provar a efetividade na remoção de vulnerabilidades pela implantação de medidas preventivas em diferentes pontos de verificação do SDLC. Uma medida desse tipo também é definida como "métrica de contenção" e fornece uma medida da capacidade da avaliação de segurança para manter a segurança em cada das fases de desenvolvimento. Estas métricas de contenção são também um fator crítico na redução do custo de correção das vulnerabilidades. É mais barato lidar com essas vulnerabilidades na mesma fase do SDLC nas quais são encontradas do que as corrigir numa fase seguinte. 

As métricas de teste de segurança podem oferecer suporte à análise de gerenciamento de riscos, custos e defeitos de segurança quando associadas a objetivos tangíveis e de tempo determinado, tais como:

- Reduzir o número total de vulnerabilidades em 30%.
- Corrigir problemas de segurança antes de uma certa data (por exemplo, antes da versão beta).

Os dados de testes de segurança podem ser absolutos, como o número de vulnerabilidades detectadas durante a revisão manual de código, e também comparativos, como o número de vulnerabilidades detectadas nas revisões de código comparados às encontradas durante os testes de invasão. Para responder a perguntas sobre a qualidade do processo de segurança, é importante determinar uma linha base para o que pode ser considerado aceitável e bom.

Os dados de testes de segurança também podem oferecer suporte a objetivos específicos da análise de segurança. Esses objetivos podem estar em conformidade com os regulamentos e padrões de segurança da informação, gerenciamento de processos, identificação de causa-raiz, melhorias de processo e análise de custo-benefício da segurança.

Quando os dados de testes de segurança são relatados, eles devem fornecer métricas para apoiar a análise. O escopo da análise é a interpretação dos dados de testes que permitem encontrar indícios sobre a segurança do software que está sendo produzido, bem como a eficácia do processo.

Alguns exemplos de indícios ou pistas sustentados por dados de testes de segurança:

- As vulnerabilidades foram reduzidas a um nível aceitável à próxima entrega?
- Como está a qualidade da segurança deste produto comparada a produtos de software semelhantes?
- Todos os requisitos de teste de segurança estão sendo atendidos? 
- Quais são as principais causas raízes dos problemas de segurança? 
- Qual o número de falhas de segurança em comparação aos bugs de segurança?
- Qual atividade de segurança é mais efetiva para revelar vulnerabilidades?
- Qual time é mais produtivo em corrigir defeitos e vulnerabilidades de segurança?
- Qual porcentagem do total de vulnerabilidades é de alto risco?
- Quais ferramentas são mais efetivas na detecção de vulnerabilidades de segurança?
- Qual tipo de testes de segurança é mais efetivo na detecção de vulnerabilidades (por exemplo, testes de caixa branca X caixa preta)?
- Quantos problemas de segurança são encontrados durante a revisão de segurança de código?
- Quantos problemas de segurança são encontrados durante a revisão de segurança de design?

Para fazer um bom julgamento usando os dados de testes, é importante um bom entendimento do processo e ferramentas de testes. Uma taxonomia, ou método de arranjo, de ferramentas de segurança deve ser adotada para decidir quais usar. As ferramentas de segurança podem ser boas em desvendar vulnerabilidades comuns e conhecidas, quando direcionadas a diferentes artefatos.

É importante observar que problemas de segurança desconhecidos não são testados. Logo, o fato de um teste de segurança estar livre de problemas não significa que o software ou aplicativo seja seguro.

Mesmo as ferramentas de automação mais sofisticadas não são páreo para um testador de segurança experiente. Resultados de testes executados em ferramentas automatizadas e com resultados bem-sucedidos dará aos profissionais de segurança uma falsa sensação de segurança. Normalmente, quanto mais experiência com a metodologia de teste de segurança e ferramentas de testes os testadores de segurança possuem, melhores serão os resultados do teste e da análise de segurança. É importante que os gerentes que investim em ferramentas de teste de segurança também considerem investir na contratação de recursos humanos qualificados, bem como em treinamentos de teste de segurança.

### Relatórios de Requisitos (Reporting Requirements)

A postura de segurança de um aplicativo pode ser caracterizada pela perspectiva do efeito, tais como pelo número de vulnerabilidades e a classificação de risco das vulnerabilidades, bem como pela perspectiva da causa ou origem, como erros de código, falhas de design e problemas de configuração.

As vulnerabilidades podem ser classificadas de acordo com diferentes critérios. A métrica de severidade da vulnerabilidade mais comumente usada é o Sistema de Métricas de Vulnerabilidades Comuns ou [Common Vulnerability Scoring System]( https://www.first.org/cvss/)) (CVSS), um padrão mantido pelo Fórum de Equipes de Resposta a Incidentes e Segurança (FIRST).

Ao relatar dados de teste de segurança, a prática recomenda a inclusão das seguintes informações:

- categorização de cada uma das vulnerabilidades por tipo;
- ameaça de segurança a que cada problema é exposto;
- causa raiz de cada problema de segurança tais como bug ou falha;
- cada técnica de teste utilizada para encontrar os problemas;
- remediação ou medida preventiva para cada vulnerabilidade; 
- grau de severidade de cada vulnerabilidade(por exemplo, baixa, media, alta ou classificação pelo CVSS score)

Ao descrever o que é a ameaça de segurança, será possível entender se e por que o controle de mitigação é ineficaz para mitigar a ameaça.

Relatar a causa raiz do problema pode ajudar a identificar o que precisa ser corrigido. No caso do teste de caixa branca, por exemplo, a causa raiz da vulnerabilidade de segurança do software será o código-fonte ofensivo.

Assim que os problemas são reportados, também é importante fornecer orientação ao desenvolvedor de software sobre como testar novamente e encontrar a vulnerabilidade. Isso pode envolver o uso de uma técnica de teste de caixa branca (por exemplo, revisão do código de segurança com um analisador de código estático) para descobrir se o código é vulnerável. Se uma vulnerabilidade pode ser encontrada por meio de um teste de invasão de caixa preta, o relatório do teste também precisa fornecer informações sobre como validar a exposição da vulnerabilidade na interface (por exemplo, cliente).

As informações sobre como corrigir a vulnerabilidade devem ser detalhadas o suficiente para que um desenvolvedor implemente uma correção. Deve fornecer exemplos de codificação segura, alterações de configuração e fornecer referências adequadas.

Por fim, a classificação de gravidade contribui para o cálculo da classificação de risco e ajuda a priorizar o esforço de remediação. Normalmente, atribuir uma classificação de risco à vulnerabilidade envolve uma análise de risco externa com base em fatores como impacto e exposição.

### Casos de Negócio (Business Case)

Para que as métricas de testes de segurança sejam úteis, elas precisam retribuir valores às partes interessadas nos dados de testes de segurança da organização. As partes interessadas podem incluir gerentes de projeto, desenvolvedores, departamentos de segurança da informação, auditores e diretores de informação. O valor pode ser em termos do caso de negócio que cada parte interessada do projeto possui, em termos de função e responsabilidade.

Os desenvolvedores de software analisam os dados de teste de segurança para mostrar que o software é codificado de forma segura e eficiente. Isso permite que eles defendam o uso de ferramentas de análise de código, seguindo padrões de codificação seguros e participando de treinamento de segurança de software.

Os gerentes de projeto procuram dados que lhes permitam gerenciar e utilizar com sucesso as atividades e recursos de testes de segurança de acordo com o plano do projeto. Para os gerentes de projeto, os dados de teste de segurança podem mostrar que os projetos estão seguindo o cronograma de entregas, e estão sendo aprimorados durante os testes.

Os dados de testes de segurança também auxiliam o caso de negócios para testes de segurança se a iniciativa vier de oficiais de segurança da informação (Information Security Officers (ISOs)). Por exemplo, pode fornecer evidências de que o teste de segurança durante o SDLC não afeta a entrega do projeto, mas reduz a carga de trabalho geral necessária para resolver vulnerabilidades posteriormente na produção.

Para os auditores de conformidade, as métricas de testes de segurança fornecem um nível de garantia e confiança de segurança do software uma vez que a conformidade com o padrão de segurança será abordada por meio dos processos de revisão de segurança dentro da organização.

Finalmente, CIOs (Chief Information Officers CIOs) e CISOs (Chief Information Security Officers) , responsáveis pelo orçamento a ser alocado em segurança, utilizam dados de teste de segurança para derivar uma análise de custo-benefício. Isso permite que eles tomem decisões informadas sobre quais atividades e ferramentas de segurança devem investir. Uma das métricas que os apoiam nessa análise é o Retorno do Investimento (ROI) em segurança. Para derivar tais métricas de dados de testes de segurança, é importante quantificar a diferença entre o risco da exposição de vulnerabilidades, e a eficácia dos testes de segurança em mitigar o risco. A partir dai, (calcular???) essa lacuna com o custo da segurança de testes ou das ferramentas de testes adotadas.

## Referências

- US National Institute of Standards (NIST) 2002 [pesquisa sobre o custo de softwares inseguros na economia americana devido a testes de software inadequados](https://www.nist.gov/director/planning/upload/report02-3.pdf)
