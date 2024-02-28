# Testando Diretório Transversal ou Path Traversal e Inclusão de Arquivo

|ID          |
|------------|
|WSTG-ATHZ-01|

## Resumo

Muitas aplicações web usam e gerenciam arquivos como parte de sua operação diária. Utilizando métodos de validação de entrada que não foram bem projetados ou implementados, um atacante pode explorar o sistema para ler ou gravar arquivos que não deveriam ser acessíveis. Em situações específicas, também é possível executar código arbitrário ou comandos de sistema.

Tradicionalmente, servidores web e aplicações web implementam mecanismos de autenticação para controlar o acesso a arquivos e recursos. Servidores web tentam confinar os arquivos dos usuários dentro de um "diretório raiz" ou "root" que representa um diretório físico no sistema de arquivos. Os usuários devem considerar este diretório como o diretório base na estrutura hierárquica da aplicação web.

A definição dos privilégios é feita usando Listas de Controle de Acesso (ACLs), que identificam quais usuários ou grupos devem ter acesso, modificar ou executar um arquivo específico no servidor. Esses mecanismos são projetados para impedir que usuários maliciosos acessem arquivos sensíveis (por exemplo, o  arquivo `/etc/passwd` em uma plataforma UNIX) ou evitar a execução de comandos de sistema.

Muitos aplicativos web usam scripts do lado do servidor para incluir diferentes tipos de arquivos. É bastante comum usar esse método para gerenciar imagens, modelos, carregar textos estáticos, etc. Infelizmente, esses aplicações expõem vulnerabilidades de segurança se os parâmetros de entrada (ou seja, parâmetros de formulário, valores de cookies) não forem validados corretamente.

Em servidores web e aplicações web, esse tipo de problema surge em ataques de traversing de diretório/inclusão de arquivo. Ao explorar esse tipo de vulnerabilidade, um atacante é capaz de ler diretórios ou arquivos que normalmente não poderia ler, acessar dados fora do diretório raiz do documento web ou incluir scripts e outros tipos de arquivos de sites externos.

Para o propósito do Guia de Teste da OWASP, apenas as ameaças à segurança relacionadas a aplicações web serão consideradas e não as ameaças a servidores web (por exemplo, o infame código de escape `%5c` no servidor web Microsoft IIS). Sugestões adicionais de leitura serão fornecidas na seção de referências para leitores interessados.

Esse tipo de ataque também é conhecido como ataque dot-dot-slash (`../`), traversing de diretório, escalada de diretório ou backtracking (Nota do Tradutor: algorítimo de busca por força bruta).

Durante uma avaliação, para descobrir falhas de traversing de diretório e inclusão de arquivo, os testadores precisam realizar duas etapas diferentes:

1. Enumeração de Vetores de Injeção (uma avaliação sistemática de cada vetor de entrada)
2. Técnicas de Teste (uma avaliação metodológica de cada técnica de ataque usada por um atacante para explorar a vulnerabilidade)

## Objetivos do Teste

- Identificar pontos de injeção relacionados a traversing de diretório.
- Avaliar técnicas de bypass e identificar a extensão do traversing de diretório.

## Como Testar

### Teste de Caixa Preta

#### Enumeração de Vetores de Injeção

A fim de determinar qual parte da aplicação é vulnerável à validação de entrada, o testador precisa enumerar todas as partes da aplicação que aceitam conteúdo do usuário. Isso inclui consultas HTTP GET e POST e opções comuns, como uploads de arquivos e formulários HTML.

Aqui estão alguns exemplos das verificações a serem realizadas nesta etapa:

- Existem parâmetros de solicitação que podem ser usados para operações relacionadas a arquivos?
- Existem extensões de arquivo incomuns?
- Existem nomes de variáveis interessantes?
  - `http://exemplo.com/getUserProfile.jsp?item=ikki.html`
  - `http://exemplo.com/index.php?file=conteudo`
  - `http://exemplo.com/main.cgi?home=index.htm`
- É possível identificar cookies usados pela aplicação web para a geração dinâmica de páginas ou modelos?
  - `Cookie: ID=d9ccd3f4f9f18cc1:TM=2166255468:LM=1162655568:S=3cFpqbJgMSSPKVMV:TEMPLATE=flower`
  - `Cookie: USER=1826cc8f:PSTYLE=GreenDotRed`

#### Técnicas de Teste

