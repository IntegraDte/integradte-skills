---
name: integradte-skills
description: Ayuda a trabajar con la API de integradte.cl. Usa esta skill cuando el usuario quiera emitir, modificar, reprocesar o consultar DTEs, cargar o pedir folios/CAF, configurar umbrales de folios bajos y recarga automática, generar PDFs, login y onboarding de la primera empresa, gestionar usuarios por provisioning, empresas, certificados, cesiones, compras/acuse, billing, consumo del plan, modo certificación/producción, o cuando pida armar payloads, endpoints, headers o ejemplos para IntegraDTE e integración con SII. Actívala también si el usuario menciona tipos DTE chilenos como 33, 34, 39, 41, 46, 52, 56 o 61, aunque no diga explícitamente "skill" ni "integradte.cl".
---

# IntegraDTE

Usa esta skill para construir requests correctos hacia `https://api.integradte.cl/api/v1`, apoyándote en el Postman oficial, en la referencia local de endpoints y en los archivos locales del repo que documentan campos SII por tipo de documento.

## Objetivo

Resolver tareas de integración con IntegraDTE sin inventar endpoints ni payloads.

Esto incluye:

- elegir el endpoint correcto
- incluir headers obligatorios
- distinguir rutas privadas y rutas de provisioning
- mapear `code_sii` al tipo de documento correcto
- proponer payloads mínimos y payloads razonables
- validar campos contra la estructura SII disponible en los markdown del proyecto
- diferenciar cuándo usar `data_dte_json` y cuándo el ejemplo oficial usa `data_dte`
- diferenciar respuestas con wrapper `success/data` de respuestas JSON planas

## Flujo

### 1. Identifica la intención

Primero clasifica la tarea del usuario en una de estas categorías:

- salud de la API, login y onboarding de la primera empresa (`x-user-key`)
- empresas
- provisioning de usuarios/empresas
- certificados
- usuario autenticado
- emisión o modificación de DTE
- consulta, estadísticas o requeue (online y offline) de documentos
- numeración / CAF / folios / umbral de folios bajos y recarga automática
- PDFs
- cesiones (crear, reprocesar, listar, ver detalle)
- compras / acuse de recibo
- billing / balance / pagos / cargos / planes / facturas / cotizar upgrade
- consumo y sobreconsumo del ciclo
- ambiente de certificación o producción

Luego abre `references/endpoints.md`.

### 2. Determina el tipo DTE si aplica

Si la tarea involucra un documento tributario, identifica el `code_sii` y el nombre del documento.

Usa `references/documentos.md` para mapear:

- `33` factura electrónica
- `34` factura exenta
- `39` boleta electrónica
- `41` boleta exenta
- `46` factura de compra
- `52` guía de despacho
- `56` nota de débito
- `61` nota de crédito

### 3. Usa el nivel correcto de payload

Aplica estas reglas:

- Si el usuario pide un ejemplo corto o una base para integrar, entrega un payload mínimo válido.
- Si el usuario pide un payload listo para negocio, usa los ejemplos JSON razonables listados en `references/documentos.md`.
- Si el usuario pide validar o completar campos específicos del SII, consulta el `.md` del tipo correspondiente y usa solo campos documentados allí.
- No agregues `TED`, `Signature`, `TmstFirma` ni bloques de firma manualmente. Este proyecto ya fue depurado para enfocarse en datos del documento.

### 4. Respeta la convención de headers

Para requests autenticados, usa como base:

- `x-api-key: <api_key>`
- `Content-Type: application/json` cuando corresponda
- `idempotency-key: <UUID>` **obligatorio** en `POST /api/v1/documents`, `PUT /api/v1/documents/:id`, `POST /api/v1/businesses`, `PUT /api/v1/businesses/:id`, `PUT /api/v1/business/:id/certificate`, `PUT /api/v1/numerations`, `DELETE /api/v1/numerations/:numerationId`, `PATCH /api/v1/numerations/:numerationId/next-number`, `PATCH /api/v1/numerations/low-stock`, `POST /api/v1/purchase-acknowledgments` y `POST /api/v1/cessions`. Sin el header la API responde `400 "idempotency-key header is required"`; un valor que no es UUID también da `400`. No lo presentes como opcional en esas rutas. Usa un UUID nuevo por operación lógica (y siempre uno nuevo en cada intento de `PUT /api/v1/documents/:id`).

