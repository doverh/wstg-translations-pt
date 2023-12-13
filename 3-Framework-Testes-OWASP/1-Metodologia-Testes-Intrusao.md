# Metodologias de Teste de Invasão

## Resumo

- [Guias de Testes OWASP](#owasp-testing-guides)
  - Guia de Testes de Segurança na Web (WSTG)
  - Guia de Testes de Segurança para Dispositivos Móveis (MSTG)
  - Metodologia de Teste de Segurança para Firmware
- [Padrão de Execução de Teste de Invasão](#penetration-testing-execution-standard)
- [Guia de Teste de Invasão PCI](#pci-penetration-testing-guide)
  - [Orientações de Teste de Invasão PCI DSS](#pci-dss-penetration-testing-guidance)
  - [Requisitos de Teste de Invasão PCI DSS](#pci-dss-penetration-testing-requirements)
- [Estrutura de Teste de Invasão](#penetration-testing-framework)
- [Guia Técnico para Testes e Avaliação de Segurança da Informação](#technical-guide-to-information-security-testing-and-assessment)
- [Manual de Metodologia de Testes de Segurança de Código Aberto](#open-source-security-testing-methodology-manual)
- [Referências](#references)

## Guias de Testes OWASP

Em termos de execução técnica de testes de segurança, os guias de teste da OWASP são altamente recomendados. Dependendo dos tipos de aplicativos, os guias de teste estão listados abaixo para serviços web/cloud, aplicativos móveis (Android/iOS) ou firmware de IoT, respectivamente.

- [Guia de Testes de Segurança na Web da OWASP](https://owasp.org/www-project-web-security-testing-guide/)
- [Guia de Testes de Segurança para Dispositivos Móveis da OWASP](https://owasp.org/www-project-mobile-security-testing-guide/)
- [Metodologia de Teste de Segurança para Firmware da OWASP](https://github.com/scriptingxss/owasp-fstm)

## Padrão de Execução de Teste de Invasão

O Padrão de Execução de Teste de Invasão (PTES) define testes de invasão em 7 fases. Em particular, as Diretrizes Técnicas do PTES oferecem sugestões práticas sobre procedimentos de teste e recomendações para ferramentas de teste de segurança.

- Interações Pré-Comprometimento
- Coleta de Inteligência
- Modelagem de Ameaças
- Análise de Vulnerabilidades
- Exploração
- Pós-Exploração
- Relatório

[Diretrizes Técnicas do PTES](http://www.pentest-standard.org/index.php/PTES_Technical_Guidelines)

## Guia de Teste de Invasão PCI

O Padrão de Segurança de Dados da Indústria de Cartões de Pagamento (PCI DSS) na Requisição 11.3 define o teste de invasão. O PCI também define Orientações para Teste de Invasão.

### Orientações de Teste de Invasão PCI DSS

O guia de teste de invasão do PCI DSS fornece orientações sobre o seguinte:

- Componentes de Teste de Invasão
- Qualificações de um Testador de Invasão
- Metodologias de Teste de Invasão
- Diretrizes para Relatórios de Teste de Invasão

### Requisitos de Teste de Invasão PCI DSS

O requisito do PCI DSS refere-se à Requisição 11.3 do Padrão de Segurança de Dados da Indústria de Cartões de Pagamento (PCI DSS).

- Com base em abordagens aceitas pela indústria
- Cobertura para Ambiente de Dados do Titular do Cartão (CDE) e sistemas críticos
- Inclui testes externos e internos
- Testes para validar a redução de escopo
- Testes de camada de aplicação
- Testes de camada de rede para rede e sistemas operacionais

[Orientações de Teste de Invasão do PCI DSS](https://www.pcisecuritystandards.org/documents/Penetration_Testing_Guidance_March_2015.pdf)

## Estrutura de Testes de Invasão

A Estrutura de Teste de Invasão (PTF) fornece um guia abrangente e prático de teste de invasão. Também lista os usos das ferramentas de teste de segurança em cada categoria de teste. As principais áreas de teste de invasão incluem:

- Levantamento de Informações de Rede (Reconhecimento)
- Descoberta & Sondagem
- Enumeração
- Quebra de Senhas
- Avaliação de Vulnerabilidades
- Auditoria AS/400
- Testes Específicos para Bluetooth
- Testes Específicos para Cisco
- Testes Específicos para Citrix
- Backbone de Rede
- Testes Específicos para Servidores
- Segurança VoIP
- Invasão Wireless
- Segurança Física
- Relatório Final - modelo

[Estrutura de Teste de Invasão](http://www.vulnerabilityassessment.co.uk/Penetration%20Test.html)

## Guia Técnico para Testes e Avaliação de Segurança da Informação

O Guia Técnico para Testes e Avaliação de Segurança da Informação (NIST 800-115) foi publicado pelo NIST e inclui algumas técnicas de avaliação listadas abaixo.

- Técnicas de Revisão
- Identificação e Análise de Alvos
- Técnicas de Validação de Vulnerabilidades de Alvos
- Planejamento de Avaliação de Segurança
- Execução de Avaliação de Segurança
- Atividades Pós-Teste

O NIST 800-115 pode ser acessado [aqui](https://csrc.nist.gov/publications/detail/sp/800-115/final)

## Manual de Metodologia de Testes de Segurança de Código Aberto

O Manual de Metodologia de Testes de Segurança de Código Aberto (OSSTMM) é uma metodologia para testar a segurança operacional de locais físicos, fluxo de trabalho, testes de segurança humana, testes de segurança física, testes de segurança sem fio, testes de segurança de telecomunicações, testes de segurança de redes de dados e conformidade. O OSSTMM pode ser uma referência de suporte para a ISO 27001, em vez de um guia prático ou técnico de teste de invasão.

O OSSTMM inclui as seguintes seções principais:

- Análise de Segurança
- Métricas de Segurança Operacional
- Análise de Confiança
- Fluxo de Trabalho
- Testes de Segurança Humana
- Testes de Segurança Física
- Testes de Segurança Sem Fio
- Testes de Segurança de Telecomunicações
- Testes de Segurança de Redes de Dados
- Regulamentações de Conformidade
- Relatório com o STAR (Relatório de Auditoria de Teste de Segurança)

[Manual de Metodologia de Testes de Segurança de Código Aberto](https://www.isecom.org/OSSTMM.3.pdf)

## Referências

- [Padrão de Segurança de Dados da Indústria de Cartões de Pagamento - Orientações para Testes de invasão](https://www.pcisecuritystandards.org/documents/Penetration-Testing-Guidance-v1_1.pdf)
- [Padrão de Execução de Teste de invasão (PTES)](http://www.pentest-standard.org/index.php/Main_Page)
- [Manual de Metodologia de Testes de Segurança de Código Aberto (OSSTMM)](http://www.isecom.org/research/osstmm.html)
- [Guia Técnico para Testes e Avaliação de Segurança da Informação NIST SP 800-115](https://csrc.nist.gov/publications/detail/sp/800-115/final)
- [Orientações de Teste de Segurança HIPAA 2012](http://csrc.nist.gov/news_events/hiipaa_june2012/day2/day2-6_kscarfone-rmetzer_security-testing-assessment.pdf)
- [Estrutura de Teste de invasão 0.59](http://www.vulnerabilityassessment.co.uk/Penetration%20Test.html)
- [Guia de Testes de Segurança para Dispositivos Móveis da OWASP](https://owasp.org/www-project-mobile-security-testing-guide/)
- [Diretrizes de Teste de Segurança para Aplicativos Móveis](https://owasp.org/www-pdf-archive/Security_Testing_Guidelines_for_mobile_Apps_-_Florian_Stahl%2BJohannes_Stroeher.pdf)
- [Kali Linux](https://www.kali.org/)
- [Suplemento de Informações: Requisito 11.3 Teste de Invasão](https://www.pcisecuritystandards.org/pdfs/infosupp_11_3_penetration_testing.pdf)
