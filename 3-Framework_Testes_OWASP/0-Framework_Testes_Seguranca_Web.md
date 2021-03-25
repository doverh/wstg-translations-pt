
# O Framework de Testes de Segurança

## Visão Geral

Essa seção descreve um modelo (framework) típico de testes que pode ser desenvolvido dentro de uma organização. Ele pode ser visto como um framework de referência, composta por técnicas e atividades que são apropriadas ao ciclo de vida de desenvolvimento de software (SDLC). Organizações e times de projetos podem utilizar um modelo para desenvolver seu próprio framework de testes, e para desenvolver serviços e testes para fornecedores.
Essa estrutura não deve ser vista como um receituário, mas como uma abordagem flexível, que pode ser estendida e moldada para se adequar ao processo de desenvolvimento e à cultura de uma organização.

Essa sessão tem por objetivo ajudar organizações a construir uma estratégia de processos de testes completa e não se destina a consultores ou terceirizados que tendem a se envolver em áreas de testes mais táticas e específicas.É crítico entender por que a construção de um framework de testes end-to-end é crucial para avaliar e melhorar a segurança do software. Em *Writing Secure Code*, Howard e LeBlanc observam que a emissão de um boletim de segurança custa à Microsoft pelo menos U$100 mil e custa a seus clientes combinadamente muito mais do que implementar os patches de segurança. Eles também observam que o [site CyberCrime] do governo dos EUA detalha casos criminais recentes e as perdas para organizações. As perdas típicas excedem em muito os U$100 mil.

Com números como esses, não é surpresa o fato de que fornecedores de software estão migrando a exclusividade de execução apenas de testes de segurança de caixa preta, que só podem ser realizados em aplicativos que já foram desenvolvidos, para concentração em testes nos primeiros ciclos de desenvolvimento de aplicativos, como durante definição, design e desenvolvimento.

Muitos profissionais de segurança ainda vêem testes de segurança como o reino dos testes de invasão. Como discutido no capítulo anterior, ainda que os testes de invasões detenham um papel no palco, são geralmente insuficientes para encontrar bugs e dependem excessivamente das habilidades do testador. Ele deveria ser apenas considerado como uma técnica de implementação, ou para tornar públicos problemas de produção. Para melhorar a segurança de aplicativos, a qualidade da segurança de softwares precisa ser melhorada. Isso significa testar segurança durantes as fases de definição, design, desenvolvimento, implantação e manutenção e não depender da onerosa estratégia de esperar até que o código seja completamente desenvolvido.   

Tal como discutido na introdução desse documento, existem muitas metodologias de desenvolvimento, tais como o processo racional unificado (RUP), os desenvolvimentos ágeis (XP e SCRUM), e as metodologias cascata tradicionais. A intenção desse guia não é sugerir uma ou outra metodologia de desenvolvimento em particular, nem mesmo prover uma direção específica para adesão de uma delas. Por outro lado, nós apresentamos um modelo de desenvolvimento genérico, e o leitor deve seguir ele de acordo com o processo da organização.
 
Esse framework de testes consiste em atividades que devem ser adotadas:

- Antes que o desenvolvimento inicie,
- Durante a definição e o design,
- Durante o desenvolvimento,
- Durante a implantação, e
- Durante a manutenção e operação.

## Fase 1 Antes que o desenvolvimento inicie

### Fase 1.1 Defina o SDLC

Antes que o desenvolvimento inicie, um SDLC adequado deve ser definido no qual a segurança é inerente a cada estágio.

### Fase 1.2 Revisão de normas e padrões
Garantir que existem normas adequadas, padrões, e documentação em vigor. 
Documentação é extremamente importante pois fornece ao time de desenvolvimento guias e normas que eles precisam seguir. Pessoas podem apenas fazer o que é certo quando sabem o que é certo fazer.
Se o aplicativo é desenvolvido em Java, é essencial que exista um padrão de código seguro em Java. Se o aplicativo usa criptografia, é essencial que exista um padrão de criptografia. Não existem normas ou padrões que cubram cada situação que o time de desenvolvimento irá enfrentar.  
Documentando os problemas comuns e previsíveis, menor o número de decisões a ser tomadas durantes o processo de desenvolvimento. 

