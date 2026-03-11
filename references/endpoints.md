# Endpoints de IntegraDTE

Fuente: colección Postman `IntegraDTE API` entregada por el usuario.
Base URL: `https://api.integradte.cl/api/v1`

## Variables observadas

- `x_api_key`
- `idempotency_key`
- `user_id`
- `business_id`
- `business_id_exento`
- `document_id`
- `range_id`
- `code_sii`

## Headers habituales

### Lectura
- `x-api-key`

### Escritura
- `Content-Type: application/json`
- `x-api-key`
- `idempotency-key`

## Endpoints por categoría

### Empresas
- `POST /businesses` crear empresa
- `PUT /businesses/{business_id}` editar empresa
- `PUT /business/{business_id}/certificate` subir certificado digital
- `GET /business/certificate-info` obtener info del certificado

Payload mínimo para crear empresa:
```json
{
  "name": "EMPRESA EJEMPLO SA",
  "rut": "76633249-8"
}
```

### Usuario
- `GET /users/me` obtener usuario autenticado

### Documentos DTE
- `POST /documents/` emitir documento
- `PUT /documents/{id}` modificar documento
- `GET /documents/{document_id}` obtener documento por ID
- `GET /documents/stats` estadísticas
- `POST /documents/requeue` reprocesar documento

Payload observado para emisión con JSON estructurado:
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

### Numeración / CAF
- `PUT /numerations` cargar CAF
- `GET /numerations/summary` resumen de folios
- `GET /numerations/last-used-number?code_sii={code_sii}` último folio usado
- `DELETE /numerations/{range_id}` eliminar rango

### PDFs
- `POST /pdfs/generate` generar PDF

### Cesiones
- `POST /cessions/` crear cesión
- `POST /cessions/requeue` reprocesar cesión

### Compras / acuse de recibo
- `POST /purchase-acknowledgments` crear acuse de recibo
- `POST /purchase-acknowledgments/requeue` reprocesar acuse de recibo

## Endpoint routing rápido

Si el usuario dice esto, probablemente quiere esto:

- "crear empresa" -> `POST /businesses`
- "editar empresa" -> `PUT /businesses/{business_id}`
- "subir certificado" -> `PUT /business/{business_id}/certificate`
- "ver mi usuario" -> `GET /users/me`
- "emitir factura / boleta / nota / guía" -> `POST /documents/`
- "modificar documento" -> `PUT /documents/{id}`
- "traer documento" -> `GET /documents/{document_id}`
- "estadísticas" -> `GET /documents/stats`
- "reprocesar documento" -> `POST /documents/requeue`
- "cargar CAF" -> `PUT /numerations`
- "ver folios" -> `GET /numerations/summary`
- "último folio" -> `GET /numerations/last-used-number`
- "generar PDF" -> `POST /pdfs/generate`
- "crear cesión" -> `POST /cessions/`
- "acuse de recibo" -> `POST /purchase-acknowledgments`