Para el onboarding de la primera empresa (`POST /api/v1/onboarding/businesses`), usa `x-user-key` (obtenido con `POST /api/v1/auth/login`), no `x-api-key`.

Para provisioning, usa:

- `x-provisioning-key: <provisioning_api_key>`
- `idempotency-key: <valor-unico>`
- `Content-Type: application/json`

No reemplaces `x-provisioning-key` con `x-api-key`.

Si el usuario pide código, explica qué headers son obligatorios y cuál es su propósito.

### 5. Prefiere precisión sobre completitud falsa

Si falta un dato crítico, dilo explícitamente.

Por ejemplo:

- si falta `business_id`, no inventarlo
- si falta `user_id`, dejar placeholder claro
- si el endpoint del Postman usa `:id` o `{{document_id}}`, explicitar qué valor real debe ir ahí
- si un campo SII depende del tipo de documento, no extrapolar desde otro tipo sin advertirlo

## Reglas prácticas

- Prefiere `data_dte_json` cuando el ejemplo oficial lo permita; es más claro y menos propenso a errores de serialización.
- Si el Postman oficial muestra `data_dte` como string JSON escapado, menciona esa diferencia y, si es útil, entrega ambas variantes.
- Cuando el usuario pida “armar el endpoint”, responde con método, URL, headers y body.
- Cuando el usuario pida “qué campos van”, responde con una lista de campos mínimos y luego un ejemplo completo.
- Cuando el usuario pida “validar este payload”, revisa estructura, `TipoDTE`, totales y coherencia básica contra el tipo documentado.
- Cuando el usuario pida “crear documento X”, usa el archivo de referencia del tipo correcto antes de responder.
- Para pedir folios usa `POST /api/v1/numerations/request` y advierte que la respuesta es un arreglo JSON plano, no wrapper `success/data`; si no hay stock responde `[]`.
- Usa siempre la ruta completa con prefijo `/api/v1` (por ejemplo `POST /api/v1/documents`, `POST /api/v1/pdfs/generate`); la única ruta fuera de ese prefijo es el alias `GET /health`.
- Si un endpoint responde sin datos, la llave `data` no viene (no es `data: null`); pasa en `PUT /api/v1/numerations`, `DELETE /api/v1/numerations/:numerationId` y `PATCH /api/v1/numerations/:numerationId/next-number`. `GET /api/v1/health` responde JSON plano sin wrapper.
- Para `GET /api/v1/business/certificate-info`, la respuesta es solo `has_valid_certificate` (`true`/`false`), con la misma validación que aplica la emisión. La API no entrega el certificado, su contraseña ni la llave privada: `GET /api/v1/certificates/current` ya no existe y la empresa no incluye `certificate` ni `certificatePassword`.
- Para modo producción, recuerda que se requiere certificado digital vigente y resoluciones; los CAF de certificación no sirven en producción.
- Para numeración/CAF usa las rutas canónicas `PUT /api/v1/numerations` (cargar rango con `caf_base64`), `GET /api/v1/numerations/ranges` (listar rangos), `PATCH /api/v1/numerations/:numerationId/next-number` (resincronizar próximo folio) y `DELETE /api/v1/numerations/:numerationId` (eliminar rango). El ambiente lo determina el backend según `isProd`; no se envía en el body. `end_number` debe ser >= `start_number` y `next_number` debe caer dentro del rango.
- Para configurar el umbral de folios bajos o la recarga automática de folios usa `PATCH /api/v1/numerations/low-stock` con `x-api-key` e `idempotency-key`. Body: `items[]` con `code_sii` como **string** (`"33"`, `"34"`, `"39"`, `"41"`, `"46"`, `"52"`, `"56"`, `"61"`, sin repetir), `threshold` (entero >= 0) y `request_quantity` (entero >= 1), ambos obligatorios en cada item. Fusiona por código (los que no vienen se conservan) y la respuesta trae la configuración completa, con `null` en la mitad que falte de un código configurado a medias (ese código no se recarga).
- Para una empresa nueva sin `x-api-key`: `POST /api/v1/auth/login` (email + password, devuelve `data.xUserKey`) y luego `POST /api/v1/onboarding/businesses` con `x-user-key`, que devuelve `data.apiToken.xApiKey`. Si el usuario ya tiene empresa responde `409` y las siguientes se crean con `POST /api/v1/businesses`.
- Para consumo usa `GET /api/v1/consumption` (ciclo actual por bucket), `GET /api/v1/consumption/overages` (excedentes paginados) y `GET /api/v1/consumption/operations?period=YYYY-MM` (detalle sin paginar). Para billing: `GET /api/v1/billing/charges` (cargos por operación), `GET /api/v1/billing/plans`, `GET /api/v1/billing/invoices` y `GET /api/v1/billing/subscription/upgrade/preview?plan_id=...` (cotiza, no cobra).
- Para cesiones: `POST /api/v1/cessions` crea, `POST /api/v1/cessions/requeue` reprocesa, `GET /api/v1/cessions` lista (con `document_id` responde si ese documento ya fue cedido; la lista viene en `data.cessions`) y `GET /api/v1/cessions/:id` trae el detalle.
- Para `POST /api/v1/provisioning/users/:user_id/businesses`, el certificado puede cargarse opcionalmente en el mismo payload con `certificate` base64, `password` opcional y `expired_date` obligatorio solo si viene `certificate`.

