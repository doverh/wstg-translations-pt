# Testar Tomada de Subdomínio

|ID          |
|------------|
|WSTG-CONF-10|

## Resumo

A exploração bem-sucedida desse tipo de vulnerabilidade permite que um adversário reivindique e assuma o controle do subdomínio da vítima. Esse ataque depende dos seguintes fatores:

1. O registro de subdomínio do servidor DNS externo da vítima está configurado para apontar para um recurso/serviço externo não existente ou inativo. A proliferação de produtos XaaS (Anything as a Service) e serviços em nuvem pública oferece muitos alvos potenciais a serem considerados.
2. O provedor de serviços que hospeda o recurso/serviço externo não lida adequadamente com a verificação de propriedade de subdomínio.

Se a tomada de subdomínio for bem-sucedida, uma ampla variedade de ataques é possível (fornecimento de conteúdo malicioso, phishing, roubo de cookies de sessão de usuário, credenciais, etc.). Essa vulnerabilidade pode ser explorada em uma ampla variedade de registros de recursos DNS, incluindo: `A`, `CNAME`, `MX`, `NS`, `TXT` etc. Em termos de gravidade do ataque, a tomada de subdomínio `NS` (embora menos provável) tem o maior impacto, pois um ataque bem-sucedido pode resultar no controle total sobre toda a zona DNS e o domínio da vítima.

### GitHub

1. A vítima (victim.com) usa o GitHub para desenvolvimento e configura um registro DNS (`coderepo.victim.com`) para acessá-lo.
2. A vítima decide migrar seu repositório de código do GitHub para uma plataforma comercial e não remove `coderepo.victim.com` de seu servidor DNS.
3. Um adversário descobre que `coderepo.victim.com` está hospedado no GitHub e usa as páginas do GitHub para reivindicar `coderepo.victim.com` usando sua conta no GitHub.

### Domínio Expirado

1. A vítima (victim.com) possui outro domínio (victimotherdomain.com) e usa um registro CNAME (www) para referenciar o outro domínio (`www.victim.com` --> `victimotherdomain.com`)
2. Em algum momento, victimotherdomain.com expira e fica disponível para registro por qualquer pessoa. Como o registro CNAME não é excluído da zona DNS da victim.com, qualquer pessoa que registrar `victimotherdomain.com` tem controle total sobre `www.victim.com` até que o registro DNS esteja presente.

## Objetivos do Teste

- Enumerar todos os domínios possíveis (anteriores e atuais).
- Identificar domínios esquecidos ou mal configurados.

## Como Testar

### Teste de Caixa Preta

O primeiro passo é enumerar os servidores DNS da vítima e os registros de recursos. Existem várias maneiras de realizar essa tarefa, como a enumeração de DNS usando uma lista de dicionários de subdomínios comuns, força bruta de DNS ou o uso de motores de busca na web e outras fontes de dados OSINT.

Usando o comando dig, o testador procura pelas seguintes mensagens de resposta do servidor DNS que exigem investigação adicional:

- `NXDOMAIN`
- `SERVFAIL`
- `REFUSED`
- `no servers could be reached.`

#### Testando Subdomínio Takeover de Registro DNS A, CNAME

Execute uma enumeração básica de DNS no domínio da vítima (`victim.com`) usando `dnsrecon`:

```bash
$ ./dnsrecon.py -d victim.com
[*] Performing General Enumeration of Domain: victim.com
...
[-] DNSSEC is not configured for victim.com
[*]      A subdomain.victim.com 192.30.252.153
[*]      CNAME subdomain1.victim.com fictioussubdomain.victim.com
...
```

Identifique quais registros de recursos DNS estão mortos e apontam para serviços inativos/não utilizados. Usando o comando dig para o registro `CNAME`:

```bash
$ dig CNAME fictioussubdomain.victim.com
; <<>> DiG 9.10.3-P4-Ubuntu <<>> ns victim.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 42950
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1
```

As seguintes respostas do DNS exigem investigação adicional: `NXDOMAIN`.

Para testar o registro `A`, o testador realiza uma pesquisa no banco de dados whois e identifica o GitHub como o provedor de serviços:

```bash
$ whois 192.30.252.153 | grep "OrgName"
OrgName: GitHub, Inc.
```

O testador visita `subdomain.victim.com` ou emite uma solicitação HTTP GET que retorna uma resposta "404 - Arquivo não encontrado", o que é uma indicação clara da vulnerabilidade.

![GitHub 404 File Not Found response](images/subdomain_takeover_ex1.jpeg)\
*Figura 4.2.10-1: Resposta do GitHub 404 Arquivo não encontrado*

O testador reivindica o domínio usando as Páginas do GitHub:

![GitHub claim domain](images/subdomain_takeover_ex2.jpeg)\
*Figura 4.2.10-2: Reivindicar domínio no GitHub*

#### Testando Subdomínio Takeover de Registro NS

Identifique todos os servidores de nomes para o domínio em questão:

```bash
$ dig ns victim.com +short
ns1.victim.com
nameserver.expireddomain.com
```

Neste exemplo fictício, o testador verifica se o domínio `expireddomain.com` está ativo com uma pesquisa de registro de domínio. Se o domínio estiver disponível para compra, o subdomínio estará vulnerável.

As seguintes respostas do DNS exigem investigação adicional: `SERVFAIL` ou `REFUSED`.

### Teste de Caixa Cinza

O testador tem o arquivo de zona DNS disponível, o que significa que a enumeração de DNS não é necessária. A metodologia de teste é a mesma.

## Remediação

Para mitigar o risco de subdomínio takeover, o(s) registro(s) de recursos DNS vulnerável(eis) devem ser removidos da zona DNS. Monitoramento contínuo e verificações periódicas são recomendados como prática recomendada.

## Ferramentas

- [dig - man page

](https://linux.die.net/man/1/dig)
- [recon-ng - Framework de Reconhecimento Web](https://github.com/lanmaster53/recon-ng)
- [theHarvester - Ferramenta de coleta de inteligência OSINT](https://github.com/laramies/theHarvester)
- [Sublist3r - Ferramenta de enumeração de subdomínios OSINT](https://github.com/aboul3la/Sublist3r)
- [dnsrecon - Script de Enumeração de DNS](https://github.com/darkoperator/dnsrecon)
- [OWASP Amass DNS enumeration](https://github.com/OWASP/Amass)

## Referências

- [HackerOne - Um Guia Para Subdomain Takeovers](https://www.hackerone.com/blog/Guide-Subdomain-Takeovers)
- [Subdomain Takeover: Noções básicas](https://0xpatrik.com/subdomain-takeover-basics/)
- [Subdomain Takeover: Indo além do CNAME](https://0xpatrik.com/subdomain-takeover-ns/)
- [OWASP AppSec Europe 2017 - Frans Rosén: Seqüestro de DNS usando provedores de nuvem - sem verificação necessária](https://2017.appsec.eu/presos/Developer/DNS%20hijacking%20using%20cloud%20providers%20%E2%80%93%20no%20verification%20needed%20-%20Frans%20Rosen%20-%20OWASP_AppSec-Eu_2017.pdf)