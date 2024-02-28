# Teste de Credenciais Padrão

|ID          |
|------------|
|WSTG-ATHN-02|

## Resumo

Hoje em dia, muitas aplicações web fazem uso de software popular de código aberto ou comercial que pode ser instalado em servidores com configuração mínima ou personalização pelo administrador do servidor. Além disso, muitos dispositivos de hardware (como roteadores de rede e servidores de banco de dados) oferecem interfaces de configuração ou administração baseadas na web.

Frequentemente, essas aplicações, uma vez instaladas, não são configuradas corretamente, e as credenciais padrão fornecidas para autenticação e configuração inicial nunca são alteradas. Essas credenciais padrão são bem conhecidas por testadores de penetração e, infelizmente, também por atacantes maliciosos, que podem usá-las para obter acesso a vários tipos de aplicações.

Além disso, em muitas situações, quando uma nova conta é criada em uma aplicação, uma senha padrão (com algumas características padrão) é gerada. Se esta senha for previsível e o usuário não a alterar no primeiro acesso, isso pode levar a um atacante obtendo acesso não autorizado à aplicação.

A causa raiz desse problema pode ser identificada como:

- Pessoal de TI inexperiente, que não tem conhecimento da importância de alterar senhas padrão em componentes de infraestrutura instalados, ou que deixa a senha como padrão para "facilitar a manutenção".
- Programadores que deixam backdoors para acessar e testar facilmente sua aplicação e depois esquecem de removê-las.
- Aplicações com contas padrão embutidas não removíveis com um nome de usuário e senha predefinidos.
- Aplicações que não forçam o usuário a alterar as credenciais padrão após o primeiro login.

## Objetivos do Teste

- Enumerar as aplicações em busca de credenciais padrão e validar se ainda existem.
- Revisar e avaliar novas contas de usuário para verificar se são criadas com alguma configuração padrão ou padrões identificáveis.

## Como Testar

### Testando Credenciais Padrão de Aplicações Comuns

No teste de caixa-preta, o testador não sabe nada sobre a aplicação e sua infraestrutura subjacente. Na realidade, isso muitas vezes não é verdade, e algumas informações sobre a aplicação são conhecidas. Supomos que você tenha identificado, por meio do uso das técnicas descritas neste Guia de Testes no capítulo [Coleta de Informações](../01-Coleta-de-Informacoes/README.md), pelo menos uma ou mais aplicações comuns que podem conter interfaces administrativas acessíveis.

Quando você identifica uma interface de aplicação, como uma interface web de roteador Cisco ou um portal de administrador WebLogic, verifique se os nomes de usuário e senhas conhecidos para esses dispositivos não resultam em autenticação bem-sucedida. Para fazer isso, você pode consultar a documentação do fabricante ou, de maneira muito mais simples, pode encontrar credenciais comuns usando um mecanismo de busca ou usando um dos sites ou ferramentas listadas na seção de Referências.

Ao enfrentar aplicações para as quais não temos uma lista de contas de usuário padrão e comuns (por exemplo, devido ao fato de que a aplicação não é muito difundida), podemos tentar adivinhar credenciais padrão válidas. Observe que a aplicação testada pode ter uma política de bloqueio de conta ativada, e várias tentativas de adivinhação de senha com um nome de usuário conhecido podem causar o bloqueio da conta. Se for possível bloquear a conta do administrador, pode ser problemático para o administrador do sistema redefini-la.

Muitas aplicações têm mensagens de erro detalhadas que informam aos usuários sobre a validade dos nomes de usuário inseridos. Essas informações serão úteis ao testar contas de usuário padrão ou adivinháveis. Essa funcionalidade pode ser encontrada, por exemplo, na página de login, página de redefinição de senha e página de esqueci minha senha, e na página de inscrição. Depois de encontrar um nome de usuário padrão, você também pode começar a adivinhar senhas para essa conta.

Mais informações sobre esse procedimento podem ser encontradas nas seguintes seções:

- [Testar Enumeração de Conta e Nome de Usuário Presumível](../03-Testes-Gerenciamento-de-Identidade/04-Testar-Enumeração-de-Conta-e-Nome-de-Usuário-Presumivel.md)
- [Teste de Política de Senha Fraca](07-Teste-de-Politica-de-Senha-Fraca.md).

Dado que essas credenciais padrão geralmente estão associadas a contas administrativas, você pode proceder da seguinte maneira:

