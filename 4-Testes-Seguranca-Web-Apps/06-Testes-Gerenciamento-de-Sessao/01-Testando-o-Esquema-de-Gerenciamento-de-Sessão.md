# Testando o Esquema de Gerenciamento de Sessão

|ID          |
|------------|
|WSTG-SESS-01|

## Resumo

Um dos componentes principais de qualquer aplicativo baseado na web é o mecanismo pelo qual ele controla e mantém o estado para um usuário que interage com ele. Para evitar autenticação contínua para cada página de um site ou serviço, os aplicativos da web implementam vários mecanismos para armazenar e validar credenciais por um período determinado. Esses mecanismos são conhecidos como Gerenciamento de Sessão.

Neste teste, o testador deseja verificar se os cookies e outros tokens de sessão são criados de maneira segura e imprevisível. Um atacante que consegue prever e forjar um cookie fraco pode facilmente se apropriar das sessões de usuários legítimos.

Os cookies são usados para implementar o gerenciamento de sessão e são detalhadamente descritos no RFC 2965. Em resumo, quando um usuário acessa um aplicativo que precisa acompanhar as ações e a identidade desse usuário em várias solicitações, um cookie (ou cookies) é gerado pelo servidor e enviado ao cliente. O cliente então envia o cookie de volta ao servidor em todas as conexões subsequentes até que o cookie expire ou seja destruído. Os dados armazenados no cookie podem fornecer ao servidor uma ampla gama de informações sobre quem é o usuário, quais ações ele realizou até agora, quais são suas preferências, etc., fornecendo assim um estado a um protocolo sem estado como o HTTP.

Um exemplo típico é fornecido por um carrinho de compras online. Ao longo da sessão de um usuário, o aplicativo deve acompanhar sua identidade, seu perfil, os produtos que ele escolheu comprar, a quantidade, os preços individuais, os descontos, etc. Os cookies são uma maneira eficiente de armazenar e transmitir essas informações (outros métodos incluem parâmetros de URL e campos ocultos).

Devido à importância dos dados que eles armazenam, os cookies são vitais para a segurança geral do aplicativo. Conseguir manipular os cookies pode resultar na apropriação de sessões de usuários legítimos, ganho de privilégios mais elevados em uma sessão ativa e, em geral, influenciar as operações do aplicativo de maneira não autorizada.

Neste teste, o testador deve verificar se os cookies emitidos para os clientes podem resistir a uma ampla gama de ataques destinados a interferir nas sessões de usuários legítimos e no próprio aplicativo. O objetivo geral é ser capaz de forjar um cookie que será considerado válido pelo aplicativo e que fornecerá algum tipo de acesso não autorizado (sequestro de sessão, escalonamento de privilégios, ...).

Geralmente, as principais etapas do padrão de ataque são as seguintes:

- **coleta de cookies**: coleta de um número suficiente de amostras de cookies;
- **engenharia reversa de cookies**: análise do algoritmo de geração de cookies;
- **manipulação de cookies**: forjar um cookie válido para realizar o ataque. Esta última etapa pode exigir um grande número de tentativas, dependendo de como o cookie é criado (ataque de força bruta de cookies).

Outro padrão de ataque consiste em transbordar um cookie. Estritamente falando, este ataque tem uma natureza diferente, pois os testadores não estão tentando recriar um cookie perfeitamente válido. Em vez disso, o objetivo é transbordar uma área de memória, interferindo no comportamento correto do aplicativo e possivelmente injetando (e executando remotamente) código malicioso.

## Objetivos do Teste

- Coletar tokens de sessão, para o mesmo usuário e para diferentes usuários, quando possível.
- Analisar e garantir que haja randomness suficiente para impedir ataques de falsificação de sessão.
- Modificar cookies que não são assinados e contêm informações que podem ser manipuladas.

## Como Testar

### Testes Black-Box e Exemplos

