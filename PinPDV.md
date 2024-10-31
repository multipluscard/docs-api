# Manual de integração PINPDV 👩‍💻💳📠👨‍💻
A integração do sistema parceiro  com  PINPDV ocorre através de integração com WEBAPI no padrão REST.

Esse material tem como objetivo servir como guia de integração técnica, descrevendo os processos a serem executados 

## Principais funcionalidades:
O **PINPDV Lite** possui quatro principais funcionalidades:

- **PRODUTOS**: Venda de produtos iniciada pelo PINPDV 
A partir de produtos previamente cadastrados na base da API PINPDV o usuário pode fazer a venda e imprimir o documento (fiscal ou não fiscal) referente a venda, uma vez que o mesmo for gerado pela sistema parceiro.

- **PRÉ-VENDA**: Venda de produtos iniciada pelo sistema parceiro 
A partir do cadastro de uma pré-venda iniciada pelo sistema do parceiro, o usuário pode finalizar o pagamento mediante a forma de pagamento solicitada e imprimir o documento (fiscal ou não fiscal) referente a venda.

- **POS TEF**: Captura simples de transação iniciada pelo sistema parceiro 
O sistema pode solicitar a captura de transação mediante parâmetros específicos. As informações de pagamento são disponibilizadas para o sistema durante e no final do processo.

- **VENDA EXPRESSA**: Captura de transação iniciada pelo PINDV 
O usuário pode simplesmente realizar uma transação no PINPDV. Posteriormente, o sistema integrador pode ter acesso aos dados dessa transação.

## Importante 🚨

1. Para qualquer tipo de uso do aplicativo ou API é necessário que o cliente tenha contratado junto à Multiplus Card o produto e não possua nenhum tipo de bloqueio de uso.

1. Todas as integrações são feitas exclusivamente por meio de integração API REST respeitando as boas práticas acordadas entre as partes (pooling, ratelimit, etc.)

1. Toda requisição via API PINPDV deve contar o token de autenticação “Bearer token”. O token pode ser gerado mediante autenticação usando login e senha ou então usando o "Token de longa duração" do cliente.

1. O retorno será prioritáriamente JSON. Os códigos de resposta devem ser observados. Códigos de resposta 2xx significam sucesso, 4xx significam erro de requisição.

1. Quando houver nessa documentação o uso de chaves {}, o conteúdo entre elas deve ser substituídos pelos dados do contexto de utilização. Exemplo: {SENHA} deve ser substituído pela senha de quem está utilizando a API.

1. Alguns dados (como nome de produtos) podem estar preenchidos apenas como efeito didático.

1. O fluxo para cada funcionalidade estão descritos abaixo, junto com exemplos de chamada (curl).

## Códigos utilizados:

### Venda Status
    Realizada = 0,
    Cancelada = 8,
    Error = 9,

### Pagamento Status
    Aguardando = 0,
    Processando = 1,
    Concluido = 2,

### Tipos de pagamento
    None = 0,
    Dinheiro = 1,
    Credito = 2,
    Debito = 3,
    Pix = 4,
	ValeRefeicao = 6,
	ValeAlimentacao = 7

## Autenticação 🔐

💡Será disponibilizado um token de longa duração para cada cliente (estabelecimentoId). 
Ideal para incluir na aplicação que fica do lado do cliente.


## Listar empresas  🏪🏬🏪🏬🏪
Para algumas requisições é necessário enviar o código de empresa. Esse código é obtido no endpoint /empresa.

````bash
# OrdenarPor = (Id, Nome, Cnpj, CadastradoEm, AtualizadoEm)
# OrdenarTipo = (ASC, DESC)

curl --request GET \
  --url https://webapi.pinpdv.com.br/empresa?OrdenarPor=Id&OrdenarTipo=DESC \
  --header 'Authorization: Bearer xyz' \
````
↪️ Exemplo de resposta:
````json
200 - OK

{
  "paginaAtual": 1,
  "itensPorPagina": 100,
  "quantidadeDePaginas": 1,
  "quantidadeTotalDeItens": 1,
  "primeiroRegistro": 1,
  "ultimoRegistro": 1,
  "paginaAnterior": false,
  "paginaProxima": false,
  "data": [
    {
      "id": 1,
      "nome": "61492096000147",
      "cnpj": "61.492.096/0001-47",
      "representante": {
        "id": 2,
        "nome": "61492096000147",
        "usuario": "61492096000147",
        "webhook": null
      }
    }
  ]
}
````
## Impressão de documento 📃
No fim dos processos que permitem a impressão de documento (fiscal ou não fiscal) uma dessas abordagens deve ser adotada pelo sistema parceiro.

