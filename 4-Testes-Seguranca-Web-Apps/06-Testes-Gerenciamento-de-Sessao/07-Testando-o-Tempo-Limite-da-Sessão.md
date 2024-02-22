# Testando o Tempo Limite da Sessão

|ID          |
|------------|
|WSTG-SESS-07|

## Resumo

Nesta fase, os testadores verificam se a aplicação faz logout automaticamente de um usuário quando esse usuário ficou inativo por um determinado período de tempo, garantindo que não seja possível "reutilizar" a mesma sessão e que nenhum dado sensível permaneça armazenado no cache do navegador.

Todas as aplicações devem implementar um tempo limite de inatividade para as sessões. Esse tempo limite define a quantidade de tempo que uma sessão permanecerá ativa no caso de não haver atividade por parte do usuário, encerrando e invalidando a sessão após o período de inatividade definido desde a última solicitação HTTP recebida pela aplicação web para um determinado ID de sessão. O tempo limite mais apropriado deve ser um equilíbrio entre segurança (tempo limite mais curto) e usabilidade (tempo limite mais longo) e depende fortemente do nível de sensibilidade dos dados manipulados pela aplicação. Por exemplo, um tempo de logout de 60 minutos para um fórum público pode ser aceitável, mas um tempo tão longo seria demais para uma aplicação bancária residencial (onde é recomendado um tempo limite máximo de 15 minutos). Em qualquer caso, qualquer aplicação que não imponha um logout com base em tempo limite deve ser considerada não segura, a menos que tal comportamento seja exigido por um requisito funcional específico.

O tempo limite de inatividade limita as chances que um atacante tem de adivinhar e usar um ID de sessão válido de outro usuário e, em certas circunstâncias, pode proteger computadores públicos contra a reutilização de sessões. No entanto, se o atacante conseguir se apropriar de uma sessão específica, o tempo limite de inatividade não limita as ações do atacante, pois ele pode gerar atividade na sessão periodicamente para mantê-la ativa por períodos mais longos.

O gerenciamento e a expiração do tempo limite de sessão devem ser aplicados no lado do servidor. Se alguns dados sob controle do cliente forem usados para impor o tempo limite da sessão, por exemplo, usando valores de cookies ou outros parâmetros do cliente para rastrear referências de tempo (por exemplo, número de minutos desde o login), um atacante poderia manipulá-los para estender a duração da sessão. Portanto, a aplicação deve rastrear o tempo de inatividade no lado do servidor e, após o vencimento do tempo limite, invalidar automaticamente a sessão do usuário atual e excluir todos os dados armazenados no cliente.

Ambas as ações devem ser implementadas cuidadosamente para evitar a introdução de fraquezas que possam ser exploradas por um atacante para obter acesso não autorizado, caso o usuário tenha esquecido de fazer logout da aplicação. Mais especificamente, assim como na função de logout, é importante garantir que todos os tokens de sessão (por exemplo, cookies) sejam destruídos corretamente ou tornados inutilizáveis, e que controles adequados sejam aplicados no lado do servidor para evitar a reutilização de tokens de sessão. Se essas ações não forem realizadas corretamente, um atacante poderia reproduzir esses tokens de sessão para "ressuscitar" a sessão de um usuário legítimo e se passar por ele (esse ataque é geralmente conhecido como 'cookie replay'). Claro, um fator atenuante é que o atacante precisa ser capaz de acessar esses tokens (que estão armazenados no PC da vítima), mas, em vários casos, isso pode não ser impossível ou particularmente difícil.

O cenário mais comum para esse tipo de ataque é um computador público usado para acessar algumas informações privadas (por exemplo, e-mail na web, conta bancária online). Se o usuário se afastar do computador sem fazer logout explicitamente e o tempo limite de sessão não for implementado na aplicação, então um atacante poderia acessar a mesma conta simplesmente pressionando o botão "voltar" do navegador.

## Objetivos do Teste

- Validar que existe um tempo limite rígido para a sessão.

## Como Testar

### Teste de Caixa Preta

A mesma abordagem vista na seção [Testando a funcionalidade de logout](#testing-for-logout-functionality) pode ser aplicada ao medir o tempo limite de logout.
A metodologia de teste é muito semelhante. Primeiro, os testadores devem verificar se existe um tempo limite, por exemplo, fazendo login e aguardando o logout por tempo limite ser acionado. Assim como na função de logout, após o tempo limite passar, todos os tokens de sessão devem ser destruídos ou tornados inutilizáveis.

Em seguida, se o tempo limite estiver configurado, os testadores precisam entender se o tempo limite é imposto pelo cliente ou pelo servidor (ou ambos). Se o cookie de sessão não for persistente (ou, mais geralmente, o cookie de sessão não armazenar nenhum dado sobre o tempo), os testadores podem assumir que o tempo limite é imposto pelo servidor. Se o cookie de sessão contiver algum dado relacionado ao tempo (por exemplo, hora de login ou última hora de acesso ou data de expiração para um cookie persistente), então é possível que o cliente esteja envolvido na imposição do tempo limite. Nesse caso, os testadores podem tentar modificar o cookie (se não estiver criptograficamente protegido) e ver o que acontece com a sessão. Por exemplo, os testadores podem definir a data de expiração do cookie para o futuro e ver se a sessão pode ser prolongada.

Como regra geral, tudo deve ser verificado no lado do servidor e não deve ser possível, redefinindo os cookies de sessão para valores anteriores, acessar a aplicação novamente.

### Teste de Caixa Cinza

O testador precisa verificar se:

- A função de logout destrói efetivamente todos os tokens de sessão ou, pelo menos, os torna inutilizáveis,
- O servidor realiza verificações adequadas no estado da sessão, impedindo que um atacante replique identificadores de sessão previamente destruídos
- Um tempo limite é imposto e é devidamente imposto pelo servidor. Se o servidor usa um tempo de expiração lido de um token de sessão enviado pelo cliente (mas isso não é aconselhável), então o token deve ser protegido criptograficamente contra adulter

ação.

Note que a coisa mais importante é que a aplicação invalide a sessão no lado do servidor. Geralmente, isso significa que o código deve invocar os métodos apropriados, como `HttpSession.invalidate()` em Java e `Session.abandon()` em .NET. Limpar os cookies do navegador é aconselhável, mas não é estritamente necessário, pois se a sessão for devidamente invalidada no servidor, ter o cookie no navegador não ajudará um atacante.

## Referências

### Recursos da OWASP

- [Guia de Referência para Gerenciamento de Sessões](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)