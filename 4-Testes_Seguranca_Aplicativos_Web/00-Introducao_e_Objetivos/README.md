# 4.0 Introdução e Objetivos

Esta seção descreve a metodologia OWASP de teste de segurança de aplicações web e explica como buscar por evidências de vulnerabilidades dentro de uma aplicação por conta de deficiências nos controles de segurança identificados.

## O que é o Teste de Segurança de Aplicações Web?

O teste de segurança é um método de avaliar a segurança de um sistema computacional ou rede através de validação metodológica e verificação da efetividade dos controles de segurança da aplicação. O teste de segurança de aplicações web foca apenas na avaliação da segurança de uma aplicação web. O processo envolve uma análise ativa da aplicação buscando por quaisquer fraquezas, falhas técnicas ou vulnerabilidades. Todos os problemas de segurança identificados serão apresentados para o responsável do sistema, juntamente com uma análise de impacto e uma proposta de mitigação ou solução técnica.

## O que é Vulnerabilidade?

Vulnerabilidade é uma falha ou fraqueza no design, implementação, operação ou gerenciamento de um sistema, o qual possa ser explorado para comprometer os objetivos de segurança do mesmo.

## O que é Ameaça?

Ameaça é qualquer coisa (um atacante malicioso externo, um usuário interno, uma instabilidade de sistema, etc.) que possa prejudicar os ativos pertencentes a uma aplicação (recursos valiosos como dados de um banco de dados ou de um sistema de arquivos) através da exploração de uma vulnerabilidade.

## O que é Teste?

Teste é uma ação para demonstrar se uma aplicação atende os requisitos de segurança das partes interessadas.

## O Formato para Escrita desse Guia

O formato OWASP é aberto e colaborativo:

- Aberto: qualquer especialista de segurança pode participar com sua experiência no projeto. Tudo é livre.
- Colaborativo: discussões são feitas antes dos artigos serem escritos, então o time pode compartilhar ideias e desenvolver uma visão coletiva do projeto. Isso significa um consenso rígido, uma maior audiência e aumento de participação.

Esse formato tende a criar uma Metodologia de Testes definida que será:

- Consistente
- Reproduzível
- Rigorosa
- Sob controle de qualidade

Os problemas que serão endereçados são totalmente documentados e testados. É importante usar um método para testar todas as vulnerabilidades conhecidas e documentar todas as atividades de teste de segurança.

## O que é a Metodologia OWASP de Testes?

Testes de segurança nunca serão uma ciência exata com uma lista completa de todos os possíveis problemas que devem ser testados que possa ser definida. Na verdade, testes de segurança é apenas uma técnica apropriada para testar a segurança de uma aplicação web em certas circustâncias. O objetivo desse projeto é coletar todas as possibilidades de técnicas de teste, explicar essas técnicas e manter o guia atualizado. O método OWASP de Teste de Segurança de Aplicações Web é baseado no formato caixa preta (blackbox). O testador não sabe nada ou tem muito pouca informação sobre a aplicação que será testada.

O modelo de teste consiste de:

- Testador: Quem realiza as atividades de teste
- Ferramentas e metodologia: O centro do projeto deste Guia de Teste
- Aplicação: A caixa preta a ser testada

Os testes podem ser classificados em ativos ou passivos:

### Teste Passivo
Durante os testes passivos, o testador tenta entender a lógica da aplicação e explorar a aplicação como um usuário. Ferramentas podem ser utilizadas para a coleta de informações (information gathering). Por exemplo, um proxy HTTP pode ser utilizado para observar todas as requisições e respostas HTTP. No final dessa fase, o testador deve normalmente entender todos os pontos de acesso e funcionalidades do sistema (por exemplo, cabeçalhos HTTP, parâmetros, cookies, APIs, uso/padrões de tecnologia, etc.). A seção [Coleta de Informações](../01-Coleta_de_Informacoes/README.md) explica como executar o teste passivo.

Por exemplo, um testador pode encontrar uma página na seguinte URL: `https://www.example.com/login/auth_form`

Isso pode indicar o uso de um formulário de autenticação, quando a aplicação solicita um usuário e senha.

Os parâmetros a seguir representam dois pontos de acesso à aplicação: `https://www.example.com/appx?a=1&b=1`

Nesse caso, a aplicação exibe dois pontos de acesso (parâmetros `a` e `b`). Todos os pontos de entrada encontrados nessa fase representam um alvo para os testes. Mantendo o rastreio do diretório ou árvore de chamadas da aplicação e de todos os pontos de acesso pode ser útil durante os testes ativos.

### Teste Ativo

Durante o teste ativo, o testador passa a utilizar a metodologia descrita nas seguintes seções.

O conjunto de testes ativos foram dividos em 12 categorias:

- Coleta de Informações
- Testes de Configuração e Gerenciamento de Implementação
- Testes de Gerenciamento de Identidade
- Testes de Autenticação
- Testes de Autorização
- Testes de Gerenciamento de Sessão
- Testes de Validação de Entrada de Dados
- Testes de Tratamento de Erros
- Testes de Criptografia
- Testes de Lógica de Negócios
- Testes de Nível do Cliente
- Testes de API# 4.0 Introdução e Objetivos

