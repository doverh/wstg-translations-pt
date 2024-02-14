# Testando Política de Senha Fraca

|ID          |
|------------|
|WSTG-ATHN-07|

## Resumo

O mecanismo de autenticação mais prevalente e mais facilmente administrado é uma senha estática. A senha representa as chaves do reino, mas muitas vezes é subvertida pelos usuários em nome da usabilidade. Em cada um dos recentes hacks de alto perfil que revelaram credenciais de usuários, é lamentavel saber que as senhas mais comuns ainda sejam: `123456`, `password` e `qwerty`.

## Objetivos do Teste

- Determinar a resistência da aplicação contra tentativas de adivinhação de senha por força bruta, usando dicionários de senhas disponíveis, avaliando os requisitos de comprimento, complexidade, reutilização e envelhecimento de senhas.

## Como Testar

1. Quais caracteres são permitidos e proibidos para uso em uma senha? É exigido que o usuário use caracteres de conjuntos de caracteres diferentes, como letras minúsculas e maiúsculas, dígitos e símbolos especiais?
2. Com que frequência um usuário pode alterar sua senha? Quão rapidamente um usuário pode alterar sua senha após uma alteração anterior? Os usuários podem ignorar os requisitos de histórico de senha alterando sua senha 5 vezes seguidas, para que, após a última alteração de senha, tenham configurado novamente sua senha inicial.
3. Quando um usuário deve alterar sua senha?
   - Tanto o [NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#memsecretver) quanto o [NCSC](https://www.ncsc.gov.uk/collection/passwords/updating-your-approach#PasswordGuidance:UpdatingYourApproach-Don'tenforceregularpasswordexpiry) recomendam **não** forçar expiração regular de senha, embora isso possa ser exigido por padrões como o PCI DSS.
4. Com que frequência um usuário pode reutilizar uma senha? A aplicação mantém um histórico das 8 senhas anteriormente usadas pelo usuário?
5. Quão diferente deve ser a próxima senha em relação à última senha?
6. O usuário é impedido de usar seu nome de usuário ou outras informações da conta (como primeiro ou último nome) na senha?
7. Quais são os comprimentos mínimo e máximo de senha que podem ser definidos, e eles são apropriados para a sensibilidade da conta e da aplicação?
8. É possível definir senhas comuns, como `Password1` ou `123456`?

## Remediação

Para mitigar o risco de senhas facilmente adivinháveis facilitando o acesso não autorizado, existem duas soluções: introduzir controles de autenticação adicionais (ou seja, autenticação de dois fatores) ou introduzir uma política de senha forte. A solução mais simples e mais barata é a introdução de uma política de senha forte que garanta comprimento, complexidade, reutilização e envelhecimento da senha; embora idealmente ambas devam ser implementadas.

## Referências

- [Ataques de Força Bruta](https://owasp.org/www-community/attacks/Brute_force_attack)