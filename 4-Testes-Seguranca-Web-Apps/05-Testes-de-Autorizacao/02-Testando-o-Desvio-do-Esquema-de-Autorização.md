# Testando o Desvio do Esquema de Autorização

|ID          |
|------------|
|WSTG-ATHZ-02|


## Resumo

Este tipo de teste concentra-se em verificar como o esquema de autorização foi implementado para cada função ou privilégio para obter acesso a funções e recursos reservados.

Para cada função específica que o testador possui durante a avaliação e para cada função e solicitação que a aplicação executa durante a fase pós-autenticação, é necessário verificar:

- É possível acessar esse recurso mesmo se o usuário não estiver autenticado?
- É possível acessar esse recurso após o logout?
- É possível acessar funções e recursos que deveriam ser acessíveis a um usuário que possui uma função ou privilégio diferente?

Tente acessar a aplicação como um usuário administrativo e rastreie todas as funções administrativas.

- É possível acessar funções administrativas se o testador estiver logado como um usuário não administrativo?
- É possível usar essas funções administrativas como um usuário com uma função diferente e para quem essa ação deveria ser negada?

## Objetivos do Teste

- Avaliar se o acesso horizontal ou vertical é possível.

## Como Testar

- Acesse recursos e conduza operações horizontalmente.
- Acesse recursos e conduza operações verticalmente.

### Testando Desvio Horizontal do Esquema de Autorização

Para cada função, função específica ou solicitação que a aplicação executa, é necessário verificar:

- É possível acessar recursos que deveriam ser acessíveis a um usuário que possui uma identidade diferente com a mesma função ou privilégio?
- É possível operar funções em recursos que deveriam ser acessíveis a um usuário que possui uma identidade diferente?

Para cada função:

1. Registre ou gere dois usuários com privilégios idênticos.
2. Estabeleça e mantenha duas sessões diferentes ativas (uma para cada usuário).
3. Para cada solicitação, altere os parâmetros relevantes e o identificador de sessão do token um para o token dois e diagnostique as respostas para cada token.
4. Uma aplicação será considerada vulnerável se as respostas forem iguais, contiverem os mesmos dados privados ou indicarem operações bem-sucedidas em recursos ou dados de outros usuários.

Por exemplo, suponha que a função `viewSettings` faça parte de todos os menus de conta da aplicação com a mesma função, e é possível acessá-la solicitando a seguinte URL: `https://www.example.com/account/viewSettings`. Então, a seguinte solicitação HTTP é gerada ao chamar a função `viewSettings`:

```http
POST /account/viewSettings HTTP/1.1
Host: www.example.com
[outras cabeçalhos HTTP]
Cookie: SessionID=USER_SESSION

username=example_user
```

Resposta válida e legítima:

```html
HTTP/1.1 200 OK
[outras cabeçalhos HTTP]

{
  "username": "example_user",
  "email": "example@email.com",
  "address": "Example Address"
}
```

O atacante pode tentar executar essa solicitação com o mesmo parâmetro `username`:

```html
POST /account/viewCCpincode HTTP/1.1
Host: www.example.com
[outras cabeçalhos HTTP]
Cookie: SessionID=ATTACKER_SESSION

username=example_user
```

Se a resposta do atacante contiver os dados do `example_user`, então a aplicação é vulnerável a ataques de movimentação lateral, onde um usuário pode ler ou gravar dados de outros usuários.

### Testando Desvio Vertical do Esquema de Autorização

Um desvio de autorização vertical é específico para o caso em que um atacante obtém uma função superior à própria. Testar esse desvio concentra-se em verificar como o esquema de autorização vertical foi implementado para cada função. Para cada função, página, função específica ou solicitação que a aplicação executa, é necessário verificar se é possível:

- Acessar recursos que deveriam ser acessíveis apenas a um usuário com função superior.
- Operar funções em recursos que deveriam ser operacionais apenas por um usuário que possui uma função ou identidade específica superior.

Para cada função:

