---
name: integradte-skills
description: Ayuda a trabajar con la API de integradte.cl. Usa esta skill cuando el usuario quiera emitir, modificar, reprocesar o consultar DTEs, cargar CAF, generar PDFs, gestionar empresas o certificados, o cuando pida armar payloads, endpoints, headers o ejemplos para IntegraDTE e integración con SII. Actívala también si el usuario menciona tipos DTE chilenos como 33, 34, 39, 41, 46, 52, 56 o 61, aunque no diga explícitamente "skill" ni "integradte.cl".
---

# IntegraDTE

Usa esta skill para construir requests correctos hacia `https://api.integradte.cl/api/v1`, apoyándote en el Postman oficial y en los archivos locales del repo que documentan campos SII por tipo de documento.

## Objetivo

Resolver tareas de integración con IntegraDTE sin inventar endpoints ni payloads.

Esto incluye:

- elegir el endpoint correcto
- incluir headers obligatorios
- mapear `code_sii` al tipo de documento correcto
- proponer payloads mínimos y payloads razonables
- validar campos contra la estructura SII disponible en los markdown del proyecto
- diferenciar cuándo usar `data_dte_json` y cuándo el ejemplo oficial usa `data_dte`

## Flujo

### 1. Identifica la intención

Primero clasifica la tarea del usuario en una de estas categorías:

- empresas
- certificados
- usuario autenticado
- emisión o modificación de DTE
- consulta o estadísticas de documentos
- numeración / CAF / folios
- PDFs
- cesiones
- compras / acuse de recibo

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
- `idempotency-key: <valor-unico>` en operaciones de escritura donde el Postman lo usa

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

- usar endpoint `POST /documents/`
- incluir `x-api-key` e `idempotency-key`
- usar `code_sii: "33"`
- construir `data_dte_json` con `Encabezado` y `Detalle`
- basarse en los campos de factura 33

**Ejemplo 2:**
Usuario: "Qué endpoint uso para saber el último folio disponible del tipo 52?"
Acción esperada:

- usar `GET /numerations/last-used-number?code_sii=52`
- explicar query param y header `x-api-key`

**Ejemplo 3:**
Usuario: "Revísame este payload de boleta 39 porque el SII me lo rechaza."
Acción esperada:

- validar que el tipo sea `39`
- revisar estructura de boleta, totales y detalle
- comparar contra los campos del archivo local de boleta 39
- señalar faltantes o inconsistencias concretas

## Referencias locales

Lee solo lo necesario:

- `references/endpoints.md`: resumen de endpoints y headers del Postman
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
