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
| POST | JSON com charset, override de parâmetro, composição de schema e segurança AND |
| PUT | `application/x-www-form-urlencoded`, encoding por campo e resposta escalar |
| PATCH | `multipart/form-data`, upload binário e resposta `application/octet-stream` |
| DELETE | operação pública, resposta 204 e ausência de bodies artificiais |
| HEAD | fallback de resposta `2XX` no OpenAPI e saída contendo somente headers |
| OPTIONS | fallback `default` e body livre/boolean schema conforme a família |

O Swagger 2.0 não possui parâmetros cookie, OpenID Connect nem resposta curinga `2XX`.
Nesses pontos, o fixture usa apenas a construção equivalente suportada pela versão,
sem alterar o path ou o conjunto de métodos.

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
constraints, arrays aninhados, mapas, objetos livres, `$ref`, `allOf`, `oneOf`,
`anyOf`, `not`, condicionais 3.1, visibilidade, segurança OR/AND/pública, API keys
em header/query/cookie, Basic, bearer, OAuth2, OpenID Connect, respostas
200/201/202/204/206/2XX/default, headers inline/referenciados e media types JSON,
vendor JSON, problem JSON, form, multipart, texto e binário.

## Casos negativos OpenAPI 3.2

A subpasta `negative` possui um arquivo isolado para cada construção que o adaptador
3.2 rejeita por não possuir projeção segura. Cada importação deve falhar com
`SWAGGER_IMPORT_VALIDATION_FAILED`, sem persistir recursos.