A próxima etapa do teste é analisar as funções de validação de entrada presentes na aplicação web. Usando o exemplo anterior, a página dinâmica chamada `getUserProfile.jsp` carrega informações estáticas de um arquivo e mostra o conteúdo aos usuários. Um atacante poderia inserir a string maliciosa `../../../../etc/passwd` para incluir o arquivo de hash de senha de um sistema Linux/UNIX. Obviamente, esse tipo de ataque é possível apenas se o ponto de verificação de validação falhar; de acordo com os privilégios do sistema de arquivos, a própria aplicação web deve ser capaz de ler o arquivo.

Para testar com sucesso essa falha, o testador precisa ter conhecimento do sistema em teste e da localização dos arquivos solicitados. Não faz sentido solicitar `/etc/passwd` de um servidor web IIS.

```text
http://exemplo.com/getUserProfile.jsp?item=../../../../etc/passwd
```

Para o exemplo dos cookies:

```text
Cookie: USER=1826cc8f:PSTYLE=../../../../etc/passwd
```

Também é possível incluir arquivos e scripts localizados em sites externos:

```text
http://exemplo.com/index.php?file=http://www.owasp.org/malicioustxt
```

Se protocolos forem aceitos como argumentos, como no exemplo acima, também é possível sondar o sistema de arquivos local dessa maneira:

```text
http://exemplo.com/index.php?file=file:///etc/passwd
```

Se protocolos forem aceitos como argumentos, como nos exemplos acima, também é possível sondar os serviços locais e serviços próximos:

```text
http://exemplo.com/index.php?file=http://localhost:8080
http://exemplo.com/index.php?file=http://192.168.0.2:9080
```

O exemplo a seguir demonstrará como é possível mostrar o código-fonte de um componente CGI, sem usar caracteres de travers

ing de diretório.

```text
http://exemplo.com/main.cgi?home=main.cgi
```

O componente chamado `main.cgi` está localizado no mesmo diretório dos arquivos HTML estáticos normais usados pela aplicação. Em alguns casos, o testador precisa codificar as solicitações usando caracteres especiais (como o ponto `.`), `%00` nulo, etc., para contornar os controles de extensão de arquivo ou evitar a execução de scripts.

**Dica:** É um erro comum dos desenvolvedores não esperar todas as formas de codificação e, portanto, fazer validação apenas para conteúdo codificado basicamente. Se a string de teste não for bem-sucedida inicialmente, tente outro esquema de codificação.

Cada sistema operacional usa caracteres diferentes como separador de diretório:

- Sistemas operacionais semelhantes ao Unix:
  - diretório raiz: `/`
  - separador de diretório: `/`
