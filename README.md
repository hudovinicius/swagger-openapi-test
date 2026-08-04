# Matriz Swagger/OpenAPI para testes de importação

Este repositório contém especificações novas e independentes para todas as versões
publicadas de Swagger/OpenAPI, exceto Swagger 1.x. Os documentos existem somente
em YAML.

## Versões

| Família | Arquivos |
|---|---|
| Swagger 2.0 | `swagger-2.0.yaml` |
| OpenAPI 3.0 | `openapi-3.0.0.yaml` até `openapi-3.0.4.yaml` |
| OpenAPI 3.1 | `openapi-3.1.0.yaml` até `openapi-3.1.2.yaml` |
| OpenAPI 3.2 | `openapi-3.2.0.yaml` |

## API comum

Todos os documentos descrevem a mesma loja fictícia, com os mesmos 6 paths,
13 operações e `operationId`s:

| Path | Métodos | Cobertura principal |
|---|---|---|
| `/products` | GET, POST | paginação, arrays, filtro estruturado, criação, exemplos e segurança OR |
| `/products/{productId}` | GET, PUT, PATCH, DELETE, HEAD, OPTIONS | path/header/cookie, JSON/XML, merge patch, segurança AND, 204 e resposta curinga |
| `/products/{productId}/image` | GET, PUT | download binário e upload multipart |
| `/orders` | POST | objetos e arrays aninhados, form URL encoded no OAS 3 e OAuth2 |
| `/orders/{orderId}` | GET | UUID, bearer/OpenID e erros referenciados |
| `/health` | GET | operação pública, resposta escalar e objeto livre |

O domínio é coerente entre versões: produtos físicos ou digitais são consultados e
mantidos, imagens são transferidas separadamente e pedidos referenciam produtos.

## Cobertura compartilhada

- metadados completos, servers/host, tags e documentação externa;
- parâmetros de path, query e header, defaults, enumerações, patterns e limites;
- JSON, XML, problem JSON, merge patch, form URL encoded, multipart, texto e binário;
- respostas inline e reutilizáveis, headers, exemplos, 2xx/4xx/default e ausência de body;
- API key em header/query, Basic ou bearer, OAuth2, OpenID e operação pública;
- `$ref`, objetos, mapas livres/tipados, arrays simples/aninhados e `allOf`;
- tipos e formatos string, boolean, integer/int32/int64, number/double, UUID, URI,
  email, date-time e password/read-only/write-only onde suportados.

## Diferenças intencionais

| Família | Recursos específicos exercitados |
|---|---|
| Swagger 2.0 | `host`, `basePath`, `consumes`, `produces`, `definitions`, `securityDefinitions`, parâmetros `body`/`formData`, `collectionFormat`, Basic Auth e `type: file` |
| OpenAPI 3.0.x | `servers`, `components`, `requestBody/content`, callbacks, links, cookie parameters, `nullable` e `type: string` + `format: binary` |
| OpenAPI 3.1.x | JSON Schema 2020-12, `jsonSchemaDialect`, união com `null`, `const`, schemas booleanos, condicionais, `dependentRequired`, `prefixItems` e `webhooks` |
| OpenAPI 3.2.0 | `$self`, tags hierárquicas (`summary`, `parent`, `kind`), parâmetro `querystring` e `itemSchema` para JSON text sequences |

As versões patch de 3.0 e 3.1 mantêm o mesmo conjunto de recursos; somente o campo
`openapi` e o título identificam o patch correspondente.

## Gerar e validar

Os patches OpenAPI são derivados da especificação canônica 3.1.2:

```bash
ruby scripts/render_openapi_version.rb 3.0.4
ruby scripts/render_openapi_version.rb 3.2.0
```

Para validar a matriz inteira, sem dependências externas:

```bash
ruby scripts/validate_matrix.rb
```

A validação confere parse YAML, versões declaradas, igualdade de paths/métodos e
`operationId`s, resolução de todos os `$ref` locais e recursos próprios de cada
família.
