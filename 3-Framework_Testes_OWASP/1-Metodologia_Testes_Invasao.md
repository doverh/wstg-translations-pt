# Metodologias para testes de invasão

## Sumário

- [Guia de testes OWASP](#guia-de-testes-owasp)
  - Guia de testes de segurança Web (WSTG)
  - Guia de testes de segurança Mobile (MSTG)
  - Metodologia de teste para segurança firmware
- Padrão de execução para testes de invasão]
- PCI Guia de testes de invasão
  - PCI DSS recomendações para testes de invasão
  - PCI DSS requisitos de testes de invasão
- Framework de testes de invasão](#framework-de-testes)
- Guia técnico de testes e verificação de segurança da informação
- Manual de metodologia de testes de segurança de código aberto
- [Referências](#referencias)

## Guia de testes owasp

Em termos de execução de testes técnicos de segurança, os guias de teste OWASP são altamente recomendados. Dependendo dos tipos de aplicativos, os guias de teste listados abaixo para os serviços da web / nuvem, aplicativos moveis (Android / iOS) ou firmware IoT, respectivamente. 

- [OWASP Guia de testes de segurança Web](https://owasp.org/www-project-web-security-testing-guide/)
- [OWASP Guia de testes de segurança Mobile](https://owasp.org/www-project-mobile-security-testing-guide/)
- [OWASP Metodologia de teste para segurança firmware](https://github.com/scriptingxss/owasp-fstm)

## Padrão de execução para testes de invasão

Padrão de execução para testes de invasão (PTES) define testes de invasão através de 7 fases. Particularmente, o guia técnico PTES sugere procedimentos interativos de testes, e recomendações para ferramentas de testes de software.

- Interações de pré-engajamento
- Reunião de inteligência 
- Modelagem de ameaças
- Análise de vulnerabilidades 
- Exploração (abuso)
- Pós exploração (abuso)
- Relatórios

[Guia técnico PTES](http://www.pentest-standard.org/index.php/PTES_Technical_Guidelines)

## PCI Guia de testes de invasão

Segurança de dados da indústria de meios de pagamento (PCI DSS) Requisito 11.3 define o teste de invasão. PCI também define as recomendações dos testes de invasão.

### PCI DSS Recomendações para testes de invasão

The PCI DSS teste de invasão fornece orientações para as seguintes atividades:

- Componentes dos testes de invasão
- Qualificações do Testador (Pen Tester)
- Metodologias de testes de invasão
- Guias de relatórios para testes de invasão

### PCI DSS requisitos de testes de invasao

Os requisitos PCI DSS referentes à segurança de dados da indústria de meios de pagamento (PCI DSS) Requisitos 11.3

- Baseados nas abordagens aceitas pela indústria
- Cobertura para CDE e sistemas críticos 
- Inclui testes internos e externos
- Testes para validar redução de escopo
- Testes de camadas de aplicação
- Testes da camada de rede para a rede e o sistema

[PCI DSS recomendações para testes de invasão](https://www.pcisecuritystandards.org/documents/Penetration_Testing_Guidance_March_2015.pdf)

## Framework de testes de invasão

O framework de testes de invasão (PTF) fornece um compreensivo guia interativo de testes de invasão. Também lista usos de ferramentas de testes de segurança em cada categoria de teste. A maior área de testes de invasão inclui:

- Rastro de rede (reconhecimento) (Network Footprinting (Reconnaissance))
- Descoberta e sondagem (Discovery & Probing)
- Enumeração (Enumeration)
- Quebra de senha (Password cracking)
- Avaliação de vulnerabilidade (Vulnerability Assessment)
- Auditoria AS/400 (AS/400 Auditing)
- Teste específico de Bluetooth (Bluetooth Specific Testing)
- Teste específico Cisco (Cisco Specific Testing)
- Teste específico Citrix (Citrix Specific Testing)
- Backbone de rede (Network Backbone)
- Testes específicos de servidor (Server Specific Tests)
- Segurança VoIP (Voip Security)
- Invasão em rede sem fio (Wireless Penetration)
- Segurança física (Physical Security)
- Modelo de relatório final (Final Report – template)

[Framework de testes de invasão
](http://www.vulnerabilityassessment.co.uk/Penetration%20Test.html)

## Guia tecnico de testes e verificação de segurança da informação

Guia técnico de testes e verificação de segurança da informação (NIST 800-115) foi publicado pela NIST, inclui algumas das técnicas de avaliação listadas abaixo.

- Técnicas de revisão 
- Identificações alvo e técnicas de análises
- Técnicas de validação de vulnerabilidades alvo 
- Planejamento de avaliação de segurança
- Execução de avaliação de segurança 
- Atividades pós teste

A norma NIST 800-115 pode ser acessada [aqui](https://csrc.nist.gov/publications/detail/sp/800-115/final)

## Manual de metodologia de testes de segurança de código aberto

O manual de metodologia de testes de segurança de código aberto (OSSTMM) é uma metodologia para testar a segurança operacional de locais físicos, workflow, testes de segurança humana, testes de segurança física, teste de segurança wireless, teste de segurança de telecomunicações, teste de segurança e conformidade de redes de dados. OSSTMM pode servir de referência de suporte da ISO 27001 em vez de um guia prático ou de um guia de testes de invasão de aplicativos técnicos.


OSSTMM inclui as seguintes seções chaves:
- Análise de segurança (Security Analysis)
- Métricas de segurança operacional (Operational Security Metrics)
- Análise de confiança (Trust analysis)
- Fluxo de trabalho (Work flow)
- Teste de segurança humana (Human Security Testing)
- Teste de segurança física (Physical Security Testing)
- Teste de segurança de telecomunicações (Wireless Security Testing)
- Teste de segurança de redes de dados (Telecommunications Security Testing)
- Teste de segurança de telecomunicações (Data Networks Security Testing)
- Regulamentos de conformidade (Compliance Regulations)
- Relatório de auditoria de teste de segurança (STAR - Security Test Audit Report)

[Manual de metodologia de testes de segurança de código aberto](https://www.isecom.org/OSSTMM.3.pdf)

## Referencias

- [Padrão PCI de Segurança de Dados - Guia de Testes de Invasão](https://www.pcisecuritystandards.org/documents/Penetration-Testing-Guidance-v1_1.pdf)
- [Padrão PTES](http://www.pentest-standard.org/index.php/Main_Page)
- [Manual de metodologia de testes de segurança de código aberto (OSSTMM)](http://www.isecom.org/research/osstmm.html)
- [Guia Técnico de Testes e Validação de Segurança da Informação  NIST SP 800-115](https://csrc.nist.gov/publications/detail/sp/800-115/final)
- [HIPAA Testes e Validação de Segurança 2012](http://csrc.nist.gov/news_events/hiipaa_june2012/day2/day2-6_kscarfone-rmetzer_security-testing-assessment.pdf)
- [Framework 0.59 de Testes de Invasão](http://www.vulnerabilityassessment.co.uk/Penetration%20Test.html)
- [Guia de Testes de Segurança Mobile OWASP](https://owasp.org/www-project-mobile-security-testing-guide/)
- [Recomendações de Segurança para Mobile Apps](https://owasp.org/www-pdf-archive/Security_Testing_Guidelines_for_mobile_Apps_-_Florian_Stahl%2BJohannes_Stroeher.pdf)
- [Kali Linux](https://www.kali.org/)
- [Informação Suplementar: Requisito 11.3 para Testes de Invasão](https://www.pcisecuritystandards.org/pdfs/infosupp_11_3_penetration_testing.pdf)

