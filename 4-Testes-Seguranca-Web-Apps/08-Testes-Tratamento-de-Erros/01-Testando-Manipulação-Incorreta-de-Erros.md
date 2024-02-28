# Testando Manipulação Incorreta de Erros

|ID          |
|------------|
|WSTG-ERRH-01|

## Resumo

Todos os tipos de aplicativos (aplicativos da web, servidores da web, bancos de dados, etc.) gerarão erros por várias razões. Os desenvolvedores frequentemente ignoram o tratamento desses erros ou afastam a ideia de que um usuário tentará provocar um erro propositadamente (*por exemplo*, enviando uma string onde um inteiro é esperado). Quando o desenvolvedor considera apenas o caminho feliz, esquece todas as outras entradas possíveis que o código pode receber, mas não pode manipular.

Os erros às vezes se manifestam como:

- rastreamentos de pilha,
- expirações de rede,
- incompatibilidade de entrada,
- e despejos de memória.

A manipulação incorreta de erros pode permitir que atacantes:

- Compreendam as APIs usadas internamente.
- Mapeiem os vários serviços que se integram entre si, obtendo informações sobre sistemas internos e estruturas usadas, abrindo portas para cadeias de ataque.
- Coletem as versões e tipos de aplicativos em uso.
- Neguem o serviço (DoS) forçando o sistema a entrar em um impasse ou em uma exceção não tratada que envia um sinal de pânico para o mecanismo que o executa.
- Ignorem controles onde uma determinada exceção não é restrita pela lógica definida no caminho feliz.

## Objetivos do Teste

- Identificar a saída de erro existente.
- Analisar as diferentes saídas retornadas.

## Como Testar

Os erros geralmente são vistos como benignos, pois fornecem dados de diagnóstico e mensagens que podem ajudar o usuário a entender o problema em questão, ou para o desenvolvedor depurar esse erro.

Ao tentar enviar dados inesperados ou forçar o sistema em certos casos e cenários extremos, o sistema ou aplicativo na maioria das vezes revelará o que está acontecendo internamente, a menos que os desenvolvedores desativem todos os erros possíveis e retornem uma mensagem personalizada específica.

### Servidores da Web

Todos os aplicativos da web são executados em um servidor da web, seja ele integrado ou completo. Os aplicativos da web devem lidar e analisar solicitações HTTP, e para isso um servidor da web sempre faz parte do conjunto de tecnologias. Alguns dos servidores da web mais famosos são NGINX, Apache e IIS.

Os servidores da web têm mensagens e formatos de erro conhecidos. Se alguém não estiver familiarizado com a aparência deles, procurar online fornecerá exemplos. Outra maneira seria consultar a documentação deles, ou simplesmente configurar um servidor localmente e descobrir os erros ao navegar pelas páginas que o servidor da web usa.

Para provocar mensagens de erro, um testador deve:

- Procurar por arquivos e pastas aleatórios que não serão encontrados (404).
- Tentar solicitar pastas que existem e ver o comportamento do servidor (403, página em branco ou listagem de diretórios).
- Tentar enviar uma solicitação que quebre o [HTTP RFC](https://tools.ietf.org/html/rfc7231). Um exemplo seria enviar um caminho muito longo, quebrar o formato dos cabeçalhos ou alterar a versão do HTTP.
  - Mesmo que os erros sejam tratados no nível da aplicação, quebrar o HTTP RFC pode fazer com que o servidor da web integrado se manifeste, já que precisa lidar com a solicitação, e os desenvolvedores esquecem de substituir esses erros.

### Aplicações

Aplicações são as mais suscetíveis a emitir uma ampla variedade de mensagens de erro, que incluem: rastreamentos de pilha, despejos de memória, exceções mal manipuladas e erros genéricos. Isso ocorre porque as aplicações são construídas sob medida na maioria das vezes e os desenvolvedores precisam observar e lidar com todos os casos de erro possíveis (ou ter um mecanismo global de captura de erros), e esses erros podem surgir de integrações com outros serviços.

Para fazer com que uma aplicação lance esses erros, um testador deve:

1. Identificar pontos de entrada possíveis onde a aplicação está esperando dados.
2. Analisar o tipo de entrada esperado (strings, inteiros, JSON, XML, etc.).
3. Fazer fuzzing em cada ponto de entrada com base nas etapas anteriores para ter um cenário de teste mais focado.
   - Fazer fuzzing em todos os pontos de entrada com todas as injeções possíveis não é a melhor solução, a menos que você tenha tempo de teste ilimitado e a aplicação possa lidar com tanta entrada.
   - Se o fuzzing não for uma opção, escolher manualmente entradas viáveis que tenham a maior chance de quebrar um determinado analisador (*por exemplo*, um colchete de fechamento para um corpo JSON, um texto longo onde são esperados apenas alguns caracteres, injeção de CLRF com parâmetros que podem ser analisados por servidores e controles de validação de entrada, caracteres especiais que não são aplicáveis para nomes de arquivos, etc.).
   - O fuzzing com dados técnicos deve ser executado para cada tipo, pois às vezes os interpretadores falharão fora do tratamento de exceção do desenvolvedor.
4. Compreender o serviço que responde com a mensagem de erro e tentar criar uma lista de fuzz mais refinada para extrair mais informações ou detalhes de erro desse serviço (pode ser um banco de dados, um serviço independente, etc.).

As mensagens de erro são às vezes a principal fraqueza na elaboração de sistemas, especialmente em uma arquitetura de microsserviços. Se os serviços não estiverem adequadamente configurados para lidar com erros de maneira genérica e uniforme, as mensagens de erro permitiriam que um testador identificasse qual serviço manipula quais solicitações, permitindo um ataque mais focado por serviço.

> O testador precisa ficar atento ao tipo de resposta. Às vezes, os erros são retornados como sucesso com um corpo de erro, ocultam o erro em um redirecionamento 302 ou simplesmente têm uma maneira personalizada de representar esse erro.

## Remediação

Para a remediação visite [Proactive Controls C10](https://owasp.org/www-project-proactive-controls/v3/en/c10-errors-exceptions) e [Error Handling Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Error_Handling_Cheat_Sheet.html).

## Playground

- [Juice Shop - Error Handling](https://bkimminich.gitbooks.io/pwning-owasp-juice-shop/content/part2/security-misconfiguration.html#provoke-an-error-that-is-neither-very-gracefully-nor-consistently-handled)

## Referencias

- [WSTG: Appendix C - Fuzz Vectors](../../6-Appendix/C-Fuzz_Vectors.md)
- [Proactive Controls C10: Handle All Errors and Exceptions](https://owasp.org/www-project-proactive-controls/v3/en/c10-errors-exceptions)
- [ASVS v4.1 v7.4: Error handling](https://github.com/OWASP/ASVS/blob/master/4.0/en/0x15-V7-Error-Logging.md#v74-error-handling)
- [CWE 728 - Improper Error Handling](https://cwe.mitre.org/data/definitions/728.html)
- [Cheat Sheet Series: Error Handling](https://cheatsheetseries.owasp.org/cheatsheets/Error_Handling_Cheat_Sheet.html)
  