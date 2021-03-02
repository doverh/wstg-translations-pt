<h1>Introdução</h1>

<h2>O Projeto de Testes OWASP</h2>
  
O Projeto de Testes OWASP está em desenvolvimento há muitos anos. O objetivo do projeto é ajudar pessoas a entender o <i>quê</i>, <i>por que</i>, <i>quando</i>, <i>onde</i> e <i>como</i> testar aplicativos web. O projeto criou um modelo de testes completo, um framework, e não apenas uma lista simples de itens ou diagnóstico de problemas que devem ser remediados. Os leitores podem usar essa estrutura como um modelo para construir seus próprios programas de teste ou ainda qualificar processos de outras pessoas. O Guia de testes descreve em detalhes o modelo geral de testes e as técnicas necessárias para implementar o modelo na prática.

Escrever o guia de testes tem sido uma tarefa árdua. Foi um desafio obter consenso e desenvolver conteúdos que permitissem aplicar os conceitos sugeridos pelo guia, ao mesmo tempo em que permitisse a adaptação a ambientes culturais específicos. Também foi um desafio modificar o foco dos testes de aplicativos web, de testes de intrusão (do inglês "Penetration Test" ou pentest"), para testes de integração, ou seja, testes integrados no ciclo de vida do desenvolvimento de software.

No entanto o grupo está muito satisfeito com os resultados do projeto. Muitos especialistas e profissionais de segurança da informação, alguns dos quais responsáveis pela segurança de software em algumas das maiores empresas do mundo, estão validando esse modelo de testes. Esse modelo tem ajudado organizações a testar seus aplicativos web e construir  softwares mais confiáveis e seguros. O modelo não enfatiza simplesmente as áreas frágeis, embora isso certamente seja um subproduto de muitas listas de guias OWASP. Decisões delicadas foram tomadas sobre a adequação do uso de certas técnicas e tecnologias de testes. O grupo entende perfeitamente que nem todos concordarão com  todas as decisões tomadas. Contudo, a renomada posição da OWASP permite a mudança de cultura a longo prazo por meio da conscientização e educação, construindo consenso através de experiências.

O restante deste guia está organizado da seguinte forma: esta introdução cobre os pré-requisitos de teste de aplicativos web e o escopo de testes. Ele também cobre os princípios e técnicas de teste executados com sucesso, práticas recomendadas para relatórios e casos de negócios para testes de segurança. O capítulo 3 apresenta o modelo de testes OWASP e explica suas técnicas e tarefas em relação às várias fases do ciclo de vida de desenvolvimento de software. O capítulo 4 cobre como testar vulnerabilidades específicas (por exemplo, injeção de SQL), inspeção de código e teste de intrusão.

<h2>Medindo a segurança: dados economicos de um software inseguro</h2>
Um princípio básico da engenharia de software é resumido em uma citação de Tom DeMarco em <a href="https://isbnsearch.org/isbn/9780131717114">Controlling Software Projects: Management, Measurement, and Estimates</a>:


    Você não pode controlar aquilo que você não pode medir.
    
Os testes de segurança não são diferentes. Infelizmente, medir  segurança é um processo notoriamente complexo.

Um aspecto que deve ser enfatizado é de que as métricas de segurança tratam de questões técnicas específicas (por exemplo, quão predominante uma determinada vulnerabilidade é) e como estas questões impactam os custos do software. A maioria daqueles que possuem conhecimento técnico irão ao menos entender os problemas básicos, ou poderão compreender mais profundamente as vulnerabilidades. Infelizmente, poucos são capazes de traduzir esse conhecimento técnico em termos monetários e quantificar o impacto financeiro que essas vulnerabilidades terão sobre o negócio do proprietário do aplicativo. Até que isso aconteça, os diretores de tecnologia não serão capazes de prever um retorno preciso sobre o investimento em segurança e, consequentemente, dimensionar orçamentos apropriados para segurança de software.

Embora estimar o custo de um software inseguro possa parecer uma tarefa desencorajadoura, uma significativa quantidade de trabalho tem sido empregado nessa direção. Em 2018, o Consórcio para Qualidade de Software de TI <a href="https://www.it-cisq.org/the-cost-of-poor-quality-software-in-the-us-a-2018-report/The-Cost-of-Poor-Quality-Software-in-the-US-2018-Report.pdf">resumiu</a>:


    ... o custo de software de baixa qualidade nos EUA em 2018 é de aproximadamente U$ 2,84 trilhões ...
    
O modelo descrito neste documento incentiva as pessoas a medir a segurança durante todo o processo de desenvolvimento. Eles podem então relacionar o custo de softwares não seguros ao impacto que ele tem no negócio e, em consequência, desenvolver processos de negócios apropriados, atribuindo recursos para gerenciar os riscos. Lembre-se de que medir e testar aplicativos da web é ainda mais crítico do que para outro software, uma vez que os aplicativos web são expostos a milhões de usuários através da Internet.

Muitas coisas precisam ser testadas durante o ciclo de vida do desenvolvimento de um aplicativo web, mas o que testes realmente significam? O Dicionário de Inglês define "teste" como:


    teste(substantivo): um procedimento destinado a estabelecer a qualidade, o desempenho ou a confiabilidade de algo, especialmente antes de que seja amplamente utilizado.
    

Para os fins deste documento, teste é um processo de comparação do estado de um sistema ou aplicativo perante um conjunto de critérios estabelecidos. No setor de segurança da informação, as pessoas frequentemente testam utilizando-se de um conjunto de critérios mentais que não são nem bem definidos nem completos. Como resultado disso, pessoas de fora do meio consideram testes de segurança uma arte obscura. O objetivo deste documento é mudar essa percepção, tornando mais fácil que pessoas sem conhecimento específicode segurança da informação possam fazer diferença ao testar.


<h2>Por que testar?</h2>

Este documento foi criado para ajudar organizações a entender a abrangência de um programa de testes, e ajudá-las a identificar as etapas que devem ser assumidas para se construir e operar um programa de testes de aplicativos web modernos. O guia fornece uma visão ampla dos elementos necessários a um compreensivo programa de segurança de aplicativos web. Este guia pode ser usado como referência e como metodologia na identificação de lacunas entre as práticas existentes e as recomendadas. Este guia permite que as organizações se comparem aos seus pares no setor, para entender a magnitude dos recursos requeridos para testar e manter o software ou ainda se preparar para uma auditoria. Este capítulo não traz detalhes técnicos de como testar um aplicativo, pois a intenção é fornecer um modelo de segurança típico a uma organização. Os detalhes técnicos sobre como testar um aplicativo, como parte de um teste de invasão ou revisão de código, serão cobertos nas partes restantes deste documento.


<h2>Quando testar?</h2>

A maioria das pessoas hoje em dia não testa o software até que ele já tenha sido criado e esteja na fase de implantação de seu ciclo de vida ( isto é, o código foi criado e instanciado em um aplicativo web em funcionamento). Esta é geralmente uma prática ineficaz e com custos proibitivos. Um dos melhores métodos para evitar que bugs de segurança apareçam em aplicativos já em ambiente de produção é melhorar o Ciclo de Vida de Desenvolvimento de Software (SDLC), incluindo segurança em cada uma de suas fases. Um SDLC é uma estrutura imposta ao desenvolvimento de artefatos de software. Se o SDLC não estiver sendo usado em seus projetos, é hora de escolher um! A figura a seguir mostra um modelo SDLC genérico, bem como o custo progressivo (estimado) para a correção de bugs de segurança em tal modelo.

<img src="https://github.com/OWASP/wstg/blob/master/document/2-Introduction/images/SDLC.jpg">

<i>Figura 2-1: Modelo SDLC Genérico</i>

    
As empresas devem revisar seu ciclo SDLC para garantir que a segurança seja parte integrante do processo de desenvolvimento. Os SDLCs devem incluir testes de segurança para garantir que a segurança seja adequadamente coberta e os controles sejam eficazes durante todo o processo de desenvolvimento.



