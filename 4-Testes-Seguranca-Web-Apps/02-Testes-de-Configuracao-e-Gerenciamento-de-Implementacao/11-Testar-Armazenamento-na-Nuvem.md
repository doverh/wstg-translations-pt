# Testar Armazenamento na Nuvem

|ID          |
|------------|
|WSTG-CONF-11|

## Resumo

Os serviços de armazenamento na nuvem facilitam que aplicativos e serviços da web armazenem e acessem objetos. No entanto, a configuração inadequada de controle de acesso pode resultar na exposição de informações sensíveis, manipulação de dados ou acesso não autorizado.

Um exemplo conhecido é quando um bucket do Amazon S3 está mal configurado, embora outros serviços de armazenamento na nuvem também possam estar expostos a riscos semelhantes. Por padrão, todos os buckets do S3 são privados e só podem ser acessados por usuários que receberam acesso explícito. Os usuários podem conceder acesso público tanto ao próprio bucket quanto a objetos individuais armazenados nesse bucket. Isso pode permitir que um usuário não autorizado faça upload de novos arquivos, modifique ou leia arquivos armazenados.

## Objetivos do Teste

- Avaliar se a configuração de controle de acesso para os serviços de armazenamento está correta.

## Como Testar

Primeiro, identifique a URL para acessar os dados no serviço de armazenamento e, em seguida, considere os seguintes testes:

- ler os dados não autorizados
- fazer upload de um novo arquivo arbitrário

Você pode usar o curl para os testes com os seguintes comandos e verificar se ações não autorizadas podem ser realizadas com sucesso.

Para testar a capacidade de ler um objeto:

```bash
curl -X GET https://<servico-de-armazenamento-na-nuvem>/<objeto>
```

Para testar a capacidade de fazer upload de um arquivo:

```bash
curl -X PUT -d 'teste' 'https://<servico-de-armazenamento-na-nuvem>/teste.txt'
```

### Testando a Má Configuração do Bucket do Amazon S3

As URLs do bucket do Amazon S3 seguem um dos dois formatos, estilo de host virtual ou estilo de caminho.

- Acesso de Estilo de Host Virtual

```text
https://bucket-name.s3.Region.amazonaws.com/key-name
```

No exemplo a seguir, `my-bucket` é o nome do bucket, `us-west-2` é a região e `puppy.png` é o nome da chave:

```text
https://my-bucket.s3.us-west-2.amazonaws.com/puppy.png
```

- Acesso de Estilo de Caminho

```text
https://s3.Region.amazonaws.com/bucket-name/key-name
```

Como acima, no exemplo a seguir, `my-bucket` é o nome do bucket, `us-west-2` é a região e `puppy.png` é o nome da chave:

```text
https://s3.us-west-2.amazonaws.com/my-bucket/puppy.jpg
```

Para algumas regiões, pode ser usado o endpoint global legado que não especifica um endpoint específico da região. Seu formato também é estilo de host virtual ou estilo de caminho.

- Acesso de Estilo de Host Virtual

```text
https://bucket-name.s3.amazonaws.com
```

- Acesso de Estilo de Caminho

```text
https://s3.amazonaws.com/bucket-name
```

#### Identificar a URL do Bucket

Para testes de caixa-preta, as URLs do S3 podem ser encontradas nas mensagens HTTP. O exemplo a seguir mostra uma URL de bucket enviada na tag `img` em uma resposta HTTP.

```html
...
<img src="https://my-bucket.s3.us-west-2.amazonaws.com/puppy.png">
...
```

Para testes de caixa cinza, você pode obter URLs de buckets a partir da interface web da Amazon, documentos, código-fonte ou qualquer outra fonte disponível.

#### Testando com AWS-CLI

Além dos testes com curl, você também pode testar com a ferramenta de linha de comando AWS. Nesse caso, é usado o protocolo `s3://`.

##### Lista

O seguinte comando lista todos os objetos do bucket quando configurado como público.

```bash
aws s3 ls s3://<nome-do-bucket>
```

##### Upload

O seguinte é o comando para fazer upload de um arquivo

```bash
aws s3 cp arquivo-arbitrario s3://nome-do-bucket/caminho-para-salvar
```

Este exemplo mostra o resultado quando o upload foi bem-sucedido.

```bash
$ aws s3 cp teste.txt s3://nome-do-bucket/teste.txt
upload: ./teste.txt to s3://nome-do-bucket/teste.txt
```

Este exemplo mostra o resultado quando o upload falhou.

```bash
$ aws s3 cp teste.txt s3://nome-do-bucket/teste.txt
upload failed: ./teste2.txt to s3://nome-do-bucket/teste2.txt An error occurred (AccessDenied) when calling the PutObject operation: Access Denied
```

##### Remover

O seguinte é o comando para remover um objeto

```bash
aws s3 rm s3://nome-do-bucket/

objeto-para-remover
```

## Ferramentas

- [AWS CLI](https://aws.amazon.com/cli/)

## Referências

- [Trabalhando com Buckets do Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingBucket.html)
- [flAWS 2](http://flaws2.cloud)