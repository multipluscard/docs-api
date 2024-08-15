# Pré-Conciliação/Conciliação

A Pré-conciliação é uma etapa anterior a Conciliação, que consiste em fazer o
provisionamento das vendas registradas com base em condições previamente cadastradas.

A Conciliação é um processo de gestão financeira que consiste em comparar as
vendas realizadas da empresa com os movimentos da conta bancária e do provedor
de pagamento (operadoras).

## Autenticação

Para acessar os recursos descritos abaixo, é necessário estar autenticado como
`cliente`, para maiores informações acesso o menu [Autenticação](/Autenticação.md).

## Resumo - Pré-Conciliação/Conciliação

Parametros da requisição:

- Cnpj `05625872000169`
- DataInicial `2024-01-01T00:00:00`
- DataFinal `2024-01-01T23:59:59` (Range máximo entre as datas de 90 dias)
- DataTipo
  - 0 : Pré-Conciliação - Data Venda
  - 1 : Pré-Conciliação - Data Previsão
  - 2 : Conciliação - Data do Agendamento
  - 3 : Conciliação - Data do Pagamento

```bash
curl -X GET --location 'https://webapi.relatoriotef.com.br/conciliacao/total?Cnpj=05625872000169&DataInicial=2024-01-01T00:00:00&DataFinal=2024-01-01T23:59:59&DataTipo=3' \
--header 'Authorization: Bearer ••••••'
```

Exemplo retorno:

```json
{
    "vendaQtde": 1913,              <- Quantidade de venda única

    -- Pré-Conciliação
    "preTotalBruto": 0.00,          <- Total bruto
    "preTotalLiquido": 0.00,        <- Total líquido
    "preTotalDeposito": 0.00,       <- Total aprox. depósito
    "preTotalDespesa": 0.00,        <- Total despesa

    -- Conciliação
    "concTotalBruto": 0.00,         <- Total bruto
    "concTotalLiquido": 0.00,       <- Total líquido
    "concTotalDeposito": 0.00,      <- Total aprox. depósito
    "concTotalConciliacao": 0.00,   <- Total depósito bancário
    "concTotalDiferenca": 0.00,     <- Diferença
    "concTotalPgtoAvulso": 0.00,    <- Total pgto. avulso
    "concTotalDespesa": 0.00        <- Total despesa
}
```

## Vendas - Pré-Conciliação/Conciliação

Parametros da requisição:

- Cnpj `05625872000169`
- DataInicial `2024-01-01T00:00:00`
- DataFinal `2024-01-01T23:59:59` (Range máximo entre as datas de 90 dias)
- DataTipo
  - 0 : Pré-Conciliação - Data Venda
  - 1 : Pré-Conciliação - Data Previsão
  - 2 : Conciliação - Data do Agendamento
  - 3 : Conciliação - Data do Pagamento
- QtdRegistro
- PaginaNumero
- OrdenarPor
  - Codigo
  - DataVenda
  - AdquirenteNome
  - BandeiraNome
  - ServicoNome
  - Parcela
  - PreTaxaValor
  - PreValorBruto
  - PreValorLiquido
  - ConcTaxaValor
  - ConcValorBruto
  - ConcValorLiquido
- OrdenarTipo (ASC ou DESC)

```bash
curl -X GET --location 'https://webapi.relatoriotef.com.br/conciliacao/venda?Cnpj=05625872000169&DataInicial=2024-01-01T00:00:00&DataFinal=2024-01-01T23:59:59&DataTipo=3&QtdRegistro=10&PaginaNumero=1' \
--header 'Authorization: Bearer ••••••'
```

## Despesas - Pré-Conciliação/Conciliação

Parametros da requisição:

- Cnpj `05625872000169`
- DataInicial `2024-01-01T00:00:00`
- DataFinal `2024-01-01T23:59:59` (Range máximo entre as datas de 90 dias)
- QtdRegistro
- PaginaNumero

```bash
curl -X GET --location 'https://webapi.relatoriotef.com.br/conciliacao/despesa?Cnpj=05625872000169&DataInicial=2024-01-01T00:00:00&DataFinal=2024-01-01T23:59:59' \
--header 'Authorization: Bearer ••••••'
```

