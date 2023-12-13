# 4.0 Introdução e Objetivos

Esta seção descreve a metodologia de teste de segurança de aplicativos da web OWASP e explica como testar evidências de vulnerabilidades dentro do aplicativo devido a deficiências com os controles de segurança identificados.

## O que é Teste de Segurança de Aplicativos da Web?

Um teste de segurança é um método de avaliação da segurança de um sistema de computador ou rede, validando e verificando método-dicamente a eficácia dos controles de segurança da aplicação. Um teste de segurança de aplicativo da web foca apenas na avaliação da segurança de um aplicativo da web. O processo envolve uma análise ativa do aplicativo em busca de fraquezas, falhas técnicas ou vulnerabilidades. Quaisquer problemas de segurança encontrados serão apresentados ao proprietário do sistema, juntamente com uma avaliação do impacto, uma proposta de mitigação ou uma solução técnica.

## O que é uma Vulnerabilidade?

Uma vulnerabilidade é uma falha ou fraqueza no design, implementação, operação ou gerenciamento de um sistema que possa ser explorada para comprometer os objetivos de segurança do sistema.

## O que é uma Ameaça?

Uma ameaça é qualquer coisa (um atacante malicioso externo, um usuário interno, uma instabilidade do sistema, etc) que possa prejudicar os ativos de um aplicativo (recursos de valor, como dados em um banco de dados ou no sistema de arquivos) explorando uma vulnerabilidade.

## O que é um Teste?

Um teste é uma ação para demonstrar que um aplicativo atende aos requisitos de segurança de seus interessados.

## A Abordagem na Elaboração deste Guia

A abordagem da OWASP é aberta e colaborativa:

- Aberta: qualquer especialista em segurança pode participar com sua experiência no projeto. Tudo é gratuito.
- Colaborativa: o brainstorming é realizado antes da redação dos artigos, para que a equipe possa compartilhar ideias e desenvolver uma visão coletiva do projeto. Isso significa consenso geral, uma audiência mais ampla e maior participação.

Essa abordagem tende a criar uma metodologia de teste definida que será:

- Consistente
- Reproduzível
- Rigorosa
- Sob controle de qualidade

Os problemas a serem abordados são totalmente documentados e testados. É importante utilizar um método para testar todas as vulnerabilidades conhecidas e documentar todas as atividades de teste de segurança.

## O que é a Metodologia de Teste OWASP?

Os testes de segurança nunca serão uma ciência exata em que uma lista completa de todos os possíveis problemas que devem ser testados possa ser definida. Na verdade, os testes de segurança são apenas uma técnica apropriada para testar a segurança de aplicativos da web em determinadas circunstâncias. O objetivo deste projeto é coletar todas as técnicas de teste possíveis, explicar essas técnicas e manter o guia atualizado. A metodologia de teste de segurança de aplicativos da web da OWASP é baseada na abordagem de caixa-preta. O testador não sabe nada ou tem muito pouca informação sobre o aplicativo a ser testado.

O modelo de teste consiste em:

- Testador: Quem realiza as atividades de teste
- Ferramentas e metodologia: O núcleo deste projeto de Guia de Testes
- Aplicativo: A caixa-preta a ser testada

Os testes podem ser categorizados como passivos ou ativos:

### Testes Passivos

Durante os testes passivos, um testador tenta entender a lógica do aplicativo e explora o aplicativo como um usuário. Ferramentas podem ser usadas para coletar informações. Por exemplo, um proxy HTTP pode ser usado para observar todas as solicitações e respostas HTTP. No final dessa fase, o testador deve entender geralmente todos os pontos de acesso e funcionalidades do sistema (por exemplo, cabeçalhos HTTP, parâmetros, cookies, APIs, uso/padrões de tecnologia, etc). A seção [Coleta de Informações](../01-Information_Gathering/README.md) explica como realizar testes passivos.

Por exemplo, um testador pode encontrar uma página no seguinte URL: `https://www.example.com/login/auth_form`

Isso pode indicar um formulário de autenticação onde o aplicativo solicita um nome de usuário e senha.

Os seguintes parâmetros representam dois pontos de acesso ao aplicativo: `https://www.example.com/appx?a=1&b=1`

Nesse caso, o aplicativo mostra dois pontos de acesso (parâmetros `a` e `b`). Todos os pontos de entrada encontrados nessa fase representam um alvo para testes. Manter controle do diretório ou árvore de chamadas do aplicativo e todos os pontos de acesso pode ser útil durante os testes ativos.

### Testes Ativos

Durante os testes ativos, um testador começa a usar as metodologias descritas nas seções seguintes.

O conjunto de testes ativos foram divididos em 12 categorias:

- Coleta de Informações
- Teste de Gerenciamento de Configuração e Implantação
- Teste de Gerenciamento de Identidade
- Teste de Autenticação
- Teste de Autorização
- Teste de Gerenciamento de Sessão
- Teste de Validação de Entrada
- Tratamento de Erro
- Criptografia
- Teste de Lógica de Negócios
- Teste do Lado do Cliente
- Teste de API