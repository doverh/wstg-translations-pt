# Testar Política de Domínio Cruzado em RIA

|ID          |
|------------|
|WSTG-CONF-08|

## Resumo

As Aplicações RIA (Rich Internet Application) adotaram os arquivos de política crossdomain.xml da Adobe para permitir acesso controlado entre domínios a dados e consumo de serviços usando tecnologias como Oracle Java, Silverlight e Adobe Flash. Portanto, um domínio pode conceder acesso remoto aos seus serviços a partir de um domínio diferente. No entanto, muitas vezes, os arquivos de política que descrevem as restrições de acesso são mal configurados. A configuração inadequada desses arquivos possibilita ataques de Cross-Site Request Forgery (CSRF) e pode permitir que terceiros acessem dados sensíveis destinados ao usuário.

### O que são arquivos de política de domínio cruzado?

Um arquivo de política de domínio cruzado especifica as permissões que um cliente web, como Java, Adobe Flash, Adobe Reader, etc., utiliza para acessar dados em diferentes domínios. No caso do Silverlight, a Microsoft adotou um subconjunto do crossdomain.xml da Adobe e criou seu próprio arquivo de política de domínio cruzado: clientaccesspolicy.xml.

Sempre que um cliente web detecta que um recurso precisa ser solicitado de outro domínio, ele procurará primeiro por um arquivo de política no domínio de destino para determinar se a realização de solicitações entre domínios, incluindo cabeçalhos e conexões baseadas em soquetes, é permitida.

Arquivos de política mestre estão localizados na raiz do domínio. Um cliente pode ser instruído a carregar um arquivo de política diferente, mas sempre verificará o arquivo de política mestre primeiro para garantir que o arquivo de política solicitado seja permitido pelo arquivo de política mestre.

#### Crossdomain.xml vs. Clientaccesspolicy.xml

A maioria das aplicações RIA suporta crossdomain.xml. No entanto, no caso do Silverlight, ele só funcionará se o crossdomain.xml especificar que o acesso é permitido a partir de qualquer domínio. Para um controle mais granular com o Silverlight, deve-se usar o clientaccesspolicy.xml.

Os arquivos de política concedem vários tipos de permissões:

- Arquivos de política aceitos (arquivos de política mestre podem desativar ou restringir arquivos de política específicos)
- Permissões de soquete
- Permissões de cabeçalho
- Permissões de acesso HTTP/HTTPS
- Permitir acesso com base em credenciais criptográficas

Um exemplo de arquivo de política excessivamente permissivo:

```xml
<?xml version="1.0"?>
<!DOCTYPE cross-domain-policy SYSTEM
"http://www.adobe.com/xml/dtds/cross-domain-policy.dtd">
<cross-domain-policy>
    <site-control permitted-cross-domain-policies="all"/>
    <allow-access-from domain="*" secure="false"/>
    <allow-http-request-headers-from domain="*" headers="*" secure="false"/>
</cross-domain-policy>
```

### Como os arquivos de política de domínio cruzado podem ser explorados?

- Políticas cruzadas de domínio excessivamente permissivas.
- Geração de respostas do servidor que podem ser tratadas como arquivos de política de domínio cruzado.
- Uso da funcionalidade de upload de arquivos para enviar arquivos que podem ser tratados como arquivos de política de domínio cruzado.

### Impacto da Exploração do Acesso entre Domínios

- Derrota de proteções CSRF.
- Leitura de dados restritos ou protegidos por políticas de origem cruzada.

## Objetivos do Teste

- Revisar e validar os arquivos de política.

## Como Testar

### Testando a Fragilidade dos Arquivos de Política RIA

Para testar a fragilidade dos arquivos de política RIA, o testador deve tentar recuperar os arquivos de política crossdomain.xml e clientaccesspolicy.xml na raiz da aplicação e de todas as pastas encontradas.

Por exemplo, se a URL da aplicação for `http://www.owasp.org`, o testador deve tentar baixar os arquivos `http://www.owasp.org/crossdomain.xml` e `http://www.owasp.org/clientaccesspolicy.xml`.

Após recuperar todos os arquivos de política, as permissões concedidas devem ser verificadas sob o princípio do menor privilégio. As solicitações devem vir apenas dos domínios, portas ou protocolos necessários. Políticas excessivamente permissivas devem ser evitadas. Políticas com `*` nelas devem ser examinadas de perto.

#### Exemplo

```xml
<cross-domain-policy>
    <allow-access-from domain="*" />
</cross-domain-policy>
```

##### Resultado Esperado

- Uma lista de arquivos de política encontrados.
- Uma lista de configurações fracas nas políticas.

## Ferramentas

- Nikto
- Projeto OWASP Zed Attack Proxy
- W3af

## Referências

- Adobe: ["Especificação de arquivo de política de domínio cruzado"](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)
- Adobe: ["Recomendações de uso de arquivo de política de domínio cruzado para o Flash Player"](http://www.adobe.com/devnet/flashplayer/articles/cross_domain_policy.html)
- Oracle: ["Suporte XML entre Domínios"](http://www.oracle.com/technetwork/java/javase/plugin2-142482.html#CROSSDOMAINXML)
- MSDN: ["Disponibilizando um Serviço através de Limites de Domínio"](http://msdn.microsoft.com/en-us/library/cc197955(v=vs.95).aspx)
- MSDN: ["Restrições de Acesso à Segurança de Rede no Silverlight"](http://msdn.microsoft.com/en-us/library/cc645032(v=vs.95).aspx)
- Stefan Esser: ["Explorando novas vulnerabilidades com arquivos de política de domínio cruzado do Flash"](http://www.hardened-php.net/library/poking_new_holes_with_flash_crossdomain_policy_files.html)
- Jeremiah Grossman: ["Crossdomain.xml convida a Mayhem de Cross-site"](http://jeremiahgrossman.blogspot.com/2008/05/crossdomainxml-invites-cross-site.html)
- Google Doctype: ["Introdução à segurança Flash"](http://code.google.com/p/doctype-mirror/wiki/ArticleFlashSecurity)
- UCSD: [Analisando as Políticas de Domínio Cruzado de Aplicações Flash](http://cseweb.ucsd.edu/~hovav/dist/crossdomain.pdf)