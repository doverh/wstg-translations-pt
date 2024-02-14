# Testando Funcionalidades Fracas para Alteração ou Redefinição de Senha

|ID          |
|------------|
|WSTG-ATHN-09|

## Resumo

A função de alteração e redefinição de senha de uma aplicação é um mecanismo de autosserviço para alteração ou redefinição de senha pelos usuários. Esse mecanismo de autosserviço permite que os usuários alterem ou redefinam rapidamente suas senhas sem a intervenção de um administrador. Quando as senhas são alteradas, geralmente são alteradas dentro da aplicação. Quando as senhas são redefinidas, elas são exibidas na aplicação ou enviadas por e-mail ao usuário. Isso pode indicar que as senhas são armazenadas em texto simples ou em um formato descriptografável.

## Objetivos do Teste

- Determinar a resistência da aplicação à subversão do processo de alteração de senha da conta, permitindo que alguém altere a senha de uma conta.
- Determinar a resistência da funcionalidade de redefinição de senha contra adivinhação ou bypass.

## Como Testar

Para tanto a alteração quanto a redefinição de senha, é importante verificar:

1. se os usuários, além dos administradores, podem alterar ou redefinir senhas para contas que não são suas.
2. se os usuários podem manipular ou subverter o processo de alteração ou redefinição de senha para alterar ou redefinir a senha de outro usuário ou administrador.
3. se o processo de alteração ou redefinição de senha é vulnerável a [CSRF](../06-Session_Management_Testing/05-Testing_for_Cross_Site_Request_Forgery.md).

### Testar Redefinição de Senha

Além das verificações anteriores, é importante verificar o seguinte:

- Quais informações são necessárias para redefinir a senha?

  O primeiro passo é verificar se são necessárias perguntas secretas. Enviar a senha (ou um link de redefinição de senha) para o endereço de e-mail do usuário sem pedir uma pergunta secreta significa depender 100% da segurança desse endereço de e-mail, o que não é adequado se a aplicação precisar de um alto nível de segurança.

  Por outro lado, se perguntas secretas são usadas, o próximo passo é avaliar a força delas. Este teste específico é discutido em detalhes no parágrafo [Testando Pergunta/Resposta de Segurança Fraca](08-Testing_for_Weak_Security_Question_Answer.md) deste guia.

- Como as senhas redefinidas são comunicadas ao usuário?

  O cenário mais inseguro aqui é se a ferramenta de redefinição de senha mostrar a senha; isso dá ao atacante a capacidade de fazer login na conta, e a menos que a aplicação forneça informações sobre o último login, a vítima não saberia que sua conta foi comprometida.

  Um cenário menos inseguro é se a ferramenta de redefinição de senha força o usuário a alterar imediatamente sua senha. Embora não seja tão furtivo quanto o primeiro caso, permite que o atacante ganhe acesso e bloqueia o usuário real.

  A melhor segurança é alcançada se a redefinição de senha for feita por e-mail para o endereço com o qual o usuário se cadastrou inicialmente, ou algum outro endereço de e-mail; isso obriga o atacante a não apenas adivinhar para qual conta de e-mail o reset de senha foi enviado (a menos que a aplicação mostre essa informação), mas também comprometer essa conta de e-mail para obter a senha temporária ou o link de redefinição de senha.

- As senhas redefinidas são geradas aleatoriamente?

  O cenário mais inseguro aqui é se a aplicação enviar ou visualizar a senha antiga em texto claro, pois isso significa que as senhas não são armazenadas em formato hash, o que é um problema de segurança em si.

  A melhor segurança é alcançada se as senhas forem geradas aleatoriamente com um algoritmo seguro que não pode ser derivado.

- A funcionalidade de redefinição de senha solicita confirmação antes de alterar a senha?

  Para limitar os ataques de negação de serviço, a aplicação deve enviar por e-mail um link para o usuário com um token aleatório, e somente se o usuário visitar o link a redefinição será concluída. Isso garante que a senha atual ainda seja válida até que a redefinição seja confirmada.

### Testar Alteração de Senha

Além do teste anterior, é importante verificar:

- A senha antiga é solicitada para concluir a alteração?

  O cenário mais inseguro aqui é se a aplicação permitir a alteração da senha sem solicitar a senha atual. De fato, se um atacante conseguir assumir o controle de uma sessão válida, poderia facilmente alterar a senha da vítima.
  Veja também o parágrafo [Testando Política de Senha Fraca](07-Testing_for_Weak_Password_Policy.md) deste guia.

## Remediação

A função de alteração ou redefinição de senha é uma função sensível e requer alguma forma de proteção, como exigir que os usuários se autentiquem novamente ou apresentar ao usuário telas de confirmação durante o processo.

## Referências

- [OWASP Forgot Password Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Forgot_Password_Cheat_Sheet.html)