# Testando Mecanismo de Bloqueio de Contas

|ID          |
|------------|
|WSTG-ATHN-03|

## Resumo

Os mecanismos de bloqueio de contas são usados para mitigar ataques de força bruta. Alguns dos ataques que podem ser derrotados pelo uso do mecanismo de bloqueio incluem:

- Ataque de adivinhação de senha ou nome de usuário no login.
- Adivinhação de código em qualquer funcionalidade de autenticação de dois fatores ou perguntas de segurança.

Os mecanismos de bloqueio de contas exigem um equilíbrio entre proteger contas contra acesso não autorizado e proteger os usuários de serem negados o acesso autorizado. As contas geralmente são bloqueadas após 3 a 5 tentativas mal-sucedidas e só podem ser desbloqueadas após um período de tempo predeterminado, por meio de um mecanismo de desbloqueio de autoatendimento ou intervenção de um administrador.

Apesar de ser fácil realizar ataques de força bruta, o resultado de um ataque bem-sucedido é perigoso, pois o invasor terá acesso total à conta do usuário e a todas as funcionalidades e serviços aos quais têm acesso.

## Objetivos do Teste

- Avaliar a capacidade do mecanismo de bloqueio de conta em mitigar tentativas de adivinhação de senha por força bruta.
- Avaliar a resistência do mecanismo de desbloqueio ao desbloqueio não autorizado da conta.

## Como Testar

### Mecanismo de Bloqueio

Para testar a força dos mecanismos de bloqueio, você precisará de acesso a uma conta que esteja disposto ou possa se dar ao luxo de bloquear. Se você tiver apenas uma conta com a qual pode fazer login na aplicação da web, realize este teste no final do seu plano de teste para evitar perder tempo de teste sendo bloqueado.

Para avaliar a capacidade do mecanismo de bloqueio de conta em mitigar tentativas de adivinhação de senha por força bruta, tente fazer login com uma senha incorreta várias vezes antes de usar a senha correta para verificar se a conta foi bloqueada. Um exemplo de teste pode ser o seguinte:

1. Tente fazer login com uma senha incorreta 3 vezes.
2. Faça login com sucesso usando a senha correta, mostrando assim que o mecanismo de bloqueio não é acionado após 3 tentativas de autenticação incorretas.
3. Tente fazer login com uma senha incorreta 4 vezes.
4. Faça login com sucesso usando a senha correta, mostrando assim que o mecanismo de bloqueio não é acionado após 4 tentativas de autenticação incorretas.
5. Tente fazer login com uma senha incorreta 5 vezes.
6. Tente fazer login com a senha correta. A aplicação retorna "Sua conta está bloqueada", confirmando assim que a conta está bloqueada após 5 tentativas de autenticação incorretas.
7. Tente fazer login com a senha correta 5 minutos depois. A aplicação retorna "Sua conta está bloqueada", mostrando assim que o mecanismo de bloqueio não é desbloqueado automaticamente após 5 minutos.
8. Tente fazer login com a senha correta 10 minutos depois. A aplicação retorna "Sua conta está bloqueada", mostrando assim que o mecanismo de bloqueio não é desbloqueado automaticamente após 10 minutos.
9. Faça login com sucesso com a senha correta 15 minutos depois, mostrando assim que o mecanismo de bloqueio é desbloqueado automaticamente após um período de 10 a 15 minutos.

Um CAPTCHA pode dificultar os ataques de força bruta, mas pode ter suas próprias fraquezas e não deve substituir um mecanismo de bloqueio. Uma falha no CAPTCHA pode incluir:

1. Desafio facilmente derrotado, como aritmética ou conjunto limitado de perguntas.
2. CAPTCHA verifica o código de resposta HTTP em vez do sucesso da resposta.
3. Lógica do servidor CAPTCHA que por padrão assume uma solução bem-sucedida.
4. O resultado do desafio CAPTCHA nunca é validado no lado do servidor.
5. O campo ou parâmetro de entrada do CAPTCHA é processado manualmente e é validado ou escapado de maneira inadequada.

Para avaliar a eficácia do CAPTCHA:

