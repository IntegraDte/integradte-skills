# Endpoints de IntegraDTE

Fuentes:

- Coleccion Postman `IntegraDTE API` entregada por el usuario.
- `/Users/joseluis/Desktop/projects/jose/full-dte/integradte-api-client/endpoints.md`: API publica de clientes (`x-api-key`).
- `integradte-api-client/internal/routes/public.routes.go` y `private.routes.go`: rutas realmente registradas por la API publica (verificadas en `5a81af8`).
- `/Users/joseluis/Desktop/projects/jose/full-dte/full-dte-api-sii/endpoints.md`: API interna, fuente de las rutas de provisioning.

Base URL publica sugerida: `https://api.integradte.cl`
Base API v1: `https://api.integradte.cl/api/v1`

En ejemplos locales del backend puede aparecer `http://localhost:3000`.

Todas las rutas de la API publica viven bajo `/api/v1` (salvo el alias `GET /health`). Las rutas distinguen mayusculas.

## Indice

- [Variables observadas](#variables-observadas)
- [Headers habituales](#headers-habituales)
- [Formato de respuesta](#formato-de-respuesta)
- [Provisioning](#provisioning)
- [Salud y login](#salud-y-login)
- [Onboarding (primera empresa)](#onboarding-primera-empresa)
- [Usuario](#usuario)
- [Empresas](#empresas)
- [Certificados](#certificados)
- [Documentos DTE online](#documentos-dte-online)
- [Documentos offline](#documentos-offline)
- [Numeracion / CAF / folios](#numeracion--caf--folios)
- [PDFs](#pdfs)
- [Cesiones](#cesiones)
- [Compras / acuse de recibo](#compras--acuse-de-recibo)
- [Billing](#billing)
- [Consumo](#consumo)
- [Endpoint routing rapido](#endpoint-routing-rapido)
- [Errores comunes](#errores-comunes)

## Variables observadas

- `x_api_key`
- `x_user_key`
- `provisioning_api_key`
- `idempotency_key`
- `user_id`
- `business_id`
- `business_id_exento`
- `document_id`
- `cession_id`
- `range_id`
- `code_sii`
- `numeration_id`
- `plan_id`
- `certificate`
- `password`
- `expired_date`

## Headers habituales

### Lectura privada

- `x-api-key`

### Escritura privada

- `Content-Type: application/json`
- `x-api-key`
- `idempotency-key: <UUID>` **obligatorio** en las rutas con middleware de idempotencia. Sin el header responde `400 "idempotency-key header is required"`; con un valor que no es UUID responde `400 "idempotency-key must be a valid UUID"`. Sirve cualquier version de UUID.

Rutas que exigen `idempotency-key`:

- `POST /api/v1/documents`
- `PUT /api/v1/documents/:id`
- `POST /api/v1/businesses`
- `PUT /api/v1/businesses/:id`
- `PUT /api/v1/business/:id/certificate`
- `PUT /api/v1/numerations`
- `DELETE /api/v1/numerations/:numerationId`
- `PATCH /api/v1/numerations/:numerationId/next-number`
- `PATCH /api/v1/numerations/low-stock`
- `POST /api/v1/purchase-acknowledgments`
- `POST /api/v1/cessions`

El resto de las rutas ignora el header (incluidos los requeue, `production-mode`, `certification-mode`, `numerations/request`, `pdfs/generate` y el onboarding).

Reglas de la clave:

- Se asocia a `(idempotency-key, usuario, ruta)` y dura 24 h. El body no se compara: reusar la clave con otro body devuelve la primera respuesta.
- Usa un UUID nuevo por cada operacion logica. Reusa la misma clave solo para reintentar exactamente la misma request.
- Si el primer intento fallo o no tuvo respuesta, reintenta con una clave nueva: algunas respuestas (errores de validacion y **todas** las de `PUT /api/v1/documents/:id`) no quedan guardadas y un reintento con la misma clave responde `500 "failed to parse cached response"`.

### Onboarding

- `x-user-key` (lo devuelve `POST /api/v1/auth/login`)
- `Content-Type: application/json`

### Provisioning

- `x-provisioning-key`
- `idempotency-key`
- `Content-Type: application/json`

Las rutas de provisioning son para aplicaciones externas que crean usuarios y empresas antes de que exista un `x-api-key`. No aceptan `x-api-key` como sustituto.

## Formato de respuesta

- La mayoria de las rutas responde con wrapper `{ "success": true, "message": "...", "data": ... }`.
- Si el endpoint no devuelve datos, **la llave `data` no viene** (no es `data: null`). Pasa, por ejemplo, en `PUT /api/v1/numerations`, `DELETE /api/v1/numerations/:numerationId` y `PATCH /api/v1/numerations/:numerationId/next-number`. Las listas vacias si vienen como `[]`.
- Los errores responden `{ "success": false, "message": "...", "code": "...", "details": ... }`. `code` solo viene cuando el endpoint lo define; `details` trae el reporte de validacion. Discrimina por `code`, no por `message`.
- Excepciones sin wrapper: `GET /api/v1/health` (JSON plano) y `POST /api/v1/numerations/request` (arreglo plano).

## Endpoints por categoria

### Provisioning

| Metodo | Ruta | Proposito | Headers clave |
| --- | --- | --- | --- |
| `POST` | `/api/v1/provisioning/users` | Crear usuario externo activo y verificado | `x-provisioning-key`, `idempotency-key` |
| `POST` | `/api/v1/provisioning/users/:user_id/businesses` | Crear empresa para un usuario y devolver `data.apiToken.xApiKey` | `x-provisioning-key`, `idempotency-key` |

Payload de `POST /api/v1/provisioning/users`:

```json
{
  "nombres": "Maria",
  "apellidos": "Gonzalez",
  "direccion": "Av. Principal 123",
  "email": "maria.gonzalez@empresa.cl",
  "password": "PasswordSeguro123"
}
```

Payload de `POST /api/v1/provisioning/users/:user_id/businesses`:

```json
{
  "businessName": "Empresa Ejemplo SpA",
  "rut": "12345678-9",
  "activity": "Desarrollo de software",
  "address": "Av. Principal 123, Oficina 45",
  "commune": "Providencia",
  "city": "Santiago",
  "emailDte": "dte@empresa.cl",
  "emailContact": "contacto@empresa.cl",
  "rutLegalAgent": "17240862-1",
  "fullNameLegalAgent": "Alejandro Jesus Cea Perez",
  "resolutionNumberDte": "0",
  "resolutionDateDte": "1992-12-31",
  "resolutionNumberTicket": "0",
  "resolutionTicketDate": "2014-05-27",
  "apiTokenName": "Token integracion externa",
  "certificate": "BASE64_DEL_PFX_OPCIONAL",
  "password": "PASSWORD_CERTIFICADO_OPCIONAL",
  "expired_date": "2027-05-04T10:30:00Z"
}
```

Notas:

- El usuario queda `active` y `verify_at` seteado.
- La password se guarda hasheada y no se devuelve.
- La empresa queda `active` y en certificacion (`isProd: false`).
- `apiTokenName` es opcional.
- `certificate` es opcional. Si se informa, debe venir como base64 del archivo `.pfx/.p12`.
- `password` del certificado es opcional. Si se informa, backend lo codifica antes de persistirlo.
- `expired_date` es requerido solo cuando se informa `certificate`.
- Si el email ya existe responde `409`; si el usuario no existe responde `404`; si el RUT ya existe responde `409`.

### Salud y login

| Metodo | Ruta | Proposito | Headers clave |
| --- | --- | --- | --- |
| `GET` | `/api/v1/health` | Identificar que servicio y que build responde (tambien en `GET /health`) | ninguno |
| `POST` | `/api/v1/auth/login` | Validar email + password de un usuario existente y obtener su `x-user-key` | `Content-Type` |

Notas de `GET /api/v1/health`:

- Publica, sin auth. Responde siempre `200` con **JSON plano, sin wrapper** `success/data`: `service`, `started_at`, `uptime_seconds` y, si estan disponibles, `commit`, `version`, `built_at`, `env`, `deployment`.
- `service` debe ser `integradte-api-client`; sirve para confirmar que servicio atiende el dominio publico.

Payload de `POST /api/v1/auth/login`:

```json
{
  "email": "maria.gonzalez@empresa.cl",
  "password": "PasswordSeguro123"
}
```

Notas de `POST /api/v1/auth/login`:

- Publica, sin auth previa ni `idempotency-key`.
- Responde `200` con `data.user_id`, `data.email` y `data.xUserKey` (ojo: `user_id` en snake_case y `xUserKey` en camelCase).
- El `xUserKey` solo sirve para `POST /api/v1/onboarding/businesses`; el resto de la API usa `x-api-key`.
- `401 invalid credentials` tanto si el email no existe como si la password es incorrecta; `403 user is not active` si el usuario esta inactivo; `400` si el body es invalido.
- No crea usuarios: el usuario debe existir antes (por provisioning o registro).

### Onboarding (primera empresa)

| Metodo | Ruta | Proposito | Headers clave |
| --- | --- | --- | --- |
| `POST` | `/api/v1/onboarding/businesses` | Crear la **primera** empresa del usuario y obtener su `data.apiToken.xApiKey` | `x-user-key` |

Notas:

- Requiere `x-user-key`, no `x-api-key`. No usa idempotencia (el header se ignora).
- Mismo payload que `POST /api/v1/businesses` (ver [Empresas](#empresas)); ademas acepta `logo` (base64 sin prefijo `data:`) y `logoContentType` opcionales. Se exige `region` o `city` (al menos uno).
- Responde `201` con la empresa y `data.apiToken.xApiKey`, que es el valor para el header `x-api-key` desde ahi en adelante.
- Si el usuario ya tiene una empresa responde `409 user already has a business`: las siguientes se crean con `POST /api/v1/businesses` y `x-api-key`.
- Otros errores: `409 business with rut already exists`, `400` por body o validacion, `401` por `x-user-key` faltante o invalido. Una fecha de resolucion que no sea `YYYY-MM-DD` ni RFC 3339 responde `500 failed to create business`, no `400`.

### Usuario

| Metodo | Ruta | Proposito |
| --- | --- | --- |
| `GET` | `/api/v1/users/me` | Obtener usuario autenticado por `x-api-key` |

### Empresas

| Metodo | Ruta | Proposito | Headers clave |
| --- | --- | --- | --- |
| `GET` | `/api/v1/businesses` | Listar empresas del usuario autenticado | `x-api-key` |
| `POST` | `/api/v1/businesses` | Crear empresa adicional para el usuario autenticado | `x-api-key`, `idempotency-key` (obligatorio) |
| `GET` | `/api/v1/businesses/:id` | Obtener detalle de empresa propia | `x-api-key` |
| `PUT` | `/api/v1/businesses/:id` | Editar empresa propia | `x-api-key`, `idempotency-key` (obligatorio) |
| `POST` | `/api/v1/businesses/production-mode` | Pasar empresa del token a produccion | `x-api-key` |
| `POST` | `/api/v1/businesses/certification-mode` | Volver empresa del token a certificacion | `x-api-key` |
| `PUT` | `/api/v1/business/:id/certificate` | Subir certificado digital | `x-api-key`, `idempotency-key` (obligatorio) |
| `GET` | `/api/v1/business/certificate-info` | Saber si la empresa tiene certificado valido para firmar | `x-api-key` |

Payload de crear/editar empresa:

```json
{
  "businessName": "Empresa Ejemplo SpA",
  "rut": "12345678-9",
  "activity": "Desarrollo de software",
  "address": "Av. Principal 123, Oficina 45",
  "commune": "Providencia",
  "city": "Santiago",
  "emailDte": "dte@empresa.cl",
  "emailContact": "contacto@empresa.cl",
  "rutLegalAgent": "17240862-1",
  "fullNameLegalAgent": "Alejandro Jesus Cea Perez",
  "resolutionNumberDte": "0",
  "resolutionDateDte": "1992-12-31",
  "resolutionNumberTicket": "0",
  "resolutionTicketDate": "2014-05-27"
}
```

Notas:

- La primera empresa de un usuario sin `x-api-key` se crea con `POST /api/v1/onboarding/businesses` (`x-user-key`); `POST /api/v1/businesses` es para las siguientes.
- `POST /api/v1/businesses` fuerza `isProd` a `false`.
- `PUT /api/v1/businesses/:id` no edita `isProd`.
- `POST /api/v1/businesses/production-mode` requiere certificado digital vigente y las cuatro resoluciones con fechas `YYYY-MM-DD`.
- Los CAF de certificacion no sirven en produccion; despues del cambio deben cargarse CAF de produccion.
- `POST /api/v1/businesses/certification-mode` no valida certificado y es idempotente.
- La empresa que devuelven `GET/POST /api/v1/businesses` y `GET/PUT /api/v1/businesses/:id` no incluye `certificate` ni `certificatePassword`; si trae los metadatos (`certificateFileName`, `certificateSubject`, `certificateExpiredDate`, `certificateUploadedAt`).

Payload para modo produccion:

```json
{
  "resolution_number_dte": "80",
  "resolution_date_dte": "2014-08-22",
  "resolution_number_ticket": "81",
  "resolution_ticket_date": "2014-08-23"
}
```

### Certificados

| Metodo | Ruta | Proposito | Headers clave |
| --- | --- | --- | --- |
| `PUT` | `/api/v1/business/:id/certificate` | Subir certificado digital (`:id` = `business_id`) | `x-api-key`, `idempotency-key` (obligatorio) |
| `GET` | `/api/v1/business/certificate-info` | Saber si la empresa tiene certificado valido para firmar | `x-api-key` |

Respuesta de `GET /api/v1/business/certificate-info`:

```json
{
  "success": true,
  "message": "certificate info retrieved successfully",
  "data": {
    "has_valid_certificate": true
  }
}
```

Notas:

- `has_valid_certificate` es `true` solo si la empresa tiene certificado cargado, abre con su contrasena y no esta vencido. Es la misma validacion que aplica la emision: `false` significa que emitir se va a rechazar por el certificado.
- Si la empresa no tiene certificado responde `200` con `false`, no un error.
- La API no entrega el certificado, su contrasena ni la llave privada por ninguna ruta. `GET /api/v1/certificates/current` ya no existe.

### Documentos DTE online

| Metodo | Ruta | Proposito | Headers clave |
| --- | --- | --- | --- |
| `POST` | `/api/v1/documents` | Emitir documento | `x-api-key`, `idempotency-key` (obligatorio) |
| `PUT` | `/api/v1/documents/:id` | Modificar documento | `x-api-key`, `idempotency-key` (obligatorio, nuevo en cada intento) |
| `GET` | `/api/v1/documents` | Listar documentos con filtros y paginacion | `x-api-key` |
| `GET` | `/api/v1/documents/:id` | Obtener detalle de documento propio | `x-api-key` |
| `GET` | `/api/v1/documents/stats` | Obtener estadisticas de documentos | `x-api-key` |
| `POST` | `/api/v1/documents/requeue` | Reencolar documento normal en cola `dte` | `x-api-key` |

Query params de `GET /api/v1/documents`:

- `code_sii`
- `status`
- `is_prod` (`true` o `false`): ambiente con que se emitio; otro valor responde `400`. Los documentos antiguos sin `is_prod` no aparecen con ninguno de los dos valores.
- `from_date` con formato `YYYY-MM-DD`
- `to_date` con formato `YYYY-MM-DD`
- `page`, default `1`
- `limit`, default `20`, max `100`

Query params de `GET /api/v1/documents/stats`:

- `code_sii`
- `status`
- `is_prod`
- `from_date`
- `to_date`
- `page` y `limit` por compatibilidad de filtro

Payload observado para emision con JSON estructurado:

```json
{
  "user_id": "<user_id>",
  "business_id": "<business_id>",
  "code_sii": "33",
  "data_dte_json": {
    "Encabezado": {},
    "Detalle": []
  }
}
```

Payload observado para boleta usando string JSON:

```json
{
  "user_id": "<user_id>",
  "business_id": "<business_id>",
  "code_sii": "39",
  "data_dte": "{\"Encabezado\":{...},\"Detalle\":[...]}"
}
```

Payload de `PUT /api/v1/documents/:id`: `data_dte` (string con el DTE serializado) o `data_dte_json` (objeto, o string que contiene JSON). Si `data_dte` viene no vacio, gana; mandar un objeto en `data_dte` responde `400 invalid request body`.

Payload de `POST /api/v1/documents/requeue`:

```json
{
  "document_id": "67b111a6c1f0e36f7f4a0101"
}
```

Notas:

- `POST /api/v1/documents` y `PUT /api/v1/documents/:id` responden `400 "idempotency-key header is required"` si falta el header.
- `PUT /api/v1/documents/:id` nunca guarda su respuesta para idempotencia: usa un UUID nuevo en cada intento (reusar la clave responde `500 "failed to parse cached response"`). Responde `409` si el documento ya fue recibido o procesado por SII, `404` si no existe y `403` si no pertenece al token.
- `POST /api/v1/documents/requeue` valida ownership y bloquea el requeue si el documento ya fue recibido o procesado por SII. Tiene limite propio: un requeue por documento cada 60 s y cinco por usuario cada 10 min (`429` con `Retry-After`).
- `GET /api/v1/documents/:id` valida ownership por token.

### Documentos offline

| Metodo | Ruta | Proposito | Respuesta |
| --- | --- | --- | --- |
| `POST` | `/api/v1/documents/requeue/status` | Reencolar consulta de estado SII offline en cola `dte_status` | wrapper `success/data` |

Payload de `POST /api/v1/documents/requeue/status`:

```json
{
  "document_id": "67d5f2b3d59fcb4caa2b8d91"
}
```

Notas:

- `POST /api/v1/documents/requeue/status` espera el `_id` del documento en `documents_offline` y responde `404` si no pertenece a la empresa autenticada.
- Comparte el limite de requeue de `POST /api/v1/documents/requeue` (`429` con `Retry-After`).

### Numeracion / CAF / folios

| Metodo | Ruta | Proposito | Headers clave | Respuesta |
| --- | --- | --- | --- | --- |
| `PUT` | `/api/v1/numerations` | Crear o agregar un rango de folios CAF para un tipo de DTE | `x-api-key`, `idempotency-key` (obligatorio) | wrapper sin `data` |
| `GET` | `/api/v1/numerations/summary` | Resumen de folios disponibles, agotados y vencidos | `x-api-key` | wrapper `success/data` |
| `GET` | `/api/v1/numerations/ranges` | Listar rangos CAF agrupados por tipo de DTE, con usados/disponibles | `x-api-key` | wrapper `success/data` |
| `PATCH` | `/api/v1/numerations/:numerationId/next-number` | Ajustar el proximo folio a emitir de un rango CAF | `x-api-key`, `idempotency-key` (obligatorio) | wrapper sin `data` |
| `PATCH` | `/api/v1/numerations/low-stock` | Configurar por `code_sii` el umbral de folios bajos y la cantidad a recargar automaticamente | `x-api-key`, `idempotency-key` (obligatorio) | wrapper `success/data` |
| `DELETE` | `/api/v1/numerations/:numerationId` | Eliminar un rango de folios CAF | `x-api-key`, `idempotency-key` (obligatorio) | wrapper sin `data` |
| `GET` | `/api/v1/numerations/last-used-number?code_sii={code_sii}` | Obtener ultimo folio usado | `x-api-key` | wrapper `success/data` |
| `POST` | `/api/v1/numerations/request` | Reservar y devolver rangos de folios disponibles | `x-api-key` | arreglo JSON plano |

Payload de `PUT /api/v1/numerations`:

```json
{
  "code_sii": "33",
  "start_number": 100,
  "end_number": 199,
  "caf_base64": "BASE64_DEL_XML_DEL_CAF",
  "creation_date": "2026-06-01T00:00:00Z",
  "due_date": "2026-12-31T00:00:00Z"
}
```

Notas de `PUT /api/v1/numerations`:

- Requiere `x-api-key`. No disponible para sesiones impersonadas (bloqueadas para escritura).
- Requiere `idempotency-key` (UUID); sin el header responde `400`. Evita duplicar la creacion ante reintentos.
- El ambiente (`isProduction`) lo determina el backend segun el modo de la empresa (`isProd`); no se toma del body.
- `caf_base64` es el XML del CAF entregado por el SII, en base64.
- `end_number` debe ser mayor o igual a `start_number`; si no, responde `400`.
- Responde `{ "success": true, "message": "numeration created successfully" }`, sin llave `data`.

Query params de `GET /api/v1/numerations/ranges`:

- `code_sii` (opcional): filtra por un tipo de DTE; si se omite, devuelve todos. Un codigo desconocido devuelve `items: []`.

Notas de `GET /api/v1/numerations/ranges`:

- Requiere `x-api-key`.
- Devuelve los rangos agrupados por `code_sii` con `total_folios`, `used_folios`, `available` y el detalle de cada rango (`id`, `start_number`, `end_number`, `last_number`, `available`, `is_exhausted`, `due_date`, `is_expired`, `created_at`).
- El `id` de cada rango es el `:numerationId` que usan `PATCH .../next-number` y `DELETE /api/v1/numerations/:numerationId`.
- El orden de `items` no es determinista; no dependas de el.
- El CAF base64 se omite a proposito para no exponer el material de firma.
- El ambiente (produccion/certificacion) lo determina el backend segun el modo de la empresa.

Payload de `PATCH /api/v1/numerations/:numerationId/next-number`:

```json
{
  "next_number": 150
}
```

Notas de `PATCH /api/v1/numerations/:numerationId/next-number`:

- `next_number` es el folio que recibira el siguiente documento (`>= 1`); internamente el contador se guarda como `next_number - 1`.
- Requiere `x-api-key`. No disponible para sesiones impersonadas (bloqueadas para escritura). Requiere `idempotency-key` (UUID); sin el header responde `400`.
- `:numerationId` es el `id` del rango CAF (de `GET /api/v1/numerations/ranges`) y debe ser un ObjectID valido (si no, responde `400`).
- `next_number` debe caer dentro del rango del CAF; fuera de rango responde `400`.
- Si el rango no existe responde `404`; ante actualizacion concurrente responde `409` (reintentar con un `idempotency-key` nuevo).
- Responde `200` sin llave `data` (no `data: null`).

Payload de `PATCH /api/v1/numerations/low-stock`:

```json
{
  "items": [
    { "code_sii": "33", "threshold": 20, "request_quantity": 100 },
    { "code_sii": "39", "threshold": 50, "request_quantity": 500 }
  ]
}
```

Notas de `PATCH /api/v1/numerations/low-stock`:

- Configura la recarga automatica de folios de la empresa del token: cuando los folios disponibles de un `code_sii` bajan a `threshold` o menos, se solicitan `request_quantity` folios nuevos. No lleva parametros de ruta ni query.
- Requiere `x-api-key`. No disponible para sesiones impersonadas. Requiere `idempotency-key` (UUID); sin el header responde `400`.
- `items` es obligatorio y con al menos un elemento.
- `code_sii` es **string** y debe ser uno de `"33"`, `"34"`, `"39"`, `"41"`, `"46"`, `"52"`, `"56"`, `"61"`. Mandarlo como numero (`33`) responde `400 invalid request body`. No puede repetirse en la misma solicitud (`400 items contiene un code_sii repetido`).
- `threshold` es entero `>= 0` (`0` = pedir recien cuando se agotan) y la llave debe venir aunque sea `0`. `request_quantity` es entero `>= 1`.
- Cada item exige ambos valores juntos: la recarga automatica necesita umbral y cantidad para el mismo codigo.
- Fusiona por `code_sii`: los codigos que no vienen en `items` conservan su configuracion.
- Responde `200` con la configuracion **completa** de la empresa en `data.items`, ordenada por codigo. Si un codigo quedo a medias (por ejemplo, configurado via `PUT /api/v1/businesses/:id`), la mitad que falta viene en `null` y ese codigo no se recarga.
- Errores de validacion (`400 "Validation error"`) reportan en `details.errors[].field` el nombre Go (`CodeSii`, `Threshold`, `RequestQuantity`), no el nombre JSON. `404 business not found` si la empresa del token no existe.

Respuesta de `PATCH /api/v1/numerations/low-stock`:

```json
{
  "success": true,
  "message": "low stock config updated successfully",
  "data": {
    "items": [
      { "code_sii": "33", "threshold": 20, "request_quantity": 100 },
      { "code_sii": "39", "threshold": null, "request_quantity": 500 }
    ]
  }
}
```

Notas de `DELETE /api/v1/numerations/:numerationId`:

- Requiere `x-api-key`. No disponible para sesiones impersonadas. Requiere `idempotency-key` (UUID); sin el header responde `400`.
- `:numerationId` debe ser un ObjectID valido (si no, responde `400`).
- Si el rango no existe responde `404`.
- Responde `200` sin llave `data`.

Payload de `POST /api/v1/numerations/request`:

```json
{
  "document_type": 33,
  "quantity": 4
}
```

Notas de `POST /api/v1/numerations/request`:

- Requiere `x-api-key`.
- Responde con arreglo JSON plano, sin wrapper `success/data`.
- Los rangos devueltos quedan inmediatamente reservados por la API.
- Los folios se marcan como usados para que no se entreguen otra vez ni se consuman en paralelo desde emision online.
- Si no hay stock responde `[]`.

### PDFs

| Metodo | Ruta | Proposito | Headers clave |
| --- | --- | --- | --- |
| `POST` | `/api/v1/pdfs/generate` | Generar PDF | `x-api-key` |

Notas:

- Es una ruta cobrable (clave de precio `pdf`): puede responder `402`/`429` de billing (ver [Errores comunes](#errores-comunes)).

### Cesiones

| Metodo | Ruta | Proposito | Headers clave |
| --- | --- | --- | --- |
| `POST` | `/api/v1/cessions` | Crear cesion | `x-api-key`, `idempotency-key` (obligatorio) |
| `POST` | `/api/v1/cessions/requeue` | Reprocesar cesion | `x-api-key` |
| `GET` | `/api/v1/cessions` | Listar cesiones de la empresa, con paginacion o por documento | `x-api-key` |
| `GET` | `/api/v1/cessions/:id` | Obtener detalle de una cesion propia | `x-api-key` |

Query params de `GET /api/v1/cessions`:

- `page`, default `1`
- `limit`, default `20`, max `100`
- `document_id` (opcional, ObjectID): responde "este documento ya fue cedido?". Un valor invalido responde `400 invalid document_id`.

Payload de `POST /api/v1/cessions/requeue`:

```json
{
  "cession_id": "66d1a2b3c4d5e6f708192a3b"
}
```

Notas:

- `POST /api/v1/cessions` requiere que el plan incluya cesiones y es cobrable (clave `cession`).
- `GET /api/v1/cessions` devuelve `data.cessions` (no `items`), `data.total`, `data.page` y `data.limit`; no trae `total_pages`.
- El objeto cesion mezcla snake_case (`business_id`, `document_id`, `factoring_*`, `status_history`) con camelCase (`processAttempts`, `statusProcess`, `statusSii`, `xmlCession`, `trackId`). `statusSii` es un string que suele contener JSON (por ejemplo `{"Glosa":"Documento Cedido"}`).
- `GET /api/v1/cessions/:id` responde `404 cession not found` si no existe y `403` si no pertenece al token. Un id que no es ObjectID responde `500 failed to get cession`, no `400`.
- `POST /api/v1/cessions/requeue` es cobrable, tiene el mismo limite de requeue que documentos (`429` con `Retry-After`) y responde `409` si la cesion ya figura como "Documento Cedido" en SII.

### Compras / acuse de recibo

| Metodo | Ruta | Proposito | Headers clave |
| --- | --- | --- | --- |
| `GET` | `/api/v1/purchase-acknowledgments` | Listar compras/facturas recibidas con filtros y paginacion | `x-api-key` |
| `POST` | `/api/v1/purchase-acknowledgments` | Crear acuse de recibo | `x-api-key`, `idempotency-key` (obligatorio) |
| `POST` | `/api/v1/purchase-acknowledgments/requeue` | Reprocesar acuse de recibo | `x-api-key` |

Query params de `GET /api/v1/purchase-acknowledgments`:

- `tipo_dte`
- `accion_doc`
- `from_date`
- `to_date`
- `page`, default `1`
- `limit`, default `20`, max `100`

Payload de `POST /api/v1/purchase-acknowledgments/requeue`:

```json
{
  "purchase_id": "66c1a2b3c4d5e6f708192a3b"
}
```

Notas:

- `POST /api/v1/purchase-acknowledgments` requiere que el plan incluya compras y es cobrable (clave `purchase`).
- `POST /api/v1/purchase-acknowledgments/requeue` es cobrable, tiene el limite de requeue (`429` con `Retry-After`) y responde `409` si la compra ya tiene respuesta del SII.

### Billing

| Metodo | Ruta | Proposito | Headers clave |
| --- | --- | --- | --- |
| `GET` | `/api/v1/billing/balance` | Obtener modo de cobro (`billing_mode`) y, segun el modo, plan con cupo mensual por bucket (`usage.documentos`, `usage.consultas`) o consumo acumulado del mes (`on_demand`) | `x-api-key` |
| `GET` | `/api/v1/billing/payments` | Historial de pagos con filtros y paginacion | `x-api-key` |
| `GET` | `/api/v1/billing/charges` | Cargos por operacion (emision, cesion, compra, lectura, PDF) con filtros y paginacion | `x-api-key` |
| `GET` | `/api/v1/billing/plans` | Catalogo de planes activos con precio y cupos | `x-api-key` |
| `GET` | `/api/v1/billing/invoices` | Facturas del servicio de la empresa, por periodo | `x-api-key` |
| `GET` | `/api/v1/billing/subscription/upgrade/preview` | Cotizar el upgrade de plan a mitad de ciclo (prorrateado, no cobra) | `x-api-key` |

Query params de `GET /api/v1/billing/payments`:

- `status`
- `from_date`
- `to_date`
- `page`, default `1`
- `limit`, default `20`, max `100`

Query params de `GET /api/v1/billing/charges`:

- `status` (opcional, coincidencia exacta, no se valida): `reserved`, `charged`, `reverted`, `rejected_insufficient`, `rejected_quota`, `rejected_no_plan`, `rejected_subscription_expired`, `rejected_emission_suspended`, `impersonate_courtesy`, `app_frontend_courtesy`
- `pricing_key` (opcional): `emission`, `cession`, `purchase`, `read`, `pdf`
- `from_date`, `to_date` (`YYYY-MM-DD`, filtran por `created_at`; fecha invalida responde `400 invalid query params`)
- `page`, default `1`
- `limit`, default `20`, max `100`

Notas de billing:

- `GET /api/v1/billing/charges` devuelve `data.items`, `page`, `limit`, `total_items` y `total_pages`. Los montos (`unit_cost_micros`, `total_cost_micros`) vienen en micros enteros.
- `GET /api/v1/billing/plans` no tiene parametros y devuelve en `data` un **arreglo** (sin paginacion). Cada plan trae `id`, `code`, `name`, `value_uf`, `price_clp` (calculado con la UF del dia), `quotas` (`documentos`, `consultas`; `0` = ilimitado) y `features` (el tipo de `value` varia).
- `GET /api/v1/billing/invoices` acepta `status` opcional (`open`, `paid`, `void`), no pagina y devuelve un **arreglo** ordenado por `period` (`YYYY-MM`) descendente. `total_clp` ya trae el descuento aplicado.
- `GET /api/v1/billing/subscription/upgrade/preview` exige `plan_id` en query (id o `code` del plan). Devuelve `amount_clp` prorrateado por `days_remaining`, `delta_clp`, `amount_uf` y `payable` (`false` si el monto es `< 1`). Errores: `400 plan_id is required`, `409 NO_SUBSCRIPTION_PLAN`, `409 UPGRADE_NOT_PRORATABLE`, `404 PLAN_NOT_AVAILABLE`, `409 NOT_AN_UPGRADE` (mismo plan o uno igual o mas barato).
- Ninguna de estas rutas es cobrable.

### Consumo

| Metodo | Ruta | Proposito | Headers clave |
| --- | --- | --- | --- |
| `GET` | `/api/v1/consumption` | Consumo del ciclo actual por bucket (`documentos`, `consultas`): limite, usado, restante, excedente y proyeccion | `x-api-key` |
| `GET` | `/api/v1/consumption/overages` | Listar excedentes (sobreconsumo) con paginacion | `x-api-key` |
| `GET` | `/api/v1/consumption/operations` | Detalle de cada operacion contada en un periodo | `x-api-key` |

Notas:

- `GET /api/v1/consumption` no tiene parametros. Devuelve `period`, `plan` (si hay), `tope_sobreconsumo_pct` y un objeto por bucket con `limit`, `used`, `remaining`, `tope_total`, `excedente`, `tarifa_exc_uf`, `excedente_uf` y `proyeccion`. `limit: 0` significa ilimitado y en ese caso `remaining` es `-1`. Las llaves estan en espanol.
- `GET /api/v1/consumption/overages` acepta `page` (default `1`) y `limit` (default `20`, max `100`). Devuelve `data.items` (`period`, `tipo`, `operacion_id`, `tarifa_uf`, `fecha`), `page`, `limit` y `total`; no trae `total_items` ni `total_pages`.
- `GET /api/v1/consumption/operations` acepta `period` opcional en formato `YYYY-MM` (default: mes actual UTC); un valor invalido responde `400` (`periodo invalido "<v>": usa YYYY-MM`). **No pagina**: devuelve todas las operaciones del periodo de la empresa del token en `data.items`, con `data.total`. Cada item trae `operation` (`emission`, `cession`, `purchase`, `read`, `pdf`), `status`, `counted` (`false` para operaciones revertidas o de certificacion), y cuando aplica `document_id`, `folio` y `code_sii`.
- Ninguna de estas rutas es cobrable.

## Endpoint routing rapido

Si el usuario dice esto, probablemente quiere esto:

- "crear usuario externo" -> `POST /api/v1/provisioning/users`
- "crear empresa por provisioning" -> `POST /api/v1/provisioning/users/:user_id/businesses`
- "la API esta arriba / que version corre / health check" -> `GET /api/v1/health`
- "login / obtener x-user-key" -> `POST /api/v1/auth/login`
- "crear mi primera empresa / onboarding sin x-api-key" -> `POST /api/v1/onboarding/businesses`
- "ver mi usuario" -> `GET /api/v1/users/me`
- "listar empresas" -> `GET /api/v1/businesses`
- "crear empresa (ya tengo x-api-key)" -> `POST /api/v1/businesses`
- "editar empresa" -> `PUT /api/v1/businesses/:id`
- "detalle empresa" -> `GET /api/v1/businesses/:id`
- "pasar a produccion" -> `POST /api/v1/businesses/production-mode`
- "volver a certificacion" -> `POST /api/v1/businesses/certification-mode`
- "subir certificado" -> `PUT /api/v1/business/:id/certificate`
- "estado del certificado / puedo emitir" -> `GET /api/v1/business/certificate-info`
- "descargar certificado" -> no existe; la API no entrega el certificado ni su llave
- "emitir factura / boleta / nota / guia" -> `POST /api/v1/documents`
- "modificar documento" -> `PUT /api/v1/documents/:id`
- "listar documentos" -> `GET /api/v1/documents`
- "traer documento" -> `GET /api/v1/documents/:id`
- "estadisticas" -> `GET /api/v1/documents/stats`
- "reprocesar documento" -> `POST /api/v1/documents/requeue`
- "consultar estado offline" -> `POST /api/v1/documents/requeue/status`
- "cargar CAF" -> `PUT /api/v1/numerations`
- "ver folios" -> `GET /api/v1/numerations/summary`
- "listar rangos CAF" -> `GET /api/v1/numerations/ranges`
- "ajustar proximo folio" -> `PATCH /api/v1/numerations/:numerationId/next-number`
- "configurar umbral de folios bajos / alerta de folios / recarga automatica de folios" -> `PATCH /api/v1/numerations/low-stock`
- "eliminar rango CAF" -> `DELETE /api/v1/numerations/:numerationId`
- "ultimo folio" -> `GET /api/v1/numerations/last-used-number`
- "pedir folios offline" -> `POST /api/v1/numerations/request`
- "generar PDF" -> `POST /api/v1/pdfs/generate`
- "crear cesion" -> `POST /api/v1/cessions`
- "reprocesar cesion" -> `POST /api/v1/cessions/requeue`
- "listar cesiones / ya cedi este documento?" -> `GET /api/v1/cessions` (con `document_id` para un documento)
- "ver una cesion / estado de la cesion" -> `GET /api/v1/cessions/:id`
- "acuse de recibo" -> `POST /api/v1/purchase-acknowledgments`
- "reprocesar acuse" -> `POST /api/v1/purchase-acknowledgments/requeue`
- "listar compras recibidas" -> `GET /api/v1/purchase-acknowledgments`
- "saldo / balance / cupo del plan" -> `GET /api/v1/billing/balance`
- "pagos" -> `GET /api/v1/billing/payments`
- "cargos / cobros por operacion / que me cobraron" -> `GET /api/v1/billing/charges`
- "planes disponibles / precios" -> `GET /api/v1/billing/plans`
- "facturas del servicio / mis facturas de IntegraDTE" -> `GET /api/v1/billing/invoices`
- "cuanto cuesta subir de plan / cotizar upgrade" -> `GET /api/v1/billing/subscription/upgrade/preview?plan_id=...`
- "consumo del mes / cuanto llevo usado" -> `GET /api/v1/consumption`
- "sobreconsumo / excedentes" -> `GET /api/v1/consumption/overages`
- "detalle de consumo / operaciones cobradas del periodo" -> `GET /api/v1/consumption/operations?period=YYYY-MM`

## Errores comunes

### 401

```json
{
  "success": false,
  "message": "x-api-key header is required",
  "code": "API_KEY_MISSING"
}
```

Otros codigos de `x-api-key`: `401 API_KEY_INVALID`, `403 API_KEY_NOT_ACTIVE`, `403 API_KEY_EXPIRED`. Los rechazos de `x-user-key` no traen `code` (`401 "x-user-key header is required"` o `401 "invalid user token"`).

### 403

```json
{
  "success": false,
  "message": "document does not belong to the authenticated context"
}
```

### 400

```json
{
  "success": false,
  "message": "invalid query params"
}
```

Falta de `idempotency-key` en una ruta que lo exige:

```json
{
  "success": false,
  "message": "idempotency-key header is required"
}
```

Error de validacion (el detalle viene en `details.errors`):

```json
{
  "success": false,
  "message": "Validation error",
  "details": {
    "success": false,
    "message": "Validation failed",
    "errors": [
      { "field": "email", "tag": "required", "value": "", "message": "Field 'email' is required" }
    ]
  }
}
```

### 402 / 429 de cobro

En rutas cobrables (emision, requeues, cesiones, acuses y PDF):

- `402` con `code` `COBRANZA_OVERDUE` (se levanta pagando), `ACCOUNT_BLOCKED` (lo levanta quien lo puso; no mandar a pagar), `EMISSION_SUSPENDED`, `SUBSCRIPTION_EXPIRED` o `BILLING_NOT_CONFIGURED`.
- `429` con `code` `PLAN_QUOTA_EXCEEDED` cuando se agota el cupo del plan.

### 429 por rate limit

```json
{
  "success": false,
  "message": "Se supero el limite de ...",
  "code": "RATE_LIMIT_EXCEEDED",
  "error": { "code": "RATE_LIMIT_EXCEEDED", "scope": "account", "class": "write", "limit": 0, "window_seconds": 0, "retry_after_seconds": 0 }
}
```

Viene con header `Retry-After`. El limite de requeue responde distinto: sin `message` en la raiz y con `error.code` en minusculas (`rate_limit_exceeded`).