### Fase 1.3 Desenvolvendo medidas e métricas para garantir a rastreabilidade

Antes que o desenvolvimento inicie, planeje um programa de métricas. Ao definir critérios que precisam ser medidos, se fornece visibilidade aos defeitos em ambos processo e produto. É essencial definir métricas antes que o desenvolvimento inicie, já que pode haver a necessidade de modificar o processo para a coleta de dados.

## Fase 2 Durante a definição e o design 

### Fase 2.1 Revisão de requisitos de segurança

Requisitos de segurança definem como o aplicativo funciona em uma perspectiva de segurança. É essencial que requisitos de segurança sejam testados. Testar nesse caso significa verificar as hipóteses levantadas nos requisitos e verificar se existem falhas na definição dos mesmos.

Por exemplo, se um requisito de segurança afirma que usuários precisam estar registrados antes de ter acesso há uma determinada sessão do site, isso significa que os usuários também precisam se registrar no sistema ou que o usuário precisa estar autenticado? Garanta que os requisitos sejam o menos ambíguos possíveis.

Ao procurar por falhas nos requisitos, considere observar os mecanismos de segurança tais como: 

- Gestão de usuários
- Autenticação
- Autorização
- Confidencialidade de dados
- Integridade
- Controladoria
- Gerenciamento de sessão
- Segurança de transporte
- Separação do sistema por camadas 
- Conformidade com legislações e padrões (incluindo privacidade, regras do governo e padrões da indústria) 

### Fase 2.2 Revisão de Design e Arquitetura

Aplicativos devem ter design e arquitetura documentados. Essa documentação pode incluir modelos, documentos textuais, e outros artefatos similares. É essencial testar esses artefatos para ter certeza que o design e a arquitetura imponham nível apropriado de segurança tal como definido nos requisitos. Identificar falhas de segurança na fase de design não é somente mais efetivo em termos de custo-benefício, mas pode também ser um dos meios mais efetivos para se promover mudanças.  
Por exemplo, se for identificado que o projeto exige que as decisões de autorização sejam feitas em vários locais, pode ser apropriado considerar um componente de autorização central. Se o aplicativo está realizando validação de dados em vários lugares, pode ser apropriado desenvolver uma estrutura de validação central (ou seja, fixar a validação de entrada em um lugar, em vez de em centenas de lugares, o que é muito mais barato).
Se pontos fracos forem identificados, eles devem ser fornecidos ao arquiteto de sistema para abordagens alternativas.

### Fase 2.3 Criar e revisar modelos UML

Uma vez que o design e a arquitetura estiverem completos, construa modelos de “Unified Modeling Language” (UML) que descrevam como o aplicativo funciona. Em alguns casos, eles já podem estar disponíveis. Use esses modelos para confirmar com os designers de sistemas uma compreensão exata de como o aplicativo funciona. Se pontos fracos forem descobertos, eles devem ser fornecidos ao arquiteto de sistema para abordagens alternativas.

### Fase 2.4 Criar e revisar modelos de ameaças

Munido das revisões de design e arquitetura e os modelos UML, que explicam exatamente como o sistema funciona, realize um exercício de modelagem de ameaças. Desenvolva cenários de ameaças realistas. Analise o design e a arquitetura para garantir que essas ameaças tenham sido mitigadas, aceitas pela empresa ou atribuídas a terceiros, como uma empresa de seguros. Quando as ameaças identificadas não têm estratégias de mitigação, revisite o design e a arquitetura com o arquiteto de sistemas para modificar o design.


## Fase 3 Durante o desenvolvimento


Teoricamente, o desenvolvimento é a implementação de um design. No entanto, no mundo real, muitas decisões de design são feitas durante o desenvolvimento do código. Frequentemente, são pequenas decisões que ou são muito detalhadas para serem descritas no projeto ou são questões que nenhuma política ou orientação padrão foi oferecida. Se o design e a arquitetura não forem adequados, o desenvolvedor se deparará com muitas decisões. Se houver políticas e padrões insuficientes, o desenvolvedor enfrentará ainda mais decisões.

