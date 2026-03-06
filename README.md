# integrafacturacion-skills

Skill para ayudar a integrar [`integrafacturacion.cl`](https://api.integrafacturacion.cl/api/v1) con foco en:
- emisión y modificación de DTEs
- consulta de documentos y estadísticas
- carga de CAF y manejo de folios
- empresas, certificados, PDFs, cesiones y acuse de recibo
- validación de payloads usando la estructura SII disponible en este repo

## Instalación

Puedes instalar la skill directamente desde GitHub con:

```bash
npx skills add https://github.com/JoseLuis21/integrafacturacion-skills
```

## Actualización

Puedes actualizar la skill instalada con:

```bash
npx skills update
```

## Releases

El repo incluye un workflow en GitHub Actions para crear versiones con tags semánticos y GitHub Releases automáticamente cuando haces push a `main`.

Uso:

1. Haz push a `main`
2. El workflow crea una release automáticamente
3. Por defecto hace bump `patch`
4. Si un commit incluye `[minor]` o `[major]`, usa ese bump para la versión siguiente
5. Si el push solo cambia `README.md`, no crea release
6. Si el push solo cambia archivos dentro de `.github/`, no crea release

El workflow:

- detecta el último tag `v*`
- calcula la siguiente versión
- crea y empuja el nuevo tag
- genera un GitHub Release con notas basadas en los commits desde la versión anterior

También puedes ejecutarlo manualmente desde `Actions > Release` si quieres forzar el bump.

## Qué incluye

- `SKILL.md`: instrucciones principales de la skill
- `references/endpoints.md`: resumen de endpoints, headers y payloads observados en el Postman
- `references/documentos.md`: mapa entre `code_sii`, archivos SII y ejemplos JSON razonables
- `source-md/`: copias locales de los markdown SII usados por la skill
- `examples-json/`: ejemplos JSON de negocio incluidos dentro de la skill
- `evals/evals.json`: prompts de prueba iniciales para evaluar la skill

## Qué resuelve

Esta skill está pensada para responder tareas como:
- "arma el endpoint para emitir una factura 33"
- "qué headers necesita IntegraFacturacion"
- "dame un payload base para boleta 39"
- "valida este JSON de nota de crédito 61"
- "qué endpoint consulta el último folio usado"

## Fuentes usadas

La skill se apoya en dos fuentes principales:

1. Colección Postman de IntegraFacturacion
2. Archivos del repo con campos SII por tipo de documento

Tipos cubiertos en esta primera versión:
- `33` Factura Electrónica
- `34` Factura Electrónica Exenta
- `39` Boleta Electrónica
- `41` Boleta Exenta Electrónica
- `46` Factura de Compra Electrónica
- `52` Guía de Despacho Electrónica
- `56` Nota de Débito Electrónica
- `61` Nota de Crédito Electrónica

## Criterios de diseño

- no inventa endpoints
- prioriza payloads observados en el Postman oficial
- usa referencias SII locales para validar campos
- evita `TED`, `Signature`, `TmstFirma` y bloques de firma manual
- prefiere `data_dte_json` cuando es posible
- deja placeholders claros cuando faltan datos como `user_id`, `business_id` o IDs de documentos

## Estructura incluida

La skill quedó autocontenida dentro de su propia carpeta:
- `source-md/` contiene los markdown SII por tipo de documento
- `examples-json/` contiene ejemplos JSON razonables de negocio
- `references/` contiene el mapa de endpoints y documentos
- `evals/` contiene prompts de prueba

## Uso esperado

Cuando la uses, la skill debería:
1. identificar la categoría del request
2. elegir el endpoint correcto
3. mapear el `code_sii` si aplica
4. proponer headers y payload
5. validar el documento contra el archivo SII correcto

## Estado

Versión inicial, lista para iterar con casos reales y ampliar cobertura de payloads y validaciones.