### Opções:

#### OPCAO 1

<details>
  <summary> <strong>Possuir URL de call-back </strong> 🚀 Recomendada! </summary>

Melhor experiência de integração!

No final de cada processo que permite impressão de documento é realizada chamada para URL de call-back informando que houve uma venda.

A resposta pode ser o documento a ser impresso, ou então nenhum conteúdo. Se houver resposta com conteúdo para impressão no call-back, esse conteúdo será impresso no APP PINPDV. Se não houver conteúdo, o sistema pode enviar o documento assim que estiver pronto, contudo, uma vez que o usuário saia da tela que executa a impressão não há alternativa de reimpressão.
</details>

<details>
  <summary> <strong>Configurar mensagem padrão de resposta</strong> 🚧 Em breve</summary>

Caso o sistema não disponha de endereço de call-back, é uma boa alternativa.

A API PINPDV se encarrega de sempre devolver um comprovante padrão de resposta mediante configuração do sistema parceiro. Essa resposta padrão pode por exemplo, ser uma lista dos produtos e a forma de pagamento.
</details>

<details>
  <summary> <strong>Pooling na API PINPDV</strong></summary>
Apesar de disponível, não recomendados principalmente devido ao “timming” da informação. O rate-limit entre as chamadas deve ser respeitado.
O sistema parceiro se encarrega de periodicamente monitorar as vendas realizadas no app PINPDV que não possuem documento gerado e providenciar o conteúdo.
</details>

## Atualizando documento comprovante
Dependendo da escolha do sistema parceiro quanto a geração de documento, o endpoint para atualizar o conteúdo deve ser usado, considerar sempre 40 caracteres. O texto será posicionado ao centro. Não há opção de formatação. O codigo de venda é retornado na resposta da requisição anterior. Exemplo, na resposta de status da pré-venda vai constar um objeto venda e seu ID.

````bash
curl --request POST \
  --url 'https://webapi.pinpdv.com.br/venda/{vendaID}/comprovante' \
  --header 'Authorization: Bearer xyz' \
  --header 'Content-Type: text/plain' \
  --data '         Aviso de comprovante          '
LINHA 1                                 
                                 LINHA 2
LINHA 3'
````
↪️ Exemplo de resposta:
````json
202 - OK
````
## PRODUTOS 🥫🍟🍔
Serão exibidos os produtos no PINPDV que estão cadastrados na API PINPDV e que estejam ativos.

### Cadastro
O cadastro dos produtos é feito no endpoint /produto passando o ID da empresa.

````bash
curl --request POST \
  --url 'https://webapi.pinpdv.com.br/produto' \
  --header 'Authorization: Bearer XYZ' \
  --header 'Content-Type: application/json' \
  --data '[
    {
        "Identificador": "cod1",
        "Nome": "Coca Cola 350",
        "Preco": 2.90,
        "Imagem": "{ImagemEmBase64}"
    }
]'
````
↪️ Exemplo de resposta:
````json
200 - OK

[
	{
		"id": 15,
		"nome": "Coca Cola 350",
		"descricao": null,
		"categoria": null,
		"marca": null,
		"modelo": null,
		"codigoBarras": null
	}
]
````

### Atualizar
O Atualizar do produto é feito no endpoint /produto passando o ID da empresa.

````bash
curl --request PUT \
  --url 'https://webapi.pinpdv.com.br/produto' \
  --header 'Authorization: Bearer XYZ' \
  --header 'Content-Type: application/json' \
  --data '{
	"Identificador": "cod1",
	"Nome": "Coca Cola 350",
	"Preco": 2.90,
	"Imagem": "{ImagemEmBase64}"
}'
````
↪️ Exemplo de resposta:
````json
200 - OK

[
	{
		"id": 15,
		"nome": "Coca Cola 350",
		"descricao": null,
		"categoria": null,
		"marca": null,
		"modelo": null,
		"codigoBarras": null
	}
]
````


### Desativar produto

````bash
curl --request PUT \
  --url 'https://webapi.pinpdv.com.br/produto/{Identificador}/desativar' \
  --header 'Authorization: Bearer xyz' \
```` 
↪️ Exemplo de resposta:
````json
202 - OK
````


### Ativar produto

````bash
curl --request PUT \
  --url 'https://webapi.pinpdv.com.br/produto/{Identificador}/ativar' \
  --header 'Authorization: Bearer xyz' \
````
↪️ Exemplo de resposta:
````json
202 - OK
````

