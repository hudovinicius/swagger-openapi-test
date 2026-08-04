# Matriz manual de Swagger/OpenAPI

A matriz usa o mesmo endpoint e os mesmos métodos em todas as versões homologadas:

- Swagger 2.0;
- OpenAPI 3.0.0, 3.0.1, 3.0.2, 3.0.3 e 3.0.4;
- OpenAPI 3.1.0, 3.1.1 e 3.1.2;
- OpenAPI 3.2.0, pelo adaptador controlado para 3.1.2.

Cada versão está disponível em YAML e JSON.

## Endpoint comum

Todos os documentos positivos definem `/manual/items/{id}`.

| Método | Resultado esperado após a importação |
|---|---|
| GET | parâmetros path/query/header/cookie, segurança OR, header de resposta e array na raiz |
| POST | endpoint deprecated, JSON com charset, override de parâmetro, composição e segurança AND |
| PUT | `application/x-www-form-urlencoded`, encoding por campo e resposta escalar |
| PATCH | `multipart/form-data`, upload binário e resposta `application/octet-stream` |
| DELETE | operação pública, resposta 204 e ausência de bodies artificiais |
| HEAD | fallback de resposta `2XX` no OpenAPI e saída contendo somente headers |
| OPTIONS | fallback `default` e body livre/boolean schema conforme a família |

As diferenças intencionais entre as famílias são:

| Família | Representação específica |
|---|---|
| Swagger 2.0 | body/formData, `collectionFormat`, `securityDefinitions`, resposta `type: file`; sem cookie, OpenID Connect, `writeOnly` ou resposta curinga `2XX` |
| OpenAPI 3.0.x | `requestBody`/`content`, `nullable`, binário por `type: string` + `format: binary` |
| OpenAPI 3.1.x | JSON Schema 2020-12, union com `null`, `const`, condicionais, `dependent*`, boolean schema e binário indicado pelo media type/encoding |
| OpenAPI 3.2.0 | mantém os recursos 3.1 e acrescenta `$self` e `itemSchema`; recursos 3.2 sem projeção no adaptador ficam nos casos negativos |

As versões de patch de uma mesma família possuem a mesma cobertura; nelas,
somente `openapi`, `info.title` e `info.version` variam.

## Como usar

Disponibilize o arquivo em uma URL aceita por `import-swagger.source.*` e envie essa
URL no campo `swaggerEndPoint` de `POST /api/v1/rest-resources/import-swagger`.

Exemplo de corpo:

```json
{
  "applicationId": "00000000-0000-0000-0000-000000000001",
  "serviceId": "00000000-0000-0000-0000-000000000002",
  "swaggerEndPoint": "https://host-permitido/manual/openapi-3.1.2.yaml",
  "update": true
}
```

Uma importação positiva deve criar sete recursos, todos com path
`/manual/items/{{{id}}}` e tag `manual-matrix`.

## Cobertura adicional

A matriz inclui parâmetros por schema e content, override por `(name, in)`, tipos e
constraints, arrays primitivos (`string`, `int64` e `double`), arrays de objetos,
arrays aninhados, mapas, objetos livres, `$ref`, `allOf`, `oneOf`,
`anyOf`, `not`, condicionais 3.1, visibilidade, segurança OR/AND/pública, API keys
em header/query/cookie, Basic, bearer, OAuth2, OpenID Connect, respostas
200/201/202/204/206/2XX/default, headers inline/referenciados e media types JSON,
vendor JSON, problem JSON, form, multipart, texto e binário.

## Casos negativos OpenAPI 3.2

A subpasta `negative` possui um arquivo isolado para cada construção válida que o
adaptador 3.2 rejeita ou não projeta de forma controlada. Esses arquivos testam o
comportamento do importador, não erros sintáticos da especificação. Cada importação
deve falhar com `SWAGGER_IMPORT_VALIDATION_FAILED`, sem persistir recursos.