1. Avalie os desafios do CAPTCHA e tente automatizar soluções dependendo da dificuldade.
2. Tente enviar uma solicitação sem resolver o CAPTCHA por meio dos mecanismos normais da interface do usuário.
3. Tente enviar uma solicitação com falha intencional no desafio CAPTCHA.
4. Tente enviar uma solicitação sem resolver o CAPTCHA (assumindo que alguns valores padrão podem ser passados pelo código do lado do cliente, etc.) enquanto usa um proxy de teste (solicitação enviada diretamente do lado do servidor).
5. Tente fuzz pontos de entrada de dados do CAPTCHA (se presentes) com cargas úteis de injeção comuns ou sequências de caracteres especiais.
6. Verifique se a solução para o CAPTCHA pode ser o texto alternativo da imagem, nome do arquivo ou um valor em um campo oculto associado.
7. Tente reenviar respostas conhecidas boas anteriormente identificadas.
8. Verifique se limpar os cookies faz com que o CAPTCHA seja ignorado (por exemplo, se o CAPTCHA é mostrado apenas após um número de falhas).
9. Se o CAPTCHA faz parte de um processo de várias etapas, tente acessar ou concluir uma etapa além do CAPTCHA (por exemplo, se o CAPTCHA for a primeira etapa em um processo de login, tente simplesmente enviar a segunda etapa [nome de usuário e senha]).
10. Verifique se existem métodos alternativos que podem não ter o CAPTCHA imposto, como um ponto de extremidade de API destinado a facilitar o acesso ao aplicativo móvel.

Repita esse processo para todas as funcionalidades possíveis que podem exigir um mecanismo de bloqueio.

### Mecanismo de Desbloqueio

Para avaliar a resistência do mecanismo de desbloqueio ao desbloqueio não autorizado da conta, inicie o mecanismo de desbloqueio e procure por fraquezas. Mecanismos de desbloqueio típicos podem envolver perguntas secretas ou um link de desbloqueio enviado por e-mail. O link de desbloqueio deve ser um link único e único, para impedir que um invasor adivinhe ou reproduza

 o link e execute ataques de força bruta em lotes.

Observe que um mecanismo de desbloqueio deve ser usado apenas para desbloquear contas. Não é o mesmo que um mecanismo de recuperação de senha, embora possa seguir as mesmas práticas de segurança.

## Remediação

Aplique mecanismos de desbloqueio de conta dependendo do nível de risco. Em ordem, do menor para o maior nível de garantia:

1. Bloqueio e desbloqueio baseados em tempo.
2. Desbloqueio de autoatendimento (envia e-mail de desbloqueio para o endereço de e-mail registrado).
3. Desbloqueio manual do administrador.
4. Desbloqueio manual do administrador com identificação positiva do usuário.

Fatores a serem considerados ao implementar um mecanismo de bloqueio de conta:

1. Qual é o risco de tentativas de adivinhação de senha por força bruta contra a aplicação?
2. Um CAPTCHA é suficiente para mitigar esse risco?
3. Um mecanismo de bloqueio do lado do cliente está sendo usado (por exemplo, JavaScript)? (Se sim, desative o código do lado do cliente para teste.)
4. Número de tentativas de logon mal-sucedidas antes do bloqueio. Se o limite de bloqueio for muito baixo, os usuários válidos podem ser bloqueados com muita frequência. Se o limite de bloqueio for muito alto, mais tentativas um atacante pode fazer para forçar a conta antes que seja bloqueada. Dependendo do propósito da aplicação, um intervalo de 5 a 10 tentativas malsucedidas é um limite de bloqueio típico.
5. Como as contas serão desbloqueadas?
   1. Manualmente por um administrador: este é o método de bloqueio mais seguro, mas pode causar inconvenientes aos usuários e ocupar o "valioso" tempo do administrador.
       1. Note que o administrador também deve ter um método de recuperação caso sua conta seja bloqueada.
       2. Este mecanismo de desbloqueio pode levar a um ataque de negação de serviço se o objetivo do invasor for bloquear as contas de todos os usuários da aplicação da web.
   2. Após um período de tempo: Qual é a duração do bloqueio? Isso é suficiente para a aplicação que está sendo protegida? Por exemplo, uma duração de bloqueio de 5 a 30 minutos pode ser um bom compromisso entre mitigar ataques de força bruta e inconveniência para usuários válidos.
   3. Por meio de um mecanismo de autoatendimento: Como mencionado anteriormente, este mecanismo de autoatendimento deve ser seguro o suficiente para evitar que o invasor desbloqueie contas.

## Referências

- Consulte o artigo OWASP sobre [Ataques de Força Bruta](https://owasp.org/www-community/attacks/Brute_force_attack).
- [Forgot Password CS](https://cheatsheetseries.owasp.org/cheatsheets/Forgot_Password_Cheat_Sheet.html).