### Deletar produto
````bash
curl --request DELETE \
  --url 'https://webapi.pinpdv.com.br/produto/{Identificador}' \
  --header 'Authorization: Bearer XYZ' \
  --header 'Content-Type: application/json'
````
↪️ Exemplo de resposta:
````json
202 - OK
````

### Impressão
Quando a venda for finalizada no APP PINPDV, será solicitado a impressão do documento (fiscal ou não fiscal) referente a venda. Ver explicação anterior sobre essa questão.

## PRÉ-VENDA 🧾💰
Serão exibidas as pré-vendas no PINPDV que foram cadastrados na API PINPDV e que não estejam concluídas.

### Cadastro
O cadastro das pré-vendas é feito no endpoint /pre-venda passando o ID da empresa. É obrigatório passar o tipo de pagamento, parcela e produtos.

````bash
curl --request POST \
  --url 'https://webapi.pinpdv.com.br/pre-venda' \
  --header 'Authorization: Bearer xyz' \
  --header 'Content-Type: application/json' \
  --data '{
    "Identificador": "{identificador}",
    "Valor": 34.83,
    "Descricao": "teste 1",
    "Parcelas": 1,
    "TipoPagamento": 3,
    "Produtos": [
        {
            "Identificador": "{identificadorProduto}",
            "Quantidade": 1
        }
    ]
}'
````
↪️ Exemplo de resposta:
````json
200 - OK

{
	"id": 8240
}
````

### Consulta de pré-venda
O sistema parceiro pode consultar o status de uma pré-venda enviada através do endpoint /pre-venda

📢 **Verifique o campo venda para dados do pagamento**

````bash
curl --request GET \
  --url 'https://webapi.pinpdv.com.br/pre-venda/{identificadorSistema}' \
  --header 'Authorization: Bearer xyz' \
````
↪️ Exemplo de resposta (Aguardando):
````json
200 - OK

{
	"id": 1,
	"identificador": "d858ee21dac64f71a10cd4a22fa1d80a",
	"descricao": "Mesa 01",
	"valor": 34.8300,
	"parcelas": 1,
	"tipoPagamento": 3,
	"status": {
		"key": 0,
		"value": "Aguardando"
	},
	"produtos": [
		{
			"id": 0,
			"identificador": "Coca01",
			"nome": "Coca Cola Lata",
			"descricao": null,
			"categoria": null,
			"marca": null,
			"modelo": null,
			"codigoBarras": null,
			"quantidade": 2,
			"precoUnitario": 10.0000,
			"precoTotal": 10.0000,
			"preco": 0,
			"isAtivo": false,
			"imagem": null
		}
	],
	"vendas": []
}
````
↪️ Exemplo de resposta:
````json
200 - OK

{
	"id": 1,
	"identificador": "d858ee21dac64f71a10cd4a22fa1d80a",
	"descricao": "Mesa 01",
	"valor": 34.8300,
	"parcelas": 1,
	"tipoPagamento": 3,
	"status": {
		"key": 0,
		"value": "Aguardando"
	},
	"produtos": [
		{
			"id": 0,
			"identificador": "Coca01",
			"nome": "Coca Cola Lata",
			"descricao": null,
			"categoria": null,
			"marca": null,
			"modelo": null,
			"codigoBarras": null,
			"quantidade": 2,
			"precoUnitario": 10.0000,
			"precoTotal": 10.0000,
			"preco": 0,
			"isAtivo": false,
			"imagem": null
		}
	],
	"vendas": [
		{
			"identificador": "44142e20828111ef8e6ca79b7a6aaeae",
			"preVendaIdentificador": "f99f8d8ce3354caca3c0cae660d14821",
			"valor": 34.83,
			"comprovante": null,
			"status": {
				"key": 0,
				"value": "Realizada"
			},
			"transacoes": [
				{
					"valor": 34.83,
					"parcelas": 1,
					"status": {
						"key": 0,
						"value": "Aprovada"
					},
					"tipoPagamento": 3,
					"dados": {
						"dataHora": "2024-10-04T15:48:37.635649",
						"nsu": "000000151",
						"autorizacao": "164550",
						"bandeira": "MASTERCARD",
						"adquirente": "GETNET"
					}
				}
			],
			"pinPdv": {
				"codigo": "526989",
				"nome": "CAIXA 01"
			}
		}
	]
}
````

### Impressão
Quando a venda for finalizada no APP PINPDV, será solicitado a impressão do documento (fiscal ou não fiscal) referente a venda. Ver explicação anterior sobre essa questão.

