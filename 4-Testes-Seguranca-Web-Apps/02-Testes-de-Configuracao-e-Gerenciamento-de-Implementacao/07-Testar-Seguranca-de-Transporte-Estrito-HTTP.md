# Testar HTTP Strict Transport Security

|ID          |
|------------|
|WSTG-CONF-07|

## Resumo

A funcionalidade HTTP Strict Transport Security (HSTS) permite que uma aplicação web informe ao navegador, por meio do uso de um cabeçalho de resposta especial, que nunca deve estabelecer uma conexão com os servidores de domínio especificados usando HTTP não criptografado. Em vez disso, ele deve automaticamente estabelecer todas as solicitações de conexão para acessar o site por meio do HTTPS. Além disso, ele impede que os usuários anulem erros de certificado.

Considerando a importância dessa medida de segurança, é prudente verificar se o site está usando esse cabeçalho HTTP para garantir que todos os dados trafeguem criptografados entre o navegador da web e o servidor.

O cabeçalho HTTP strict transport security usa dois diretivas:

- `max-age`: para indicar o número de segundos que o navegador deve converter automaticamente todas as solicitações HTTP para HTTPS.
- `includeSubDomains`: para indicar que todos os subdomínios relacionados devem usar HTTPS.
- `preload` (não oficial): para indicar que o(s) domínio(s) está(ão) na lista de pré-carregamento(s) e que os navegadores nunca devem se conectar sem HTTPS.
  - Isso é suportado por todos os principais navegadores, mas não faz parte oficial da especificação. (Veja [hstspreload.org](https://hstspreload.org/) para mais informações.)

Aqui está um exemplo de implementação do cabeçalho HSTS:

`Strict-Transport-Security: max-age=31536000; includeSubDomains`

O uso deste cabeçalho por aplicações web deve ser verificado para verificar se os seguintes problemas de segurança podem ser produzidos:

- Atacantes que capturam o tráfego de rede e acessam as informações transferidas por meio de um canal não criptografado.
- Atacantes explorando um ataque de manipulador no meio devido ao problema de aceitar certificados que não são confiáveis.
- Usuários que inserem erroneamente um endereço no navegador colocando HTTP em vez de HTTPS, ou usuários que clicam em um link em uma aplicação web que erroneamente indicou o uso do protocolo HTTP.

## Objetivos do Teste

- Revisar o cabeçalho HSTS e sua validade.

## Como Testar

A presença do cabeçalho HSTS pode ser confirmada examinando a resposta do servidor por meio de um proxy de interceptação ou usando o curl da seguinte forma:

```bash
$ curl -s -D- https://owasp.org | grep -i strict
Strict-Transport-Security: max-age=31536000
```

## Referências

- [OWASP HTTP Strict Transport Security](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html)
- [OWASP Appsec Tutorial Series - Episode 4: Strict Transport Security](https://www.youtube.com/watch?v=zEV3HOuM_Vw)
- [Especificação HSTS](https://tools.ietf.org/html/rfc6797)