Esta seção descreve a metodologia OWASP de teste de segurança de aplicações web e explica como buscar por evidências de vulnerabilidades dentro de uma aplicação por conta de deficiências nos controles de segurança identificados.

## O que é o Teste de Segurança de Aplicações Web?

O teste de segurança é um método de avaliar a segurança de um sistema computacional ou rede através de validação metodológica e verificação da efetividade dos controles de segurança da aplicação. O teste de segurança de aplicações web foca apenas na avaliação da segurança de uma aplicação web. O processo envolve uma análise ativa da aplicação buscando por quaisquer fraquezas, falhas técnicas ou vulnerabilidades. Todos os problemas de segurança identificados serão apresentados para o responsável do sistema, juntamente com uma análise de impacto e uma proposta de mitigação ou solução técnica.

## O que é Vulnerabilidade?

Vulnerabilidade é uma falha ou fraqueza no design, implementação, operação ou gerenciamento de um sistema, o qual possa ser explorado para comprometer os objetivos de segurança do mesmo.

## O que é Ameaça?

Ameaça é qualquer coisa (um atacante malicioso externo, um usuário interno, uma instabilidade de sistema, etc.) que possa prejudicar os ativos pertencentes a uma aplicação (recursos valiosos como dados de um banco de dados ou de um sistema de arquivos) através da exploração de uma vulnerabilidade.

## O que é Teste?

Teste é uma ação para demonstrar se uma aplicação atende os requisitos de segurança das partes interessadas.

## O Formato para Escrita desse Guia

O formato OWASP é aberto e colaborativo:

- Aberto: qualquer especialista de segurança pode participar com sua experiência no projeto. Tudo é livre.
- Colaborativo: discussões são feitas antes dos artigos serem escritos, então o time pode compartilhar ideias e desenvolver uma visão coletiva do projeto. Isso significa um consenso rígido, uma maior audiência e aumento de participação.

Esse formato tende a criar uma Metodologia de Testes definida que será:

- Consistente
- Reproduzível
- Rigorosa
- Sob controle de qualidade

Os problemas que serão endereçados são totalmente documentados e testados. É importante usar um método para testar todas as vulnerabilidades conhecidas e documentar todas as atividades de teste de segurança.

## O que é a Metodologia OWASP de Testes?

Testes de segurança nunca serão uma ciência exata com uma lista completa de todos os possíveis problemas que devem ser testados que possa ser definida. Na verdade, testes de segurança é apenas uma técnica apropriada para testar a segurança de uma aplicação web em certas circustâncias. O objetivo desse projeto é coletar todas as possibilidades de técnicas de teste, explicar essas técnicas e manter o guia atualizado. O método OWASP de Teste de Segurança de Aplicações Web é baseado no formato caixa preta (blackbox). O testador não sabe nada ou tem muito pouca informação sobre a aplicação que será testada.

O modelo de teste consiste de:

- Testador: Quem realiza as atividades de teste
- Ferramentas e metodologia: O centro do projeto deste Guia de Teste
- Aplicação: A caixa preta a ser testada

Os testes podem ser classificados em ativos ou passivos:

### Teste Passivo
Durante os testes passivos, o testador tenta entender a lógica da aplicação e explorar a aplicação como um usuário. Ferramentas podem ser utilizadas para a coleta de informações (information gathering). Por exemplo, um proxy HTTP pode ser utilizado para observar todas as requisições e respostas HTTP. No final dessa fase, o testador deve normalmente entender todos os pontos de acesso e funcionalidades do sistema (por exemplo, cabeçalhos HTTP, parâmetros, cookies, APIs, uso/padrões de tecnologia, etc.). A seção [Coleta de Informações](../01-Coleta_de_Informacoes/README.md) explica como executar o teste passivo.

Por exemplo, um testador pode encontrar uma página na seguinte URL: `https://www.example.com/login/auth_form`

Isso pode indicar o uso de um formulário de autenticação, quando a aplicação solicita um usuário e senha.

Os parâmetros a seguir representam dois pontos de acesso à aplicação: `https://www.example.com/appx?a=1&b=1`

Nesse caso, a aplicação exibe dois pontos de acesso (parâmetros `a` e `b`). Todos os pontos de entrada encontrados nessa fase representam um alvo para os testes. Mantendo o rastreio do diretório ou árvore de chamadas da aplicação e de todos os pontos de acesso pode ser útil durante os testes ativos.

### Teste Ativo

Durante o teste ativo, o testador passa a utilizar a metodologia descrita nas seguintes seções.

O conjunto de testes ativos foram dividos em 12 categorias:

- Coleta de Informações
- Testes de Configuração e Gerenciamento de Implementação
- Testes de Gerenciamento de Identidade
- Testes de Autenticação
- Testes de Autorização
- Testes de Gerenciamento de Sessão
- Testes de Validação de Entrada de Dados
- Testes de Tratamento de Erros
- Testes de Criptografia
- Testes de Lógica de Negócios
- Testes de Nível do Cliente
- Testes de API