## POS-TEF
Para iniciar uma transação no modo POS TEF deve-se escolher o equipamento que vai receber a solicitação. Para isso, deve-se listar os equipamentos que sinalizaram estar ativos. 

⚠️ Cabe ao sistema parceiro definir o critério de escolha do equipamento, já que cada 
equipamento será listado com o Nome, Código, data e hora da última identificação de atividade do equipamento. 

⚠️ Importante: o equipamento é considerado ativo e a data/hora atualizada sempre que entrar no fluxo POS-TEF ou for para a página inicial do APP PINPDV. 

### Listar dispositivos disponíveis 📱📲📱📲
Para listar os PINPDVS disponíveis deve ser usado o endpoint /pinpdv

````bash
curl --request GET \
  --url 'https://webapi.pinpdv.com.br/pinpdv' \
  --header 'Authorization: Bearer xyz' \
````
↪️ Exemplo de resposta:
````json
200 - OK

{
	"paginaAtual": 1,
	"itensPorPagina": 100,
	"quantidadeDePaginas": 1,
	"quantidadeTotalDeItens": 2,
	"primeiroRegistro": 1,
	"ultimoRegistro": 2,
	"paginaAnterior": false,
	"paginaProxima": false,
	"data": [
		{
			"id": 1,
			"codigo": "526989",
			"nome": "CAIXA 01",
			"isAtivo": true,
			"heartbeat": "2024-10-04T15:49:28.894765"
		},
		{
			"id": 2,
			"codigo": "687836",
			"nome": "CAIXA 02",
			"isAtivo": true,
			"heartbeat": "2024-10-01T15:04:15.204673"
		}
	]
}
````

### Cadastro
O cadastro da solicitação é feito no endpoint /pos-venda. 

⚠️ É obrigatório passar o valor e o tipo de pagamento é opcional. 

⚠️ Uma vez informado o tipo de pagamento a quantidade de parcelas é obrigatória.

````bash
curl --request POST \
  --url 'https://webapi.pinpdv.com.br/pos-venda \
  --header 'Authorization: Bearer xyz' \
  --header 'Content-Type: application/json' \
  --data '{
    "PinPdvId": {pinpdvId},
    "Identificador": "vendaCf981239a",
    "Valor": 100.11,
    "Descricao": "mensagem",
    "TipoPagamento": 2,
    "Parcelas": 1
}'
````
↪️ Exemplo de resposta:
````json
200 - OK

{
	"id": 61239
}
````


### Abortar solicitação
O sistema parceiro pode cancelar a solicitação enviada enquanto o PINPDV não iniciar a transação. É utilizado o endpoint /pos-venda

````bash
curl --request DELETE \
  --url 'https://webapi.pinpdv.com.br/pos-venda/{Identificador}' \
  --header 'Authorization: Bearer xyz' \
````
↪️ Exemplo de resposta:
````json
202 - OK
````

### Consulta de solicitação
O sistema parceiro pode consultar o status de uma solicitação enviada através do endpoint /pre-venda

📢 **Verifique o campo venda para dados do pagamento**

````bash
curl --request GET \
  --url 'https://webapi.pinpdv.com.br/pos-venda/{Identificador}' \
  --header 'Authorization: Bearer xyz' \
````
↪️ Exemplo de resposta:
````json
200 - OK
{
	"id": 10,
	"identificador": "Id010101",
	"descricao": "Exemplo",
	"valor": 789.0000,
	"parcelas": 1,
	"tipoPagamento": 3,
	"status": {
		"key": 2,
		"value": "Concluido"
	},
	"vendas": [
		{
			"identificador": "90672461828311ef8e6ca79b7a6aaeae",
			"posVendaIdentificador": "Id010101",
			"valor": 789.0000,
			"comprovante": null,
			"status": {
				"key": 0,
				"value": "Realizada"
			},
			"transacoes": [
				{
					"valor": 789.0000,
					"parcelas": 0,
					"status": {
						"key": 0,
						"value": "Aprovada"
					},
					"tipoPagamento": 3,
					"dados": {
						"dataHora": "2024-10-04T16:05:04.679023",
						"nsu": "000000153",
						"autorizacao": "164550",
						"bandeira": "MASTERCARD",
						"adquirente": "GETNET"
					}
				}
			],
			"pinPdv": {
				"codigo": "526989",
				"nome": "CAIXA01"
			}
		}
	]
}
````


### VENDA EXPRESSA
A captura de transação realizada no APP PINPDV através da modalidade VENDA EXPRESSA está disponível em outra API, pertencente às soluções Multiplus Card. Verifique documentação referente a “reimpressão de comprovante” (https://github.com/multipluscard/docs-api) .