## Respuesta recomendada

Cuando el usuario esté integrando la API, estructura la respuesta así:

1. Objetivo
2. Endpoint
3. Headers
4. Payload
5. Notas de validación

Si además pide código, agrega:

6. Ejemplo en el lenguaje solicitado

## Ejemplos de uso

**Ejemplo 1:**
Usuario: "Necesito emitir una factura 33 en IntegraDTE con Node."
Acción esperada:

- usar endpoint `POST /api/v1/documents`
- incluir `x-api-key` e `idempotency-key` (UUID, obligatorio)
- usar `code_sii: "33"`
- construir `data_dte_json` con `Encabezado` y `Detalle`
- basarse en los campos de factura 33

**Ejemplo 2:**
Usuario: "Qué endpoint uso para saber el último folio disponible del tipo 52?"
Acción esperada:

- usar `GET /api/v1/numerations/last-used-number?code_sii=52`
- explicar query param y header `x-api-key`

**Ejemplo 3:**
Usuario: "Revísame este payload de boleta 39 porque el SII me lo rechaza."
Acción esperada:

- validar que el tipo sea `39`
- revisar estructura de boleta, totales y detalle
- comparar contra los campos del archivo local de boleta 39
- señalar faltantes o inconsistencias concretas

**Ejemplo 4:**
Usuario: "Quiero que cuando me queden 20 facturas se pidan 100 folios más automáticamente."
Acción esperada:

- usar `PATCH /api/v1/numerations/low-stock`
- incluir `x-api-key` e `idempotency-key` (obligatorio)
- body `{"items":[{"code_sii":"33","threshold":20,"request_quantity":100}]}` con `code_sii` como string
- explicar que fusiona por código y que la respuesta trae la configuración completa

## Referencias locales

Lee solo lo necesario:

- `references/endpoints.md`: endpoints de la API pública por categoría, headers, payloads y errores
- `references/documentos.md`: mapa entre `code_sii`, markdowns SII y ejemplos JSON

Si necesitas profundidad de campos SII, consulta directamente estos archivos del repo:

- `source-md/DTE_33_Factura_Electronica.md`
- `source-md/DTE_34_Factura_Electronica_Exenta_No_Afecta.md`
- `source-md/BOLETA_39_Boleta_Electronica.md`
- `source-md/BOLETA_41_Boleta_Exenta_Electronica.md`
- `source-md/DTE_46_Factura_de_Compra_Electronica.md`
- `source-md/DTE_52_Guia_de_Despacho_Electronica.md`
- `source-md/DTE_56_Nota_de_Debito_Electronica.md`
- `source-md/DTE_61_Nota_de_Credito_Electronica.md`

Y para ejemplos de negocio:

- `examples-json/00_INDICE.md`