## Resumo Vendas - Pré-Conciliação/Conciliação

Parametros da requisição:

- Cnpj `05625872000169`
- DataInicial `2024-01-01T00:00:00`
- DataFinal `2024-01-01T23:59:59` (Range máximo entre as datas de 90 dias)
- DataTipo
  - 0 : Pré-Conciliação - Data Venda
  - 1 : Pré-Conciliação - Data Previsão
  - 2 : Conciliação - Data do Agendamento
  - 3 : Conciliação - Data do Pagamento
- QtdRegistro
- PaginaNumero

```bash
curl -X GET --location 'https://webapi.relatoriotef.com.br/conciliacao/resumo?Cnpj=05625872000169&DataInicial=2024-01-01T00:00:00&DataFinal=2024-01-01T23:59:59' \
--header 'Authorization: Bearer ••••••'
```

## Pagamentos Avulsos - Conciliação

Parametros da requisição:

- Cnpj `05625872000169`
- DataInicial `2024-01-01T00:00:00`
- DataFinal `2024-01-01T23:59:59` (Range máximo entre as datas de 90 dias)
- DataTipo
  - 0 : Pré-Conciliação - Data Venda
  - 1 : Pré-Conciliação - Data Previsão
  - 2 : Conciliação - Data do Agendamento
  - 3 : Conciliação - Data do Pagamento
- QtdRegistro
- PaginaNumero
- OrdenarPor
  - DataVenda
  - AdquirenteNome
  - BandeiraNome
  - Taxa
  - TaxaValor
  - ValorBruto
  - ValorLiquido
  - DataPgto
- OrdenarTipo (ASC ou DESC)

```bash
curl -X GET --location 'https://webapi.relatoriotef.com.br/conciliacao/pagamento/avulso?Cnpj=05625872000169&DataInicial=2024-01-01T00:00:00&DataFinal=2024-01-01T23:59:59&DataTipo=3&QtdRegistro=10&PaginaNumero=1&OrdenarPor=DataVenda&OrdenarTipo=ASC' \
--header 'Authorization: Bearer ••••••'
```

## Conciliação Bancária - Conciliação

Parametros da requisição:

- Cnpj `05625872000169`
- DataInicial `2024-01-01T00:00:00`
- DataFinal `2024-01-01T23:59:59` (Range máximo entre as datas de 90 dias)
- DataTipo
  - 0 : Pré-Conciliação - Data Venda
  - 1 : Pré-Conciliação - Data Previsão
  - 2 : Conciliação - Data do Agendamento
  - 3 : Conciliação - Data do Pagamento
- QtdRegistro
- PaginaNumero

```bash
curl -X GET --location 'https://webapi.relatoriotef.com.br/conciliacao/deposito?Cnpj=05625872000169&DataInicial=2024-01-01T00:00:00&DataFinal=2024-01-01T23:59:59&DataTipo=3&QtdRegistro=10&PaginaNumero=1' \
--header 'Authorization: Bearer ••••••'
```

## Conciliação Bancária/Resumo Taxas - Conciliação

Parametros da requisição:

- Cnpj `05625872000169`
- DataInicial `2024-01-01T00:00:00`
- DataFinal `2024-01-01T23:59:59` (Range máximo entre as datas de 90 dias)
- DataTipo
  - 0 : Pré-Conciliação - Data Venda
  - 1 : Pré-Conciliação - Data Previsão
  - 2 : Conciliação - Data do Agendamento
  - 3 : Conciliação - Data do Pagamento
- QtdRegistro
- PaginaNumero
- Agrupar
  - 1 : Servico
  - 2 : Bandeira
  - 3 : Adquirente

```bash
curl -X GET --location 'https://webapi.relatoriotef.com.br/conciliacao/taxa?Cnpj=05625872000169&DataInicial=2024-01-01T00:00:00&DataFinal=2024-01-01T23:59:59&DataTipo=3&QtdRegistro=10&PaginaNumero=1&Agrupar=1' \
--header 'Authorization: Bearer ••••••'
```
