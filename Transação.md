# Transação

## Autenticação

Para acessar os recursos descritos abaixo, é necessário estar autenticado como
`cliente` ou `Representante`, para maiores informações acesso o menu
[Autenticação](/Autenticação.md).

## Consultar transações

Parametros da requisição:

- Cnpj `05625872000169` (Obrigatório)
- DataInicial `2024-01-01T00:00:00` (Obrigatório)
- DataFinal `2024-01-01T23:59:59` (Range máximo entre as datas de 30 dias) (Obrigatório)
- PaginaNumero `1` (Opcional)
- QtdRegistro `100` (Máximo 100 registro por pagina) (Opcional)
- PDV **Pode enviar até 5 registro** *Ex: ?cnpj=05625872000169&PDV=1&PDV=2*  (Opcional)
- Adquirente **Pode enviar até 5 registro** *Ex: ?cnpj=05625872000169&Adquirente=1&Adquirente=2*  (Opcional)
- Bandeira **Pode enviar até 5 registro** *Ex: ?cnpj=05625872000169&Bandeira=1&Bandeira=2*  (Opcional)
- GrupoServico **Pode enviar até 5 registro** *Ex: ?cnpj=05625872000169&GrupoServico=1&GrupoServico=2*  (Opcional)
- Servico **Pode enviar até 5 registro** *Ex: ?cnpj=05625872000169&Servico=1&Servico=2*  (Opcional)
- Status **Pode enviar até 5 registro** *Ex: ?cnpj=05625872000169&Status=1&Status=2*  (Opcional)
- Autorizacao **Pode enviar até 5 registro** *Ex: ?cnpj=05625872000169&Autorizacao=1&Autorizacao=2*  (Opcional)
- NSU **Pode enviar até 5 registro** *Ex: ?cnpj=05625872000169&NSU=1&NSU=2*  (Opcional)
- RetornaSplit=True - Retorna os dados de divisão, se disponíveis  (Opcional)
- RetornaComprovante=True - Retorna comprovante em um modelo padrão  (Opcional)

```bash
curl --location 'https://webapi.relatoriotef.com.br/transacao?cnpj=05625872000169&DataInicial=2024-01-01T00:00:00&DataFinal=2024-01-01T23:59:59' \
--header 'Authorization: Bearer ••••••'
```

## Consultar transações POS

> [Segue os mesmo parametros de transações](#consultar-transações)

```bash
curl --location 'https://webapi.relatoriotef.com.br/transacao/pos?cnpj=05625872000169&DataInicial=2024-01-01T00:00:00&DataFinal=2024-01-01T23:59:59' \
--header 'Authorization: Bearer ••••••'
```

## Consultar detalhes de uma transação específica

Informar o código da transação na requisição

```bash
curl --location 'https://webapi.relatoriotef.com.br/transacao/{id}' \
--header 'Authorization: Bearer ••••••'
```

## Consultar comprovante de uma transação específica

Informar o código da transação na requisição

```bash
curl --location 'https://webapi.relatoriotef.com.br/transacao/{id}/comprovante' \
--header 'Authorization: Bearer ••••••'
```
