# Endpoints de IntegraDTE

Fuentes:

- Coleccion Postman `IntegraDTE API` entregada por el usuario.
- `/Users/joseluis/Desktop/projects/jose/full-dte/integradte-api-client/endpoints.md`: API publica de clientes (`x-api-key`).
- `/Users/joseluis/Desktop/projects/jose/full-dte/full-dte-api-sii/endpoints.md`: API interna, fuente de las rutas de provisioning.

Base URL publica sugerida: `https://api.integradte.cl`
Base API v1: `https://api.integradte.cl/api/v1`

En ejemplos locales del backend puede aparecer `http://localhost:3000`.

## Indice

- [Variables observadas](#variables-observadas)
- [Headers habituales](#headers-habituales)
- [Provisioning](#provisioning)
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
- [Endpoint routing rapido](#endpoint-routing-rapido)
- [Errores comunes](#errores-comunes)

## Variables observadas

- `x_api_key`
- `provisioning_api_key`
- `idempotency_key`
- `user_id`
- `business_id`
- `business_id_exento`
- `document_id`
- `range_id`
- `code_sii`
- `numeration_id`
- `certificate`
- `password`
- `expired_date`

## Headers habituales

### Lectura privada

- `x-api-key`

### Escritura privada

- `Content-Type: application/json`
- `x-api-key`
- `idempotency-key` cuando el endpoint lo documenta o la operacion debe ser idempotente

### Provisioning

- `x-provisioning-key`
- `idempotency-key`
- `Content-Type: application/json`

Las rutas de provisioning son para aplicaciones externas que crean usuarios y empresas antes de que exista un `x-api-key`. No aceptan `x-api-key` como sustituto.

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

### Usuario

| Metodo | Ruta | Proposito |
| --- | --- | --- |
| `GET` | `/api/v1/users/me` | Obtener usuario autenticado por `x-api-key` |

### Empresas

| Metodo | Ruta | Proposito | Headers clave |
| --- | --- | --- | --- |
| `GET` | `/api/v1/businesses` | Listar empresas del usuario autenticado | `x-api-key` |
| `POST` | `/api/v1/businesses` | Crear empresa para el usuario autenticado | `x-api-key`, `idempotency-key` |
| `GET` | `/api/v1/businesses/:id` | Obtener detalle de empresa propia | `x-api-key` |
| `PUT` | `/api/v1/businesses/:id` | Editar empresa propia | `x-api-key`, `idempotency-key` |
| `POST` | `/api/v1/businesses/production-mode` | Pasar empresa del token a produccion | `x-api-key` |
| `POST` | `/api/v1/businesses/certification-mode` | Volver empresa del token a certificacion | `x-api-key` |
| `PUT` | `/business/{business_id}/certificate` | Subir certificado digital | `x-api-key`, `idempotency-key` |
| `GET` | `/business/certificate-info` | Saber si la empresa tiene certificado valido para firmar | `x-api-key` |

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

| Metodo | Ruta | Proposito |
| --- | --- | --- |
| `PUT` | `/business/{business_id}/certificate` | Subir certificado digital |
| `GET` | `/business/certificate-info` | Saber si la empresa tiene certificado valido para firmar |

Respuesta de `GET /business/certificate-info`:

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
| `POST` | `/documents/` | Emitir documento | `x-api-key`, `idempotency-key` |
| `PUT` | `/documents/{id}` | Modificar documento | `x-api-key`, `idempotency-key` |
| `GET` | `/api/v1/documents` | Listar documentos con filtros y paginacion | `x-api-key` |
| `GET` | `/api/v1/documents/:id` | Obtener detalle de documento propio | `x-api-key` |
| `GET` | `/api/v1/documents/stats` | Obtener estadisticas de documentos | `x-api-key` |
| `POST` | `/api/v1/documents/requeue` | Reencolar documento normal en cola `dte` | `x-api-key` |

Query params de `GET /api/v1/documents`:

- `code_sii`
- `status`
- `from_date` con formato `YYYY-MM-DD`
- `to_date` con formato `YYYY-MM-DD`
- `page`, default `1`
- `limit`, default `20`, max `100`

Query params de `GET /api/v1/documents/stats`:

- `code_sii`
- `status`
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

Payload de `POST /api/v1/documents/requeue`:

```json
{
  "document_id": "67b111a6c1f0e36f7f4a0101"
}
```

Notas:

- `POST /api/v1/documents/requeue` valida ownership y bloquea el requeue si el documento ya fue recibido o procesado por SII.
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

### Numeracion / CAF / folios

| Metodo | Ruta | Proposito | Respuesta |
| --- | --- | --- | --- |
| `PUT` | `/api/v1/numerations` | Crear o agregar un rango de folios CAF para un tipo de DTE | wrapper `success/data` |
| `GET` | `/api/v1/numerations/summary` | Resumen de folios disponibles, agotados y vencidos | wrapper `success/data` |
| `GET` | `/api/v1/numerations/ranges` | Listar rangos CAF agrupados por tipo de DTE, con usados/disponibles | wrapper `success/data` |
| `PATCH` | `/api/v1/numerations/:numerationId/next-number` | Ajustar el proximo folio a emitir de un rango CAF | wrapper `success/data` |
| `DELETE` | `/api/v1/numerations/:numerationId` | Eliminar un rango de folios CAF | wrapper `success/data` |
| `GET` | `/numerations/last-used-number?code_sii={code_sii}` | Obtener ultimo folio usado (observado en Postman) | wrapper `success/data` |
| `POST` | `/api/v1/numerations/request` | Reservar y devolver rangos de folios disponibles | arreglo JSON plano |

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
- Soporta `idempotency-key` (opcional) para no duplicar la creacion ante reintentos.
- El ambiente (`isProduction`) lo determina el backend segun el modo de la empresa (`isProd`); no se toma del body.
- `caf_base64` es el XML del CAF entregado por el SII, en base64.
- `end_number` debe ser mayor o igual a `start_number`; si no, responde `400`.

Query params de `GET /api/v1/numerations/ranges`:

- `code_sii` (opcional): filtra por un tipo de DTE; si se omite, devuelve todos.

Notas de `GET /api/v1/numerations/ranges`:

- Requiere `x-api-key`.
- Devuelve los rangos agrupados por `code_sii` con `total_folios`, `used_folios`, `available` y el detalle de cada rango (`start_number`, `end_number`, `last_number`, `available`, `is_exhausted`, `due_date`, `is_expired`, `created_at`).
- El CAF base64 se omite a proposito para no exponer el material de firma.
- El ambiente (produccion/certificacion) lo determina el backend segun el modo de la empresa.

Payload de `PATCH /api/v1/numerations/:numerationId/next-number`:

```json
{
  "next_number": 150
}
```

Notas de `PATCH /api/v1/numerations/:numerationId/next-number`:

- `next_number` es el folio que recibira el siguiente documento; internamente el contador se guarda como `next_number - 1`.
- Requiere `x-api-key`. No disponible para sesiones impersonadas (bloqueadas para escritura). Soporta `idempotency-key` (opcional).
- `:numerationId` debe ser un ObjectID valido (si no, responde `400`).
- `next_number` debe caer dentro del rango del CAF; fuera de rango responde `400`.
- Si el rango no existe responde `404`; ante actualizacion concurrente responde `409` (reintentar).

Notas de `DELETE /api/v1/numerations/:numerationId`:

- Requiere `x-api-key`. No disponible para sesiones impersonadas. Soporta `idempotency-key` (opcional).
- `:numerationId` debe ser un ObjectID valido (si no, responde `400`).
- Si el rango no existe responde `404`.

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

| Metodo | Ruta | Proposito |
| --- | --- | --- |
| `POST` | `/pdfs/generate` | Generar PDF |

### Cesiones

| Metodo | Ruta | Proposito |
| --- | --- | --- |
| `POST` | `/cessions/` | Crear cesion |
| `POST` | `/cessions/requeue` | Reprocesar cesion |

### Compras / acuse de recibo

| Metodo | Ruta | Proposito |
| --- | --- | --- |
| `GET` | `/api/v1/purchase-acknowledgments` | Listar compras/facturas recibidas con filtros y paginacion |
| `POST` | `/purchase-acknowledgments` | Crear acuse de recibo |
| `POST` | `/purchase-acknowledgments/requeue` | Reprocesar acuse de recibo |

Query params de `GET /api/v1/purchase-acknowledgments`:

- `tipo_dte`
- `accion_doc`
- `from_date`
- `to_date`
- `page`, default `1`
- `limit`, default `20`, max `100`

### Billing

| Metodo | Ruta | Proposito |
| --- | --- | --- |
| `GET` | `/api/v1/billing/balance` | Obtener modo de cobro (`billing_mode`) y, segun el modo, plan con cupo mensual por bucket (`usage.documentos`, `usage.consultas`) o consumo acumulado del mes (`on_demand`) |
| `GET` | `/api/v1/billing/payments` | Historial de pagos con filtros y paginacion |

Query params de `GET /api/v1/billing/payments`:

- `status`
- `from_date`
- `to_date`
- `page`, default `1`
- `limit`, default `20`, max `100`

## Endpoint routing rapido

Si el usuario dice esto, probablemente quiere esto:

- "crear usuario externo" -> `POST /api/v1/provisioning/users`
- "crear empresa por provisioning" -> `POST /api/v1/provisioning/users/:user_id/businesses`
- "ver mi usuario" -> `GET /api/v1/users/me`
- "listar empresas" -> `GET /api/v1/businesses`
- "crear empresa" -> `POST /api/v1/businesses`
- "editar empresa" -> `PUT /api/v1/businesses/:id`
- "detalle empresa" -> `GET /api/v1/businesses/:id`
- "pasar a produccion" -> `POST /api/v1/businesses/production-mode`
- "volver a certificacion" -> `POST /api/v1/businesses/certification-mode`
- "subir certificado" -> `PUT /business/{business_id}/certificate`
- "estado del certificado / puedo emitir" -> `GET /business/certificate-info`
- "descargar certificado" -> no existe; la API no entrega el certificado ni su llave
- "emitir factura / boleta / nota / guia" -> `POST /documents/`
- "modificar documento" -> `PUT /documents/{id}`
- "listar documentos" -> `GET /api/v1/documents`
- "traer documento" -> `GET /api/v1/documents/:id`
- "estadisticas" -> `GET /api/v1/documents/stats`
- "reprocesar documento" -> `POST /api/v1/documents/requeue`
- "consultar estado offline" -> `POST /api/v1/documents/requeue/status`
- "cargar CAF" -> `PUT /api/v1/numerations`
- "ver folios" -> `GET /api/v1/numerations/summary`
- "listar rangos CAF" -> `GET /api/v1/numerations/ranges`
- "ajustar proximo folio" -> `PATCH /api/v1/numerations/:numerationId/next-number`
- "eliminar rango CAF" -> `DELETE /api/v1/numerations/:numerationId`
- "ultimo folio" -> `GET /numerations/last-used-number`
- "pedir folios offline" -> `POST /api/v1/numerations/request`
- "generar PDF" -> `POST /pdfs/generate`
- "crear cesion" -> `POST /cessions/`
- "acuse de recibo" -> `POST /purchase-acknowledgments`
- "listar compras recibidas" -> `GET /api/v1/purchase-acknowledgments`
- "saldo / balance / cupo del plan" -> `GET /api/v1/billing/balance`
- "pagos" -> `GET /api/v1/billing/payments`

## Errores comunes

### 401

```json
{
  "success": false,
  "message": "x-api-key header is required"
}
```

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