1. Registre um usuário.
2. Estabeleça e mantenha duas sessões diferentes com base nas duas funções diferentes.
3. Para cada solicitação, altere o identificador de sessão do original para o identificador de sessão de outra função e avalie as respostas para cada uma.
4. Uma aplicação será considerada vulnerável se a sessão menos privilegiada contiver os mesmos dados ou indicar operações bem-sucedidas em funções mais privilegiadas.

#### Cenário de Funções de um Site Bancário

A tabela a seguir ilustra as funções do sistema em um site bancário. Cada função possui permissões específicas para a funcionalidade do menu de eventos:

|      FUNÇÃO     |     PERMISSÕES    | PERMISSÕES ADICIONAIS |
|---------------|-------------------|-----------------------|
| Administrador | Controle Total      | Excluir                |
| Gerente       | Modificar, Adicionar, Ler | Adicionar                   |
| Funcionário         | Ler, Modificar      | Modificar                |
| Cliente      | Somente Leitura         |                       |

A aplicação será considerada vulnerável se:

1. O cliente puder operar funções de administrador, gerente ou funcionário;
2. O usuário do funcionário puder operar funções de gerente ou administrador;
3. O gerente puder operar funções de administrador.

Suponha que a função `deleteEvent` faça parte do menu da conta do administrador da aplicação e seja possível acessá-la solicitando a seguinte URL: `https://www.example.com/account/deleteEvent`. Então, a seguinte solicitação HTTP é gerada ao chamar a função `deleteEvent`:

```http
POST /account/deleteEvent HTTP/1.1
Host: www.example.com
[outras cabeçalhos HTTP]
Cookie: SessionID=ADMINISTRATOR_USER_SESSION

EventID=1000001
```

Resposta válida:

```http
HTTP/1.1 200 OK
[outras cabeçalhos HTTP]

{"message": "Evento foi excluído"}
```

O atacante pode tentar executar a mesma solicitação:

```http
POST /account/deleteEvent HTTP/1.1
Host: www.example.com
[outras cabeçalhos HTTP]
Cookie: SessionID=CUSTOMER_USER_SESSION

EventID=1000002
```

Se a resposta da solicitação do atacante contiver os mesmos dados `{"message": "Evento foi excluído"}`, a aplicação

 é vulnerável.

#### Acesso à Página do Administrador

Suponha que o menu do administrador faça parte da conta do administrador.

A aplicação será considerada vulnerável se qualquer função que não seja a de administrador puder acessar o menu do administrador. Às vezes, os desenvolvedores realizam validação de autorização apenas no nível da interface gráfica, deixando as funções sem validação de autorização, o que pode resultar em uma vulnerabilidade.

### Testando Acesso a Funções Administrativas

Por exemplo, suponha que a função `addUser` faça parte do menu administrativo da aplicação, e seja possível acessá-la solicitando a seguinte URL `https://www.example.com/admin/addUser`.

Então, a seguinte solicitação HTTP é gerada ao chamar a função `addUser`:

```http
POST /admin/addUser HTTP/1.1
Host: www.example.com
[...]

userID=fakeuser&role=3&group=grp001
```

Outras perguntas ou considerações seguiriam na seguinte direção:

- O que acontece se um usuário não administrativo tentar executar essa solicitação?
- O usuário será criado?
- Se sim, o novo usuário pode usar seus privilégios?

### Testando Acesso a Recursos Associados a uma Função Diferente

Diversas aplicações configuram controles de recursos com base em funções de usuário. Vamos considerar o exemplo de currículos enviados em um formulário de carreiras para um bucket S3.

Como usuário normal, tente acessar a localização desses arquivos. Se você conseguir recuperá-los, modificá-los ou excluí-los, a aplicação é vulnerável.

### Testando Manipulação Especial de Cabeçalhos de Solicitação

Algumas aplicações suportam cabeçalhos não padrão, como `X-Original-URL` ou `X-Rewrite-URL`, para permitir a substituição da URL de destino em solicitações com a especificada no valor do cabeçalho.

Esse comportamento pode ser explorado em situações em que a aplicação está atrás de um componente que aplica restrição de controle de acesso com base na URL da solicitação.

