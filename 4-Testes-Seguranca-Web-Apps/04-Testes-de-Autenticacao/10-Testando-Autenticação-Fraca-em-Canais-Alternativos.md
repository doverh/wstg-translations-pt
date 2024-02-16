# Testando Autenticação Fraca em Canais Alternativos

|ID          |
|------------|
|WSTG-ATHN-10|

## Resumo

Mesmo que os mecanismos de autenticação primários não apresentem vulnerabilidades, pode ser que existam vulnerabilidades em canais alternativos legítimos de autenticação de usuários para as mesmas contas de usuário. Testes devem ser realizados para identificar canais alternativos e, sujeitos ao escopo do teste, identificar vulnerabilidades.

Os canais alternativos de interação do usuário podem ser utilizados para contornar o canal primário ou expor informações que podem ser usadas para auxiliar um ataque contra o canal primário. Alguns desses canais podem ser aplicativos web separados usando hostnames ou caminhos diferentes. Por exemplo:

- Site padrão
- Site otimizado para dispositivos móveis ou dispositivo específico
- Site otimizado para acessibilidade
- Sites alternativos de diferentes países e idiomas
- Sites paralelos que utilizam as mesmas contas de usuário (por exemplo, outro site oferecendo funcionalidades diferentes da mesma organização, um site de parceiro com o qual as contas de usuário são compartilhadas)
- Versões de desenvolvimento, teste, UAT (User Acceptance Testing) e staging do site padrão

Mas também podem ser outros tipos de aplicação ou processos de negócios:

- Aplicativo para dispositivos móveis
- Aplicação para desktop
- Operadores de centro de atendimento
- Sistemas interativos de resposta de voz ou árvore telefônica

Observe que o foco deste teste é nos canais alternativos; algumas alternativas de autenticação podem aparecer como conteúdo diferente entregue pelo mesmo site e quase certamente estariam no escopo do teste. Essas não são discutidas mais a fundo aqui e devem ter sido identificadas durante a coleta de informações e os testes de autenticação primária. Por exemplo:

- Enriquecimento progressivo e degradação suave que alteram a funcionalidade
- Uso do site sem cookies
- Uso do site sem JavaScript
- Uso do site sem plugins, como para Flash e Java

Mesmo que o escopo do teste não permita testar os canais alternativos, a existência deles deve ser documentada. Isso pode minar o grau de segurança nos mecanismos de autenticação e pode ser um precursor para testes adicionais.

## Exemplo

O site primário é `http://www.exemplo.com` e as funções de autenticação sempre ocorrem em páginas usando TLS `https://www.exemplo.com/minhaconta/`.

No entanto, existe um site separado otimizado para dispositivos móveis que não usa TLS, e possui um mecanismo mais fraco de recuperação de senha `http://m.exemplo.com/minhaconta/`.

## Objetivos do Teste

- Identificar canais alternativos de autenticação.
- Avaliar as medidas de segurança utilizadas e se existem contornos nos canais alternativos.

## Como Testar

### Compreender o Mecanismo Primário

Teste completamente as funções de autenticação primárias do site. Isso deve identificar como as contas são emitidas, criadas ou alteradas e como as senhas são recuperadas, redefinidas ou alteradas. Além disso, o conhecimento de qualquer autenticação de privilégios elevados e medidas de proteção de autenticação deve ser conhecido. Esses precursores são necessários para poder comparar com quaisquer canais alternativos.

### Identificar Outros Canais

Outros canais podem ser encontrados usando os seguintes métodos:

- Lendo o conteúdo do site, especialmente a página inicial, páginas de contato, páginas de ajuda, artigos de suporte e FAQs, Termos e Condições, avisos de privacidade, o arquivo robots.txt e quaisquer arquivos sitemap.xml.
- Pesquisando logs de proxy HTTP, registrados durante a coleta de informações e testes anteriores, por strings como "mobile", "android", blackberry", "ipad", "iphone", "aplicativo móvel", "e-reader", "wireless", "auth", "sso", "single sign on" em caminhos de URL e conteúdo do corpo.
- Usar mecanismos de busca para encontrar diferentes sites da mesma organização, ou usando o mesmo nome de domínio, que tenham conteúdo de página inicial semelhante ou que também tenham mecanismos de autenticação.

Para cada canal possível, confirme se as contas de usuário são compartilhadas entre eles ou fornecem acesso à mesma ou a funcionalidades semelhantes.

### Enumerar Funcionalidades de Autenticação

Para cada canal alternativo onde contas de usuário ou funcionalidades são compartilhadas, identifique se todas as funções de autenticação do canal primário estão disponíveis e se algo extra existe. Pode ser útil criar uma grade como a mostrada abaixo:

  | Primário | Móvel   |  Central de Atendimento | Site do Parceiro |
  |---------|---------|--------------|-----------------|
  | Registrar| Sim     |     -        |       -         |
  | Logar   | Sim     |    Sim       |    Sim(SSO)     |
  | Deslogar |   -     |     -        |       -         |
  |Redefinir senha |   Sim  |   Sim   |       -         |
  | -       | Mudar senha |   -  |       -         |

Neste exemplo, o site móvel tem uma função extra "mudar senha", mas não oferece "deslogar". Um número limitado de tarefas também é possível ligando para a central de atendimento. As centrais de atendimento podem ser interessantes, pois suas verificações de confirmação de identidade podem ser mais fracas do que as do site, permitindo que esse canal seja usado para ajudar em um ataque contra a conta de um usuário.

Ao enumerar essas funcionalidades, vale a pena observar como o gerenciamento de sessão é realizado, caso haja sobreposição em qualquer canal (por exemplo, cookies com escopo para o mesmo nome de domínio pai, sessões concorrentes permitidas em canais diferentes, mas não no mesmo canal).

### Revisar e Testar

Os canais alternativos devem ser mencionados no relatório de teste, mesmo que sejam marcados como "apenas informações" ou "fora de escopo". Em alguns casos, o escopo do teste pode incluir o canal alternativo (por exemplo, porque é apenas outro caminho no mesmo nome de host), ou pode ser adicionado ao escopo após

 discussão com os proprietários de todos os canais. Se os testes forem permitidos e autorizados, todos os outros testes de autenticação deste guia devem ser realizados e comparados com o canal primário.

## Casos de Teste Relacionados

Os casos de teste para todos os outros testes de autenticação devem ser utilizados.

## Remediação

Garanta que uma política de autenticação consistente seja aplicada em todos os canais para que sejam igualmente seguros.