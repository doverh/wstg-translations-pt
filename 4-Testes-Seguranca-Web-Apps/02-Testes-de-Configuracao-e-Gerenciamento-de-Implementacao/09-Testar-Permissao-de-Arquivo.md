# Testar Permissão de Arquivo

|ID          |
|------------|
|WSTG-CONF-09|

## Resumo

Quando um recurso recebe uma configuração de permissões que proporciona acesso a um grupo mais amplo de atores do que o necessário, isso pode levar à exposição de informações sensíveis ou à modificação desse recurso por partes não intencionadas. Isso é especialmente perigoso quando o recurso está relacionado à configuração, execução ou dados sensíveis do usuário.

Um exemplo claro é um arquivo de execução que é executável por usuários não autorizados. Outro exemplo é quando as informações da conta ou um valor de token para acessar uma API - cada vez mais visto em serviços web modernos ou microsserviços - são armazenados em um arquivo de configuração cujas permissões estão definidas como leitura global a partir da instalação por padrão. Tais dados sensíveis podem ser expostos por atores maliciosos internos ao host ou por um atacante remoto que comprometeu o serviço com outras vulnerabilidades, mas obteve apenas uma permissão de usuário comum.

## Objetivos do Teste

- Revisar e identificar quaisquer permissões de arquivo incorretas.

## Como Testar

No Linux, use o comando `ls` para verificar as permissões do arquivo. Alternativamente, `namei` também pode ser usado para listar recursivamente as permissões do arquivo.

```bash
$ namei -l /CaminhoParaVerificar/
```

Os arquivos e diretórios que exigem teste de permissão de arquivo incluem, mas não estão limitados a:

- Arquivos/diretórios da web
- Arquivos/diretórios de configuração
- Arquivos/diretórios sensíveis (dados criptografados, senha, chave)
- Arquivos/diretórios de logs (logs de segurança, logs de operações, logs de administração)
- Executáveis (scripts, EXE, JAR, class, PHP, ASP)
- Arquivos/diretórios de banco de dados
- Arquivos/diretórios temporários
- Arquivos/diretórios de upload

## Remediação

Defina as permissões dos arquivos e diretórios adequadamente para que usuários não autorizados não possam acessar recursos críticos desnecessariamente.

## Ferramentas

- [Windows AccessEnum](https://technet.microsoft.com/en-us/sysinternals/accessenum)
- [Windows AccessChk](https://technet.microsoft.com/en-us/sysinternals/accesschk)
- [Linux namei](https://linux.die.net/man/1/namei)

## Referências

- [CWE-732: Atribuição de Permissão Incorreta para Recurso Crítico](https://cwe.mitre.org/data/definitions/732.html)