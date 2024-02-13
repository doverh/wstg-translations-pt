# Traduz a versão 4.2 do Guia de Testes de Segurança da OWASP para o Português do Brasil. 
Texto original em inglês pode ser encontrado em https://github.com/OWASP/wstg/releases/tag/v4.2.

# Como contribuir para esta tradução


Ao submeter um Pull Request [pull request](#how-to-submit-a-pull-request), revisores e editores devem associar as mudanças a uma sessão:

1. Escolha uma sessão a qual você gostaria de revisar.
2. Clone o repositório e submite um Pull Request, em uma branch com o nome `revisa-<numero>`. Por exemplo, `git checkout -b revisa-4-01-07`.


# Sumário

## 0. [Prefácio por Eoin Keary](0-Prefacio/README.md)

## 1. [Frontispicio](1-Frontispicio/)

## 2. [Introdução](2-Introducao/)

### 2.1 [O Projeto de Testes OWASP](2-Introducao/README.md#O-Projeto-de-Testes-OWASP)

### 2.2 [Principios de Testes](2-Introducao/README.md#Principios-de-Testes)

### 2.3 [Técnicas de teste explicadas](2-Introducao/README.md#Técnicas-de-teste-explicadas)

### 2.4 [Inspeção manual e revisões](2-Introducao/README.md#MInspeção-manual-e-revisões)

### 2.5 [Modelagem de ameaças](2-Introducao/README.md#Modelagem-de-ameaças)

### 2.6 [Revisão de Código-Fonte](2-Introducao/README.md#Revisão-de-Código-Fonte)

### 2.7 [Testes de Invasão](2-Introducao/README.md#Testes-de-Invasão)

### 2.8 [A necessidade de uma abordagem equilibrada](2-Introducao/README.md#A-necessidade-de-uma-abordagem-equilibrada)

### 2.9 [Obtendo requisitos de testes seguros](2-Introducao/README.md#Obtendo-requisitos-de-testes-seguros)

### 2.10 [Testes de segurança integrados ao ciclo de desenvolvimento e testes](2-Introducao/README.md#Testes-de-segurança-integrados-ao-ciclo-de-desenvolvimento-e-testes)

### 2.11 [Testes de Segurança - Análise de dados e relatórios](2-Introducao/README.md#Testes-de-Segurança-Análise-de-dados-e-relatórios)

## 3. [Framework de Testes OWASP](3-Framework-Testes-OWASP/)

### 3.1 [Framework de Testes de Seguranca Web](3-Framework-Testes-OWASP/0-Framework_Testes-Seguranca-Web.md)

### 3.2 [Fase 1 Pré Desenvolvimento](3-Framework-Testes-OWASP/0-Framework_Testes-Seguranca-Web.md#Fase-1-Pre-Desenvolvimento)

### 3.3 [Fase 2 Durante a Definição e o Design](3-Framework-Testes-OWASP/0-Framework_Testes-Seguranca-Web.md#Fase-2-Durante-a-Definição-e-o-Design)

### 3.4 [Fase 3 Durante o Desenvolvimento](3-Framework-Testes-OWASP/0-Framework_Testes-Seguranca-Web.md#Fase-3-Durante-o-Desenvolvimento)

### 3.5 [Fase 4 Durante a Entrega](3-Framework-Testes-OWASP/0-Framework_Testes-Seguranca-Web.md#Fase-4-Durante-a-Entrega)

### 3.6 [Fase 5 Durante a Manutenção e Operações and Operations](3-Framework-Testes-OWASP/0-Framework_Testes-Seguranca-Web.md#Fase-5-Durante-a-Manutenção-e-Operacoes-and-Operations)

### 3.7 [Típico Workflow de Testes SDLC](3-Framework-Testes-OWASP/0-Framework_Testes-Seguranca-Web.md#/0-Framework-Testes-Seguranca-Web)

### 3.8 [Metodologias de Testes de Invasão](3-Framework-Testes-OWASP/1-Penetration_Testing_Methodologies.md)

## 4. [Testes Seguranca de Aplicativos Web](4-Testes-Seguranca-Web-Apps/)

### 4.0 [Introdução e Objetivos](4-Testes-Seguranca-Web-Apps/00-Introducao-e-Objetivos/README.md)

### 4.1 [Coleta de Informações](4-Testes-Seguranca-Web-Apps/01-Coleta-de-Informacoes/README.md)

### 4.2 [Testes de Configuração e Gerenciamento de Implementacao](4-Testes-Seguranca-Web-Apps/02-Testes-de-Configuracao-e-Gerenciamento-de-Implementacao/README.md)

### 4.3 [Testes Gerenciamento de Identidade](4-Testes-Seguranca-Web-Apps/03-Testes-Gerenciamento-de-Identidade/README.md)

### 4.4 [Testes de Autenticação](4-Testes-Seguranca-Web-Apps/04-Testes-de-Autenticacao/README.md)

### 4.5 [Testes de Autorização](4-Testes-Seguranca-Web-Apps/05-Testes-de-Autorizacao/README.md)

### 4.6 [Testes Gerenciamento de Sessão](4-Testes-Seguranca-Web-Apps/06-Testes-Gerenciamento-de-Sessao/README.md)

### 4.7 [Testes de Validação de Entrada de Dados](4-Testes-Seguranca-Web-Apps/07-Testes-Validacao-de-Entrada-de-Dados/README.md)

### 4.8 [Testes Tratamento de Erros](4-Testes-Seguranca-Web-Apps/08-Testes-Tratamento-de-Erros/README.md)

### 4.9 [Testes para Criptografia Frágil](4-Testes-Seguranca-Web-Apps/09-Testes-para-Criptografia-Fragil/README.md)

### 4.10 [Testes de Lógica de Negócios](4-Testes-Seguranca-Web-Apps/10-Testes-de-Logica-de-Negocios/README.md)

### 4.11 [Testes a Nível do Cliente](4-Testes-Seguranca-Web-Apps/11-Testes-a-Nivel-do-Cliente/README.md)

### 4.12 [Testes de API](4-Testes-Seguranca-Web-Apps/12-Testes-de-API/README.md)

## 5. [Relatórios](5-Relatorios/README.md)

## 6. [Apêndice](6-Apendice/README.md)

## Apêndice A. [Testing Tools Resource](6-Apendice/A-Testing_Tools_Resource.md)

## Apêndice B. [Suggested Reading](6-Apendice/B-Suggested_Reading.md)

## Apêndice C. [Fuzzing](6-Apendice/C-Fuzzing.md)

## Apêndice D. [Encoded Injection](6-Apendice/D-Encoded_Injection.md)

## Apêndice E. [History](6-Apendice/E-History.md)

## Apêndice F. [Leveraging Dev Tools](6-Apendice/F-Leveraging_Dev_Tools.md)
