# Teste de evasão do Esquema de Autenticação

|ID          |
|------------|
|WSTG-ATHN-04|

## Resumo

Em segurança da computação, autenticação é o processo de tentar verificar a identidade digital do remetente de uma comunicação. Um exemplo comum desse processo é o processo de login. Testar o esquema de autenticação significa entender como o processo de autenticação funciona e usar essas informações para contornar o mecanismo de autenticação.

Embora a maioria das aplicações exija autenticação para acessar informações privadas ou executar tarefas, nem todo método de autenticação é capaz de fornecer segurança adequada. Negligência, ignorância ou subestimação simples das ameaças de segurança muitas vezes resultam em esquemas de autenticação que podem ser superados simplesmente pulando a página de login e chamando diretamente uma página interna que deveria ser acessada apenas após a autenticação.

Além disso, muitas vezes é possível superar as medidas de autenticação manipulando solicitações e enganando a aplicação para pensar que o usuário já está autenticado. Isso pode ser feito alterando o parâmetro da URL fornecida, manipulando o formulário ou falsificando sessões.

Problemas relacionados ao esquema de autenticação podem ser encontrados em diferentes estágios do ciclo de vida de desenvolvimento de software (SDLC), como nas fases de design, desenvolvimento e implantação:

- Na fase de design, erros podem incluir uma definição incorreta das seções da aplicação a serem protegidas, a escolha de não aplicar protocolos de criptografia robustos para garantir a transmissão de credenciais e muitos outros.
- Na fase de desenvolvimento, erros podem incluir a implementação incorreta da funcionalidade de validação de entrada ou a não adoção das melhores práticas de segurança para a linguagem específica.
- Na fase de implantação da aplicação, pode haver problemas durante a configuração (atividades de instalação e configuração) devido à falta de habilidades técnicas necessárias ou à falta de boa documentação.

## Objetivos do Teste

- Garantir que a autenticação seja aplicada a todos os serviços que a exigem.

## Como Testar

### Teste de Caixa Preta

Existem vários métodos para superar o esquema de autenticação usado por uma aplicação da web:

- Solicitação direta de página ([navegação forçada](https://owasp.org/www-community/attacks/Forced_browsing))
- Modificação de parâmetros
- Previsão de ID de sessão
- Injeção de SQL

#### Solicitação Direta de Página

Se uma aplicação da web implementa controle de acesso apenas na página de login, o esquema de autenticação pode ser superado. Por exemplo, se um usuário solicitar diretamente uma página diferente por meio de navegação forçada, essa página pode não verificar as credenciais do usuário antes de conceder acesso. Tente acessar diretamente uma página protegida pela barra de endereços do navegador para testar usando este método.

![Solicitação Direta a Página Protegida](images/Basm-directreq.jpg)\
*Figura 4.4.4-1: Solicitação Direta a Página Protegida*

#### Modificação de Parâmetros

Outro problema relacionado ao design de autenticação ocorre quando a aplicação verifica um login bem-sucedido com base em parâmetros de valor fixo. Um usuário pode modificar esses parâmetros para acessar áreas protegidas sem fornecer credenciais válidas. No exemplo abaixo, o parâmetro "authenticated" é alterado para um valor "yes", o que permite ao usuário obter acesso. Neste exemplo, o parâmetro está na URL, mas um proxy também poderia ser usado para modificar o parâmetro, especialmente quando os parâmetros são enviados como elementos de formulário em uma solicitação POST ou quando os parâmetros são armazenados em um cookie.

```html
http://www.site.com/page.asp?authenticated=no

raven@blackbox /home $nc www.site.com 80
GET /page.asp?authenticated=yes HTTP/1.0

HTTP/1.1 200 OK
Date: Sat, 11 Nov 2006 10:22:44 GMT
Server: Apache
Connection: close
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<HTML><HEAD>
</HEAD><BODY>
<H1>You Are Authenticated</H1>
</BODY></HTML>
```

![Solicitação com Parâmetro Modificado](images/Basm-parammod.jpg)\
*Figura 4.4.4-2: Solicitação com Parâmetro Modificado*

#### Previsão de ID de Sessão

Muitas aplicações da web gerenciam a autenticação usando identificadores de sessão (session IDs). Portanto, se a geração de ID de sessão for previsível, um usuário mal-intencionado pode ser capaz de encontrar um ID de sessão válido e obter acesso não autorizado à aplicação, assumindo a identidade de um usuário previamente autenticado.

Na figura a seguir, os valores dentro dos cookies aumentam linearmente, facilitando para um invasor adivinhar um ID de sessão válido.

![Valores de Cookies ao Longo do Tempo](images/Basm-sessid.jpg)\
*Figura 4.4.4-3: Valores de Cookies ao Longo do Tempo*

Na figura a seguir, os valores dentro dos cookies mudam apenas parcialmente, permitindo restringir um ataque de força bruta aos campos definidos abaixo.

![Valores de Cookies Alterados Parcialmente](images/Basm-sessid2.jpg)\
*Figura 4.4.4-4: Valores de Cookies Alterados Parcialmente*

#### Injeção de SQL (Autenticação por Formulário HTML)

A injeção de SQL é uma técnica de ataque amplamente conhecida. Esta seção não vai descrever essa técnica em detalhes, pois existem várias seções neste guia que explicam técnicas de injeção além do escopo desta seção.

![Injeção de SQL](images/Basm-sqlinj.jpg)\
*Figura 4.4.4-5: Injeção de SQL*

A figura a seguir mostra que, com um simples ataque de injeção de SQL, às vezes é possível contornar o formulário de autenticação.

![Ataque Simples de Injeção de SQL](images/Basm-sqlinj2

.gif)\
*Figura 4.4.4-6: Ataque Simples de Injeção de SQL*

### Teste de Caixa Cinza

Se um invasor conseguir recuperar o código-fonte da aplicação explorando uma vulnerabilidade previamente descoberta (por exemplo, travessia de diretório) ou de um repositório da web (Aplicações de Código Aberto), pode ser possível realizar ataques refinados contra a implementação do processo de autenticação.

No exemplo a seguir (PHPBB 2.0.13 - Vulnerabilidade de Superação de Autenticação), na linha 5, a função unserialize() analisa um cookie fornecido pelo usuário e define valores dentro do array $row. Na linha 10, o hash da senha MD5 do usuário armazenado no banco de dados backend é comparado ao fornecido.

```php
if (isset($HTTP_COOKIE_VARS[$cookiename . '_sid']) {
    $sessiondata = isset($HTTP_COOKIE_VARS[$cookiename . '_data']) ? unserialize(stripslashes($HTTP_COOKIE_VARS[$cookiename . '_data'])) : array();
    $sessionmethod = SESSION_METHOD_COOKIE;
}
if(md5($password) == $row['user_password'] && $row['user_active']) {
    $autologin = (isset($HTTP_POST_VARS['autologin'])) ? TRUE : 0;
}
```

Em PHP, uma comparação entre um valor de string e um valor booleano (1 e `TRUE`) é sempre `TRUE`, então, fornecendo a seguinte string (a parte importante é `b:1`) para a função `unserialize()`, é possível contornar o controle de autenticação:

```text
a:2:{s:11:"autologinid";b:1;s:6:"userid";s:1:"2";}
```

## Ferramentas

- [WebGoat](https://owasp.org/www-project-webgoat/)
- [OWASP Zed Attack Proxy (ZAP)](https://www.zaproxy.org)

## Referências

### Artigos

- Mark Roxberry: "Vulnerabilidade do PHPBB 2.0.13"
- [David Endler: "Exploração e Previsão de Força Bruta de IDs de Sessão"](https://www.cgisecurity.com/lib/SessionIDs.pdf)