A restrição de controle de acesso com base na URL da solicitação pode ser, por exemplo, bloquear o acesso da Internet a um console de administração exposto em `/console` ou `/admin`.

Para detectar o suporte ao cabeçalho `X-Original-URL` ou `X-Rewrite-URL`, podem ser aplicadas as seguintes etapas.

#### 1. Enviar uma Solicitação Normal sem Nenhum Cabeçalho X-Original-Url ou X-Rewrite-Url

```http
GET / HTTP/1.1
Host: www.example.com
[...]
```

#### 2. Enviar uma Solicitação com um Cabeçalho X-Original-Url Apontando para um Recurso Inexistente

```html
GET / HTTP/1.1
Host: www.example.com
X-Original-URL: /donotexist1
[...]
```

#### 3. Enviar uma Solicitação com um Cabeçalho X-Rewrite-Url Apontando para um Recurso Inexistente

```html
GET / HTTP/1.1
Host: www.example.com
X-Rewrite-URL: /donotexist2
[...]
```

Se a resposta para qualquer uma das solicitações contiver marcadores de que o recurso não foi encontrado, isso indica que a aplicação suporta os cabeçalhos de solicitação especiais. Esses marcadores podem incluir o código de status de resposta HTTP 404 ou uma mensagem de "recurso não encontrado" no corpo da resposta.

Uma vez validado o suporte ao cabeçalho `X-Original-URL` ou `X-Rewrite-URL`, a tentativa de desvio contra a restrição de controle de acesso pode ser alavancada enviando a solicitação esperada para a aplicação, mas especificando uma URL "permitida" pelo componente frontal como a URL principal da solicitação e especificando a URL real de destino no cabeçalho `X-Original-URL` ou `X-Rewrite-URL`, dependendo do suportado. Se ambos forem suportados, tente um após o outro para verificar qual cabeçalho permite o desvio.

#### 4. Outros Cabeçalhos a Serem Considerados

Frequentemente, painéis de administração ou funcionalidades relacionadas à administração só são acessíveis a clientes em redes locais. Portanto, pode ser possível abusar de vários cabeçalhos HTTP relacionados a proxy ou encaminhamento para obter acesso. Alguns cabeçalhos e valores para testar são:

- Cabeçalhos:
  - `X-Forwarded-For`
  - `X-Forward-For`
  - `X-Remote-IP`
  - `X-Originating-IP`
  - `X-Remote-Addr`
  - `X-Client-IP`
- Valores
  - `127.0.0.1` (ou qualquer coisa nos espaços de endereço `127.0.0.0/8` ou `::1/128`)
  - `localhost`
  - Qualquer endereço [RFC1918](https://tools.ietf.org/html/rfc1918):
    - `10.0.0.0/8`
    - `172.16.0.0/12`
    - `192.168.0.0/16`
  - Endereços locais de link: `169.254.0.0/16`

Observação: Incluir um elemento de porta junto com o endereço ou nome do host também pode ajudar a burlar proteções externas, como firewalls de aplicativos da web, etc.
Por exemplo: `127.0.0.4:80`, `127.0.0.4:443`, `127.0.0.4:43982`

## Remediação

Aplique os princípios de privilégio mínimo aos usuários, funções e recursos para garantir que nenhum acesso não autorizado ocorra.

## Ferramentas

- [OWASP Zed Attack Proxy (ZAP)](https://www.zaproxy.org/)
  - [ZAP add-on: Access Control Testing](https://www.zaproxy.org/docs/desktop/addons/access-control-testing/)
- [Port Swigger Burp Suite](https://portswigger.net/burp)
  - [Burp extension: AuthMatrix](https://github.com/SecurityInnovation/AuthMatrix/)
  - [Burp extension: Autorize](https://github.com/Quitten/Autorize)

## Referências

[OWASP Application Security Verification Standard 4.0.1](https

://github.com/OWASP/ASVS/tree/master/4.0), v4.0.1-1, v4.0.1-4, v4.0.1-9, v4.0.1-16