Toda interação entre o cliente e o aplicativo deve ser testada pelo menos nos seguintes critérios:

- Todos os direcionamentos `Set-Cookie` são marcados como `Secure`?
- Alguma operação de Cookie ocorre sobre transporte não criptografado?
- O Cookie pode ser forçado sobre transporte não criptografado?
- Se sim, como o aplicativo mantém a segurança?
- Alguns Cookies são persistentes?
- Quais tempos de `Expires` são usados nos cookies persistentes e são razoáveis?
- Os cookies que se espera serem transitórios estão configurados como tal?
- Quais configurações de `Cache-Control` HTTP/1.1 são usadas para proteger os cookies?
- Quais configurações de `Cache-Control` HTTP/1.0 são usadas para proteger os cookies?

#### Coleta de Cookies

A primeira etapa necessária para manipular o cookie é entender como o aplicativo cria e gerencia cookies. Para essa tarefa, os testadores precisam tentar responder às seguintes perguntas:

- Quantos cookies são usados pelo aplicativo?

  Navegue pelo aplicativo. Anote quando os cookies são criados. Faça uma lista dos cookies recebidos, a página que os define (com a diretiva set-cookie), o domínio para o qual são válidos, seu valor e suas características.

- Quais partes do aplicativo geram ou modificam o cookie?

  Navegando pelo aplicativo, descubra quais cookies permanecem constantes e quais são modificados. Quais eventos modificam o cookie?

- Quais partes do aplicativo exigem esse cookie para serem acessadas e utilizadas?

  Descubra quais partes do aplicativo precisam de um cookie. Acesse uma página e tente novamente sem o cookie, ou com um valor modificado. Tente mapear quais cookies são usados onde.

Uma planilha que mapeia cada cookie para as partes correspondentes do aplicativo e as informações relacionadas pode ser uma saída valiosa desta fase.

#### Análise de Sessão

Os tokens de sessão (Cookie, SessionID ou Campo Oculto) em si devem ser examinados para garantir sua qualidade do ponto de vista da segurança. Eles devem ser testados em relação a critérios como aleatoriedade, unicidade, resistência a análise estatística e criptográfica e vazamento de informações.

- Estrutura do Token e Vazamento de Informações

A primeira etapa é examinar a estrutura e o conte

údo de um ID de sessão fornecido pelo aplicativo. Um erro comum é incluir dados específicos no Token em vez de emitir um valor genérico e fazer referência aos dados reais no servidor.

Se o ID da sessão estiver em texto simples, a estrutura e os dados pertinentes podem ser imediatamente óbvios, como `192.168.100.1:owaspuser:password:15:58`.

Se parte ou a totalidade do token parecer estar codificada ou hash, ela deve ser comparada com várias técnicas para verificar a obstrução óbvia. Por exemplo, a string `192.168.100.1:owaspuser:password:15:58` é representada em hexadecimal, Base64 e como um hash MD5:

- Hex: `3139322E3136382E3130302E313A6F77617370757365723A70617373776F72643A31353A3538`
- Base64: `MTkyLjE2OC4xMDAuMTpvd2FzcHVzZXI6cGFzc3dvcmQ6MTU6NTg=`
- MD5: `01c2fc4f0a817afd8366689bd29dd40a`

Tendo identificado o tipo de obstrução, pode ser possível decodificar de volta para os dados originais. Na maioria dos casos, no entanto, isso é improvável. Mesmo assim, pode ser útil enumerar a codificação do formato da mensagem. Além disso, se tanto o formato quanto a técnica de obstrução puderem ser deduzidos, ataques automatizados de força bruta poderiam ser elaborados.

Tokens híbridos podem incluir informações como endereço IP ou ID do usuário juntamente com uma parte codificada, como `owaspuser:192.168.100.1:a7656fafe94dae72b1e1487670148412`.

