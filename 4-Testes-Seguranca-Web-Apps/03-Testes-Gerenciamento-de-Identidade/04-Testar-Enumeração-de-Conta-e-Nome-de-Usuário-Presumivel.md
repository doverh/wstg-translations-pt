# Testar Enumeração de Conta e Nome de Usuário Presumível

|ID          |
|------------|
|WSTG-IDNT-04|

## Resumo

O escopo deste teste é verificar se é possível coletar um conjunto de nomes de usuário válidos interagindo com o mecanismo de autenticação da aplicação. Este teste será útil para testes de força bruta, nos quais o testador verifica se, dado um nome de usuário válido, é possível encontrar a senha correspondente.

Frequentemente, as aplicações web revelam se um nome de usuário existe no sistema, seja como consequência de uma má configuração ou como uma decisão de design. Por exemplo, às vezes, ao enviar credenciais incorretas, recebemos uma mensagem que informa que ou o nome de usuário está presente no sistema ou a senha fornecida está errada. As informações obtidas podem ser usadas por um atacante para obter uma lista de usuários no sistema. Essas informações podem ser usadas para atacar a aplicação web, por exemplo, por meio de um ataque de força bruta ou de nome de usuário e senha padrão.

O testador deve interagir com o mecanismo de autenticação da aplicação para entender se o envio de determinadas solicitações faz com que a aplicação responda de maneiras diferentes. Esse problema ocorre porque as informações liberadas pela aplicação web ou servidor web quando o usuário fornece um nome de usuário válido são diferentes das quando usam um inválido.

Em alguns casos, uma mensagem é recebida que revela se as credenciais fornecidas estão erradas devido a um nome de usuário ou senha inválido. Às vezes, os testadores podem enumerar os usuários existentes enviando um nome de usuário e uma senha em branco.

## Objetivos do Teste

- Revisar processos relacionados à identificação do usuário (*por exemplo*, registro, login, etc.).
- Enumerar usuários sempre que possível por meio da análise das respostas.

## Como Testar

No teste de caixa preta, o testador não sabe nada sobre a aplicação específica, nome de usuário, lógica da aplicação, mensagens de erro na página de login ou facilidades de recuperação de senha. Se a aplicação for vulnerável, o testador recebe uma mensagem de resposta que revela, direta ou indiretamente, alguma informação útil para enumerar usuários.

### Mensagem de Resposta HTTP

#### Testando Credenciais Válidas

Registre a resposta do servidor quando você envia um ID de usuário válido e uma senha válida.

> Usando um proxy da web, observe as informações obtidas desta autenticação bem-sucedida (Resposta HTTP 200, comprimento da resposta).

#### Testando Usuário Válido com Senha Errada

Agora, o testador deve tentar inserir um ID de usuário válido e uma senha incorreta e registrar a mensagem de erro gerada pela aplicação.

> O navegador deve exibir uma mensagem semelhante à seguinte:
>
> ![Autenticação Falhou](images/AuthenticationFailed.png)\
> *Figura 4.3.4-1: Autenticação Falhou*
>
> Ao contrário de qualquer mensagem que revele a existência do usuário, como a seguinte:
>
> `Login para Usuário foo: senha inválida`
>
> Usando um proxy da web, observe as informações obtidas desta tentativa de autenticação mal-sucedida (Resposta HTTP 200, comprimento da resposta).

#### Testando um Nome de Usuário Inexistente

Agora, o testador deve tentar inserir um ID de usuário inválido e uma senha incorreta e registrar a resposta do servidor (o testador deve ter certeza de que o nome de usuário não é válido na aplicação). Registre a mensagem de erro e a resposta do servidor.

> Se o testador inserir um ID de usuário inexistente, pode receber uma mensagem semelhante a:
>
> ![Este Usuário Não Está Ativo](images/Userisnotactive.png)\
> *Figura 4.3.4-3: Este Usuário Não Está Ativo*
>
> ou uma mensagem como a seguinte:
>
> `Falha no login para o Usuário foo: Conta inválida`
>
> Geralmente, a aplicação deve responder com a mesma mensagem de erro e comprimento para diferentes solicitações incorretas. Se as respostas não forem iguais, o testador deve investigar e descobrir a chave que cria uma diferença entre as duas respostas. Por exemplo:
>
> 1. Solicitação do cliente: Usuário válido/senha errada
> 2. Resposta do servidor: A senha não está correta
> 3. Solicitação do cliente: Usuário errado/senha errada
> 4. Resposta do servidor: Usuário não reconhecido
>
> As respostas acima permitem ao cliente entender que, para a primeira solicitação, eles têm um nome de usuário válido. Portanto, eles podem interagir com a aplicação solicitando um conjunto de possíveis IDs de usuário e observando a resposta.
>
> Olhando para a segunda resposta do servidor, o testador entende da mesma forma que eles não possuem um nome de usuário válido. Portanto, eles podem interagir da mesma maneira e criar uma lista de IDs de usuário válidos olhando para as respostas do servidor.

### Outras Maneiras de Enumerar Usuários

Os testadores podem enumerar usuários de várias maneiras, como:

#### Analisando o Código de Erro Recebido nas Páginas de Login

Algumas aplicações web liberam um código de erro ou mensagem específica que podemos analisar.

#### Analisando URLs e Redirecionamentos de URLs

Por exemplo:

- `http://www.foo.com/err.jsp?User=baduser&Error=0`
- `http://www.foo.com/err.jsp?User=gooduser&Error=2`

Como visto acima, quando um testador fornece um ID de usuário e uma senha para a aplicação web, eles veem uma mensagem indicando que ocorreu um erro na URL. No primeiro caso, eles forneceram um ID de usuário e senha ruins. No segundo, um bom ID de usuário e uma senha ruim, para que possam identificar um ID de usuário válido.

