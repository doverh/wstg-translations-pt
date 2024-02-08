# Enumerar Interfaces de Administração da Infraestrutura e Aplicação

|ID          |
|------------|
|WSTG-CONF-05|

## Resumo

Interfaces de administrador podem estar presentes na aplicação ou no servidor de aplicativos para permitir que certos usuários realizem atividades privilegiadas no site. Testes devem ser realizados para revelar se e como essa funcionalidade privilegiada pode ser acessada por um usuário não autorizado ou padrão.

Uma aplicação pode exigir uma interface de administrador para permitir que um usuário privilegiado acesse funcionalidades que possam fazer alterações na forma como o site funciona. Tais mudanças podem incluir:

- provisionamento de contas de usuário
- design e layout do site
- manipulação de dados
- alterações de configuração

Em muitos casos, essas interfaces não têm controles suficientes para protegê-las contra acessos não autorizados. Os testes visam descobrir essas interfaces de administrador e acessar funcionalidades destinadas aos usuários privilegiados.

## Objetivos do Teste

- Identificar interfaces e funcionalidades de administrador ocultas.

## Como Testar

### Testes de Caixa Preta

A seção a seguir descreve vetores que podem ser usados para testar a presença de interfaces administrativas. Essas técnicas também podem ser usadas para testar problemas relacionados, incluindo escalonamento de privilégios, e são descritas em maior detalhe em outras partes deste guia (por exemplo, [Testes de violação de esquema de autorização](../05-Authorization_Testing/02-Testing_for_Bypassing_Authorization_Schema.md) e [Testes para Referências Diretas de Objetos Inseguras](../05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References.md)).

- Enumeração de diretórios e arquivos. Uma interface administrativa pode estar presente, mas não visivelmente disponível para o testador. Tentar adivinhar o caminho da interface administrativa pode ser tão simples quanto solicitar: */admin ou /administrator, etc.* ou, em alguns cenários, pode ser revelado em segundos usando [Google dorks](https://www.exploit-db.com/google-hacking-database).
- Existem muitas ferramentas disponíveis para realizar força bruta em conteúdos de servidores, consulte a seção de ferramentas abaixo para obter mais informações. Um testador também pode ter que identificar o nome do arquivo da página de administração. Navegar forçadamente para a página identificada pode fornecer acesso à interface.
- Comentários e links no código-fonte. Muitos sites usam código comum carregado para todos os usuários do site. Examinando todo o código fonte enviado para o cliente, links para funcionalidades de administrador podem ser descobertos e devem ser investigados.
- Revisão da documentação do servidor e da aplicação. Se o servidor de aplicativos ou a aplicação estiverem implantados em sua configuração padrão, pode ser possível acessar a interface de administração usando informações descritas na documentação de configuração ou ajuda. Listas de senhas padrão devem ser consultadas se uma interface administrativa for encontrada e forem necessárias credenciais.
- Informações publicamente disponíveis. Muitas aplicações, como o WordPress, têm interfaces administrativas padrão.
- Porta do servidor alternativa. As interfaces de administração podem estar em uma porta diferente no host do que a aplicação principal. Por exemplo, a interface de administração do Apache Tomcat pode ser vista frequentemente na porta 8080.
- Manipulação de parâmetros. Pode ser necessário um parâmetro GET ou POST, ou uma variável de cookie, para habilitar a funcionalidade do administrador. Pistas para isso incluem a presença de campos ocultos como:

```html
<input type="hidden" name="admin" value="no">
```

ou em um cookie:

`Cookie: session_cookie; useradmin=0`

Depois de descobrir uma interface administrativa, uma combinação das técnicas acima pode ser usada para tentar ignorar a autenticação. Se isso falhar, o testador pode tentar um ataque de força bruta. Nesse caso, o testador deve estar ciente da possibilidade de bloqueio da conta administrativa se essa funcionalidade estiver presente.

### Testes de Caixa Cinza

Uma análise mais detalhada dos componentes do servidor e da aplicação deve ser realizada para garantir a segurança (ou seja, páginas de administrador não são acessíveis a todos através do uso de filtragem de IP ou outros controles) e, quando aplicável, verificação de que todos os componentes não usam credenciais ou configurações padrão.
O código-fonte deve ser revisado para garantir que o modelo de autorização e autenticação assegure uma clara separação de funções entre usuários normais e administradores do site. Funções de interface do usuário compartilhadas entre usuários normais e administradores devem ser revisadas para garantir uma separação clara entre o desenho desses componentes e a divulgação de informações dessas funcionalidades compartilhadas.

Cada estrutura web pode ter suas próprias páginas ou caminhos de administração padrão. Por exemplo:

WebSphere:

```html
/admin
/admin-authz.xml
/admin.conf
/admin.passwd
/admin/*
/admin/logon.jsp
/admin/secure/logon.jsp
```

PHP:

```html
/phpinfo
/phpmyadmin/
/phpMyAdmin/
/mysqladmin/
/MySQLadmin
/MySQLAdmin
/login.php
/logon.php
/xmlrpc.php
/dbadmin
```

FrontPage:

```html
/admin.dll
/admin.exe
/administrators.pwd
/author.dll
/author.exe
/author.log
/authors.pwd
/cgi-bin
```

WebLogic:

```html
/AdminCaptureRootCA
/AdminClients
/AdminConnections
/AdminEvents
/AdminJDBC
/AdminLicense
/AdminMain
/AdminProps
/AdminRealm
/AdminThreads
```

WordPress:

```html
wp-admin/
wp-admin/about.php
wp-admin/admin-ajax.php
wp-admin/admin-db.php
wp-admin/admin-footer.php
wp-admin/admin-functions.php
wp-admin/admin-header.php
```

## Ferramentas

- [OWASP ZAP - Forced Browse](https://www.zaproxy.org/docs/desktop/addons/forced-browse/) é uma utilização atualmente mantida do projeto anterior DirBuster da OWASP.
- [THC-HYDRA](https://github.com/vanhauser-thc/thc-hydra) é uma ferramenta que permite a força bruta em muitas interfaces, incluindo autenticação HTTP baseada em formulário

.
- Um testador de força bruta é muito melhor quando usa um bom dicionário, por exemplo, o dicionário [netsparker](https://www.netsparker.com/blog/web-security/svn-digger-better-lists-for-forced-browsing/).

## Referências

- [Cirt: Lista de senhas padrão](https://cirt.net/passwords)
- [FuzzDB pode ser usado para força bruta na página de login admin](https://github.com/fuzzdb-project/fuzzdb/blob/master/discovery/predictable-filepaths/login-file-locations/Logins.txt)
- [Parâmetros de administração ou depuração comuns](https://github.com/fuzzdb-project/fuzzdb/blob/master/attack/business-logic/CommonDebugParamNames.txt)