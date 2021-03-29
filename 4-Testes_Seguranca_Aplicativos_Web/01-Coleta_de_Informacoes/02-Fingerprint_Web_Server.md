# Servidor de Impressão Digital

|ID          |
|------------|
|WSTG-INFO-02|

## Resumo

Servidor de impressão digital (web server fingerprinting) remete à tarefa de identificar o tipo e a versão do servidor que um alvo está rodando. Enquanto o servidor de impressão digital é geralmente encapsulado em ferramentas de testes automatizados, é importante para os pesquisadores entender os fundamentos de como essas ferramentas tentam identificar softwares, e porque isso é útil. 

Descobrindo precisamente o tipo de servidor que a aplicação roda pode ativar testes de segurança para determinar se o ela é vulnerável a ataques. Em particular, servidores rodando versões antigas de softwares sem correções (patches) de segurança atualizados podem ser suscetíveis a vulnerabilidades conhecidas em versões especificas.  

## Objetivos de Testes

- Determine a versão e o tipo de servidor para ativar a descoberta adicional de vulnerabilidades desconhecidas. 

## Como testar

As técnicas usadas para impressão digital do servidor web incluem captura de banner, obtenção de respostas a solicitações malformadas e uso de ferramentas automatizadas para realizar varreduras mais robustas que usam uma combinação de táticas. A premissa fundamental pela qual todas essas técnicas operam é a mesma. Todos elas se esforçam para obter alguma resposta do servidor web, que pode então ser comparada a um banco de dados de respostas e comportamentos conhecidos e, assim, combinada a um tipo de servidor conhecido.

### Banner Grabbing

