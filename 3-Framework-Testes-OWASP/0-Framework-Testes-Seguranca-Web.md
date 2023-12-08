# O Framework de Testes de Segurança Web

## Visão Geral

Esta seção descreve um framework de testes típico que pode ser desenvolvido dentro de uma organização. Ele pode ser visto como um framework de referência composto por técnicas e tarefas apropriadas em várias fases do ciclo de vida de desenvolvimento de software (SDLC). Empresas e equipes de projetos podem usar esse modelo para desenvolver seu próprio framework de testes e para definir os serviços de testes para fornecedores. Este framework não deve ser visto como definitivo, mas como uma abordagem flexível que pode ser estendida e adaptada para se ajustar ao processo de desenvolvimento e à cultura de uma organização.

Esta seção tem como objetivo ajudar as organizações a construir um processo de testes estratégico completo e não se destina a consultores ou terceirizados que costumam se envolver em áreas mais táticas e específicas de testes.

É crucial entender por que construir um framework de testes abrangente do início ao fim é fundamental para avaliar e melhorar a segurança do software. Em *Escrevendo Código Seguro*, Howard e LeBlanc observam que o lançamento de um boletim de segurança custa à Microsoft pelo menos US $100.000, mas custa aos seus clientes coletivamente muito mais para implementar as correções de segurança. Eles também observam que há um site do governo dos EUA sobre [CyberCrime](https://www.justice.gov/criminal-ccips), que detalha casos criminais recentes e perdas para as organizações. As perdas típicas ultrapassam em muito USD $100.000.

Com uma economia como essa, não é surpresa que os fornecedores de software estejam passando de testes de segurança de caixa-preta, que só podem ser realizados em aplicativos que já foram desenvolvidos, para se concentrar nos testes nas primeiras etapas do desenvolvimento de aplicativos, como definição, design e desenvolvimento.

Muitos profissionais de segurança ainda veem os testes de segurança na esfera dos testes de invasão. Como discutido no capítulo anterior, embora os testes de penetração tenham um papel a desempenhar, eles são geralmente ineficientes em encontrar bugs e dependem excessivamente das habilidades do testador. Eles devem ser considerados apenas como uma técnica de implementação ou para aumentar a conscientização sobre questões de produção. Para melhorar a segurança das aplicações, a qualidade de segurança do software deve ser aprimorada. Isso significa testar a segurança durante as etapas de definição, design, desenvolvimento, implantação e manutenção, e não depender da estratégia dispendiosa de esperar até que o código esteja totalmente concluído.

Como discutido na introdução deste documento, existem muitas metodologias de desenvolvimento, como o Rational Unified Process, desenvolvimento eXtreme e Agile e metodologias tradicionais em cascata. A intenção deste guia não é sugerir uma metodologia de desenvolvimento específica ou fornecer orientações específicas que aderem a qualquer metodologia específica. Em vez disso, estamos apresentando um modelo de desenvolvimento genérico e o leitor deve segui-lo de acordo com seu processo empresarial.

Este framework de testes consiste em atividades que devem ocorrer em:

- Antes do início do desenvolvimento,
- Durante a definição e design,
- Durante o desenvolvimento,
- Durante a implantação, e
- Durante a manutenção e operações.

## Fase 1 Pre Desenvolvimento

### Fase 1.1 Definir um SDLC

Antes do início do desenvolvimento do aplicativo, um SDLC adequado deve ser definido, onde a segurança está presente em cada etapa.

### Fase 1.2 Revisar Políticas e Padrões

Certifique-se de que existam políticas, padrões e documentação apropriados. A documentação é extremamente importante, pois fornece orientações e políticas para as equipes de desenvolvimento seguirem. As pessoas só podem fazer a coisa certa se souberem o que é certo.

Se o aplicativo for desenvolvido em Java, é essencial ter um padrão de codificação segura em Java. Se o aplicativo for utilizar criptografia, é essencial ter um padrão de criptografia. Nenhuma política ou padrão pode abranger todas as situações que a equipe de desenvolvimento enfrentará. Ao documentar os problemas em comum e previsíveis, haverá menos decisões a serem tomadas durante o processo de desenvolvimento.

### Fase 1.3 Desenvolver Critérios de Medição e Métricas e Garantir Rastreabilidade

Antes do início do desenvolvimento, planeje o programa de medição. Ao definir critérios que precisam ser medidos, ele fornece visibilidade para defeitos tanto no processo quanto no produto. É essencial definir as métricas antes do início do desenvolvimento, pois pode haver a necessidade de modificar o processo para capturar os dados.

## Fase 2 Durante a Definição e o Design

### Fase 2.1 Revisar Requisitos de Segurança

Os requisitos de segurança definem como um aplicativo funciona do ponto de vista da segurança. É essencial que os requisitos de segurança sejam testados. Testar, nesse caso, significa testar as suposições feitas nos requisitos e verificar se há lacunas nas definições dos requisitos.

Por exemplo, se houver um requisito de segurança que afirma que os usuários devem ser registrados antes de terem acesso à seção de whitepapers de um site, isso significa que o usuário deve ser registrado no sistema ou o usuário deve ser autenticado? Certifique-se de que os requisitos sejam o mais unívocos possível.

Ao procurar por lacunas nos requisitos, considere aspectos de mecanismos de segurança, como:

- Gerenciamento de usuários
- Autenticação
- Autorização
- Confidencialidade de dados
- Integridade
- Responsabilidade
- Gerenciamento de sessão
- Segurança de transporte
- Segregação de sistema em camadas
- Conformidade com legislação e padrões (incluindo privacidade, governamentais e padrões do setor)

### Fase 2.2 Revisar Design e Arquitetura

Os aplicativos devem ter um design e arquitetura documentados. Essa documentação pode incluir modelos, documentos textuais e outros artefatos similares. É essencial testar esses artefatos para garantir que o design e a arquitetura imponham o nível adequado de segurança, conforme definido nos requisitos.

Identificar falhas de segurança na fase de design não apenas é um dos lugares mais eficientes em termos de custo para identificar falhas, mas também pode ser um dos lugares mais eficazes para fazer alterações. Por exemplo, se for identificado que o design exige que as decisões de autorização sejam tomadas em vários locais, pode ser apropriado considerar um componente de autorização centralizada. Se o aplicativo estiver realizando validação de dados em vários locais, pode ser apropriado desenvolver um framework de validação centralizado (ou seja, corrigir a validação de entrada em apenas um local, em vez de centenas de locais, é muito mais barato).

Se fraquezas forem descobertas, elas devem ser apresentadas ao arquiteto do sistema para abordagens alternativas.

### Fase 2.3 Criar e Revisar Modelos UML

Assim que o design e a arquitetura estiverem concluídos, construa modelos de Linguagem de Modelagem Unificada (UML) que descrevam como o aplicativo funciona. Em alguns casos, esses modelos já podem estar disponíveis. Use esses modelos para confirmar com os designers de sistemas uma compreensão precisa de como o aplicativo funciona. Se forem descobertas fraquezas, elas devem ser apresentadas ao arquiteto do sistema para abordagens alternativas.

### Fase 2.4 Criação e Revisão de Modelos de Ameaças

Armado com a revisão de design e arquitetura e os modelos UML que explicam exatamente como o sistema funciona, realize um exercício de modelagem de ameaças. Desenvolva cenários de ameaças realistas. Analise o design e a arquitetura para garantir que essas ameaças tenham sido mitigadas, aceitas pelos negócios ou atribuídas a terceiros, como uma seguradora. Quando as ameaças identificadas não possuem estratégias de mitigação, reveja o design e a arquitetura com o arquiteto de sistemas para modificar o design.

## Fase 3 Durante o Desenvolvimento

Teoricamente, o desenvolvimento é a implementação de um design. No entanto, no mundo real, muitas decisões de design são tomadas durante o desenvolvimento do código. Essas são frequentemente decisões menores que eram muito detalhadas para serem descritas no design, ou questões em que nenhuma política ou orientação padrão foi oferecida. Se o design e a arquitetura não foram adequados, o desenvolvedor enfrentará muitas decisões. Se houver políticas e padrões insuficientes, o desenvolvedor enfrentará ainda mais decisões.

### Fase 3.1 Revisão do Código

A equipe de segurança deve realizar uma revisão do código com os desenvolvedores e, em alguns casos, com os arquitetos do sistema. Uma revisão do código é uma visão geral do código durante a qual os desenvolvedores podem explicar a lógica e o fluxo do código implementado. Isso permite que a equipe de revisão de código obtenha uma compreensão geral do código e permite que os desenvolvedores expliquem por que certas coisas foram desenvolvidas da maneira como foram.

O objetivo não é realizar uma revisão de código, mas entender em um alto nível o fluxo, o layout e a estrutura do código que compõe a aplicação.

### Fase 3.2 Revisões de Código

Armado com um bom entendimento de como o código está estruturado e por que certas coisas foram codificadas da maneira como foram, o testador pode agora examinar o código real em busca de defeitos de segurança.

As revisões de código estático validam o código em relação a um conjunto de listas de verificação, incluindo:

- Requisitos de negócios para disponibilidade, confidencialidade e integridade;
- Lista de verificação OWASP ou Top 10 para exposições técnicas (dependendo da profundidade da revisão);
- Problemas específicos relacionados à linguagem ou estrutura em uso, como o artigo Scarlet para PHP ou listas de verificação de codificação segura da Microsoft para ASP.NET; e
- Quaisquer requisitos específicos da indústria, como Sarbanes-Oxley 404, COPPA, ISO/IEC 27002, APRA, diretrizes de comerciante Visa ou outros regimes regulatórios.

Em termos de retorno sobre os recursos investidos (principalmente tempo), as revisões de código estático produzem retornos de qualidade muito melhores do que qualquer outro método de revisão de segurança e dependem menos da habilidade do revisor. No entanto, elas não são uma solução milagrosa e devem ser consideradas cuidadosamente dentro de um regime de testes de espectro completo.

Para mais detalhes sobre as listas de verificação OWASP, consulte a última edição do [OWASP Top 10](https://owasp.org/www-project-top-ten/).

## Fase 4 Durante a Implementação

### Fase 4.1 Teste de Penetração da Aplicação

Após testar os requisitos, analisar o design e realizar a revisão do código, pode-se assumir que todos os problemas foram identificados. Espera-se que esse seja o caso, mas o teste de penetração da aplicação após a implantação fornece uma verificação adicional para garantir que nada tenha sido esquecido.

### Fase 4.2 Teste de Gerenciamento de Configuração

O teste de penetração da aplicação deve incluir um exame de como a infraestrutura foi implantada e protegida. É importante revisar os aspectos de configuração, por menores que sejam, para garantir que nenhum deles tenha sido deixado em uma configuração padrão que possa ser vulnerável à exploração.

## Fase 5 Durante a Manutenção e Operações

### Fase 5.1 Realizar Revisões de Gerenciamento Operacional

Deve haver um processo que detalhe como o lado operacional da aplicação e da infraestrutura é gerenciado.

### Fase 5.2 Realizar Verificações de Saúde Periódicas

Verificações de saúde mensais ou trimestrais devem ser realizadas tanto na aplicação quanto na infraestrutura para garantir que nenhum novo risco de segurança tenha sido introduzido e que o nível de segurança ainda esteja intacto.

### Fase 5.3 Garantir Verificação de Mudança

Após cada mudança ter sido aprovada e testada no ambiente de QA e implantada no ambiente de produção, é vital verificar se a mudança não afetou o nível de segurança. Isso deve ser integrado ao processo de gerenciamento de mudanças.

## Um Fluxo de Trabalho Típico de Testes do SDLC

A figura a seguir mostra um fluxo de trabalho típico de testes do SDLC.

![Fluxo de trabalho típico de testes do SDLC](images/Typical_SDLC_Testing_Workflow.gif)\
 *Figura 3-1: Fluxo de trabalho típico de testes do SDLC*