### Fase 3.1 Passo a passo do código

A equipe de segurança deve realizar uma análise do código com os desenvolvedores e, em alguns casos, os arquitetos do sistema. O passo a passo do código é uma visão de alto nível do código durante a qual os desenvolvedores podem explicar a lógica e o fluxo do código implementados. Ele permite que a equipe de revisão de código obtenha uma compreensão geral do código e permite que os desenvolvedores expliquem por que certas coisas foram desenvolvidas da maneira que foram.
O objetivo não é realizar uma revisão de código, mas entender o fluxo em alto nível, o layout e a estrutura do código que compõe o aplicativo.

### Fase 3.2 Revisões de código

De posse de um bom entendimento de como o código está estruturado e por que certas coisas foram codificadas da maneira que foram, o testador agora pode examinar o código em si na busca de defeitos de segurança.
A revisão estática valida o código comparando a uma série de itens, incluindo:

- Requisitos de negócios para disponibilidade, confidencialidade e integridade;
- Guia OWASP ou “Top 10 Checklists” para exposições técnicas (dependendo da profundidade da revisão);
- Questões específicas relacionadas à linguagem ou estrutura em uso, como o guia Scarlet para PHP ou [checklist Microsoft para Código Seguro ASP.NET](https://msdn.microsoft.com/en-us/library/ff648269.aspx); e
- Quaisquer requisitos específicos do setor, como Sarbanes-Oxley 404, COPPA, ISO / IEC 27002, APRA, HIPAA, guia do comerciante Visa ou outros regimes regulatórios.
Em termos de retorno sobre recursos investidos (principalmente tempo), as revisões de código estático produzem retornos de qualidade muito mais alta do que qualquer outro método de revisão de segurança, e dependem menos da habilidade do revisor. No entanto, ela não é uma solução mágica e precisa ser considerada cuidadosamente em um regime de teste de escopo total.
Para mais detalhes sobre os checklists OWASP, por favor visite a última edição do [OWASP Top 10](https://owasp.org/www-project-top-ten/).

## Fase 4 Durante o desenvolvimento

### Fase 4.1 Testes de invasão do aplicativo

Testados os requisitos, analisado o design e revisado o código, pode-se presumir que todos os problemas foram detectados. Espera-se, que este seja o caso, mas o teste de invasão do aplicativo, pós implantação, fornece uma verificação adicional para garantir que nada foi perdido.

### Fase 4.2 Testes de gerenciamento de configuração 

O teste de invasão do aplicativo deve incluir um exame de como a infraestrutura foi implantada e protegida. É importante revisar os aspectos de configuração, não importa quão pequenos sejam, para garantir que nada seja deixado sob configuração padrão vulnerável a ataques.

## Fase 5 Durante a manutenção e operação

### Fase 5.1 Conduza revisões de gerenciamento operacional 

Deve haver um processo em curso que detalhe como a parte operacional do aplicativo e da infraestrutura é gerenciado.

### Fase 5.2 Conduza checkups de saúde periodicamente

Mensalmente ou trimestralmente verifique a integridade no aplicativo e na infraestrutura para garantir que nenhum risco de segurança tenha sido introduzido e que o nível de segurança ainda esteja intacto.

### Fase 5.3 Garanta a verificação das mudanças 
Após cada mudança ter sido aprovada e testada no ambiente de controle de qualidade e implantada no ambiente de produção, é essencial que a mudança seja verificada para garantir que o nível de segurança não foi afetado. Isso deve ser integrado ao processo de gerenciamento de mudanças.

## Um típico workflow de testes SDLC

As figuras a seguir demonstram um workflow de testes típico. 

![ Um típico workflow de testes SDLC](images/Typical_SDLC_Testing_Workflow.gif)\
 *Figura 3-1: Um típico workflow de testes SDLC *
![image](https://user-images.githubusercontent.com/25070780/111929974-3b4fb280-8a8e-11eb-8269-e34462303bd7.png)
