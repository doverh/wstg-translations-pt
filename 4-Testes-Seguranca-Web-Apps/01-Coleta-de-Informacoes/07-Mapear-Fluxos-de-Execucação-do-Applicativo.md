# Mapear Fluxos de Execução do Aplicativo

|ID          |
|------------|
|WSTG-INFO-07|

## Resumo

Antes de iniciar os testes de segurança, entender a estrutura do aplicativo é primordial. Sem um entendimento aprofundado do layout do aplicativo, é improvável que ele seja testada de forma abrangente.

## Objetivos do Teste

- Mapear a aplicação-alvo e compreender os fluxos de código principais.

## Como Testar

No teste de caixa-preta, é extremamente difícil testar todo o código-fonte. Não apenas porque o testador não tem visão do fluxo de código através do aplicativo, mas mesmo se tivesse, testar todos os fluxos de código seria muito demorado. Uma maneira de reconciliar isso é documentar quais fluxos de código foram descobertos e testados.

Existem várias maneiras de abordar o teste e medição de cobertura de código:

- **Fluxo** - testar cada um dos fluxos através de uma aplicação que inclui testes de análise combinatória e de valores limites para cada fluxo de decisão. Embora essa abordagem ofereça minúcia, o número de fluxos testáveis cresce exponencialmente a cada ramificação de decisão.
- **Fluxo de Dados (ou Análise de Taint)** - testa a atribuição de variáveis via interação externa (normalmente usuários). Concentra-se em mapear o fluxo, transformação e uso de dados em toda a aplicação.
- **Concorrência** - testa várias instâncias simultâneas do aplicativo manipulando os mesmos dados.

A compensação quanto ao método utilizado e em que grau cada método é utilizado deve ser negociada com o proprietário do aplicativo. Abordagens mais simples também podem ser adotadas, incluindo perguntar ao proprietário do aplicativo sobre quais funções ou seções de código eles estão particularmente preocupados e como esses segmentos de código podem ser alcançados.

Para demonstrar a cobertura de código ao proprietário do aplicativo, o testador pode começar com uma planilha e documentar todos os links descobertos ao explorar a aplicação (manualmente ou automaticamente). Em seguida, o testador pode examinar mais de perto os pontos de decisão na aplicação e investigar quantos fluxos de código significativos são descobertos. Esses devem ser documentados na planilha com URLs, descrições em prosa e capturas de tela dos fluxos descobertos.

### Revisão de Código

Garantir cobertura de código suficiente para o proprietário do aplicativo é muito mais fácil com uma abordagem de teste de caixa cinza e caixa branca. As informações solicitadas e fornecidas ao testador garantirão que os requisitos mínimos de cobertura de código sejam atendidos.

Muitas ferramentas modernas de Teste Dinâmico de Segurança de Aplicações (DAST) facilitam o uso de um agente de servidor web ou podem ser emparelhadas com um agente de terceiros para monitorar especificidades de cobertura de aplicação web.

### Spidering Automático

O spidering automático é uma ferramenta usada para descobrir automaticamente novos recursos (URLs) em um site específico. Ele começa com uma lista de URLs para visitar, chamada sementes, que depende de como o Spider é iniciado. Embora existam muitas ferramentas de Spidering, o exemplo a seguir usa o [Zed Attack Proxy (ZAP)](https://github.com/zaproxy/zaproxy):

![Tela do Zed Attack Proxy](images/OWASPZAPSP.png)\
*Figura 4.1.7-1: Tela do Zed Attack Proxy*

[ZAP](https://github.com/zaproxy/zaproxy) oferece várias opções de spidering automático, que podem ser aproveitadas com base nas necessidades do testador:

- [Spider](https://www.zaproxy.org/docs/desktop/start/features/spider/)
- [AJAX Spider](https://www.zaproxy.org/docs/desktop/addons/ajax-spider/)
- [Suporte OpenAPI](https://www.zaproxy.org/docs/desktop/addons/openapi-support/)

## Ferramentas

- [Zed Attack Proxy (ZAP)](https://github.com/zaproxy/zaproxy)
- [Lista de softwares de planilha](https://pt.wikipedia.org/wiki/Lista_de_programas_de_planilha)
- [Softwares de diagramação](https://pt.wikipedia.org/wiki/Lista_de_programas_de_diagrama%C3%A7%C3%A3o)

## Referências

- [Cobertura de Código](https://pt.wikipedia.org/wiki/Cobertura_de_c%C3%B3digo)
```