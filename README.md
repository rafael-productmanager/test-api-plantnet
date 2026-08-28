# Guia de Teste da API Pl@ntNet via Postman

Este guia explica, passo a passo e em linguagem simples, como testar a API de identificação botânica por imagem da [Pl@ntNet](https://plantnet.org/) usando o Postman — sem precisar escrever código.

## O que você vai precisar

- Uma conta gratuita em [my.plantnet.org](https://my.plantnet.org) (para gerar sua chave de API — a "api-key")
- O [Postman](https://www.postman.com/downloads/) instalado no computador
- Fotos de plantas para testar (folha, flor, fruto, casca/tronco)

## Passo 1 — Criar a chave de API

Crie uma conta em my.plantnet.org e gere sua **api-key** pessoal na área de identificação. Guarde essa chave — ela será usada em toda requisição.

## Passo 2 — Criar a requisição no Postman

1. Abra o Postman e clique em **New** → **HTTP Request** (ou no botão **+** de nova aba).
2. Troque o método de `GET` para `POST`.
3. Na URL, cole: https://my-api.plantnet.org/v2/identify/all


> `all` é o "projeto" da Pl@ntNet com cobertura global de espécies. Existem projetos mais específicos por região, mas `all` é o mais indicado para um teste geral.

## Passo 3 — Parâmetros (aba "Params")

Na aba **Params**, adicione:

| Key | Value |
|---|---|
| `api-key` | sua chave gerada no Passo 1 |
| `lang` | `pt` (retorna nomes populares em português) |

## Passo 4 — Corpo da requisição (aba "Body")

⚠️ **Ponto de atenção importante**: a API **não** aceita imagens em base64 ou JSON. Ela espera um envio como **form-data**, com os arquivos de imagem de verdade.

1. Clique na aba **Body**.
2. Marque a opção **form-data**.
3. Você verá uma tabela com colunas Key / Value / e um seletor de tipo (Text/File). Adicione duas linhas:

   - **Linha 1**: Key = `organs` → no seletor de tipo, deixe como **Text** → Value = `leaf` (ou `flower`, `fruit`, `bark`, dependendo da foto)
   - **Linha 2**: Key = `images` → no seletor de tipo, troque para **File** → clique em "Select Files" e escolha a foto

> Erro comum: deixar a linha `organs` também como tipo **File** por engano. Se isso acontecer, o valor de texto (`leaf`, `bark` etc.) não é salvo. Confira sempre se `organs` está como **Text** e só `images` está como **File**.

Se enviar mais de uma foto na mesma requisição, repita o par de linhas `organs`/`images` para cada imagem (até 5 fotos por requisição).

## Passo 5 — Enviar e ler o resultado

Clique em **Send**. A resposta vem em JSON. Os campos mais importantes:

- `results`: lista de espécies candidatas, da mais provável para a menos provável
- `results[0].species.scientificNameWithoutAuthor`: nome científico do melhor palpite
- `results[0].score`: confiança da API (de 0 a 1 — quanto mais perto de 1, mais confiante)
- `results[0].species.commonNames`: nomes populares conhecidos pela API
- `predictedOrgans`: o órgão que a própria API detectou na imagem (pode divergir do que você informou em `organs` — a API prioriza sua própria detecção)

## Passo 6 — Cota de uso

A conta gratuita permite **500 identificações por dia**, resetando à meia-noite UTC. Para checar quanto ainda resta:
GET https://my-api.plantnet.org/v2/quota/daily?api-key=SUA_CHAVE


Requisições malformadas (erro) também consomem a cota — por isso vale conferir o corpo da requisição antes de enviar.

## Dicas para testes mais confiáveis

- Teste fotos de diferentes órgãos da planta separadamente (folha, flor, fruto, casca) para comparar a confiança de cada uma.
- Compare sempre o resultado da API com uma espécie já conhecida/confirmada, para calcular taxa de acerto.
- Fotos nítidas e com boa iluminação tendem a gerar scores mais altos.

{
    "query": {
        "project": "all",
        "images": [
            "27a6515cabd1118760eabd551ab3ac0a"
        ],
        "organs": [
            "leaf"
        ],
        "includeRelatedImages": false,
        "noReject": false,
        "type": null
    },
    "predictedOrgans": [
        {
            "image": "27a6515cabd1118760eabd551ab3ac0a",
            "filename": "quaresmeira_folha.jpg",
            "organ": "leaf",
            "score": 0.90015
        }
    ],
    "language": "pt",
    "preferedReferential": "k-world-flora",
    "bestMatch": "Pleroma granulosum (Desr.) D.Don",
    "results": [
        {
            "score": 0.30567,
            "species": {
                "scientificNameWithoutAuthor": "Pleroma granulosum",
                "scientificNameAuthorship": "(Desr.) D.Don",
                "genus": {
                    "scientificNameWithoutAuthor": "Pleroma",
                    "scientificNameAuthorship": "",
                    "scientificName": "Pleroma"
                },
                "family": {
                    "scientificNameWithoutAuthor": "Melastomataceae",
                    "scientificNameAuthorship": "",
                    "scientificName": "Melastomataceae"
                },
                "commonNames": [
                    "Quaresmeira"
                ],
                "scientificName": "Pleroma granulosum (Desr.) D.Don"
            },
            "gbif": {
                "id": "5601452"
            },
            "powo": {
                "id": "1028835-2"
            }
        },
        {
            "score": 0.19162,
            "species": {
                "scientificNameWithoutAuthor": "Pleroma urvilleanum",
                "scientificNameAuthorship": "(DC.) P.J.F.Guim. & Michelang.",
                "genus": {
                    "scientificNameWithoutAuthor": "Pleroma",
                    "scientificNameAuthorship": "",
                    "scientificName": "Pleroma"
                },
                "family": {
                    "scientificNameWithoutAuthor": "Melastomataceae",
                    "scientificNameAuthorship": "",
                    "scientificName": "Melastomataceae"
                },
                "commonNames": [
                    "Orelha de-onça",
                    "Manaca"
                ],
                "scientificName": "Pleroma urvilleanum (DC.) P.J.F.Guim. & Michelang."
            },
            "gbif": {
                "id": "10863132"
            },
            "powo": {
                "id": "77206410-1"
            }
        },
        {
            "score": 0.0835,
            "species": {
                "scientificNameWithoutAuthor": "Pleroma stenocarpum",
                "scientificNameAuthorship": "(DC.) Triana",
                "genus": {
                    "scientificNameWithoutAuthor": "Pleroma",
                    "scientificNameAuthorship": "",
                    "scientificName": "Pleroma"
                },
                "family": {
                    "scientificNameWithoutAuthor": "Melastomataceae",
                    "scientificNameAuthorship": "",
                    "scientificName": "Melastomataceae"
                },
                "commonNames": [],
                "scientificName": "Pleroma stenocarpum (DC.) Triana"
            },
            "gbif": {
                "id": "5601297"
            },
            "powo": {
                "id": "574874-1"
            }
        },
        {
            "score": 0.03008,
            "species": {
                "scientificNameWithoutAuthor": "Rosettea princeps",
                "scientificNameAuthorship": "(Kunth) Ver.-Lib. & G.Kadereit",
                "genus": {
                    "scientificNameWithoutAuthor": "Rosettea",
                    "scientificNameAuthorship": "",
                    "scientificName": "Rosettea"
                },
                "family": {
                    "scientificNameWithoutAuthor": "Melastomataceae",
                    "scientificNameAuthorship": "",
                    "scientificName": "Melastomataceae"
                },
                "commonNames": [],
                "scientificName": "Rosettea princeps (Kunth) Ver.-Lib. & G.Kadereit"
            },
            "gbif": {
                "id": "11147712"
            },
            "powo": {
                "id": "77214941-1"
            }
        },
        {
            "score": 0.01711,
            "species": {
                "scientificNameWithoutAuthor": "Pleroma mutabile",
                "scientificNameAuthorship": "(Vell.) Triana",
                "genus": {
                    "scientificNameWithoutAuthor": "Pleroma",
                    "scientificNameAuthorship": "",
                    "scientificName": "Pleroma"
                },
                "family": {
                    "scientificNameWithoutAuthor": "Melastomataceae",
                    "scientificNameAuthorship": "",
                    "scientificName": "Melastomataceae"
                },
                "commonNames": [
                    "Manacá da-serra",
                    "Cuipeúna",
                    "Flor de-quaresma"
                ],
                "scientificName": "Pleroma mutabile (Vell.) Triana"
            },
            "gbif": {
                "id": "5601379"
            },
            "powo": {
                "id": "574841-1"
            },
            "iucn": {
                "id": "148760241",
                "category": "LC"
            }
        },
        {
            "score": 0.0151,
            "species": {
                "scientificNameWithoutAuthor": "Melastoma malabathricum",
                "scientificNameAuthorship": "L.",
                "genus": {
                    "scientificNameWithoutAuthor": "Melastoma",
                    "scientificNameAuthorship": "",
                    "scientificName": "Melastoma"
                },
                "family": {
                    "scientificNameWithoutAuthor": "Melastomataceae",
                    "scientificNameAuthorship": "",
                    "scientificName": "Melastomataceae"
                },
                "commonNames": [
                    "Canela de velho",
                    "Manacá da serra"
                ],
                "scientificName": "Melastoma malabathricum L."
            },
            "gbif": {
                "id": "3188511"
            },
            "powo": {
                "id": "570989-1"
            }
        },
        {
            "score": 0.0092,
            "species": {
                "scientificNameWithoutAuthor": "Pleroma heteromallum",
                "scientificNameAuthorship": "(D.Don) D.Don",
                "genus": {
                    "scientificNameWithoutAuthor": "Pleroma",
                    "scientificNameAuthorship": "",
                    "scientificName": "Pleroma"
                },
                "family": {
                    "scientificNameWithoutAuthor": "Melastomataceae",
                    "scientificNameAuthorship": "",
                    "scientificName": "Melastomataceae"
                },
                "commonNames": [
                    "Orelha de-onça",
                    "Quaresmeira",
                    "Orelha de onça"
                ],
                "scientificName": "Pleroma heteromallum (D.Don) D.Don"
            },
            "gbif": {
                "id": "5601449"
            },
            "powo": {
                "id": "574812-1"
            }
        },
        {
            "score": 0.00658,
            "species": {
                "scientificNameWithoutAuthor": "Andesanthus lepidotus",
                "scientificNameAuthorship": "(Humb. & Bonpl.) P.J.F.Guim. & Michelang.",
                "genus": {
                    "scientificNameWithoutAuthor": "Andesanthus",
                    "scientificNameAuthorship": "",
                    "scientificName": "Andesanthus"
                },
                "family": {
                    "scientificNameWithoutAuthor": "Melastomataceae",
                    "scientificNameAuthorship": "",
                    "scientificName": "Melastomataceae"
                },
                "commonNames": [],
                "scientificName": "Andesanthus lepidotus (Humb. & Bonpl.) P.J.F.Guim. & Michelang."
            },
            "gbif": {
                "id": "10767444"
            },
            "powo": {
                "id": "77205900-1"
            },
            "iucn": {
                "id": "223103566",
                "category": "LC"
            }
        },
        {
            "score": 0.00249,
            "species": {
                "scientificNameWithoutAuthor": "Pleroma candolleanum",
                "scientificNameAuthorship": "(DC.) Triana",
                "genus": {
                    "scientificNameWithoutAuthor": "Pleroma",
                    "scientificNameAuthorship": "",
                    "scientificName": "Pleroma"
                },
                "family": {
                    "scientificNameWithoutAuthor": "Melastomataceae",
                    "scientificNameAuthorship": "",
                    "scientificName": "Melastomataceae"
                },
                "commonNames": [
                    "Quaresmeira da-serra",
                    "Quaresmeira do-cerrado"
                ],
                "scientificName": "Pleroma candolleanum (DC.) Triana"
            },
            "gbif": {
                "id": "5601584"
            },
            "powo": {
                "id": "574772-1"
            }
        },
        {
            "score": 0.00185,
            "species": {
                "scientificNameWithoutAuthor": "Pleroma semidecandrum",
                "scientificNameAuthorship": "(Schrank & Mart. ex DC.) Triana",
                "genus": {
                    "scientificNameWithoutAuthor": "Pleroma",
                    "scientificNameAuthorship": "",
                    "scientificName": "Pleroma"
                },
                "family": {
                    "scientificNameWithoutAuthor": "Melastomataceae",
                    "scientificNameAuthorship": "",
                    "scientificName": "Melastomataceae"
                },
                "commonNames": [],
                "scientificName": "Pleroma semidecandrum (Schrank & Mart. ex DC.) Triana"
            },
            "gbif": {
                "id": "5601313"
            },
            "powo": {
                "id": "574868-1"
            }
        }
    ],
    "version": "2026-03-20 (7.5)",
    "remainingIdentificationRequests": 497
}