#### Sondagem de URI

Às vezes, um servidor web responde de maneira diferente se receber uma solicitação para um diretório existente ou não. Por exemplo, em alguns portais, cada usuário está associado a um diretório. Se os testadores tentarem acessar um diretório existente, poderão receber um erro do servidor web.

Alguns dos erros comuns recebidos de servidores web são:

- Código de erro 403 Forbidden
- Código de erro 404 Not Found

Exemplo:

- `http://www.foo.com/account1` - recebemos do servidor web: 403 Forbidden
- `http://www.foo.com/account2` - recebemos do servidor web: 404 Not Found

No primeiro caso, o usuário existe, mas o testador não pode visualizar a página da web; no segundo caso, o usuário "account2" não existe. Ao coletar essas informações, os testadores podem enumerar os usuários.

#### Analisando Títulos de Páginas da Web

Os testadores podem receber informações úteis sobre o título da página da web, onde podem obter um código de erro específico ou mensagens que revelem se os problemas estão relacionados ao nome de usuário ou senha.

Por exemplo, se um usuário não puder se autenticar em uma aplicação e receber uma página da web com o título semelhante a:

- `Usuário inválido`
- `Autenticação inválida`

#### Analisando uma Mensagem Recebida de uma Facilidade de Recuperação

Quando usamos uma facilidade de recuperação (ou seja, uma função de esquecimento de senha), uma aplicação vulnerável pode retornar uma mensagem que revele se um nome de usuário existe ou não.

Por exemplo, mensagens semelhantes às seguintes:

- `Nome de usuário inválido: o endereço de e-mail não é válido ou o usuário especificado não foi encontrado.`
- `Nome de usuário válido: Sua senha foi enviada com sucesso para o endereço de e-mail com o qual você se registrou.`

#### Mensagem de Erro 404 Amigável

Quando solicitamos um usuário dentro do diretório que não existe, nem sempre recebemos o código de erro 404. Em vez disso, podemos receber "200 OK" com uma imagem; nesse caso, podemos assumir que, quando recebermos a imagem específica, o usuário não existe. Essa lógica pode ser aplicada a outras respostas de servidor web; o truque é uma boa análise das mensagens do servidor web e da aplicação web.

#### Analisando Tempos de Resposta

Além de analisar o conteúdo das respostas, o tempo que a resposta leva também deve ser considerado. Especialmente quando a solicitação causa uma interação com um serviço externo (como enviar um e-mail de senha esquecida), isso pode adicionar vários milissegundos à resposta, o que pode ser usado para determinar se o usuário solicitado é válido.

### Adivinhando Usuários

Em alguns casos, os IDs de usuário são criados com políticas específicas do administrador ou da empresa. Por exemplo, podemos ver um usuário com um ID de usuário criado em ordem sequencial:

```text
CN000100
CN000101
...
```

Às vezes, os nomes de usuário são criados com um alias de REALM e, em seguida, números sequenciais:

- R1001 – usuário 001 para REALM1
- R2001 – usuário 001 para REALM2

No exemplo acima, podemos criar scripts shell simples que compõem IDs de usuário e enviam uma solicitação com uma ferramenta como wget para automatizar uma consulta web para discernir IDs de usuário válidos. Para criar um script, também podemos usar Perl e curl.

Outras possibilidades incluem: - IDs de usuário associados a números de cartão de crédito, ou em geral números com um padrão. - IDs de usuário associados a nomes reais, por exemplo, se Freddie Mercury tem um ID de usuário "fmercury", então você pode supor que Roger Taylor terá o ID de usuário "rtaylor".

Novamente, podemos adivinhar um nome de usuário a partir das informações recebidas de uma consulta LDAP ou de uma coleta de informações do Google, por exemplo, de um domínio específico. O Google pode ajudar a encontrar usuários de domínio por meio de consultas específicas ou por meio de um simples script shell ou ferramenta.

> Ao enumerar contas de usuário, você corre o risco de bloquear contas após um número predefinido de tentativas falhadas (com base na política da aplicação). Além disso, às vezes, seu endereço IP pode ser banido por regras dinâmicas no firewall da aplicação ou no Sistema de Prevenção de Intrusões.

### Teste de Caixa Cinza

#### Testando Mensagens de Erro de Autenticação

Verifique se a aplicação responde da mesma forma para cada solicitação do cliente que produz uma autenticação falha. Para este problema, o teste de caixa preta e o teste de caixa cinza têm o mesmo conceito baseado na análise de mensagens ou códigos de erro recebidos da aplicação web.

> A aplicação deve responder da mesma forma para cada tentativa falhada de autenticação.
>
> Por exemplo: *Credenciais enviadas não são válidas*

## Remediação

Garanta que a aplicação retorne mensagens de erro genéricas consistentes em resposta a nomes de conta inválidos, senhas ou outras credenciais de usuário inseridas durante o processo de login.

Garanta que contas de sistema padrão e contas de teste sejam excluídas antes de liberar o sistema para produção (ou expô-lo a uma rede não confiável).

## Ferramentas

- [OWASP Zed Attack Proxy (ZAP)](https://www.zaproxy.org)
- [curl](https://curl.haxx.se/)
- [PERL](https://www.perl.org)

## Referências

- [Marco Mella, Sun Java Access & Identity Manager Users enumeration](https://securiteam.com/exploits/5ep0f0uquo/)
- [Username Enumeration Vulnerabilities](https://www.gnucitizen.org/blog/username-enumeration-vulnerabilities/)