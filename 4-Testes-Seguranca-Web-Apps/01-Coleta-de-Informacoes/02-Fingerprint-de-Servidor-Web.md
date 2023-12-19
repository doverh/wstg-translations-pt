# Fingerprinting de Servidor Web

|ID          |
|------------|
|WSTG-INFO-02|

## Resumo

Fingerprinting de servidor web é a tarefa de identificar o tipo e a versão de servidor web que um alvo está executando. Embora o fingerprinting de servidor web seja frequentemente encapsulado em ferramentas de teste automatizado, é importante para os pesquisadores entender os fundamentos de como essas ferramentas tentam identificar o software e por que isso é útil.

Descobrir com precisão o tipo de servidor web em que um aplicativo é executado, pode permitir que testadores de segurança determinem se o aplicativo é vulnerável a ataques. Em particular, servidores que executam versões mais antigas de software sem atualizações de segurança atualizadas podem ser suscetíveis a ataques em vulnerabilidades públicas de uma versão.

## Objetivos do Teste

- Determinar a versão e o tipo de servidor web em execução para possibilitar uma maior descoberta de possíveis vulnerabilidades conhecidas.

## Como Testar

As técnicas usadas para fingerprinting de servidor web incluem [banner grabbing](https://en.wikipedia.org/wiki/Banner_grabbing), elicitar respostas a solicitações malformadas e usar ferramentas automatizadas para realizar varreduras mais robustas que usam uma combinação de técnicas. A premissa fundamental pela qual todas essas técnicas operam é a mesma. Todas elas se esforçam para obter alguma resposta do servidor web, que pode ser comparada a um banco de dados de respostas conhecidas e comportamentos e, assim, correspondida a um tipo de servidor conhecido.

### Banner Grabbing

Um banner grab é realizado enviando uma solicitação HTTP para o servidor web e examinando seu [cabeçalho de resposta](https://developer.mozilla.org/en-US/docs/Glossary/Response_header). Isso pode ser realizado usando uma variedade de ferramentas, incluindo `telnet` para solicitações HTTP ou `openssl` para solicitações por SSL.

Por exemplo, aqui está a resposta a uma solicitação de um servidor Apache.

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

Aqui está outra resposta, desta vez do nginx.

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

Aqui está como uma resposta do lighttpd se parece.

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

Nesses exemplos, o tipo e a versão do servidor são claramente expostos. No entanto, aplicativos conscientes de segurança podem obfuscar suas informações de servidor modificando o cabeçalho. Por exemplo, aqui está um trecho da resposta a uma solicitação para um site com um cabeçalho modificado:

```sh
HTTP/1.1 200 OK
Server: Website.com
Date: Thu, 05 Sep 2019 17:57:06 GMT
Content-Type: text/html; charset=utf-8
Status: 200 OK
...
```

Nos casos em que as informações do servidor estão obscurecidas, os testadores podem adivinhar o tipo de servidor com base na ordem dos campos do cabeçalho. Observe que, no exemplo do Apache acima, os campos seguem esta ordem:

- Date
- Server
- Last-Modified
- ETag
- Accept-Ranges
- Content-Length
- Connection
- Content-Type

No entanto, nos exemplos do nginx e do servidor obscurecido, os campos em comum seguem esta ordem:

- Server
- Date
- Content-Type

Os testadores podem usar essas informações para supor que o servidor obscuro é o nginx. No entanto, considerando que vários servidores web diferentes podem compartilhar a mesma ordem de campos e os campos podem ser modificados ou removidos, esse método não é definitivo.

### Envio de Solicitações Malformadas

Servidores web podem ser identificados examinando suas respostas de erro e, nos casos em que eles não foram personalizados, suas páginas de erro padrão. Uma maneira de fazer com que um servidor apresente erros é enviando solicitações incorretas ou malformadas de propósito.

Por exemplo, aqui está a resposta a uma solicitação para o método inexistente `SANTA CLAUS` de um servidor Apache.

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

Aqui está a resposta para a mesma solicitação do nginx.

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

Aqui está a resposta para a mesma solicitação do lighttpd.

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

Como as páginas de erro padrão oferecem muitos fatores que diferenciam os tipos de servidores web, sua análise pode ser um método eficaz para fingerprinting, mesmo quando os campos do cabeçalho do servidor estão obscuros.

### Uso de Ferramentas de Varredura Automatizada

Como mencionado anteriormente, o fingerprinting de servidor web é frequentemente incluído como funcionalidade de ferramentas de varredura automatizada. Essas ferramentas são capazes de fazer solicitações semelhantes às demonstradas acima, bem como enviar outras sondas mais específicas do servidor. Ferramentas automatizadas podem comparar respostas de servidores web muito mais rapidamente do que testes manuais e utilizar grandes bancos de dados de respostas conhecidas para tentar identificar o servidor. Por esses motivos, as ferramentas automatizadas têm mais chances de produzir resultados precisos.

Aqui estão algumas ferramentas de varredura comumente usadas que incluem funcionalidade de fingerprinting de servidor web.

- [Netcraft](https://toolbar.netcraft.com/site_report), uma ferramenta online que faz varreduras em sites em busca de informações, incluindo o servidor web.
- [Nikto](https://github.com/sullo/nikto), uma ferramenta de varredura em linha de comando de código aberto.
- [Nmap](https://nmap.org/), uma ferramenta de linha de comando de código aberto que também possui uma interface gráfica, [Zenmap](https://nmap.org/zenmap/).

## Remediação

Embora as informações do servidor expostas não sejam necessariamente uma vulnerabilidade em si, são informações que podem ajudar os hackers a explorar outras vulnerabilidades que possam existir. As informações do servidor expostas também podem levar os hackers a encontrar vulnerabilidades específicas do servidor que podem ser usadas para explorar servidores não corrigidos. Por esse motivo, é recomendável tomar algumas precauções. Essas ações incluem:

- Obscurecer as informações do servidor web nos cabeçalhos, como com o módulo [mod_headers](https://httpd.apache.org/docs/current/mod/mod_headers.html) do Apache.
- Usar um servidor proxy reverso protegido para criar uma camada adicional de segurança entre o servidor web e a Internet.
- Garantir que os servidores web sejam mantidos atualizados com o software e as atualizações de segurança mais recentes.