- Sistemas operacionais Windows:
  - diretório raiz: `<letra da unidade>:`
  - separador de diretório: `\` ou `/`
- macOS clássico:
  - diretório raiz: `<letra da unidade>:`
  - separador de diretório: `:`

Devemos levar em consideração os seguintes mecanismos de codificação de caracteres:

- Codificação de URL e dupla codificação de URL
  - `%2e%2e%2f` representa `../`
  - `%2e%2e/` representa `../`
  - `..%2f` representa `../`
  - `%2e%2e%5c` representa `..\`
  - `%2e%2e\` representa `..\`
  - `..%5c` representa `..\`
  - `%252e%252e%255c` representa `..\`
  - `..%255c` representa `..\` e assim por diante.
- Codificação Unicode/UTF-8 (funciona apenas em sistemas que conseguem aceitar sequências de UTF-8 excedentes)
  - `..%c0%af` representa `../`
  - `..%c1%9c` representa `..\`

Existem outras considerações específicas de sistema operacional e framework de aplicação também. Por exemplo, o Windows é flexível na análise de caminhos de arquivo.

- Shell do Windows: Adicionar qualquer um dos seguintes aos caminhos usados em um comando de shell não resulta em nenhuma diferença na função:
  - Sinais de maior e menor `<` e `>` no final do caminho
  - Aspas duplas (fechadas corretamente) no final do caminho
  - Marcadores de diretório atual supérfluos, como `./` ou `.\\`
  - Marcadores de diretório pai supérfluos com itens arbitrários que podem ou não existir:
    - `file.txt`
    - `file.txt...`
    - `file.txt<espaços>`
    - `file.txt""""`
    - `file.txt<<<>>><`
    - `./././file.txt`
    - `não_existe/../file.txt`
- API do Windows: Os seguintes itens são descartados ao serem usados em qualquer comando de shell ou chamada de API em que uma string é considerada como um nome de arquivo:
  - pontos
  - espaços
- Caminhos UNC do Windows: Usados para referenciar arquivos em compartilhamentos SMB. Às vezes, é possível fazer com que uma aplicação se refira a arquivos em um caminho UNC remoto. Se assim for, o servidor SMB do Windows pode enviar credenciais armazenadas ao atacante, que podem ser capturadas e quebradas. Essas também podem ser usadas com um endereço IP ou nome de domínio auto-referencial para evitar filtros ou para acessar arquivos em compartilhamentos SMB inacessíveis ao atacante, mas acessíveis ao servidor web.
  - `\\servidor_ou_ip\caminho\para\arquivo.abc`
  - `\\?\servidor_ou_ip\caminho\para\arquivo.abc`
- Namespace de Dispositivos do Windows NT: Usado para se referir ao namespace de dispositivos do Windows. Certas referências permitirão acesso a sistemas de arquivos usando um caminho diferente.
  - Pode ser equivalente a uma letra de unidade, como `c:\`, ou até mesmo a um volume de unidade sem uma letra atribuída: `\\.\GLOBALROOT\Device\HarddiskVolume1\`
  - Refere-se à primeira unidade de disco na máquina: `\\.\CdRom0\`

### Teste de Caixa Cinza

Quando a análise é realizada com uma abordagem de teste de caixa cinza, os testadores precisam seguir a mesma metodologia do teste de caixa preta. No entanto, como eles podem revisar o código-fonte, é possível procurar os vetores de entrada de maneira mais fácil e precisa. Durante uma revisão de código-fonte, eles podem usar ferramentas simples (como o comando *grep*) para procurar um ou mais padrões comuns no código da aplicação: funções/métodos de inclusão, operações de sistema de arquivos, etc.

- `PHP: include(), include_once(), require(), require_once(), fopen(), readfile(), ...`
- `JSP/Servlet: java.io.File(), java.io.FileReader(), ...`
- `ASP: include file, include virtual, ...`

Usando mecanismos de pesquisa de código online (por exemplo, [Searchcode](https://searchcode.com/)), também pode ser possível encontrar falhas de traversing de diretório em software de código aberto publicado na Internet.

Para o PHP, os testadores podem usar a seguinte expressão regular:

```text
(include|require)(_once)?\s*['"(]?\s*\$_(GET|POST|COOKIE)
```

Usando o método de teste de caixa cinza, é possível descobrir vulnerabilidades que geralmente são mais difíceis de encontrar ou até impossíveis de encontrar durante uma avaliação padrão de caixa preta.

Algumas aplicações web geram páginas dinâmicas usando valores e parâmetros armazenados em um banco de dados. Pode ser possível inserir strings de traversing de diretório especialmente criadas quando a aplicação adiciona dados ao banco de dados. Esse tipo de problema de segurança é difícil de descobrir devido ao fato de que os parâmetros dentro das funções de inclusão parecem ser internos e **seguros**, mas na realidade não são.

Além disso, ao revisar o código-fonte, é possível analisar as funções que devem lidar com entradas inválidas: alguns desenvolvedores tentam alterar entradas

 inválidas para torná-las válidas, evitando avisos e erros. Essas funções geralmente são propensas a falhas de segurança.

Considere uma aplicação web com estas instruções:

```php
nome_do_arquivo = Request.QueryString("file");
Substituir(nome_do_arquivo, "/", "\");
Substituir(nome_do_arquivo, "..\", "");
```

Testar a falha é alcançado por:

```text
file=....//....//boot.ini
file=....\\....\\boot.ini
file= ..\..\boot.ini
```

## Ferramentas

- [DotDotPwn - The Directory Traversal Fuzzer](https://github.com/wireghoul/dotdotpwn)
- [Cadeias de fuzzing para Traversing de Diretório (do WFuzz Tool)](https://github.com/xmendez/wfuzz/blob/master/wordlist/Injections/Traversal.txt)
- [OWASP ZAP](https://www.zaproxy.org/)
- [Burp Suite](https://portswigger.net)
- Ferramentas de codificação/decodificação
- [Procurador de strings "grep"](https://www.gnu.org/software/grep/)
- [DirBuster](https://wiki.owasp.org/index.php/Category:OWASP_DirBuster_Project)

## Referências

### Artigos

- [phpBB Attachment Mod Directory Traversal HTTP POST Injection](https://seclists.org/vulnwatch/2004/q4/33)
- [Windows File Pseudonyms: Pwnage and Poetry](https://www.slideshare.net/BaronZor/windows-file-pseudonyms)

### Nota do Tradutor
Diretorio ou Path Traversal nesse contexto significa visitar cada elemento do diretorio literalmente atravessando-o. 