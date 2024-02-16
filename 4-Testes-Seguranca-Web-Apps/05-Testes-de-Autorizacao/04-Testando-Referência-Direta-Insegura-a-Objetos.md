# Testando Referência Direta Insegura a Objetos

|ID          |
|------------|
|WSTG-ATHZ-04|

## Resumo

Referência Direta Insegura a Objetos (IDOR em Inglês Insecure Direct Object Reference) ocorrem quando uma aplicação fornece acesso direto a objetos com base em entrada fornecida pelo usuário. Como resultado dessa vulnerabilidade, os atacantes podem contornar a autorização e acessar recursos no sistema diretamente, como registros de banco de dados ou arquivos. As Referências Diretas Inseguras a Objetos permitem que os atacantes contornem a autorização e acessem recursos diretamente, modificando o valor de um parâmetro usado para apontar diretamente para um objeto. Tais recursos podem ser entradas de banco de dados pertencentes a outros usuários, arquivos no sistema e mais. Isso ocorre devido ao fato de a aplicação aceitar entrada fornecida pelo usuário e usá-la para recuperar um objeto sem realizar verificações de autorização suficientes.

## Objetivos do Teste

- Identificar pontos onde referências a objetos podem ocorrer.
- Avaliar as medidas de controle de acesso e se estão vulneráveis a IDOR.

## Como Testar

Para testar essa vulnerabilidade, o testador primeiro precisa mapear todos os locais na aplicação onde a entrada do usuário é usada para referenciar objetos diretamente. Por exemplo, locais onde a entrada do usuário é usada para acessar uma linha de banco de dados, um arquivo, páginas de aplicativos e mais. Em seguida, o testador deve modificar o valor do parâmetro usado para referenciar objetos e avaliar se é possível recuperar objetos pertencentes a outros usuários ou contornar a autorização.

A melhor maneira de testar referências diretas a objetos seria ter pelo menos dois (muitas vezes mais) usuários para cobrir objetos e funções de propriedade diferentes. Por exemplo, dois usuários cada um com acesso a objetos diferentes (como informações de compra, mensagens privadas, etc.), e (se relevante) usuários com diferentes privilégios (por exemplo, usuários administradores) para ver se existem referências diretas a funcionalidades da aplicação. Ao ter vários usuários, o testador economiza tempo valioso de teste ao tentar adivinhar diferentes nomes de objetos, pois pode tentar acessar objetos que pertencem a outros usuários.

Abaixo estão vários cenários típicos para essa vulnerabilidade e os métodos para testar cada um:

### O Valor de um Parâmetro É Usado Diretamente para Recuperar um Registro de Banco de Dados

Requisição de exemplo:

```text
http://foo.bar/somepage?invoice=12345
```

Neste caso, o valor do parâmetro *invoice* é usado como índice em uma tabela de faturas no banco de dados. A aplicação pega o valor desse parâmetro e o usa em uma consulta ao banco de dados. A aplicação então retorna as informações da fatura para o usuário.

Como o valor de *invoice* vai diretamente para a consulta, ao modificar o valor do parâmetro, é possível recuperar qualquer objeto de fatura, independentemente do usuário a quem a fatura pertence. Para testar esse caso, o testador deve obter o identificador de uma fatura pertencente a um usuário de teste diferente (garantindo que ele não deve visualizar essas informações de acordo com a lógica de negócios da aplicação) e, em seguida, verificar se é possível acessar objetos sem autorização.

### O Valor de um Parâmetro É Usado Diretamente para Realizar uma Operação no Sistema

Requisição de exemplo:

```text
http://foo.bar/changepassword?user=someuser
```

Neste caso, o valor do parâmetro `user` é usado para informar à aplicação para qual usuário ela deve alterar a senha. Em muitos casos, esta etapa será parte de um assistente ou de uma operação de várias etapas. Na primeira etapa, a aplicação receberá uma solicitação indicando para qual usuário a senha deve ser alterada, e na próxima etapa, o usuário fornecerá uma nova senha (sem pedir a senha atual).

O parâmetro `user` é usado para referenciar diretamente o objeto do usuário para o qual a operação de alteração de senha será realizada. Para testar esse caso, o testador deve tentar fornecer um nome de usuário de teste diferente do que está atualmente logado e verificar se é possível modificar a senha de outro usuário.

### O Valor de um Parâmetro É Usado Diretamente para Recuperar um Recurso do Sistema de Arquivos

Requisição de exemplo:

```text
http://foo.bar/showImage?img=img00011
```

Neste caso, o valor do parâmetro `file` é usado para informar à aplicação qual arquivo o usuário pretende recuperar. Ao fornecer o nome ou identificador de um arquivo diferente (por exemplo, file=image00012.jpg), o atacante poderá recuperar objetos pertencentes a outros usuários.

Para testar esse caso, o testador deve obter uma referência à qual o usuário não deveria ter acesso e tentar acessá-la usando-a como valor do parâmetro `file`. Observação: Essa vulnerabilidade muitas vezes é explorada em conjunto com uma vulnerabilidade de travessia de diretório/caminho (veja [Testando Traversão de Diretório](01-Testing_Directory_Traversal_File_Include.md))

### O Valor de um Parâmetro É Usado Diretamente para Acessar Funcionalidades da Aplicação

Requisição de exemplo:

```text
http://foo.bar/accessPage?menuitem=12
```

Neste caso, o valor do parâmetro `menuitem` é usado para informar à aplicação qual item de menu (e, portanto, qual funcionalidade da aplicação) o usuário está tentando acessar. Suponha que o usuário deve ser restrito e, portanto, tem links disponíveis apenas para acessar os itens de menu 1, 2 e 3. Ao modificar o valor do parâmetro `menuitem`, é possível contornar a autorização e acessar funcionalidades adicionais da aplicação. Para testar esse caso, o testador identifica um local onde a funcionalidade da aplicação é determinada pela referência a um item de menu, mapeia os valores dos itens de menu aos quais o usuário de teste pode acessar e, em seguida,

 tenta outros itens de menu.

Nos exemplos acima, a modificação de um único parâmetro é suficiente. No entanto, às vezes, a referência ao objeto pode estar dividida entre mais de um parâmetro, e o teste deve ser ajustado de acordo.

## Referências

[Top 10 2013-A4-Insecure Direct Object References](https://owasp.org/www-project-top-ten/2017/Release_Notes)