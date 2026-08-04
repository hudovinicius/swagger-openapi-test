# Matriz manual de Swagger/OpenAPI

Esta pasta contém um documento válido para cada versão explicitamente homologada pelo
importador deste microsserviço:

- Swagger 2.0;
- OpenAPI 3.0.0, 3.0.1, 3.0.2, 3.0.3 e 3.0.4;
- OpenAPI 3.1.0, 3.1.1 e 3.1.2;
- OpenAPI 3.2.0, por meio do adaptador controlado para 3.1.2.

## Como usar

Disponibilize o arquivo escolhido em uma URL permitida pela configuração
`import-swagger.source.*` e envie essa URL no campo `swaggerEndPoint` de
`POST /api/v1/rest-resources/import-swagger`.

Use novos UUIDs de aplicação/serviço ou `update: true` conforme o objetivo do teste.
Cada arquivo 3.x possui somente uma operação em `/manual/items/{id}`. O Swagger 2.0
possui POST e PUT no mesmo path porque a especificação não permite combinar um parâmetro
`body` com parâmetros `formData` na mesma operação.

## Cobertura controlada

| Área | Cobertura |
|---|---|
| Versão | Todas as versões exatas da matriz homologada |
| Parâmetros | path, query, header, cookie (3.x), arrays, serialização, default e enum |
| Body | JSON, urlencoded, multipart, arquivo/binário, objeto, mapa e array |
| Schema | tipos escalares, constraints, `$ref`, `allOf`, composição 3.1 e nullable |
| HTTP | múltiplos media types, respostas 2xx/4xx/default e header de resposta |
| Segurança | API key, HTTP Basic (2.0) e bearer (3.x), com alternativas OR |
| Visibilidade | `readOnly`, `writeOnly` e deprecated |
| OpenAPI 3.2 | `$self`, binário bruto e `itemSchema` sequencial adaptável |

O fixture 3.2 é deliberadamente válido. O adaptador rejeita, por design, recursos sem
projeção segura: método QUERY, parâmetro `in: querystring`, schema booleano,
`$dynamicRef`, `itemEncoding`, uso simultâneo de `schema` e `itemSchema`, e
binário definido por `contentEncoding`/`contentMediaType`.

