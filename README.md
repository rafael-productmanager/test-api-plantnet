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