- Tente os seguintes nomes de usuário: "admin", "administrator", "root", "system", "guest", "operator" ou "super". Esses são populares entre os administradores do sistema e são frequentemente utilizados. Além disso, você pode tentar "qa", "test", "test1", "testing" e nomes semelhantes. Tente qualquer combinação dos acima nos campos de nome de usuário e senha. Se a aplicação for vulnerável à enumeração de nomes de usuário e você conseguir identificar com sucesso qualquer um dos nomes de usuário acima, tente senhas de maneira semelhante. Além disso, tente uma senha em branco ou uma das seguintes: "password", "pass123", "password123", "admin" ou "guest" com as contas acima ou qualquer outra conta enumerada. Também é possível tentar outras permutações dessas credenciais. Se essas senhas falharem, pode valer a pena usar uma lista comum de nomes de usuário e senhas e tentar várias solicitações contra a aplicação. Isso pode, é claro, ser automatizado para economizar tempo.
- Usuários administrativos de aplicativos são frequentemente nomeados de acordo com o nome do aplicativo ou da organização. Isso significa que, se você estiver testando um aplicativo chamado "Obscurity", tente usar obscurity/obscurity ou qualquer outra combinação semelhante como nome de usuário e senha.
- Ao realizar um teste para um cliente, tente usar nomes de contatos que você tenha recebido como nomes de usuários com senhas comuns. Os endereços de e-mail dos clientes podem revelar a convenção de nomenclatura de contas de usuário: se o funcionário "John Doe" tiver o endereço de e-mail `jdoe@example.com`, você pode tentar encontrar os nomes dos administradores do sistema nas redes sociais e adivinhar seus nomes de usuário aplicando a mesma convenção de nomenclatura aos seus nomes.
- Tente usar todos os nomes de usuário acima com senhas em branco.
- Analise o código-fonte e o JavaScript, seja por meio de um proxy ou visualizando o código-fonte. Procure por quaisquer referências a usuários e senhas no código-fonte. Por exemplo, `Se username='admin', então starturl=/admin.asp, senão /index.asp` (para um login bem-sucedido versus um login mal-sucedido). Além disso, se você tiver uma conta válida, faça login e visualize todas as solicitações e respostas para um login válido em comparação com um login inválido, como parâmetros GET adicionais (login=yes), etc.
- Procure por nomes de contas e senhas escritos em comentários no código-fonte. Procure também em diretórios de backup por código-fonte (ou backups de código-fonte) que possam conter comentários e código interessantes.

### Testando Senhas Padrão de Novas Contas

Também pode acontecer que, quando uma nova conta é criada em uma aplicação, a conta seja atribuída a uma senha padrão. Essa senha pode ter algumas características padrão, tornando-a previsível. Se o usuário não a alterar no primeiro uso (isso frequentemente acontece se o usuário não for obrigado a alterá-la) ou se o usuário ainda não tiver feito login na aplicação, isso pode levar um atacante a obter acesso não autorizado à aplicação.

Os conselhos dados anteriormente sobre uma possível política de bloqueio e mensagens de erro detalhadas também são aplicáveis aqui ao testar senhas padrão.

As seguintes etapas podem ser aplicadas para testar esses tipos de credenciais padrão:

- Olhar para a página de Registro de Usuário pode ajudar a determinar o formato esperado e o comprimento mínimo ou máximo dos nomes de usuário e senhas da aplicação. Se não houver uma página de registro de usuário, determine se a organização usa uma convenção padrão para nomes de usuário, como o endereço de e-mail ou o nome antes do `@` no e-mail.
- Tente extrapolar da aplicação como os nomes de usuário são gerados. Por exemplo, um usuário pode escolher seu próprio nome de usuário ou o sistema gera um nome de conta para o usuário com base em algumas informações pessoais ou usando uma sequência previsível? Se a aplicação gerar os nomes de conta em uma sequência previsível, como `user7811`, tente fuzz todos os possíveis contas recursivamente. Se você puder identificar uma resposta diferente da aplicação ao usar um nome de usuário válido e uma senha incorreta, você pode tentar um ataque de força bruta no nome de usuário válido (ou tentar rapidamente qualquer uma das senhas comuns identificadas acima ou na seção de referências).
- Tente determinar se a senha gerada pelo sistema é previsível. Para fazer isso, crie muitas contas novas rapidamente uma após a outra para que você possa comparar e determinar se as senhas são previsíveis. Se forem previsíveis, tente correlacioná-las com os nomes de usuários ou quaisquer contas enumeradas e use-as como base para um ataque de força bruta.
- Se você identificou a convenção de nomenclatura correta para o nome de usuário, tente "força bruta" com senhas com alguma sequência comum previsível, como por exemplo datas de nascimento.
- Tente usar todos os nomes de usuário acima com senhas em branco ou usando o nome de usuário também

 como valor de senha.

#### Teste de Caixa-Cinza

As seguintes etapas dependem de uma abordagem completamente de caixa-cinza. Se apenas algumas dessas informações estiverem disponíveis para você, consulte o teste de caixa-preta para preencher as lacunas.

- Converse com o pessoal de TI para determinar quais senhas eles usam para acesso administrativo e como a administração da aplicação é realizada.
- Pergunte ao pessoal de TI se as senhas padrão são alteradas e se as contas de usuário padrão são desativadas.
- Examine o banco de dados de usuários em busca de credenciais padrão, conforme descrito na seção de teste de caixa-preta. Verifique também se há campos de senha vazios.
- Examine o código em busca de nomes de usuário e senhas codificados.
- Verifique se existem arquivos de configuração que contenham nomes de usuário e senhas.
- Examine a política de senha e, se a aplicação gerar suas próprias senhas para novos usuários, verifique a política em uso para esse procedimento.

## Ferramentas

- [Burp Intruder](https://portswigger.net/burp)
- [THC Hydra](https://github.com/vanhauser-thc/thc-hydra)
- [Nikto 2](https://www.cirt.net/nikto2)

## Referências

- [CIRT](https://cirt.net/passwords)