Tendo analisado um único token de sessão, a amostra representativa deve ser examinada. Uma análise simples dos tokens deve revelar imediatamente quaisquer padrões óbvios. Por exemplo, um token de 32 bits pode incluir 16 bits de dados estáticos e 16 bits de dados variáveis. Isso pode indicar que os primeiros 16 bits representam um atributo fixo do usuário - por exemplo, o nome de usuário ou o endereço IP. Se a segunda parte de 16 bits estiver aumentando a uma taxa regular, pode indicar um elemento sequencial ou até baseado no tempo na geração do token. Veja exemplos.

Se elementos estáticos nos Tokens forem identificados, mais amostras devem ser reunidas, variando um elemento de entrada potencial de cada vez. Por exemplo, tentativas de login por meio de uma conta de usuário diferente ou de um endereço IP diferente podem resultar em uma variação na parte anteriormente estática do token de sessão.

As seguintes áreas devem ser abordadas durante o teste de estrutura de ID de sessão única e múltipla:

- Quais partes do ID de sessão são estáticas?
- Quais informações confidenciais em texto claro são armazenadas no ID de sessão? Por exemplo, nomes de usuário/UID, endereços IP
- Quais informações confidenciais facilmente decodificáveis são armazenadas?
- Que informações podem ser deduzidas da estrutura do ID de sessão?
- Quais partes do ID de sessão são estáticas para as mesmas condições de login?
- Quais padrões óbvios estão presentes no ID de sessão como um todo ou em partes individuais?

#### Previsibilidade e Aleatoriedade do ID de Sessão

A análise das áreas variáveis (se houver) do ID de sessão deve ser realizada para estabelecer a existência de padrões reconhecíveis ou previsíveis. Essas análises podem ser realizadas manualmente e com ferramentas estatísticas ou criptoanalíticas específicas para deduzir quaisquer padrões no conteúdo do ID de sessão. Verificações manuais devem incluir comparações de IDs de sessão emitidos para as mesmas condições de login - por exemplo, o mesmo nome de usuário, senha e endereço IP.

O tempo é um fator importante que também deve ser controlado. Um grande número de conexões simultâneas deve ser feito para coletar amostras na mesma janela de tempo e manter essa variável constante. Mesmo uma quantização de 50 ms ou menos pode ser muito grosseira, e uma amostra coletada dessa forma pode revelar componentes baseados no tempo que, de outra forma, seriam perdidos.

Elementos variáveis devem ser analisados ao longo do tempo para determinar se são incrementais. Onde são incrementais, padrões relacionados ao tempo absoluto ou decorrido devem ser investigados. Muitos sistemas usam o tempo como uma semente para seus elementos pseudoaleatórios. Quando os padrões são aparentemente aleatórios, hashes unidirecionais de tempo ou outras variações ambientais também devem ser considerados como uma possibilidade. Tipicamente, o resultado de um hash criptográfico é um número decimal ou hexadecimal e, portanto, deve ser identificável.

Ao analisar sequências de ID de sessão, padrões ou ciclos, elementos estáticos e dependências do cliente também devem ser considerados como possíveis elementos contribuintes para a estrutura e funcionamento do aplicativo.

- Os IDs de sessão são comprovadamente aleatórios? Os valores resultantes podem ser reproduzidos?
- As mesmas condições de entrada produzem o mesmo ID em uma execução subsequente?
- Os IDs de sessão são comprovadamente resistentes a análises estatísticas ou criptográficas?
- Quais elementos dos IDs de sessão estão vinculados ao tempo?
- Quais partes dos IDs de sessão são previsíveis?
- O próximo ID pode ser deduzido, dado o conhecimento total do algoritmo de geração e IDs anteriores?

#### Engenharia Reversa de Cookies

Agora que o testador enumerou os cookies e tem uma ideia geral de seu uso, é hora de dar uma olhada mais aprofundada nos cookies que parecem interessantes. Em quais cookies o testador está interessado? Um cookie, para fornecer um método seguro de gerenciamento de sessão, deve combinar várias características, cada uma delas voltada para proteger o cookie de uma classe diferente de ataques.

