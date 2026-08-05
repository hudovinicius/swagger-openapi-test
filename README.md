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

Todos os documentos descrevem a mesma loja fictícia, com os mesmos 7 paths,
14 operações e `operationId`s:

| Path | Métodos | Cobertura principal |
|---|---|---|
| `/products` | GET, POST | paginação, arrays, filtro estruturado, criação, exemplos e segurança OR |
| `/products/{productId}` | GET, PUT, PATCH, DELETE, HEAD, OPTIONS | path/header/cookie, JSON/XML, merge patch, segurança AND, 204 e resposta curinga |
| `/products/{productId}/image` | GET, PUT | download binário e upload multipart |
| `/orders` | POST | objetos e arrays aninhados, form URL encoded no OAS 3 e OAuth2 |
| `/orders/{orderId}` | GET | UUID, bearer/OpenID e erros referenciados |
| `/health` | GET | operação pública, resposta escalar e objeto livre |
| `/data-types` | POST | todos os tipos de schema e formatos comuns no request e no response |

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
  email, byte, binary, date, date-time, password, float, arrays, objetos e valores
  nullable/null, além de read-only/write-only, onde suportados.

## Diferenças intencionais

| Família | Recursos específicos exercitados |
|---|---|
| Swagger 2.0 | `host`, `basePath`, `consumes`, `produces`, `definitions`, `securityDefinitions`, parâmetros `body`/`formData`, `collectionFormat`, Basic Auth e `type: file` |
| OpenAPI 3.0.x | `servers`, `components`, `requestBody/content`, callbacks, links, cookie parameters, `nullable` e `type: string` + `format: binary` |
| OpenAPI 3.1.x | JSON Schema 2020-12, `jsonSchemaDialect`, união com `null`, `const`, schemas booleanos, condicionais, `dependentRequired`, `prefixItems` e `webhooks` |
| OpenAPI 3.2.0 | `$self`, tags hierárquicas (`summary`, `parent`, `kind`), parâmetro `querystring` e `itemSchema` para JSON text sequences |

As versões patch de 3.0 e 3.1 mantêm o mesmo conjunto de recursos; somente o campo
`openapi` e o título identificam o patch correspondente.

## Validação

Cada documento deve ser validado contra o schema oficial mais recente da sua
família. O schema de uma versão minor se aplica a todas as versões patch da mesma
família.

| Família | Schema oficial |
|---|---|
| Swagger 2.0 | [2017-08-27](https://spec.openapis.org/oas/2.0/schema/2017-08-27) |
| OpenAPI 3.0.x | [2024-10-18](https://spec.openapis.org/oas/3.0/schema/2024-10-18) |
| OpenAPI 3.1.x | [2025-11-23](https://spec.openapis.org/oas/3.1/schema/2025-11-23) |
| OpenAPI 3.2.x | [2025-11-23](https://spec.openapis.org/oas/3.2/schema/2025-11-23) |

Além da conformidade estrutural com esses schemas, a matriz deve preservar:

- parse YAML válido e versão declarada correspondente ao nome do arquivo;
- os mesmos paths, métodos e `operationId`s em todos os documentos;
- `operationId`s únicos e parâmetros de path obrigatórios;
- resolução de todos os `$ref` locais;
- uso de recursos somente nas famílias que os suportam.

Os YAMLs são independentes e mantidos diretamente. O repositório não inclui
scripts de geração nem dependências de validação.
