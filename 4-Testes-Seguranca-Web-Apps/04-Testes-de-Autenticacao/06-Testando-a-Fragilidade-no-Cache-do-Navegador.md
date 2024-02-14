# Testando a Fragilidade no Cache do Navegador

|ID          |
|------------|
|WSTG-ATHN-06|

## Resumo

Nesta fase, o testador verifica se a aplicação instrui corretamente o navegador a não reter dados sensíveis.

Os navegadores podem armazenar informações para fins de cache e histórico. O cache é usado para melhorar o desempenho, para que as informações anteriormente exibidas não precisem ser baixadas novamente. Os mecanismos de histórico são usados para a conveniência do usuário, para que o usuário possa ver exatamente o que viu no momento em que o recurso foi recuperado. Se informações sensíveis forem exibidas ao usuário (como seu endereço, detalhes do cartão de crédito, número de Seguro Social ou nome de usuário), essas informações podem ser armazenadas para fins de cache ou histórico e, portanto, podem ser recuperadas examinando o cache do navegador ou simplesmente pressionando o botão **Voltar** do navegador.

## Objetivos do Teste

- Verificar se a aplicação armazena informações sensíveis no lado do cliente.
- Verificar se o acesso pode ocorrer sem autorização.

## Como Testar

### Histórico do Navegador

Tecnicamente, o botão **Voltar** é um histórico e não um cache (consulte [Caching in HTTP: History Lists](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html#sec13.13)). O cache e o histórico são duas entidades diferentes. No entanto, eles compartilham a mesma fraqueza de apresentar informações sensíveis anteriormente exibidas.

O primeiro e mais simples teste consiste em inserir informações sensíveis na aplicação e fazer logout. Em seguida, o testador clica no botão **Voltar** do navegador para verificar se as informações sensíveis anteriormente exibidas podem ser acessadas sem autenticação.

Se, ao pressionar o botão **Voltar**, o testador puder acessar páginas anteriores, mas não acessar novas, não é um problema de autenticação, mas sim um problema de histórico do navegador. Se essas páginas contiverem dados sensíveis, significa que a aplicação não proibiu o navegador de armazená-los.

A autenticação não precisa necessariamente estar envolvida nos testes. Por exemplo, quando um usuário insere seu endereço de e-mail para se inscrever em um boletim informativo, essas informações podem ser recuperáveis se não forem manipuladas corretamente.

O botão **Voltar** pode ser impedido de mostrar dados sensíveis. Isso pode ser feito por:

- Entregar a página via HTTPS.
- Definir `Cache-Control: must-revalidate`

### Cache do Navegador

Aqui, os testadores verificam se a aplicação não vaza nenhum dado sensível para o cache do navegador. Para fazer isso, eles podem usar um proxy (como o OWASP ZAP) e procurar nas respostas do servidor que pertencem à sessão, verificando se, para cada página que contém informações sensíveis, o servidor instruiu o navegador a não armazenar em cache nenhum dado. Essa diretiva pode ser emitida nos cabeçalhos de resposta HTTP com as seguintes diretivas:

- `Cache-Control: no-cache, no-store`
- `Expires: 0`
- `Pragma: no-cache`

Essas diretivas são geralmente robustas, embora flags adicionais possam ser necessárias para o cabeçalho `Cache-Control` a fim de evitar melhor a persistência de arquivos vinculados no sistema de arquivos. Estas incluem:

- `Cache-Control: must-revalidate, max-age=0, s-maxage=0`

```http
HTTP/1.1:
Cache-Control: no-cache
```

```html
HTTP/1.0:
Pragma: no-cache
Expires: "data passada ou valor ilegal (por exemplo, 0)"
```

Por exemplo, se os testadores estiverem testando uma aplicação de comércio eletrônico, eles devem procurar todas as páginas que contenham um número de cartão de crédito ou alguma outra informação financeira e verificar se todas essas páginas estão aplicando a diretiva `no-cache`. Se encontrarem páginas que contenham informações críticas, mas que falham em instruir o navegador a não armazenar em cache o conteúdo delas, sabem que informações sensíveis serão armazenadas no disco e podem verificar isso simplesmente procurando a página no cache do navegador.

O local exato onde essas informações são armazenadas depende do sistema operacional do cliente e do navegador que foi usado. Aqui estão alguns exemplos:

- Mozilla Firefox:
  - Unix/Linux: `~/.cache/mozilla/firefox/`
  - Windows: `C:\Users\<nome_do_usuário>\AppData\Local\Mozilla\Firefox\Profiles\<id_do_perfil>\Cache2\`
- Internet Explorer:
  - `C:\Users\<nome_do_usuário>\AppData\Local\Microsoft\Windows\INetCache\`
- Chrome:
  - Windows: `C:\Users\<nome_do_usuário>\AppData\Local\Google\Chrome\User Data\Default\Cache`
  - Unix/Linux: `~/.cache/google-chrome`

#### Revisando Informações em Cache

O Firefox fornece funcionalidades para visualizar informações em cache, o que pode ser benéfico para você como testador. Claro que a indústria também produziu várias extensões e aplicativos externos que você pode preferir ou precisar para Chrome, Internet Explorer ou Edge.

Detalhes do cache também estão disponíveis por meio das ferramentas de desenvolvedor na maioria dos navegadores modernos, como [Firefox](https://developer.mozilla.org/en-US/docs/Tools/Storage_Inspector#Cache_Storage), [Chrome](https://developers.google.com/web/tools/chrome-devtools/storage/cache) e Edge. Com o Firefox, também é possível usar a URL `about:cache` para verificar os detalhes do cache.

#### Verificar o Tratamento para Navegadores Móveis

O tratamento das diretivas de cache pode ser completamente diferente para navegadores móveis. Portanto, os testadores devem iniciar uma nova sessão de navegação com caches limpos e aproveitar recursos como o [Modo de Dispositivo](https://developers.google.com/web/tools/chrome-devtools/device-mode) do Chrome ou o [Modo de Design Responsivo](https://developer.mozilla.org/en-US/docs/Tools/Responsive_Design_Mode) do Firefox para retestar ou testar separadamente os conceitos delineados acima.

Além disso, proxies pessoais como ZAP e Burp Suite permitem que o testador especifique qual `User-Agent` deve

 ser enviado por seus spiders/crawlers. Isso pode ser configurado para corresponder a uma string `User-Agent` de navegador móvel e usado para ver quais diretivas de cache são enviadas pela aplicação em teste.

### Teste Gray-Box

A metodologia de teste é equivalente ao caso de caixa preta, pois em ambos os cenários os testadores têm acesso total aos cabeçalhos de resposta do servidor e ao código HTML. No entanto, com o teste gray-box, o testador pode ter acesso a credenciais de conta que permitirão testar páginas sensíveis acessíveis apenas a usuários autenticados.

## Ferramentas

- [OWASP Zed Attack Proxy](https://www.zaproxy.org)

## Referências

### Whitepapers

- [Caching in HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html)