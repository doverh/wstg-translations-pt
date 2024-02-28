# Testando o Sequestro da Sessão

|ID          |
|------------|
|WSTG-SESS-09|

## Resumo

Um atacante que obtém acesso aos cookies de sessão do usuário pode se passar por eles apresentando esses cookies. Esse ataque é conhecido como sequestro de sessão. Ao considerar atacantes de rede, ou seja, atacantes que controlam a rede usada pela vítima, os cookies de sessão podem ser indevidamente expostos ao atacante via HTTP. Para evitar isso, os cookies de sessão devem ser marcados com o atributo `Secure` para que sejam comunicados apenas via HTTPS.

Observação: O atributo `Secure` também deve ser usado quando a aplicação web é totalmente implantada via HTTPS, caso contrário, o ataque de roubo de cookies descrito a seguir é possível. Suponha que `example.com` seja totalmente implantado via HTTPS, mas não marca seus cookies de sessão como `Secure`. Os seguintes passos de ataque são possíveis:

1. A vítima envia uma solicitação para `http://another-site.com`.
2. O atacante corrompe a resposta correspondente para que ela desencadeie uma solicitação para `http://example.com`.
3. O navegador agora tenta acessar `http://example.com`.
4. Embora a solicitação falhe, os cookies de sessão vazam em texto claro via HTTP.

Alternativamente, o sequestro de sessão pode ser prevenido proibindo o uso de HTTP usando [HSTS](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security). Observe que há uma sutileza aqui relacionada ao escopo de cookies. Em particular, a adoção total do HSTS é necessária quando os cookies de sessão são emitidos com o atributo `Domain` definido.

A adoção total do HSTS é descrita em um artigo chamado *Testing for Integrity Flaws in Web Sessions* por Stefano Calzavara, Alvise Rabitti, Alessio Ragazzo e Michele Bugliesi. A adoção total do HSTS ocorre quando um host ativa o HSTS para si mesmo e para todos os seus subdomínios. A adoção parcial do HSTS ocorre quando um host ativa o HSTS apenas para si mesmo.

Com o atributo `Domain` definido, os cookies de sessão podem ser compartilhados entre subdomínios. O uso de HTTP com subdomínios deve ser evitado para evitar a divulgação de cookies não criptografados enviados via HTTP. Para exemplificar essa falha de segurança, suponha que o site `example.com` ativa o HSTS sem a opção `includeSubDomains`. O site emite cookies de sessão com o atributo `Domain` definido como `example.com`. O seguinte ataque é possível:

1. A vítima envia uma solicitação para `http://another-site.com`.
2. O atacante corrompe a resposta correspondente para que ela desencadeie uma solicitação para `http://fake.example.com`.
3. O navegador agora tenta acessar `http://fake.example.com`, o que é permitido pela configuração HSTS.
4. Como a solicitação é enviada a um subdomínio de `example.com` com o atributo `Domain` definido, ela inclui os cookies de sessão, que vazam em texto claro via HTTP.

O HSTS completo deve ser ativado no domínio principal para evitar esse ataque.

## Objetivos do Teste

- Identificar cookies de sessão vulneráveis.
- Sequestrar cookies vulneráveis e avaliar o nível de risco.

## Como Testar

A estratégia de teste é direcionada a atacantes de rede, portanto, ela só precisa ser aplicada a sites sem adoção completa do HSTS (sites com adoção completa do HSTS são seguros, pois seus cookies não são comunicados via HTTP). Supomos ter duas contas de teste no site em teste, uma para agir como vítima e outra para agir como atacante. Simulamos um cenário em que o atacante rouba todos os cookies que não estão protegidos contra divulgação via HTTP e os apresenta ao site para acessar a conta da vítima. Se esses cookies forem suficientes para agir em nome da vítima, o sequestro de sessão é possível.

Aqui estão as etapas para executar este teste:

1. Faça login no site como a vítima e alcance qualquer página que ofereça uma função segura que exija autenticação.
2. Exclua do repositório de cookies todos os cookies que atendem a qualquer uma das seguintes condições.
    - no caso de não haver adoção do HSTS: o atributo `Secure` está definido.
    - no caso de haver adoção parcial do HSTS: o atributo `Secure` está definido ou o atributo `Domain` não está definido.
3. Salve uma captura do repositório de cookies.
4. Acione a função segura identificada na etapa 1.
5. Observe se a operação na etapa 4 foi realizada com sucesso. Se sim, o ataque foi bem-sucedido.
6. Limpe o repositório de cookies, faça login como atacante e alcance a página na etapa 1.
7. Escreva no repositório de cookies, um por um, os cookies salvos na etapa 3.
8. Acione novamente a função segura identificada na etapa 1.
9. Limpe o repositório de cookies e faça login novamente como a vítima.
10. Observe se a operação na etapa 8 foi realizada com sucesso na conta da vítima. Se sim, o ataque foi bem-sucedido; caso contrário, o site está seguro contra o sequestro de sessão.

Recomendamos o uso de duas máquinas ou navegadores diferentes para a vítima e o atacante. Isso permite reduzir o número de falsos positivos se a aplicação web realizar fingerprinting para verificar o acesso habilitado a partir de um determinado cookie. Uma variante mais curta, mas menos precisa, da estratégia de teste requer apenas uma conta de teste. Segue o mesmo padrão, mas interrompe na etapa 5 (observe que isso torna a etapa 3 inútil).

## Ferramentas

- [OWASP ZAP](https://www.zaproxy.org)
- [JHijack - uma ferramenta numérica de sequestro de sessão](https://sourceforge.net/projects/jhijack/)