Essas características são resumidas abaixo:

1. Imprevisibilidade: um cookie deve conter alguma quantidade de dados difíceis de adivinhar. Quanto mais difícil for forjar um cookie válido, mais difícil será invadir a sessão de um usuário legítimo.

 Se um atacante puder adivinhar o cookie usado em uma sessão ativa de um usuário legítimo, poderá se passar totalmente por esse usuário (sequestro de sessão). Para tornar um cookie imprevisível, podem ser usados valores aleatórios ou criptografia.
2. Resistência a alterações: um cookie deve resistir a tentativas maliciosas de modificação. Se o testador receber um cookie como `IsAdmin=No`, é trivial modificá-lo para obter direitos administrativos, a menos que o aplicativo execute uma verificação dupla (por exemplo, anexando ao cookie um hash criptografado de seu valor).
3. Expiração: um cookie crítico deve ser válido apenas por um período apropriado e deve ser excluído do disco ou da memória posteriormente para evitar o risco de ser reproduzido. Isso não se aplica a cookies que armazenam dados não críticos que precisam ser lembrados entre sessões (por exemplo, aparência e sensação do site).
4. Sinal `Secure`: um cookie cujo valor é crítico para a integridade da sessão deve ter esse sinal ativado para permitir sua transmissão apenas em um canal criptografado, para evitar a interceptação.

A abordagem aqui é coletar um número suficiente de instâncias de um cookie e começar a procurar padrões em seu valor. O significado exato de "suficiente" pode variar de um punhado de amostras, se o método de geração de cookie for muito fácil de quebrar, para vários milhares, se o testador precisar prosseguir com alguma análise matemática (por exemplo, qui-quadrados, atratores. Veja mais tarde para mais informações).

É importante prestar atenção especial ao fluxo de trabalho do aplicativo, pois o estado de uma sessão pode ter um grande impacto nos cookies coletados. Um cookie coletado antes da autenticação pode ser muito diferente de um cookie obtido após a autenticação.

Outro aspecto a se levar em consideração é o tempo. Sempre registre o horário exato em que um cookie foi obtido, quando há a possibilidade de que o tempo desempenhe um papel no valor do cookie (o servidor pode usar um carimbo de data/hora como parte do valor do cookie). O tempo registrado pode ser o horário local ou o carimbo de data/hora do servidor incluído na resposta HTTP (ou ambos).

Ao analisar os valores coletados, o testador deve tentar descobrir todas as variáveis que podem ter influenciado o valor do cookie e tentar variá-las uma de cada vez. Passar para o servidor versões modificadas do mesmo cookie pode ser muito útil para entender como o aplicativo lê e processa o cookie.

Exemplos de verificações a serem realizadas nesta fase incluem:

- Qual conjunto de caracteres é usado no cookie? O cookie tem um valor numérico? alfanumérico? hexadecimal? O que acontece se o testador inserir em um cookie caracteres que não pertencem ao conjunto de caracteres esperado?
- O cookie é composto por diferentes subpartes que carregam diferentes informações? Como as diferentes partes são separadas? Com quais delimitadores? Algumas partes do cookie podem ter uma variação maior, outras podem ser constantes, outras podem assumir apenas um conjunto limitado de valores. Dividir o cookie em seus componentes básicos é o primeiro e fundamental passo.

Um exemplo de um cookie estruturado facilmente identificável é o seguinte:

```text
ID=5a0acfc7ffeb919:CR=1:TM=1120514521:LM=1120514521:S=j3am5KzC4v01ba3q
```

Este exemplo mostra 5 campos diferentes, carregando diferentes tipos de dados:

- ID – hexadecimal
- CR – número inteiro pequeno
- TM e LM – número inteiro grande (e curiosamente eles têm o mesmo valor. Vale a pena ver o que acontece modificando um deles)
- S – alfanumérico

