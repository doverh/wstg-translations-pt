# Testando Respostas Fracas para Perguntas de Segurança

|ID          |
|------------|
|WSTG-ATHN-08|

## Resumo

Frequentemente chamadas de perguntas e respostas "secretas", as perguntas e respostas de segurança são frequentemente usadas para recuperar senhas esquecidas (consulte [Testando funcionalidades fracas de alteração ou redefinição de senha](09-Testing_for_Weak_Password_Change_or_Reset_Functionalities.md) ou como segurança adicional além da senha.

Elas são geralmente geradas durante a criação da conta e exigem que o usuário selecione algumas perguntas pré-geradas e forneça uma resposta apropriada. Elas também podem permitir que o usuário gere suas próprias perguntas e respostas. Ambos os métodos são propensos a inseguranças. Idealmente, as perguntas de segurança devem gerar respostas conhecidas apenas pelo usuário, não sendo adivinháveis ou descobríveis por mais ninguém. Isso é mais difícil do que parece.

Perguntas e respostas de segurança dependem do sigilo da resposta. As perguntas e respostas devem ser escolhidas de forma que as respostas sejam conhecidas apenas pelo titular da conta. No entanto, embora muitas respostas possam não ser de conhecimento público, a maioria das perguntas implementadas por sites promove respostas pseudo-privadas.

### Perguntas Pré-geradas

A maioria das perguntas pré-geradas é bastante simplista e pode levar a respostas inseguras. Por exemplo:

- As respostas podem ser conhecidas por membros da família ou amigos próximos do usuário, por exemplo, "Qual é o sobrenome de solteira da sua mãe?", "Qual é a sua data de nascimento?"
- As respostas podem ser facilmente adivinháveis, por exemplo, "Qual é a sua cor favorita?", "Qual é o seu time de beisebol favorito?"
- As respostas podem ser sujeitas a força bruta, por exemplo, "Qual é o primeiro nome do seu professor favorito do ensino médio?" - a resposta provavelmente está em algumas listas facilmente baixáveis de nomes populares, e, portanto, um ataque de força bruta simples pode ser scriptado.
- As respostas podem ser descobertas publicamente, por exemplo, "Qual é o seu filme favorito?" - a resposta pode ser facilmente encontrada na página de perfil de mídia social do usuário.

### Perguntas Autogeradas

O problema ao permitir que os usuários gerem suas próprias perguntas é que isso permite que eles gerem perguntas muito inseguras ou até mesmo ignorem completamente o propósito de ter uma pergunta de segurança. Aqui estão alguns exemplos do mundo real que ilustram esse ponto:

- "Qual é 1+1?"
- "Qual é o seu nome de usuário?"
- "Minha senha é S3cur|ty!"

## Objetivos do Teste

- Determinar a complexidade e a simplicidade das perguntas.
- Avaliar possíveis respostas do usuário e capacidades de força bruta.

## Como Testar

### Testando Perguntas Pré-geradas Fracas

Tente obter uma lista de perguntas de segurança criando uma nova conta ou seguindo o processo de "Não lembro minha senha". Tente gerar o maior número possível de perguntas para ter uma boa ideia do tipo de perguntas de segurança que são feitas. Se alguma das perguntas de segurança se enquadrar nas categorias descritas acima, elas estarão vulneráveis a ataques (adivinhação, força bruta, disponibilidade em mídias sociais, etc.).

### Testando Perguntas Autogeradas Fracas

Tente criar perguntas de segurança criando uma nova conta ou configurando as propriedades de recuperação de senha da sua conta existente. Se o sistema permitir que o usuário gere suas próprias perguntas de segurança, estará vulnerável a ter perguntas inseguras criadas. Se o sistema usar as perguntas de segurança autogeradas durante a funcionalidade de senha esquecida e se os nomes de usuário puderem ser enumerados (consulte [Testando Enumeração de Conta e Conta de Usuário Adivinhável](../03-Identity_Management_Testing/04-Testing_for_Account_Enumeration_and_Guessable_User_Account.md), então deve ser fácil para o testador enumerar várias perguntas autogeradas. É de se esperar encontrar várias perguntas autogeradas fracas usando este método.

### Testando Respostas Força Bruta

Use os métodos descritos em [Testando Mecanismo de Bloqueio Fraco](03-Testing_for_Weak_Lock_Out_Mechanism.md) para determinar se um número de respostas de segurança incorretas aciona um mecanismo de bloqueio.

A primeira coisa a se considerar ao tentar explorar perguntas de segurança é o número de perguntas que precisam ser respondidas. A maioria das aplicações precisa apenas que o usuário responda a uma única pergunta, enquanto algumas aplicações críticas podem exigir que o usuário responda a duas ou até mais perguntas.

O próximo passo é avaliar a força das perguntas de segurança. As respostas podem ser obtidas por uma simples pesquisa no Google ou com um ataque de engenharia social? Como testador de penetração, aqui está um passo a passo para explorar um esquema de perguntas de segurança:

- A aplicação permite que o usuário final escolha a pergunta que precisa ser respondida? Se sim, concentre-se em perguntas que tenham:

  - Uma resposta "pública"; por exemplo, algo que pode ser encontrado com uma simples pesquisa em mecanismos de busca.
  - Uma resposta factual, como uma "primeira escola" ou outras informações que podem ser consultadas.
  - Poucas respostas possíveis, como "qual era o modelo do seu primeiro carro". Essas perguntas apresentariam ao atacante uma lista curta de respostas possíveis e, com base em estatísticas, o atacante poderia classificar as respostas de mais prováveis a menos prováveis.

- Determine quantas tentativas você tem, se possível.
  - A redefinição de senha permite tentativas ilimitadas?
  - Existe um período de bloqueio após X respostas incorretas? Tenha em mente que um sistema de bloqueio pode ser um problema de segurança em si, pois pode ser explorado por um atacante para lançar uma negação de serviço contra usuários legítimos.
  - Escolha a per

gunta apropriada com base na análise dos pontos acima e pesquise para determinar as respostas mais prováveis.

A chave para explorar com sucesso e contornar um esquema de perguntas de segurança fraco é encontrar uma pergunta ou conjunto de perguntas que ofereça a possibilidade de encontrar facilmente as respostas. Procure sempre por perguntas que possam lhe dar a maior chance estatística de adivinhar a resposta correta, se você não tiver certeza de nenhuma das respostas. No final, um esquema de perguntas de segurança é tão forte quanto a pergunta mais fraca.

## Referências

- [A Maldição da Pergunta Secreta](https://www.schneier.com/essay-081.html)
- [The OWASP Security Questions Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Choosing_and_Using_Security_Questions_Cheat_Sheet.html)