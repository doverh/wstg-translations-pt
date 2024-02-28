# Testando a Escalada de Privilégios

|ID          |
|------------|
|WSTG-ATHZ-03|

## Resumo

Esta seção descreve o problema de escalar privilégios de um estágio para outro. Durante esta fase, o testador deve verificar se não é possível para um usuário modificar seus privilégios ou funções dentro do aplicativode maneiras que possam permitir ataques de escalonamento de privilégios.

O escalonamento de privilégios ocorre quando um usuário obtém acesso a mais recursos ou funcionalidades do que normalmente é permitido, e tal elevação ou mudanças deveriam ter sido impedidas pela aplicação. Isso geralmente é causado por uma falha na aplicação. O resultado é que a aplicação executa ações com mais privilégios do que os pretendidos pelo desenvolvedor ou administrador do sistema.

O grau de escalonamento depende dos privilégios que o atacante está autorizado a possuir e dos privilégios que podem ser obtidos em uma exploração bem-sucedida. Por exemplo, um erro de programação que permite a um usuário obter privilégios extras após autenticação bem-sucedida limita o grau de escalonamento, porque o usuário já está autorizado a ter alguns privilégios. Da mesma forma, um atacante remoto ganhando privilégio de superusuário sem autenticação apresenta um maior grau de escalonamento.

Normalmente, as pessoas se referem ao *escalamento vertical* quando é possível acessar recursos concedidos a contas mais privilegiadas (por exemplo, adquirir privilégios administrativos para a aplicação), e ao *escalamento horizontal* quando é possível acessar recursos concedidos a uma conta configurada de forma semelhante (por exemplo, em um aplicativo de banco online, acessar informações relacionadas a um usuário diferente).

## Objetivos do Teste

- Identificar pontos de injeção relacionados à manipulação de privilégios.
- Fazer fuzzing ou de outra forma tentar contornar medidas de segurança.

## Como Testar

### Testando Manipulação de Funções/Privilégios

Em cada parte do aplicativoonde um usuário pode criar informações no banco de dados (por exemplo, fazer um pagamento, adicionar um contato ou enviar uma mensagem), pode receber informações (extrato de conta, detalhes do pedido, etc.) ou excluir informações (excluir usuários, mensagens, etc.), é necessário registrar essa funcionalidade. O testador deve tentar acessar tais funções como outro usuário para verificar se é possível acessar uma função que não deveria ser permitida pelo papel/privilégio do usuário (mas pode ser permitida como outro usuário).

#### Manipulação do Grupo de Usuários

Por exemplo:
O seguinte POST HTTP permite ao usuário que pertence a `grp001` acessar o pedido nº 0001:

```http
POST /user/viewOrder.jsp HTTP/1.1
Host: www.example.com
...

groupID=grp001&orderID=0001
```

Verifique se um usuário que não pertence a `grp001` pode modificar o valor dos parâmetros `groupID` e `orderID` para obter acesso a esses dados privilegiados.

#### Manipulação do Perfil do Usuário

Por exemplo:
A resposta do servidor a seguir mostra um campo oculto no HTML retornado ao usuário após uma autenticação bem-sucedida.

```html
HTTP/1.1 200 OK
Server: Netscape-Enterprise/6.0
Date: Wed, 1 Apr 2006 13:51:20 GMT
Set-Cookie: USER=aW78ryrGrTWs4MnOd32Fs51yDqp; path=/; domain=www.example.com
Set-Cookie: SESSION=k+KmKeHXTgDi1J5fT7Zz; path=/; domain= www.example.com
Cache-Control: no-cache
Pragma: No-cache
Content-length: 247
Content-Type: text/html
Expires: Thu, 01 Jan 1970 00:00:00 GMT
Connection: close

<form  name="autoriz" method="POST" action = "visual.jsp">
<input type="hidden" name="profile" value="SysAdmin">\

<body onload="document.forms.autoriz.submit()">
</td>
</tr>
```

E se o testador modificar o valor da variável `profile` para `SysAdmin`? É possível se tornar **administrador**?

#### Manipulação do Valor de Condição

Por exemplo:
Em um ambiente onde o servidor envia uma mensagem de erro contida como valor em um parâmetro específico em um conjunto de códigos de resposta, como o seguinte:

```text
@0`1`3`3``0`UC`1`Status`OK`SEC`5`1`0`ResultSet`0`PVValid`-1`0`0` Notifications`0`0`3`Command  Manager`0`0`0` StateToolsBar`0`0`0`
StateExecToolBar`0`0`0`FlagsToolBar`0
```

O servidor confia implicitamente no usuário. Ele acredita que o usuário responderá com a mensagem acima encerrando a sessão.

Nesta condição, verifique se não é possível escalar privilégios modificando os valores dos parâmetros. Neste exemplo específico, ao modificar o valor `PVValid` de `-1` para `0` (sem condições de erro), pode ser possível autenticar como administrador no servidor.

#### Manipulação do Endereço IP

Alguns sites limitam o acesso ou contam o número de tentativas de login falhadas com base no endereço IP.

Por exemplo:

```text
X-Forwarded-For: 8.1.1.1
```

Neste caso, se o site usar o valor de `X-forwarded-For` como endereço IP do cliente, o testador pode alterar o valor de IP do cabeçalho HTTP `X-forwarded-For` para contornar a identificação da origem do IP.

### Travessia de URL

Tente atravessar o site e verifique se algumas páginas podem não ter verificação de autorização.

Por exemplo:

```text
/../.././userInfo.html
```

### WhiteBox

Se a verificação de autorização de URL for feita apenas por correspondência parcial de URL, é provável que testadores ou hackers possam contornar a autorização por técnicas de codificação de URL.

Por exemplo:

```text
startswith(), endswith(), contains(), indexOf()
```

### ID de Sessão Fraca

ID de sessão fraca pode ser vulnerável a ataques de força bruta. Por exemplo, um site está usando

 `MD5(Senha + ID do Usuário)` como ID de sessão. Então, testadores podem adivinhar ou gerar o ID de sessão para outros usuários.

## Referências

### Artigos

- [Wikipedia - Escalonamento de Privilégios](https://pt.wikipedia.org/wiki/Escalonamento_de_privil%C3%A9gios)

## Ferramentas

- [OWASP Zed Attack Proxy (ZAP)](https://www.zaproxy.org)