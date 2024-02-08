# Testar o Manejo de Extensões de Arquivo para Informações Sensíveis

|ID          |
|------------|
|WSTG-CONF-03|

## Resumo

As extensões de arquivo são comumente usadas em servidores web para determinar facilmente quais tecnologias, linguagens e plugins devem ser usados para atender à solicitação web. Embora esse comportamento seja consistente com RFCs e Padrões da Web, o uso de extensões de arquivo padrão fornece ao testador de penetração informações úteis sobre as tecnologias subjacentes usadas em um dispositivo web e simplifica muito a tarefa de determinar o cenário de ataque a ser usado em tecnologias específicas. Além disso, a má configuração dos servidores web pode facilmente revelar informações confidenciais sobre credenciais de acesso.

A verificação de extensões é frequentemente usada para validar arquivos a serem enviados, o que pode levar a resultados inesperados devido ao conteúdo que não é o esperado ou devido à manipulação inesperada de nomes de arquivo pelo sistema operacional.

Determinar como os servidores web lidam com solicitações correspondentes a arquivos com diferentes extensões pode ajudar a entender o comportamento do servidor web dependendo do tipo de arquivos acessados. Por exemplo, pode ajudar a entender quais extensões de arquivo são retornadas como texto ou plano versus aquelas que causam execução no lado do servidor. Estas últimas são indicativas de tecnologias, linguagens ou plugins que são usados por servidores web ou servidores de aplicativos, e podem fornecer informações adicionais sobre como a aplicação web é projetada. Por exemplo, uma extensão ".pl" está geralmente associada ao suporte Perl do lado do servidor. No entanto, a extensão de arquivo sozinha pode ser enganosa e não totalmente conclusiva. Por exemplo, recursos do lado do servidor Perl podem ser renomeados para ocultar o fato de que são relacionados ao Perl. Consulte a próxima seção sobre "componentes do servidor web" para obter mais informações sobre a identificação de tecnologias e componentes do lado do servidor.

## Objetivos do Teste

- Testar extensões de arquivo sensíveis, ou extensões que podem conter dados brutos (*por exemplo*, scripts, dados brutos, credenciais, etc.).
- Validar que não existem contornos do framework do sistema nas regras definidas.

## Como Testar

### Navegação Forçada

Envie solicitações com diferentes extensões de arquivo e verifique como elas são tratadas. A verificação deve ser feita com base em diretórios da web específicos. Verifique diretórios que permitem a execução de scripts. Os diretórios do servidor web podem ser identificados por ferramentas de digitalização que procuram a presença de diretórios conhecidos. Além disso, espelhar a estrutura do site permite ao testador reconstruir a árvore de diretórios da web servidos pela aplicação.

Se a arquitetura da aplicação web for balanceada, é importante avaliar todos os servidores web. Isso pode ser fácil ou não, dependendo da configuração da infraestrutura de balanceamento. Em uma infraestrutura com componentes redundantes, pode haver variações sutis na configuração de servidores web ou de aplicativos individuais. Isso pode acontecer se a arquitetura da web empregar tecnologias heterogêneas (pense em um conjunto de servidores web IIS e Apache em uma configuração de balanceamento de carga, que pode introduzir um comportamento ligeiramente assimétrico entre eles e possivelmente diferentes vulnerabilidades).

#### Exemplo

O testador identificou a existência de um arquivo chamado `connection.inc`. Tentar acessá-lo diretamente retorna seu conteúdo, que é:

```php
<?
    mysql_connect("127.0.0.1", "root", "password")
        or die("Could not connect");
?>
```

O testador determina a existência de um banco de dados MySQL como backend e as credenciais (fracas) usadas pela aplicação web para acessá-lo.

As seguintes extensões de arquivo nunca devem ser retornadas por um servidor web, pois estão relacionadas a arquivos que podem conter informações sensíveis ou a arquivos para os quais não há motivo para serem servidos.

- `.asa`
- `.inc`
- `.config`

As seguintes extensões de arquivo estão relacionadas a arquivos que, quando acessados, são exibidos ou baixados pelo navegador. Portanto, os arquivos com essas extensões devem ser verificados para garantir que realmente devem ser servidos (e não são restos) e que não contêm informações sensíveis.

- `.zip`, `.tar`, `.gz`, `.tgz`, `.rar`, etc.: Arquivos de arquivos (comprimidos)
- `.java`: Sem motivo para fornecer acesso a arquivos de origem Java
- `.txt`: Arquivos de texto
- `.pdf`: Documentos PDF
- `.docx`, `.rtf`, `.xlsx`, `.pptx`, etc.: Documentos do Office
- `.bak`, `.old` e outras extensões indicativas de arquivos de backup (por exemplo: `~` para arquivos de backup do Emacs)

A lista fornecida acima detalha apenas alguns exemplos, já que as extensões de arquivo são muitas para serem tratadas de forma abrangente aqui. Consulte [FILExt](https://filext.com/) para um banco de dados mais abrangente de extensões.

Para identificar arquivos com determinadas extensões, podem ser empregadas várias técnicas. Essas técnicas podem incluir scanners de vulnerabilidades, ferramentas de digitalização e espelhamento, inspeção manual da aplicação (isso supera as limitações na digitalização automática), consultas a mecanismos de busca (consulte [Testando: Digitalização e pesquisa no Google](../01-Information_Gathering/01-Conduct_Search_Engine_Discovery_Reconnaissance_for_Information_Leakage.md)). Consulte também [Testando Arquivos Antigos, de Backup e Não Referenciados](04-Review_Old_Backup_and_Unreferenced_Files_for_Sensitive_Information.md), que trata dos problemas de segurança relacionados a arquivos "esquecidos".

### Upload de Arquivo

O tratamento de arquivos legados do Windows 8.3 às vezes pode ser usado para contornar filtros de upload de arquivos.

Exemplos de uso:

1. `file.phtml` é processado como código PHP.
2. `FILE~1.PHT` é servido, mas não processado pelo manipulador

 PHP ISAPI.
3. `shell.phPWND` pode ser enviado.
4. `SHELL~1.PHP` será expandido e retornado pelo shell do sistema operacional e, em seguida, processado pelo manipulador PHP ISAPI.

### Teste Gray-Box

Realizar testes de caixa branca contra a manipulação de extensões de arquivo significa verificar as configurações dos servidores web ou servidores de aplicativos que fazem parte da arquitetura da aplicação web e verificar como eles são instruídos a servir diferentes extensões de arquivo.

Se a aplicação web depender de uma infraestrutura balanceada e heterogênea, determine se isso pode introduzir um comportamento diferente.

## Ferramentas

Scanners de vulnerabilidades, como Nessus e Nikto, verificam a existência de diretórios web conhecidos. Eles podem permitir que o testador baixe a estrutura do site, o que é útil ao tentar determinar a configuração dos diretórios da web e como as extensões de arquivo individuais são servidas. Outras ferramentas que podem ser usadas para esse fim incluem:

- [wget](https://www.gnu.org/software/wget)
- [curl](https://curl.haxx.se)
- procure por "ferramentas de espelhamento web" no Google.