Mesmo quando não são usados delimitadores, ter amostras suficientes pode ajudar a entender a estrutura.

#### Ataques de Força Bruta

Ataques de força bruta inevitavelmente derivam de perguntas relacionadas à previsibilidade e aleatoriedade. A variação dentro dos IDs de sessão deve ser considerada junto com a duração e os tempos limite da sessão do aplicativo. Se a variação dentro dos IDs de sessão for relativamente pequena e a validade do ID de sessão for longa, a probabilidade de um ataque de força bruta bem-sucedido é muito maior.

Um ID de sessão longo (ou melhor, um com muita variação) e um período de validade mais curto tornariam muito mais difícil ter sucesso em um ataque de força bruta.

- Quanto tempo levaria um ataque de força bruta em todos os possíveis IDs de sessão?
- O espaço do ID de sessão é grande o suficiente para evitar a força bruta? Por exemplo, o comprimento da chave é suficiente em comparação com a vida útil válida?
- Os atrasos entre as tentativas de conexão com IDs de sessão diferentes mitigam o risco desse ataque?

### Testes Gray-Box e Exemplo

Se o testador tiver acesso à implementação do esquema de gerenciamento de sessão, ele pode verificar o seguinte:

- Token de Sessão Aleatório

  O ID de Sessão ou Cookie emitido para o cliente não deve ser facilmente previsível (não use algoritmos lineares baseados em variáveis previsíveis, como o endereço IP do cliente). É incentivado o uso de algoritmos criptográficos com comprimento de chave de

 pelo menos 128 bits.

- Proteção Contra Ataques de Força Bruta

  O mecanismo de gerenciamento de sessão deve resistir a ataques de força bruta, ou seja, não deve ser prontamente comprometido mesmo que um atacante possa gerar uma grande quantidade de IDs de sessão possíveis.

- Expiração de Sessão

  O sistema deve garantir que as sessões expiram após um período de inatividade ou após um período de tempo especificado. Isso é para garantir que mesmo que um ID de sessão seja comprometido, ele terá uma vida útil limitada.

- Integridade do Token de Sessão

  Se o ID de sessão ou Cookie contiver informações críticas para a sessão, ele deve ser protegido contra alterações por meio de assinatura digital ou outra forma de verificação de integridade.

- Configurações de Cookie Seguro

  O uso do sinal `Secure` em cookies críticos deve ser verificado para garantir que eles sejam transmitidos apenas por meio de canais seguros (HTTPS).

## Relatórios

Os relatórios devem fornecer uma visão clara dos resultados do teste, destacando qualquer descoberta significativa relacionada à previsibilidade, aleatoriedade e proteção contra ataques de força bruta nos tokens de sessão. Se forem encontrados problemas, eles devem ser detalhadamente descritos, juntamente com recomendações para correção.

A documentação deve incluir amostras de cookies coletados, métodos usados para análise e quaisquer ferramentas que tenham facilitado o processo.

## Resultados Esperados

Os resultados esperados deste teste incluem:

1. Confirmação de que os tokens de sessão (Cookies, SessionID) são gerados de maneira segura e imprevisível.
2. Identificação e relato de quaisquer problemas relacionados à previsibilidade, aleatoriedade ou resistência a ataques de força bruta nos tokens de sessão.
3. Recomendações para mitigar quaisquer problemas encontrados.

## Referências

1. [RFC 2965 - HTTP State Management Mechanism](https://tools.ietf.org/html/rfc2965)
2. [OWASP Web Security Testing Guide - Session Management Testing](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Session_Management_Testing)
3. [OWASP Cheat Sheet - Session Management](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)
4. [OWASP Testing Guide - Session Management](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Session_Management_Testing)
5. [OWASP Application Security Verification Standard - Session Management](https://owasp.org/www-project-application-security-verification-standard/)
