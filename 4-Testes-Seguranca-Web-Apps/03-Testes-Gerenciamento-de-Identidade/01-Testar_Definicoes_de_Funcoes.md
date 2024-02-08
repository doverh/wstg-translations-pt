# Testar Definições de Funções

|ID          |
|------------|
|WSTG-IDNT-01|

## Resumo

Aplicações têm vários tipos de funcionalidades e serviços, e esses requerem permissões de acesso com base nas necessidades do usuário. Esse usuário pode ser:

- um administrador, onde gerenciam as funcionalidades da aplicação.
- um auditor, onde revisam as transações da aplicação e fornecem um relatório detalhado.
- um engenheiro de suporte, onde ajudam os clientes a depurar e corrigir problemas em suas contas.
- um cliente, onde interagem com a aplicação e se beneficiam de seus serviços.

Para lidar com esses casos de uso e qualquer outro caso de uso para essa aplicação, são configuradas definições de funções (mais comumente conhecidas como [RBAC](https://en.wikipedia.org/wiki/Role-based_access_control)). Com base nessas funções, o usuário é capaz de realizar a tarefa necessária.

## Objetivos do Teste

- Identificar e documentar as funções usadas pela aplicação.
- Tentar trocar, alterar ou acessar outra função.
- Rever a granularidade das funções e as necessidades por trás das permissões concedidas.

## Como Testar

### Identificação de Funções

O testador deve começar identificando as funções da aplicação a serem testadas por meio de qualquer um dos seguintes métodos:

- Documentação da aplicação.
- Orientação dos desenvolvedores ou administradores da aplicação.
- Comentários da aplicação.
- Testar funções possíveis:
  - variável de cookie (*por exemplo, `role=admin`, `isAdmin=True`)
  - variável de conta (*por exemplo, `Role: manager`)
  - diretórios ou arquivos ocultos (*por exemplo, `/admin`, `/mod`, `/backups`)
  - trocar para usuários conhecidos (*por exemplo, `admin`, `backups`, etc.)

### Trocar para Funções Disponíveis

Após identificar possíveis vetores de ataque, o testador precisa testar e validar que pode acessar as funções disponíveis.

> Algumas aplicações definem as funções do usuário na criação, por meio de verificações e políticas rigorosas ou garantindo que a função do usuário seja devidamente protegida por uma assinatura criada no backend. Descobrir que as funções existem não significa que são uma vulnerabilidade.

### Revisar Permissões das Funções

Após obter acesso às funções no sistema, o testador deve entender as permissões fornecidas a cada função.

Um engenheiro de suporte não deve ser capaz de realizar funcionalidades administrativas, gerenciar backups ou realizar transações no lugar de um usuário.

Um administrador não deve ter poderes totais no sistema. Funcionalidades administrativas sensíveis devem utilizar um princípio de fabricante-verificador ou usar MFA para garantir que o administrador está realizando a transação. Um exemplo claro disso foi o [incidente no Twitter em 2020](https://blog.twitter.com/en_us/topics/company/2020/an-update-on-our-security-incident.html).

## Ferramentas

Os testes mencionados acima podem ser conduzidos sem o uso de qualquer ferramenta, exceto aquela usada para acessar o sistema.

Para facilitar as coisas e documentar mais, pode-se usar:

- [Extensão Autorize do Burp](https://github.com/Quitten/Autorize)
- [Complemento de Teste de Controle de Acesso do ZAP](https://www.zaproxy.org/docs/desktop/addons/access-control-testing/)

## Referências

- [Engenharia de Funções para Gerenciamento de Segurança Empresarial, E Coyne & J Davis, 2007](https://www.bookdepository.co.uk/Role-Engineering-for-Enterprise-Security-Management-Edward-Coyne/9781596932180)
- [Engenharia de funções e padrões RBAC](https://csrc.nist.gov/projects/role-based-access-control#rbac-standard)