# Autenticação

A autenticação `JWT` é uma maneira eficaz de proteger os endpoints de uma API
REST, assegurando que apenas usuários autorizados possam acessá-los. Para isso,
é necessário que o usuário envie um token válido no cabeçalho Authorization de
cada requisição, o qual será verificado pelo servidor.

O token contém informações sobre o usuário e a aplicação, além de uma assinatura
que garante sua integridade. Se o token for inválido ou expirado, o servidor
rejeitará a requisição e retornará um código de erro.

## Utilizando credenciais

Os recursos da api são divididos em 2 categorias `Cliente` e `Representante`
existe recursos que são exclusivos do `Cliente` e outros exclusivos do `Representante`.

### Cliente

Esse tipo de o acesso é feito com os dados do próprio cliente *(estabelecimento)*.

```bash
curl -X POST --location 'https://webapi.relatoriotef.com.br/autenticacao' \
--header 'Content-Type: application/json' \
--data '{
    "usuario": "<USUARIO>",
    "senha": "<SENHA EM BASE64>",
    "portal": "<SUFIXO>"
}'
```

> Nota: A senha deve estar codificada em Base64.

> Nota: A URL do portal deve ser informada no campo *SUFIXO*, identificando o portal que deseja acessar.

### Representante

Esse tipo de o acesso é feito com os dados do próprio representante *(parceiro comercial)*.

```bash
curl -X POST --location 'https://webapi.relatoriotef.com.br/autenticacao' \
--header 'Content-Type: application/json' \
--data '{
    "usuario": "<USUARIO>",
    "senha": "<SENHA EM BASE64>",
    "tipo": 1
}'
```

### Representante: Inspecionar cliente

Existe casos em que o representante deseja acessar dados de seus clientes, para
isso existe uma forma de autenticação que não é necessário informar os dados de
acesso do cliente, onde o representante com seus dados **Inspeciona** o cliente.

#### Inspecionar utilizando credenciais

```bash
curl -X POST --location 'https://webapi.relatoriotef.com.br/autenticacao' \
--header 'Content-Type: application/json' \
--data '{
    "usuario": "<USUARIO REPRESENTANTE>",
    "senha": "<SENHA REPRESENTANTE EM BASE64>",
    "tipo": 3,
    "inspecionar": "<USUARIO CLIENTE>"
}'
```

#### Inspecionar utilizando token de longa duração

```bash
curl -X POST --location 'https://webapi.relatoriotef.com.br/autenticacao/token' \
--header 'Authorization: Bearer ••••••'
--header 'Content-Type: application/json' \
--data '{
    "tipo": 3,
    "inspecionar": "<USUARIO CLIENTE>"
}'
```

## Token de longa duração

Se for necessário conceder acesso à API a terceiros, é possível utilizar um token
de acesso com duração prolongada, evitando assim a necessidade de compartilhar
seu usuário e senha. Com essa opção, você também terá controle para bloquear o
token quando necessário.

### Gerar token

Segue um exemplo de como solicitar um token de acesso de longa duração,
lembrando que deve-se informar a validade de no máximo 3 anos, utilize o campo
'descrição' para um melhor controle dos token gerados.

```bash
curl -X POST --location 'https://webapi.relatoriotef.com.br/usuario/token' \
--header 'Authorization: Bearer ••••••'
--header 'Content-Type: application/json' \
--data '{
    "descricao": "DESCRIÇÃO",
    "dataExpiracao": "2025-12-31T00:00:00"
}'
```

> O retorno da requisição conterá o token. Lembre-se de anotá-lo, pois não
> armazenamos essa informação. Caso o perca, será necessário gerar um novo token.

### Listar token

```bash
curl --location 'https://webapi.relatoriotef.com.br/usuario/token' \
--header 'Authorization: Bearer ••••••'
```

### Revogar/Excluir token

```bash
curl --location --request DELETE 'https://webapi.relatoriotef.com.br/usuario/token' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer ••••••' \
--data '{"id": <ID>}'
```