O banner grab ocorre quando uma solicitação HTTP é feita ao servidor examinando seu [o cabeçalho de respostas](https://developer.mozilla.org/en-US/docs/Glossary/Response_header). 
Isso pode ser alcançado através do uso de uma variedade de ferramentas incluindo `telnet` para solicitações HTTP, ou `openssl`  para solicitações SSL.

Por exemplo, isso é a resposta a solicitações de um servidor Apache.

```http
HTTP/1.1 200 OK
Date: Thu, 05 Sep 2019 17:42:39 GMT
Server: Apache/2.4.41 (Unix)
Last-Modified: Thu, 05 Sep 2019 17:40:42 GMT
ETag: "75-591d1d21b6167"
Accept-Ranges: bytes
Content-Length: 117
Connection: close
Content-Type: text/html
...
```

Aqui uma outra resposta, desta vez do nginx, servidor que pode ser usado como servidor de proxy reverso.

```http
HTTP/1.1 200 OK
Server: nginx/1.17.3
Date: Thu, 05 Sep 2019 17:50:24 GMT
Content-Type: text/html
Content-Length: 117
Last-Modified: Thu, 05 Sep 2019 17:40:42 GMT
Connection: close
ETag: "5d71489a-75"
Accept-Ranges: bytes
...
```
Aqui uma resposta de lighttpd, servidor para ambientes de alta performance:

```sh
HTTP/1.0 200 OK
Content-Type: text/html
Accept-Ranges: bytes
ETag: "4192788355"
Last-Modified: Thu, 05 Sep 2019 17:40:42 GMT
Content-Length: 117
Connection: close
Date: Thu, 05 Sep 2019 17:57:57 GMT
Server: lighttpd/1.4.54
```
Nestes exemplos, o tipo e a versão do servidor são claramente expostos. No entanto, os aplicativos atentos à segurança podem ofuscar suas informações de servidor, modificando o cabeçalho. Por exemplo, aqui está um trecho da resposta a uma solicitação de um site com um cabeçalho modificado:

```sh
HTTP/1.1 200 OK
Server: Website.com
Date: Thu, 05 Sep 2019 17:57:06 GMT
Content-Type: text/html; charset=utf-8
Status: 200 OK
...
```
Nos casos em que as informações do servidor estão ocultadas, os testadores podem adivinhar o tipo de servidor com base na ordem dos campos de cabeçalho. Observe que no exemplo do Apache acima, os campos seguem esta ordem:

- Data (Date)
- Servidor (Server)
- Última modificação (Last Modified)
- ETag (tag identificadora da versão específica)
- Intervalos aceitos (Accept-Ranges)
- Tamanho do conteúdo (Content-Length)
- Conexão (Connection)
- Tipo de conteúdo (Content-Type)

Contudo, nos exemplos de servidores nginx e obscuros, os campos em comum seguem esta ordem:

- Servidor
- Data
- Tipo de conteúdo

Os testadores podem usar essas informações para adivinhar que o servidor ocultado é nginx. No entanto, considerando que vários servidores web podem compartilhar a mesma ordem de campos e esses podem ser modificados ou removidos, este método não é definitivo.

### Enviando Solicitações Malformadas

Os servidores web podem ser identificados através do exame de suas respostas de erro e, nos casos em que não foram personalizados, suas páginas de erro padrão. Uma maneira de obrigar um servidor a apresentá-los é o envio de solicitações intencionalmente incorretas ou malformadas. Por exemplo, aqui está uma resposta a solicitação a um método não existente, método `SANTA CLAUS`, para um servidor Apache.

```sh
GET / SANTA CLAUS/1.1


HTTP/1.1 400 Bad Request
Date: Fri, 06 Sep 2019 19:21:01 GMT
Server: Apache/2.4.41 (Unix)
Content-Length: 226
Connection: close
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>400 Bad Request</title>
</head><body>
<h1>Bad Request</h1>
<p>Your browser sent a request that this server could not understand.<br />
</p>
</body></html>
```
Aqui está a resposta a mesma solicitação a um servidor nginx.

```sh
GET / SANTA CLAUS/1.1

<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.17.3</center>
</body>
</html>
```
Aqui está a resposta a mesma solicitação a um servidor lighttpd.

```sh
GET / SANTA CLAUS/1.1


HTTP/1.0 400 Bad Request
Content-Type: text/html
Content-Length: 345
Connection: close
Date: Sun, 08 Sep 2019 21:56:17 GMT
Server: lighttpd/1.4.54

<?xml version="1.0" encoding="iso-8859-1"?>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
         "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">
 <head>
  <title>400 Bad Request</title>
 </head>
 <body>
  <h1>400 Bad Request</h1>
 </body>
</html>
```
Como as páginas de erro padrão oferecem muitos fatores de diferenciação entre os tipos de servidores web, seu exame pode ser um método eficaz para impressão digital (fingerprinting), mesmo quando os campos de cabeçalho do servidor estão ocultados.

### Usando ferramentas de escaneamento automático

Conforme declarado anteriormente, a impressão digital do servidor web é frequentemente incluída como uma funcionalidade de ferramentas de escaneamento automatizadas. Essas ferramentas são capazes de fazer solicitações semelhantes às demonstradas acima, bem como capazes de enviar outras sondagens mais específicas ao servidor. Ferramentas automatizadas podem comparar respostas de servidores web muito mais rápido do que testes manuais e utilizar grandes bancos de dados de respostas conhecidas para tentar a identificação do servidor. Por esses motivos, as ferramentas automatizadas têm maior probabilidade de produzir resultados precisos.

Aqui estão algumas das ferramentas de escaneamento que incluem a funcionalidade de fingerprinting:

- [Netcraft](https://toolbar.netcraft.com/site_report), uma ferramenta online de escanemento de informação de websites, incluindo o servidor web.
- [Nikto](https://github.com/sullo/nikto), ferramenta de escaneamento de linha de comando open source.
- [Nmap](https://nmap.org/), uma ferramenta de linha de comando open source que também tem uma interface, [Zenmap](https://nmap.org/zenmap/).

## Remediação

Embora a exposição de informações do servidor não sejam necessariamente uma vulnerabilidade em si, são informações que podem ajudar os invasores a explorar outras vulnerabilidades existentes. As informações expostas do servidor também podem levar os invasores a encontrar vulnerabilidades de servidor específicas da versão que podem ser usadas para explorar servidores sem correções (patch). Por este motivo, é recomendável que alguns cuidados sejam tomados. Essas ações incluem:

- Ocultar informações no cabeçalho de servidores web tais como com Apache [mod_headers module](https://httpd.apache.org/docs/current/mod/mod_headers.html).
- Usar um [servidor de proxy reverso] (https://en.wikipedia.org/wiki/Proxy_server#Reverse_proxies) reforçado para criar uma camada adicional de segurança entre o servidor web e a Internet. 
- Garantir que os servidores web sejam mantidos atualizados com os softwares e patches de segurança mais recentes.
![image](https://user-images.githubusercontent.com/25070780/112854496-ea3a5280-907b-11eb-8f6a-f6cd6feee705.png)
