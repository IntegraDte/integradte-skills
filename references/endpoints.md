# Endpoints de IntegraDTE

Fuentes:

- Coleccion Postman `IntegraDTE API` entregada por el usuario.
- `/Users/joseluis/Desktop/projects/jose/full-dte/full-dte-api-sii/endpoints.md` con rutas API v1 nuevas.

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
- [Documentos offline / sync](#documentos-offline--sync)
- [Numeracion / CAF / folios](#numeracion--caf--folios)
- [PDFs](#pdfs)
- [Cesiones](#cesiones)
- [Compras / acuse de recibo](#compras--acuse-de-recibo)
- [Billing](#billing)
- [Licencias offline](#licencias-offline)
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
- `license_id`
- `device_id`
- `device_fingerprint`

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
  "apiTokenName": "Token integracion externa"
}
```

Notas:

- El usuario queda `active` y `verify_at` seteado.
- La password se guarda hasheada y no se devuelve.
- La empresa queda `active` y en certificacion (`isProd: false`).
- `apiTokenName` es opcional.
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
| `GET` | `/business/certificate-info` | Obtener informacion del certificado | `x-api-key` |

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
| `GET` | `/api/v1/certificates/current` | Descargar certificado digital asociado al `x-api-key` actual |
| `PUT` | `/business/{business_id}/certificate` | Subir certificado digital |
| `GET` | `/business/certificate-info` | Obtener informacion del certificado |

`GET /api/v1/certificates/current` devuelve `certificate_base64`, `private_key_base64`, `source` y `downloaded_at`. Se usa para firmar XML en workers o servicios firmadores; guardar el material en cache segura.

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

### Documentos offline / sync

| Metodo | Ruta | Proposito | Respuesta |
| --- | --- | --- | --- |
| `POST` | `/api/v1/documents/sync` | Sincronizar DTE ya firmado hacia backend y persistir en `documents_offline` | JSON plano |
| `POST` | `/api/v1/documents/requeue/offline` | Reencolar documento offline en cola `offline_invoices` | wrapper `success/data` |
| `POST` | `/api/v1/documents/requeue/status` | Reencolar consulta de estado SII offline en cola `dte_status` | wrapper `success/data` |

Payload minimo de `POST /api/v1/documents/sync`:

```json
{
  "document_id": "DTE_33_xxx",
  "document_type": 33,
  "folio": 1001,
  "xml_base64": "BASE64_OF_SIGNED_XML",
  "pdf_base64": "BASE64_OF_RENDERED_PDF",
  "ted_xml_base64": "BASE64_OF_TED_XML",
  "generated_at": "2026-03-14T12:00:00Z",
  "raw_payload": {
    "Encabezado": {
      "IdDoc": {
        "TipoDTE": 33
      }
    }
  },
  "license": {
    "payload": {
      "license_id": "lic_123",
      "business_id": "biz_123",
      "device_id": "dev_123",
      "device_fingerprint": "machine-1",
      "business": {
        "business_name": "Empresa Demo SPA",
        "rut": "76000000-0",
        "is_prod": true
      },
      "features": ["dte", "sync", "signing"],
      "issued_at": "2026-03-01T00:00:00Z",
      "expires_at": "2026-04-01T00:00:00Z",
      "last_validated_at": "2026-03-14T11:55:00Z",
      "status": "active",
      "cli_min_version": "dev"
    },
    "signature": "BASE64_SIGNATURE"
  }
}
```

Payload de requeue offline/status:

```json
{
  "document_id": "67d5f2b3d59fcb4caa2b8d91"
}
```

Notas:

- `POST /api/v1/documents/sync` no genera XML ni consume folios.
- `POST /api/v1/documents/sync` guarda `raw_payload`, XML firmado, PDF si viene, TED y licencia enviada por cliente.
- Los requeues offline esperan el `_id` del documento en `documents_offline` y responden `404` si no pertenece a la empresa autenticada.

### Numeracion / CAF / folios

| Metodo | Ruta | Proposito | Respuesta |
| --- | --- | --- | --- |
| `PUT` | `/numerations` | Cargar CAF | wrapper `success/data` |
| `GET` | `/api/v1/numerations/summary` | Resumen de folios disponibles, agotados y vencidos | wrapper `success/data` |
| `GET` | `/numerations/last-used-number?code_sii={code_sii}` | Obtener ultimo folio usado | wrapper `success/data` |
| `DELETE` | `/numerations/{range_id}` | Eliminar rango | wrapper `success/data` |
| `POST` | `/v1/numbers/request` | Reservar y devolver rangos de folios disponibles | arreglo JSON plano |
| `POST` | `/v1/folios/request` | Alias temporal de `/v1/numbers/request` | arreglo JSON plano |
| `POST` | `/api/v1/numerations/request-rabbitmq` | Publicar solicitud de folios en RabbitMQ | wrapper `success/data` |

Payload de `POST /v1/numbers/request`:

```json
{
  "document_type": 33,
  "quantity": 4
}
```

Notas de `POST /v1/numbers/request`:

- Responde con arreglo JSON plano, sin wrapper `success/data`.
- Los rangos devueltos quedan inmediatamente reservados por la API.
- Los folios se marcan como usados para que no se entreguen otra vez ni se consuman en paralelo desde emision online.
- Si no hay stock responde `[]`.

Payload de `POST /api/v1/numerations/request-rabbitmq`:

```json
{
  "code_sii": "33",
  "quantity": 120
}
```

Notas de `POST /api/v1/numerations/request-rabbitmq`:

- Toma la empresa autenticada desde el token y publica `businessId`.
- Publica en la cola `request_numerations`.
- No reserva folios localmente ni devuelve rangos disponibles.

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
| `GET` | `/api/v1/billing/balance` | Obtener saldo DTE por packs activos, restantes y expiracion |
| `GET` | `/api/v1/billing/payments` | Historial de pagos con filtros y paginacion |

Query params de `GET /api/v1/billing/payments`:

- `status`
- `from_date`
- `to_date`
- `page`, default `1`
- `limit`, default `20`, max `100`

### Licencias offline

Modelo:

- Una empresa puede tener muchas licencias.
- Cada licencia autoriza un solo dispositivo activo.
- No existen `plans`, `subscriptions` ni `customers`.
- El payload firmado usa `business_id`.
- La firma es Ed25519.

Colecciones Mongo observadas:

- `offline_licenses`
- `offline_license_devices`
- `offline_license_activation_logs`
- `offline_license_refresh_logs`
- `offline_license_audit_logs`

| Metodo | Ruta | Proposito | Alias |
| --- | --- | --- | --- |
| `POST` | `/api/v1/licenses` | Crear licencia offline para la empresa autenticada | |
| `GET` | `/api/v1/licenses` | Listar licencias offline de la empresa autenticada | |
| `GET` | `/api/v1/licenses/:id` | Obtener licencia especifica | |
| `GET` | `/api/v1/licenses/:id/devices` | Listar dispositivos de una licencia | |
| `POST` | `/api/v1/licenses/:id/enable` | Reactivar licencia deshabilitada | |
| `POST` | `/api/v1/licenses/:id/disable` | Deshabilitar licencia sin revocarla | |
| `POST` | `/api/v1/licenses/:id/revoke` | Revocar licencia definitivamente | |
| `POST` | `/api/v1/licenses/activate` | Activar licencia offline y devolver licencia firmada | `/v1/licenses/activate` |
| `POST` | `/api/v1/licenses/refresh` | Refrescar, denegar o revocar licencia offline | `/v1/licenses/refresh` |
| `POST` | `/v1/licenses/activate` | Alias compatible con cliente offline para activar licencia | `/api/v1/licenses/activate` |
| `POST` | `/v1/licenses/refresh` | Alias compatible con cliente offline para refrescar licencia | `/api/v1/licenses/refresh` |

Payload de crear licencia:

```json
{
  "name": "Caja 01",
  "device_fingerprint": "motherboard-abc",
  "features": ["dte", "sync", "signing"],
  "cli_min_version": "1.0.0",
  "validity_hours": 360
}
```

Notas:

- Si no se envia `license_key`, la API lo genera.
- Si no se envia `features`, usa `["dte", "sync", "signing"]`.
- Si se omite `device_id`, la API usa internamente el mismo valor de `device_fingerprint`.

Payload de activar licencia:

```json
{
  "license_key": "ABCDE-12345-FGHIJ",
  "device_id": "machine-id",
  "machine_fingerprint": "machine-id",
  "hostname": "pc-contabilidad-01",
  "platform": "windows",
  "arch": "amd64",
  "cli_version": "1.0.0"
}
```

Payload de refrescar licencia:

```json
{
  "device_id": "machine-id",
  "machine_fingerprint": "machine-id",
  "cli_version": "1.0.0",
  "license": {
    "payload": {
      "license_id": "lic_01ABC",
      "business_id": "biz_123",
      "device_id": "machine-id",
      "device_fingerprint": "machine-id",
      "features": ["dte", "sync", "signing"],
      "issued_at": "2026-03-14T12:00:00Z",
      "expires_at": "2026-03-29T12:00:00Z",
      "last_validated_at": "2026-03-14T12:00:00Z",
      "status": "active",
      "cli_min_version": "1.0.0"
    },
    "signature": "BASE64_ED25519_SIGNATURE"
  }
}
```

Respuestas posibles de refresh:

- `{"action": "renewed", "reason": "ok", "license": {...}}`
- `{"action": "deny", "reason": "device_limit_exceeded"}`
- `{"action": "revoke", "reason": "license_revoked"}`

Payload de enable/disable/revoke:

```json
{
  "reason": "manual_enable"
}
```

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
- "descargar certificado actual" -> `GET /api/v1/certificates/current`
- "emitir factura / boleta / nota / guia" -> `POST /documents/`
- "modificar documento" -> `PUT /documents/{id}`
- "listar documentos" -> `GET /api/v1/documents`
- "traer documento" -> `GET /api/v1/documents/:id`
- "estadisticas" -> `GET /api/v1/documents/stats`
- "reprocesar documento" -> `POST /api/v1/documents/requeue`
- "sincronizar documento offline" -> `POST /api/v1/documents/sync`
- "reprocesar offline" -> `POST /api/v1/documents/requeue/offline`
- "consultar estado offline" -> `POST /api/v1/documents/requeue/status`
- "cargar CAF" -> `PUT /numerations`
- "ver folios" -> `GET /api/v1/numerations/summary`
- "ultimo folio" -> `GET /numerations/last-used-number`
- "pedir folios offline" -> `POST /v1/numbers/request`
- "pedir folios por RabbitMQ" -> `POST /api/v1/numerations/request-rabbitmq`
- "generar PDF" -> `POST /pdfs/generate`
- "crear cesion" -> `POST /cessions/`
- "acuse de recibo" -> `POST /purchase-acknowledgments`
- "listar compras recibidas" -> `GET /api/v1/purchase-acknowledgments`
- "saldo / balance / packs" -> `GET /api/v1/billing/balance`
- "pagos" -> `GET /api/v1/billing/payments`
- "crear licencia offline" -> `POST /api/v1/licenses`
- "listar licencias" -> `GET /api/v1/licenses`
- "activar licencia" -> `POST /api/v1/licenses/activate` o alias `/v1/licenses/activate`
- "refrescar licencia" -> `POST /api/v1/licenses/refresh` o alias `/v1/licenses/refresh`
- "habilitar licencia" -> `POST /api/v1/licenses/:id/enable`
- "deshabilitar licencia" -> `POST /api/v1/licenses/:id/disable`
- "revocar licencia" -> `POST /api/v1/licenses/:id/revoke`

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
