# DTE 46 - Factura de Compra Electronica

Campos extraídos desde `DTE_v10.xsd` + restricciones reutilizadas desde `SiiTypes_v10.xsd` + firma desde `xmldsignature_v10.xsd`.

## Cómo leer este archivo

- **Requerido** se obtiene de `minOccurs` o `use="required"`.
- **Mínimo / Máximo** refleja restricciones explícitas del XSD: `minInclusive`, `maxInclusive`, `minLength`, `maxLength` o `length`.
- **Largo** solo se llena cuando el XSD fija un largo exacto.
- **Enumeración / patrón** muestra valores cerrados o regex cuando existen.
- **Notas** incluye `totalDigits`, `fractionDigits`, documentación del tipo y valores fijos (`fixed`) cuando aplica.

## Notas

- Este archivo corresponde específicamente al **TipoDTE 46 (Factura de Compra Electronica)**.
- La estructura base proviene de la rama **Documento** del `choice` principal del `DTE_v10.xsd`.
- El campo `TipoDTE` fue acotado en esta documentación al valor **46**.
- Las reglas de “requerido / opcional” aquí reflejan lo que exige el **XSD**. Si el SII tiene validaciones de negocio adicionales por tipo de documento, esas no siempre quedan expresadas en el esquema.

- Esta variante corresponde a la estructura general del DTE usada por documentos como factura, guía, nota de débito y nota de crédito según el tipo permitido por el esquema.

## Campos

| Ruta | Tipo XSD | Requerido | Ocurrencias | Base | Mínimo | Máximo | Largo | Enumeración / patrón | Descripción | Notas |
|---|---|---|---|---|---|---|---|---|---|---|
| DTE | SiiDte:DTEDefType | Sí | 1..1 | - | - | - | - | - | - | - |
| DTE/@version | xs:decimal | Sí | - | - | - | - | - | - | - | valor fijo: 1.0 |
| DTE/Documento | anonymous | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Informacion Tributaria del DTE | - |
| DTE/Documento/@ID | xs:ID | Sí | - | - | - | - | - | - | [DTE choice opción 1: Documento] | - |
| DTE/Documento/Encabezado | anonymous | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Identificacion y Totales del Documento | - |
| DTE/Documento/Encabezado/IdDoc | anonymous | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Identificacion del DTE | - |
| DTE/Documento/Encabezado/IdDoc/TipoDTE | SiiDte:DTEType | Sí | 1..1 | xs:positiveInteger | - | - | - | 46 | Tipo de DTE (Factura de Compra Electronica) | Tipo específico documentado: 46 = Factura de Compra Electronica |
| DTE/Documento/Encabezado/IdDoc/Folio | SiiDte:FolioType | Sí | 1..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Folio del Documento Electronico | totalDigits=10; Folio de DTE - 10 digitos |
| DTE/Documento/Encabezado/IdDoc/FchEmis | SiiDte:FechaType | Sí | 1..1 | xs:date | 2000-01-01 | 2050-12-31 | - | - | [DTE choice opción 1: Documento] Fecha Emision Contable del DTE (AAAA-MM-DD) | Fecha entre 2000-01-01 y 2050-12-31 |
| DTE/Documento/Encabezado/IdDoc/IndNoRebaja | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | 1 | [DTE choice opción 1: Documento] Nota de Credito sin Derecho a Descontar Debito | - |
| DTE/Documento/Encabezado/IdDoc/TipoDespacho | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | 1, 2, 3 | [DTE choice opción 1: Documento] Indica Modo de Despacho de los Bienes que Acompanan al DTE | - |
| DTE/Documento/Encabezado/IdDoc/IndTraslado | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | 1, 2, 3, 4, 5, 6, 7, 8, 9 | [DTE choice opción 1: Documento] Incluido en Guias de Despacho para Especifiicar el Tipo de Traslado de Productos | - |
| DTE/Documento/Encabezado/IdDoc/TpoImpresion | anonymous | No | 0..1 | xs:string | =1 | =1 | 1 | N, T | [DTE choice opción 1: Documento] Tipo de impresión N (Normal) o T (Ticket) | - |
| DTE/Documento/Encabezado/IdDoc/IndServicio | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | 1, 2, 3 | [DTE choice opción 1: Documento] Indica si Transaccion Corresponde a la Prestacion de un Servicio | - |
| DTE/Documento/Encabezado/IdDoc/MntBruto | anonymous | No | 0..1 | xs:integer | - | - | - | 1 | [DTE choice opción 1: Documento] Indica el Uso de Montos Brutos en Detalle | - |
| DTE/Documento/Encabezado/IdDoc/TpoTranCompra | SiiDte:TipoTransCOMPRA | No | 0..1 | xs:positiveInteger | 1 | 7 | - | 1, 2, 3, 4, 5, 6, 7 | [DTE choice opción 1: Documento] Tipo de Transacción para el comprador \| Indica el Uso de Montos Brutos en Detalle | Tipo de Transacción para el comprador |
| DTE/Documento/Encabezado/IdDoc/TpoTranVenta | SiiDte:TipoTransVENTA | No | 0..1 | xs:positiveInteger | 1 | 4 | - | 1, 2, 3, 4 | [DTE choice opción 1: Documento] Tipo de Transacción para el vendedor \| Indica el Uso de Montos Brutos en Detalle | Tipo de Transacción para el vendedor |
| DTE/Documento/Encabezado/IdDoc/FmaPago | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | 1, 2, 3 | [DTE choice opción 1: Documento] Forma de Pago del DTE | - |
| DTE/Documento/Encabezado/IdDoc/FmaPagExp | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Forma de Pago Exportación Tabla Formas de Pago de Aduanas | totalDigits=2 |
| DTE/Documento/Encabezado/IdDoc/FchCancel | SiiDte:FechaType | No | 0..1 | xs:date | 2000-01-01 | 2050-12-31 | - | - | [DTE choice opción 1: Documento] Fecha de Cancelacion del DTE (AAAA-MM-DD) | Fecha entre 2000-01-01 y 2050-12-31 |
| DTE/Documento/Encabezado/IdDoc/MntCancel | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Monto Cancelado al emitirse el documento | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/IdDoc/SaldoInsol | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Saldo Insoluto al emitirse el documento | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/IdDoc/MntPagos | anonymous | No | 0..30 | - | - | - | - | - | [DTE choice opción 1: Documento] Tabla de Montos de Pago | - |
| DTE/Documento/Encabezado/IdDoc/MntPagos/FchPago | SiiDte:FechaType | Sí | 1..1 | xs:date | 2000-01-01 | 2050-12-31 | - | - | [DTE choice opción 1: Documento] Fecha de Pago (AAAA-MM-DD) | Fecha entre 2000-01-01 y 2050-12-31 |
| DTE/Documento/Encabezado/IdDoc/MntPagos/MntPago | SiiDte:MontoType | Sí | 1..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Monto de Pago | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/IdDoc/MntPagos/GlosaPagos | anonymous | No | 0..1 | xs:string | - | 40 | - | - | [DTE choice opción 1: Documento] | - |
| DTE/Documento/Encabezado/IdDoc/PeriodoDesde | SiiDte:FechaType | No | 0..1 | xs:date | 2000-01-01 | 2050-12-31 | - | - | [DTE choice opción 1: Documento] Periodo de Facturacion - Desde (AAAA-MM-DD) | Fecha entre 2000-01-01 y 2050-12-31 |
| DTE/Documento/Encabezado/IdDoc/PeriodoHasta | SiiDte:FechaType | No | 0..1 | xs:date | 2000-01-01 | 2050-12-31 | - | - | [DTE choice opción 1: Documento] Periodo Facturacion - Hasta (AAAA-MM-DD) | Fecha entre 2000-01-01 y 2050-12-31 |
| DTE/Documento/Encabezado/IdDoc/MedioPago | SiiDte:MedioPagoType | No | 0..1 | xs:string | - | - | - | CH, LT, EF, PE, TC, CF, OT | [DTE choice opción 1: Documento] Medio de Pago | Medios de Pago |
| DTE/Documento/Encabezado/IdDoc/TpoCtaPago | anonymous | No | 0..1 | xs:string | 5 | 10 | - | AHORRO, CORRIENTE, VISTA | [DTE choice opción 1: Documento] Tipo Cuenta de Pago | - |
| DTE/Documento/Encabezado/IdDoc/NumCtaPago | anonymous | No | 0..1 | xs:string | - | 20 | - | - | [DTE choice opción 1: Documento] Número de la cuenta del pago | - |
| DTE/Documento/Encabezado/IdDoc/BcoPago | anonymous | No | 0..1 | xs:string | - | 40 | - | - | [DTE choice opción 1: Documento] Banco donde se realiza el pago | - |
| DTE/Documento/Encabezado/IdDoc/TermPagoCdg | anonymous | No | 0..1 | xs:string | - | 4 | - | - | [DTE choice opción 1: Documento] Codigo del Termino de Pago Acordado | - |
| DTE/Documento/Encabezado/IdDoc/TermPagoGlosa | anonymous | No | 0..1 | xs:string | - | 100 | - | - | [DTE choice opción 1: Documento] Términos del Pago - glosa | - |
| DTE/Documento/Encabezado/IdDoc/TermPagoDias | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Dias de Acuerdo al Codigo de Termino de Pago | totalDigits=3 |
| DTE/Documento/Encabezado/IdDoc/FchVenc | SiiDte:FechaType | No | 0..1 | xs:date | 2000-01-01 | 2050-12-31 | - | - | [DTE choice opción 1: Documento] Fecha de Vencimiento del Pago (AAAA-MM-DD) | Fecha entre 2000-01-01 y 2050-12-31 |
| DTE/Documento/Encabezado/IdDoc/TipoFactEsp | anonymous | No | 0..1 | xs:nonNegativeInteger | - | - | - | 1 | [DTE choice opción 1: Documento] Tipo de Factura Especial | totalDigits=1 |
| DTE/Documento/Encabezado/Emisor | anonymous | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Datos del Emisor | - |
| DTE/Documento/Encabezado/Emisor/RUTEmisor | SiiDte:RUTType | Sí | 1..1 | xs:string | 3 | 10 | - | [0-9]+-([0-9]\|K) | [DTE choice opción 1: Documento] RUT del Emisor del DTE | Rol Unico Tributario (99..99-X) |
| DTE/Documento/Encabezado/Emisor/RznSoc | anonymous | Sí | 1..1 | SiiDte:RznSocLargaType | - | - | - | - | [DTE choice opción 1: Documento] Nombre o Razon Social del Emisor | - |
| DTE/Documento/Encabezado/Emisor/GiroEmis | anonymous | Sí | 1..1 | xs:string | 1 | 80 | - | - | [DTE choice opción 1: Documento] Giro Comercial del Emisor Relevante para el DTE | - |
| DTE/Documento/Encabezado/Emisor/Telefono | anonymous | No | 0..2 | SiiDte:FonoType | - | 20 | - | - | [DTE choice opción 1: Documento] Telefono Emisor | - |
| DTE/Documento/Encabezado/Emisor/CorreoEmisor | SiiDte:MailType | No | 0..1 | xs:string | - | 80 | - | - | [DTE choice opción 1: Documento] Correo Elect. de contacto en empresa del receptor | Dirección email |
| DTE/Documento/Encabezado/Emisor/Acteco | anonymous | Sí | 1..4 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Codigo de Actividad Economica del Emisor Relevante para el DTE | totalDigits=6 |
| DTE/Documento/Encabezado/Emisor/GuiaExport | anonymous | No | 0..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Emisor de una Guía de despacho para Exportación | - |
| DTE/Documento/Encabezado/Emisor/GuiaExport/CdgTraslado | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | 1, 2, 3, 4 | [DTE choice opción 1: Documento] Código Emisor Traslado Excepcional | totalDigits=1 |
| DTE/Documento/Encabezado/Emisor/GuiaExport/FolioAut | SiiDte:NroResolType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Folio Autorización ( N° de Resolución del SI) | totalDigits=6; Número de Resolución |
| DTE/Documento/Encabezado/Emisor/GuiaExport/FchAut | SiiDte:FechaType | No | 0..1 | xs:date | 2000-01-01 | 2050-12-31 | - | - | [DTE choice opción 1: Documento] Fecha de emisión de la Resolución de autorización (AAAA-MM-DD) | Fecha entre 2000-01-01 y 2050-12-31 |
| DTE/Documento/Encabezado/Emisor/Sucursal | anonymous | No | 0..1 | xs:string | - | 20 | - | - | [DTE choice opción 1: Documento] Sucursal que Emite el DTE | - |
| DTE/Documento/Encabezado/Emisor/CdgSIISucur | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Codigo de Sucursal Entregado por el SII | totalDigits=9 |
| DTE/Documento/Encabezado/Emisor/DirOrigen | anonymous | No | 0..1 | xs:string | - | 70 | - | - | [DTE choice opción 1: Documento] Direccion de Origen | - |
| DTE/Documento/Encabezado/Emisor/CmnaOrigen | SiiDte:ComunaType | No | 0..1 | xs:string | - | 20 | - | - | [DTE choice opción 1: Documento] Comuna de Origen | Comuna |
| DTE/Documento/Encabezado/Emisor/CiudadOrigen | SiiDte:CiudadType | No | 0..1 | xs:string | - | 20 | - | - | [DTE choice opción 1: Documento] Ciudad de Origen | Ciudad |
| DTE/Documento/Encabezado/Emisor/CdgVendedor | anonymous | No | 0..1 | xs:string | - | 60 | - | - | [DTE choice opción 1: Documento] Codigo del Vendedor | - |
| DTE/Documento/Encabezado/Emisor/IdAdicEmisor | anonymous | No | 0..1 | xs:string | 1 | 20 | - | - | [DTE choice opción 1: Documento] Identificador Adicional del Emisor | - |
| DTE/Documento/Encabezado/Emisor/RUTProveedor | SiiDte:RUTType | No | 0..1 | xs:string | 3 | 10 | - | [0-9]+-([0-9]\|K) | [DTE choice opción 1: Documento] RUT del Proveedor de madera | Rol Unico Tributario (99..99-X) |
| DTE/Documento/Encabezado/Emisor/RznSocProveedor | anonymous | No | 0..1 | SiiDte:RznSocLargaType | - | - | - | - | [DTE choice opción 1: Documento] Nombre o Razon Social del Proveedor de madera | - |
| DTE/Documento/Encabezado/RUTMandante | SiiDte:RUTType | No | 0..1 | xs:string | 3 | 10 | - | [0-9]+-([0-9]\|K) | [DTE choice opción 1: Documento] RUT a Cuenta de Quien se Emite el DTE | Rol Unico Tributario (99..99-X) |
| DTE/Documento/Encabezado/Receptor | anonymous | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Datos del Receptor | - |
| DTE/Documento/Encabezado/Receptor/RUTRecep | SiiDte:RUTType | Sí | 1..1 | xs:string | 3 | 10 | - | [0-9]+-([0-9]\|K) | [DTE choice opción 1: Documento] RUT del Receptor del DTE | Rol Unico Tributario (99..99-X) |
| DTE/Documento/Encabezado/Receptor/CdgIntRecep | anonymous | No | 0..1 | xs:string | - | 20 | - | - | [DTE choice opción 1: Documento] Codigo Interno del Receptor | - |
| DTE/Documento/Encabezado/Receptor/RznSocRecep | SiiDte:RznSocLargaType | Sí | 1..1 | xs:string | - | 100 | - | - | [DTE choice opción 1: Documento] Nombre o Razon Social del Receptor | Razón Social (max 100) |
| DTE/Documento/Encabezado/Receptor/Extranjero | anonymous | No | 0..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Receptor Extranjero | - |
| DTE/Documento/Encabezado/Receptor/Extranjero/NumId | anonymous | No | 0..1 | xs:string | 1 | 20 | - | - | [DTE choice opción 1: Documento] Num. Identif. Receptor Extranjero | - |
| DTE/Documento/Encabezado/Receptor/Extranjero/Nacionalidad | anonymous | No | 0..1 | xs:string | 1 | 3 | - | - | [DTE choice opción 1: Documento] Nacionalidad Receptor Extranjero | - |
| DTE/Documento/Encabezado/Receptor/Extranjero/TipoDocID | anonymous | No | 0..1 | xs:nonNegativeInteger | - | - | - | 1, 2 | [DTE choice opción 1: Documento] Tipo de Documento del Turista | - |
| DTE/Documento/Encabezado/Receptor/GiroRecep | anonymous | No | 0..1 | xs:string | - | 40 | - | - | [DTE choice opción 1: Documento] Giro Comercial del Receptor | - |
| DTE/Documento/Encabezado/Receptor/Contacto | anonymous | No | 0..1 | xs:string | - | 80 | - | - | [DTE choice opción 1: Documento] Telefono o E-mail de Contacto del Receptor | - |
| DTE/Documento/Encabezado/Receptor/CorreoRecep | SiiDte:MailType | No | 0..1 | xs:string | - | 80 | - | - | [DTE choice opción 1: Documento] Correo Elect. de contacto en empresa del receptor | Dirección email |
| DTE/Documento/Encabezado/Receptor/DirRecep | anonymous | No | 0..1 | xs:string | - | 70 | - | - | [DTE choice opción 1: Documento] Direccion en la Cual se Envian los Productos o se Prestan los Servicios | - |
| DTE/Documento/Encabezado/Receptor/CmnaRecep | SiiDte:ComunaType | No | 0..1 | xs:string | - | 20 | - | - | [DTE choice opción 1: Documento] Comuna de Recepcion | Comuna |
| DTE/Documento/Encabezado/Receptor/CiudadRecep | SiiDte:CiudadType | No | 0..1 | xs:string | - | 20 | - | - | [DTE choice opción 1: Documento] Ciudad de Recepcion | Ciudad |
| DTE/Documento/Encabezado/Receptor/DirPostal | anonymous | No | 0..1 | xs:string | - | 70 | - | - | [DTE choice opción 1: Documento] Direccion Postal | - |
| DTE/Documento/Encabezado/Receptor/CmnaPostal | SiiDte:ComunaType | No | 0..1 | xs:string | - | 20 | - | - | [DTE choice opción 1: Documento] Comuna Postal | Comuna |
| DTE/Documento/Encabezado/Receptor/CiudadPostal | SiiDte:CiudadType | No | 0..1 | xs:string | - | 20 | - | - | [DTE choice opción 1: Documento] Ciudad Postal | Ciudad |
| DTE/Documento/Encabezado/RUTSolicita | SiiDte:RUTType | No | 0..1 | xs:string | 3 | 10 | - | [0-9]+-([0-9]\|K) | [DTE choice opción 1: Documento] RUT que solicita el DTE en Venta a Publico | Rol Unico Tributario (99..99-X) |
| DTE/Documento/Encabezado/Transporte | anonymous | No | 0..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Informacion de Transporte de Mercaderias | - |
| DTE/Documento/Encabezado/Transporte/Patente | anonymous | No | 0..1 | xs:string | - | 8 | - | - | [DTE choice opción 1: Documento] Patente del Vehiculo que Transporta los Bienes | - |
| DTE/Documento/Encabezado/Transporte/PatenteCarro | anonymous | No | 0..1 | xs:string | - | 8 | - | - | [DTE choice opción 1: Documento] Patente de Carro o remolque | - |
| DTE/Documento/Encabezado/Transporte/RUTTrans | SiiDte:RUTType | No | 0..1 | xs:string | 3 | 10 | - | [0-9]+-([0-9]\|K) | [DTE choice opción 1: Documento] RUT del Transportista | Rol Unico Tributario (99..99-X) |
| DTE/Documento/Encabezado/Transporte/Chofer | anonymous | No | 0..1 | - | - | - | - | - | [DTE choice opción 1: Documento] | - |
| DTE/Documento/Encabezado/Transporte/Chofer/RUTChofer | SiiDte:RUTType | Sí | 1..1 | xs:string | 3 | 10 | - | [0-9]+-([0-9]\|K) | [DTE choice opción 1: Documento] RUT del Chofer | Rol Unico Tributario (99..99-X) |
| DTE/Documento/Encabezado/Transporte/Chofer/NombreChofer | anonymous | Sí | 1..1 | xs:string | - | 30 | - | - | [DTE choice opción 1: Documento] Nombre del Chofer | - |
| DTE/Documento/Encabezado/Transporte/DirDest | anonymous | No | 0..1 | xs:string | - | 70 | - | - | [DTE choice opción 1: Documento] Direccion de Destino | - |
| DTE/Documento/Encabezado/Transporte/CmnaDest | SiiDte:ComunaType | No | 0..1 | xs:string | - | 20 | - | - | [DTE choice opción 1: Documento] Comuna de Destino | Comuna |
| DTE/Documento/Encabezado/Transporte/CiudadDest | SiiDte:CiudadType | No | 0..1 | xs:string | - | 20 | - | - | [DTE choice opción 1: Documento] Ciudad de Destino | Ciudad |
| DTE/Documento/Encabezado/Transporte/Aduana | anonymous | No | 0..1 | - | - | - | - | - | [DTE choice opción 1: Documento] documentos de Exportación y guías de despacho | - |
| DTE/Documento/Encabezado/Transporte/Aduana/CodModVenta | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Código según tabla "Modalidad de Venta" de aduana | totalDigits=2 |
| DTE/Documento/Encabezado/Transporte/Aduana/CodClauVenta | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Código según Tabla "Cláusula compra-venta" de Aduana | totalDigits=2 |
| DTE/Documento/Encabezado/Transporte/Aduana/TotClauVenta | SiiDte:Dec16_2Type | No | 0..1 | xs:decimal | 0.01 | 9999999999999999.99 | - | - | [DTE choice opción 1: Documento] Total Cláusula de venta | totalDigits=18; fractionDigits=2; Monto con 16 Digitos de Cuerpo y 2 Decimales |
| DTE/Documento/Encabezado/Transporte/Aduana/CodViaTransp | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Indicar el Código de la vía de transporte utilizada para transportar la mercadería, según tabla Vías de Transporte de Aduana | totalDigits=2 |
| DTE/Documento/Encabezado/Transporte/Aduana/NombreTransp | anonymous | No | 0..1 | xs:string | - | 40 | - | - | [DTE choice opción 1: Documento] Nombre o Identificación del Medio de Transporte | - |
| DTE/Documento/Encabezado/Transporte/Aduana/RUTCiaTransp | SiiDte:RUTType | No | 0..1 | xs:string | 3 | 10 | - | [0-9]+-([0-9]\|K) | [DTE choice opción 1: Documento] Rut Cía. Transportadora | Rol Unico Tributario (99..99-X) |
| DTE/Documento/Encabezado/Transporte/Aduana/NomCiaTransp | anonymous | No | 0..1 | xs:string | - | 40 | - | - | [DTE choice opción 1: Documento] Nombre Cía. Transportadora | - |
| DTE/Documento/Encabezado/Transporte/Aduana/IdAdicTransp | anonymous | No | 0..1 | xs:string | 1 | 20 | - | - | [DTE choice opción 1: Documento] Identificador Adicional Cía. Transportadora | - |
| DTE/Documento/Encabezado/Transporte/Aduana/Booking | anonymous | No | 0..1 | xs:string | 1 | 20 | - | - | [DTE choice opción 1: Documento] Numero de reserva del Operador | - |
| DTE/Documento/Encabezado/Transporte/Aduana/Operador | anonymous | No | 0..1 | xs:string | 1 | 20 | - | - | [DTE choice opción 1: Documento] Código del Operador | - |
| DTE/Documento/Encabezado/Transporte/Aduana/CodPtoEmbarque | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Código del puerto de embarque según tabla de Aduana | totalDigits=4 |
| DTE/Documento/Encabezado/Transporte/Aduana/IdAdicPtoEmb | anonymous | No | 0..1 | xs:string | 1 | 20 | - | - | [DTE choice opción 1: Documento] Identificador Adicional Puerto de Embarque | - |
| DTE/Documento/Encabezado/Transporte/Aduana/CodPtoDesemb | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Código del puerto de desembarque según tabla de Aduana | totalDigits=4 |
| DTE/Documento/Encabezado/Transporte/Aduana/IdAdicPtoDesemb | anonymous | No | 0..1 | xs:string | 1 | 20 | - | - | [DTE choice opción 1: Documento] Identificador Adicional Puerto de Desembarque | - |
| DTE/Documento/Encabezado/Transporte/Aduana/Tara | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] | totalDigits=7 |
| DTE/Documento/Encabezado/Transporte/Aduana/CodUnidMedTara | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Código de la unidad de medida según tabla de Aduana | totalDigits=2 |
| DTE/Documento/Encabezado/Transporte/Aduana/PesoBruto | anonymous | No | 0..1 | xs:decimal | - | - | - | - | [DTE choice opción 1: Documento] Sumatoria de los pesos brutos de todos los ítems del documento | totalDigits=10; fractionDigits=2 |
| DTE/Documento/Encabezado/Transporte/Aduana/CodUnidPesoBruto | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Código de la unidad de medida según tabla de Aduana | totalDigits=2 |
| DTE/Documento/Encabezado/Transporte/Aduana/PesoNeto | anonymous | No | 0..1 | xs:decimal | - | - | - | - | [DTE choice opción 1: Documento] Sumatoria de los pesos netos de todos los ítems del documento | totalDigits=10; fractionDigits=2 |
| DTE/Documento/Encabezado/Transporte/Aduana/CodUnidPesoNeto | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Código de la unidad de medida según tabla de Aduana | totalDigits=2 |
| DTE/Documento/Encabezado/Transporte/Aduana/TotItems | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Indique el total de items del documento | totalDigits=18 |
| DTE/Documento/Encabezado/Transporte/Aduana/TotBultos | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Cantidad total de bultos que ampara el documento. | totalDigits=18 |
| DTE/Documento/Encabezado/Transporte/Aduana/TipoBultos | anonymous | No | 0..10 | - | - | - | - | - | [DTE choice opción 1: Documento] Tabla de descripción de los distintos tipos de bultos | - |
| DTE/Documento/Encabezado/Transporte/Aduana/TipoBultos/CodTpoBultos | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Código según tabla "Tipos de Bultos" de aduana | totalDigits=3 |
| DTE/Documento/Encabezado/Transporte/Aduana/TipoBultos/CantBultos | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Cantidad de Bultos | totalDigits=10 |
| DTE/Documento/Encabezado/Transporte/Aduana/TipoBultos/Marcas | anonymous | No | 0..1 | xs:string | - | 255 | - | - | [DTE choice opción 1: Documento] Identificación de marcas, cuando es distinto de contenedor | - |
| DTE/Documento/Encabezado/Transporte/Aduana/TipoBultos/IdContainer | anonymous | No | 0..1 | xs:string | - | 25 | - | - | [DTE choice opción 1: Documento] Se utiliza cuando el tipo de bulto es contenedor | - |
| DTE/Documento/Encabezado/Transporte/Aduana/TipoBultos/Sello | anonymous | No | 0..1 | xs:string | - | 20 | - | - | [DTE choice opción 1: Documento] Sello contenedor. Con digito verificador | - |
| DTE/Documento/Encabezado/Transporte/Aduana/TipoBultos/EmisorSello | anonymous | No | 0..1 | xs:string | - | 70 | - | - | [DTE choice opción 1: Documento] Nombre emisor sello | - |
| DTE/Documento/Encabezado/Transporte/Aduana/MntFlete | SiiDte:Dec14_4Type | No | 0..1 | xs:decimal | 0.0001 | 99999999999999.9999 | - | - | [DTE choice opción 1: Documento] Monto del flete según moneda de venta | totalDigits=18; fractionDigits=4; Monto con 14 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/Encabezado/Transporte/Aduana/MntSeguro | SiiDte:Dec14_4Type | No | 0..1 | xs:decimal | 0.0001 | 99999999999999.9999 | - | - | [DTE choice opción 1: Documento] Monto del seguro , según moneda de venta | totalDigits=18; fractionDigits=4; Monto con 14 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/Encabezado/Transporte/Aduana/CodPaisRecep | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Código del país del receptor extranjero de la mercadería, según tabla Países aduana | totalDigits=3 |
| DTE/Documento/Encabezado/Transporte/Aduana/CodPaisDestin | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Código del país de destino extranjero de la mercadería, según tabla Países aduana | totalDigits=3 |
| DTE/Documento/Encabezado/Transporte/FchSalida | anonymous | No | 0..1 | SiiDte:FechaType | 2003-04-01 | - | - | - | [DTE choice opción 1: Documento] Fecha de Salida | - |
| DTE/Documento/Encabezado/Transporte/HraSalida | anonymous | No | 0..1 | SiiDte:HoraType | 00:00:00 | 23:59:59 | - | - | [DTE choice opción 1: Documento] Hora de Salida | - |
| DTE/Documento/Encabezado/Transporte/FchLlegada | anonymous | No | 0..1 | SiiDte:FechaType | 2003-04-01 | - | - | - | [DTE choice opción 1: Documento] Fecha de Llegada | - |
| DTE/Documento/Encabezado/Totales | anonymous | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Montos Totales del DTE | - |
| DTE/Documento/Encabezado/Totales/MntNeto | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Monto Neto del DTE | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/Totales/MntExe | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Monto Exento del DTE | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/Totales/MntBase | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Monto Base Faenamiento Carne | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/Totales/MntMargenCom | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Monto Base de Márgenes de Comercialización. Monto informado | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/Totales/TasaIVA | SiiDte:PctType | No | 0..1 | xs:decimal | 0.01 | 999.99 | - | - | [DTE choice opción 1: Documento] Tasa de IVA | totalDigits=5; fractionDigits=2; Monto de Porcentaje ( 3 y 2) |
| DTE/Documento/Encabezado/Totales/IVA | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Monto de IVA del DTE | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/Totales/IVAProp | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Monto del IVA propio | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/Totales/IVATerc | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Monto del IVA de Terceros | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/Totales/ImptoReten | anonymous | No | 0..20 | - | - | - | - | - | [DTE choice opción 1: Documento] Impuestos y Retenciones Adicionales | - |
| DTE/Documento/Encabezado/Totales/ImptoReten/TipoImp | SiiDte:ImpAdicDTEType | Sí | 1..1 | xs:string | - | 3 | - | 14, 15, 16, 17, 18, 19, 23, 24, 25, 26, 27, 28, 30, 31, 32, 33, 34, 35, 36, 37 ... (44 valores) | [DTE choice opción 1: Documento] Tipo de Impuesto o Retencion Adicional | Tipo de Impuesto o Retencion Adicional de los DTE |
| DTE/Documento/Encabezado/Totales/ImptoReten/TasaImp | anonymous | No | 0..1 | SiiDte:PctType | - | 100.00 | - | - | [DTE choice opción 1: Documento] Tasa de Impuesto o Retencion | - |
| DTE/Documento/Encabezado/Totales/ImptoReten/MontoImp | SiiDte:ValorType | Sí | 1..1 | xs:integer | - | - | - | - | [DTE choice opción 1: Documento] Monto del Impuesto o Retencion | totalDigits=18; Monto de 18 digitos - Positivo o Negativo |
| DTE/Documento/Encabezado/Totales/IVANoRet | SiiDte:MntImpType | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] IVA No Retenido | totalDigits=18; Monto de Impuesto - 18 digitos |
| DTE/Documento/Encabezado/Totales/CredEC | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Credito Especial Empresas Constructoras | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/Totales/GrntDep | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Garantia por Deposito de Envases o Embalajes | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/Totales/Comisiones | anonymous | No | 0..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Comisiones y otros cargos es obligatoria para Liquidaciones Factura | - |
| DTE/Documento/Encabezado/Totales/Comisiones/ValComNeto | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Valor Neto Comisiones y Otros Cargos | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/Totales/Comisiones/ValComExe | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Val. Comis. y Otros Cargos no Afectos o Exentos | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/Totales/Comisiones/ValComIVA | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Valor IVA Comisiones y Otros Cargos | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/Totales/MntTotal | SiiDte:MontoType | Sí | 1..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Monto Total del DTE | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Encabezado/Totales/MontoNF | SiiDte:ValorType | No | 0..1 | xs:integer | - | - | - | - | [DTE choice opción 1: Documento] Monto No Facturable - Corresponde a Bienes o Servicios Facturados Previamente | totalDigits=18; Monto de 18 digitos - Positivo o Negativo |
| DTE/Documento/Encabezado/Totales/MontoPeriodo | SiiDte:ValorType | No | 0..1 | xs:integer | - | - | - | - | [DTE choice opción 1: Documento] Total de Ventas o Servicios del Periodo | totalDigits=18; Monto de 18 digitos - Positivo o Negativo |
| DTE/Documento/Encabezado/Totales/SaldoAnterior | SiiDte:ValorType | No | 0..1 | xs:integer | - | - | - | - | [DTE choice opción 1: Documento] Saldo Anterior - Puede ser Negativo o Positivo | totalDigits=18; Monto de 18 digitos - Positivo o Negativo |
| DTE/Documento/Encabezado/Totales/VlrPagar | SiiDte:ValorType | No | 0..1 | xs:integer | - | - | - | - | [DTE choice opción 1: Documento] Valor a Pagar Total del documento | totalDigits=18; Monto de 18 digitos - Positivo o Negativo |
| DTE/Documento/Encabezado/OtraMoneda | anonymous | No | 0..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Otra Moneda | - |
| DTE/Documento/Encabezado/OtraMoneda/TpoMoneda | SiiDte:TipMonType | Sí | 1..1 | xs:string | - | 15 | - | BOLIVAR, BOLIVIANO, CHELIN, CORONA DIN, CORONA NOR, CORONA SC, CRUZEIRO REAL, DIRHAM, DOLAR AUST, DOLAR CAN, DOLAR HK, DOLAR NZ, DOLAR SIN, DOLAR TAI, DOLAR USA, DRACMA, ESCUDO, EURO, FLORIN, FRANCO BEL ... (40 valores) | [DTE choice opción 1: Documento] Tipo Ottra moneda Tabla de Monedas de Aduanas | Tipos de Moneda de Aduana |
| DTE/Documento/Encabezado/OtraMoneda/TpoCambio | SiiDte:Dec6_4Type | No | 0..1 | xs:decimal | 0.0001 | 999999.9999 | - | - | [DTE choice opción 1: Documento] Tipo de Cambio fijado por el Banco Central de Chile | totalDigits=10; fractionDigits=4; Monto con 6 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/Encabezado/OtraMoneda/MntNetoOtrMnda | SiiDte:Dec14_4Type | No | 0..1 | xs:decimal | 0.0001 | 99999999999999.9999 | - | - | [DTE choice opción 1: Documento] Monto Neto del DTE en Otra Moneda | totalDigits=18; fractionDigits=4; Monto con 14 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/Encabezado/OtraMoneda/MntExeOtrMnda | SiiDte:Dec14_4Type | No | 0..1 | xs:decimal | 0.0001 | 99999999999999.9999 | - | - | [DTE choice opción 1: Documento] Monto Exento del DTE en Otra Moneda | totalDigits=18; fractionDigits=4; Monto con 14 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/Encabezado/OtraMoneda/MntFaeCarneOtrMnda | SiiDte:Dec14_4Type | No | 0..1 | xs:decimal | 0.0001 | 99999999999999.9999 | - | - | [DTE choice opción 1: Documento] Monto Base Faenamiento Carne en Otra Moneda | totalDigits=18; fractionDigits=4; Monto con 14 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/Encabezado/OtraMoneda/MntMargComOtrMnda | SiiDte:Dec14_4Type | No | 0..1 | xs:decimal | 0.0001 | 99999999999999.9999 | - | - | [DTE choice opción 1: Documento] Monto Base de Márgenes de Comercialización. Monto informado | totalDigits=18; fractionDigits=4; Monto con 14 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/Encabezado/OtraMoneda/IVAOtrMnda | SiiDte:Dec14_4Type | No | 0..1 | xs:decimal | 0.0001 | 99999999999999.9999 | - | - | [DTE choice opción 1: Documento] Monto de IVA del DTE en Otra Moneda | totalDigits=18; fractionDigits=4; Monto con 14 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/Encabezado/OtraMoneda/ImpRetOtrMnda | anonymous | No | 0..20 | - | - | - | - | - | [DTE choice opción 1: Documento] Impuestos y Retenciones Adicionales | - |
| DTE/Documento/Encabezado/OtraMoneda/ImpRetOtrMnda/TipoImpOtrMnda | SiiDte:ImpAdicDTEType | Sí | 1..1 | xs:string | - | 3 | - | 14, 15, 16, 17, 18, 19, 23, 24, 25, 26, 27, 28, 30, 31, 32, 33, 34, 35, 36, 37 ... (44 valores) | [DTE choice opción 1: Documento] Tipo de Impuesto o Retencion Adicional | Tipo de Impuesto o Retencion Adicional de los DTE |
| DTE/Documento/Encabezado/OtraMoneda/ImpRetOtrMnda/TasaImpOtrMnda | SiiDte:PctType | No | 0..1 | xs:decimal | 0.01 | 999.99 | - | - | [DTE choice opción 1: Documento] Tasa de Impuesto o Retencion | totalDigits=5; fractionDigits=2; Monto de Porcentaje ( 3 y 2) |
| DTE/Documento/Encabezado/OtraMoneda/ImpRetOtrMnda/VlrImpOtrMnda | SiiDte:Dec14_4Type | Sí | 1..1 | xs:decimal | 0.0001 | 99999999999999.9999 | - | - | [DTE choice opción 1: Documento] Valor del impuesto o retención en otra moneda | totalDigits=18; fractionDigits=4; Monto con 14 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/Encabezado/OtraMoneda/IVANoRetOtrMnda | SiiDte:Dec14_4Type | No | 0..1 | xs:decimal | 0.0001 | 99999999999999.9999 | - | - | [DTE choice opción 1: Documento] IVA no retenido Otra Moneda | totalDigits=18; fractionDigits=4; Monto con 14 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/Encabezado/OtraMoneda/MntTotOtrMnda | SiiDte:Dec14_4Type | Sí | 1..1 | xs:decimal | 0.0001 | 99999999999999.9999 | - | - | [DTE choice opción 1: Documento] Monto Total Otra Moneda | totalDigits=18; fractionDigits=4; Monto con 14 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/Detalle | anonymous | Sí | 1..60 | - | - | - | - | - | [DTE choice opción 1: Documento] Detalle de Itemes del Documento | - |
| DTE/Documento/Detalle/NroLinDet | anonymous | Sí | 1..1 | xs:positiveInteger | - | 99 | - | - | [DTE choice opción 1: Documento] Numero Secuencial de Linea | - |
| DTE/Documento/Detalle/CdgItem | anonymous | No | 0..5 | - | - | - | - | - | [DTE choice opción 1: Documento] Codificacion del Item | - |
| DTE/Documento/Detalle/CdgItem/TpoCodigo | anonymous | Sí | 1..1 | xs:string | - | 10 | - | - | [DTE choice opción 1: Documento] Tipo de Codificacion | - |
| DTE/Documento/Detalle/CdgItem/VlrCodigo | anonymous | Sí | 1..1 | xs:string | - | 35 | - | - | [DTE choice opción 1: Documento] Valor del Codigo de Item, para la Codificacion Particular | - |
| DTE/Documento/Detalle/IndExe | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | 1, 2, 3, 4, 5, 6 | [DTE choice opción 1: Documento] Indicador de Exencion/Facturacion | - |
| DTE/Documento/Detalle/Retenedor | anonymous | No | 0..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Sólo para transacciones realizadas por agentes retenedores | - |
| DTE/Documento/Detalle/Retenedor/IndAgente | anonymous | Sí | 1..1 | xs:string | - | 1 | - | R | [DTE choice opción 1: Documento] Indicador Agente Retenedor | - |
| DTE/Documento/Detalle/Retenedor/MntBaseFaena | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Monto Base Faenamiento | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Detalle/Retenedor/MntMargComer | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Márgenes de Comercialización | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Detalle/Retenedor/PrcConsFinal | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Precio Unitario Neto Consumidor Final | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Detalle/NmbItem | anonymous | Sí | 1..1 | xs:string | - | 80 | - | - | [DTE choice opción 1: Documento] Nombre del Item | - |
| DTE/Documento/Detalle/DscItem | anonymous | No | 0..1 | xs:string | - | 1000 | - | - | [DTE choice opción 1: Documento] Descripcion del Item | - |
| DTE/Documento/Detalle/QtyRef | SiiDte:Dec12_6Type | No | 0..1 | xs:decimal | 0.000001 | 999999999999.999999 | - | - | [DTE choice opción 1: Documento] Cantidad para la Unidad de Medida de Referencia | totalDigits=18; fractionDigits=6; Monto con 12 Digitos de Cuerpo y 6 Decimales |
| DTE/Documento/Detalle/UnmdRef | anonymous | No | 0..1 | xs:string | - | 4 | - | - | [DTE choice opción 1: Documento] Unidad de Medida de Referencia | - |
| DTE/Documento/Detalle/PrcRef | SiiDte:Dec12_6Type | No | 0..1 | xs:decimal | 0.000001 | 999999999999.999999 | - | - | [DTE choice opción 1: Documento] Precio Unitario de Referencia para Unidad de Referencia | totalDigits=18; fractionDigits=6; Monto con 12 Digitos de Cuerpo y 6 Decimales |
| DTE/Documento/Detalle/QtyItem | SiiDte:Dec12_6Type | No | 0..1 | xs:decimal | 0.000001 | 999999999999.999999 | - | - | [DTE choice opción 1: Documento] Cantidad del Item | totalDigits=18; fractionDigits=6; Monto con 12 Digitos de Cuerpo y 6 Decimales |
| DTE/Documento/Detalle/Subcantidad | anonymous | No | 0..5 | - | - | - | - | - | [DTE choice opción 1: Documento] Distribucion de la Cantidad | - |
| DTE/Documento/Detalle/Subcantidad/SubQty | SiiDte:Dec12_6Type | Sí | 1..1 | xs:decimal | 0.000001 | 999999999999.999999 | - | - | [DTE choice opción 1: Documento] Cantidad Distribuida | totalDigits=18; fractionDigits=6; Monto con 12 Digitos de Cuerpo y 6 Decimales |
| DTE/Documento/Detalle/Subcantidad/SubCod | anonymous | Sí | 1..1 | xs:string | - | 35 | - | - | [DTE choice opción 1: Documento] Codigo Descriptivo de la Subcantidad | - |
| DTE/Documento/Detalle/FchElabor | SiiDte:FechaType | No | 0..1 | xs:date | 2000-01-01 | 2050-12-31 | - | - | [DTE choice opción 1: Documento] Fecha Elaboracion del Item | Fecha entre 2000-01-01 y 2050-12-31 |
| DTE/Documento/Detalle/FchVencim | SiiDte:FechaType | No | 0..1 | xs:date | 2000-01-01 | 2050-12-31 | - | - | [DTE choice opción 1: Documento] Fecha Vencimiento del Item | Fecha entre 2000-01-01 y 2050-12-31 |
| DTE/Documento/Detalle/UnmdItem | anonymous | No | 0..1 | xs:string | - | 4 | - | - | [DTE choice opción 1: Documento] Unidad de Medida | - |
| DTE/Documento/Detalle/PrcItem | SiiDte:Dec12_6Type | No | 0..1 | xs:decimal | 0.000001 | 999999999999.999999 | - | - | [DTE choice opción 1: Documento] Precio Unitario del Item en Pesos | totalDigits=18; fractionDigits=6; Monto con 12 Digitos de Cuerpo y 6 Decimales |
| DTE/Documento/Detalle/OtrMnda | anonymous | No | 0..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Precio del Item en Otra Moneda | - |
| DTE/Documento/Detalle/OtrMnda/PrcOtrMon | SiiDte:Dec12_6Type | Sí | 1..1 | xs:decimal | 0.000001 | 999999999999.999999 | - | - | [DTE choice opción 1: Documento] Precio Unitario en Otra Moneda | totalDigits=18; fractionDigits=6; Monto con 12 Digitos de Cuerpo y 6 Decimales |
| DTE/Documento/Detalle/OtrMnda/Moneda | anonymous | Sí | 1..1 | xs:string | - | 3 | - | - | [DTE choice opción 1: Documento] Codigo de Otra Moneda (Usar Codigos de Moneda del Banco Central) | - |
| DTE/Documento/Detalle/OtrMnda/FctConv | SiiDte:Dec6_4Type | No | 0..1 | xs:decimal | 0.0001 | 999999.9999 | - | - | [DTE choice opción 1: Documento] Factor para Conversion a Pesos | totalDigits=10; fractionDigits=4; Monto con 6 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/Detalle/OtrMnda/DctoOtrMnda | SiiDte:Dec14_4Type | No | 0..1 | xs:decimal | 0.0001 | 99999999999999.9999 | - | - | [DTE choice opción 1: Documento] Descuento en Otra Moneda | totalDigits=18; fractionDigits=4; Monto con 14 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/Detalle/OtrMnda/RecargoOtrMnda | SiiDte:Dec14_4Type | No | 0..1 | xs:decimal | 0.0001 | 99999999999999.9999 | - | - | [DTE choice opción 1: Documento] Recargo en Otra Moneda | totalDigits=18; fractionDigits=4; Monto con 14 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/Detalle/OtrMnda/MontoItemOtrMnda | SiiDte:Dec14_4Type | No | 0..1 | xs:decimal | 0.0001 | 99999999999999.9999 | - | - | [DTE choice opción 1: Documento] Valor por línea de detalle en Otra Moneda | totalDigits=18; fractionDigits=4; Monto con 14 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/Detalle/DescuentoPct | SiiDte:PctType | No | 0..1 | xs:decimal | 0.01 | 999.99 | - | - | [DTE choice opción 1: Documento] Porcentaje de Descuento | totalDigits=5; fractionDigits=2; Monto de Porcentaje ( 3 y 2) |
| DTE/Documento/Detalle/DescuentoMonto | SiiDte:MntImpType | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Monto de Descuento | totalDigits=18; Monto de Impuesto - 18 digitos |
| DTE/Documento/Detalle/SubDscto | anonymous | No | 0..5 | - | - | - | - | - | [DTE choice opción 1: Documento] Desglose del Descuento | - |
| DTE/Documento/Detalle/SubDscto/TipoDscto | SiiDte:DineroPorcentajeType | Sí | 1..1 | xs:string | 1 | 1 | - | %, $ | [DTE choice opción 1: Documento] Tipo de SubDescuento | Unidad en que se expresa el Valor |
| DTE/Documento/Detalle/SubDscto/ValorDscto | SiiDte:Dec16_2Type | Sí | 1..1 | xs:decimal | 0.01 | 9999999999999999.99 | - | - | [DTE choice opción 1: Documento] Valor del SubDescuento | totalDigits=18; fractionDigits=2; Monto con 16 Digitos de Cuerpo y 2 Decimales |
| DTE/Documento/Detalle/RecargoPct | anonymous | No | 0..1 | SiiDte:PctType | - | 999.99 | - | - | [DTE choice opción 1: Documento] Porcentaje de Recargo | - |
| DTE/Documento/Detalle/RecargoMonto | SiiDte:MntImpType | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Monto de Recargo | totalDigits=18; Monto de Impuesto - 18 digitos |
| DTE/Documento/Detalle/SubRecargo | anonymous | No | 0..5 | - | - | - | - | - | [DTE choice opción 1: Documento] Desglose del Recargo | - |
| DTE/Documento/Detalle/SubRecargo/TipoRecargo | SiiDte:DineroPorcentajeType | Sí | 1..1 | xs:string | 1 | 1 | - | %, $ | [DTE choice opción 1: Documento] Tipo de SubRecargo | Unidad en que se expresa el Valor |
| DTE/Documento/Detalle/SubRecargo/ValorRecargo | SiiDte:Dec16_2Type | Sí | 1..1 | xs:decimal | 0.01 | 9999999999999999.99 | - | - | [DTE choice opción 1: Documento] Valor de SubRecargo | totalDigits=18; fractionDigits=2; Monto con 16 Digitos de Cuerpo y 2 Decimales |
| DTE/Documento/Detalle/CodImpAdic | SiiDte:ImpAdicDTEType | No | 0..2 | xs:string | - | 3 | - | 14, 15, 16, 17, 18, 19, 23, 24, 25, 26, 27, 28, 30, 31, 32, 33, 34, 35, 36, 37 ... (44 valores) | [DTE choice opción 1: Documento] Codigo de Impuesto Adicional o Retencion | Tipo de Impuesto o Retencion Adicional de los DTE |
| DTE/Documento/Detalle/MontoItem | SiiDte:MontoType | Sí | 1..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Monto por Linea de Detalle. Corresponde al Monto Neto, a menos que MntBruto Indique lo Contrario | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/SubTotInfo | anonymous | No | 0..20 | - | - | - | - | - | [DTE choice opción 1: Documento] Subtotales Informativos | - |
| DTE/Documento/SubTotInfo/NroSTI | anonymous | Sí | 1..1 | xs:positiveInteger | - | 99 | - | - | [DTE choice opción 1: Documento] Número de Subtotal | - |
| DTE/Documento/SubTotInfo/GlosaSTI | anonymous | Sí | 1..1 | xs:string | - | 40 | - | - | [DTE choice opción 1: Documento] Glosa | - |
| DTE/Documento/SubTotInfo/OrdenSTI | anonymous | No | 0..1 | xs:positiveInteger | - | 99 | - | - | [DTE choice opción 1: Documento] Ubicación para Impresión | - |
| DTE/Documento/SubTotInfo/SubTotNetoSTI | SiiDte:Dec16_2Type | No | 0..1 | xs:decimal | 0.01 | 9999999999999999.99 | - | - | [DTE choice opción 1: Documento] Valor Neto del Subtotal | totalDigits=18; fractionDigits=2; Monto con 16 Digitos de Cuerpo y 2 Decimales |
| DTE/Documento/SubTotInfo/SubTotIVASTI | SiiDte:Dec16_2Type | No | 0..1 | xs:decimal | 0.01 | 9999999999999999.99 | - | - | [DTE choice opción 1: Documento] Valor del IVA del Subtotal | totalDigits=18; fractionDigits=2; Monto con 16 Digitos de Cuerpo y 2 Decimales |
| DTE/Documento/SubTotInfo/SubTotAdicSTI | SiiDte:Dec16_2Type | No | 0..1 | xs:decimal | 0.01 | 9999999999999999.99 | - | - | [DTE choice opción 1: Documento] Valor de los Impuestos adicionales o específicos del Subtotal | totalDigits=18; fractionDigits=2; Monto con 16 Digitos de Cuerpo y 2 Decimales |
| DTE/Documento/SubTotInfo/SubTotExeSTI | SiiDte:Dec16_2Type | No | 0..1 | xs:decimal | 0.01 | 9999999999999999.99 | - | - | [DTE choice opción 1: Documento] Valor no Afecto o Exento del Subtotal | totalDigits=18; fractionDigits=2; Monto con 16 Digitos de Cuerpo y 2 Decimales |
| DTE/Documento/SubTotInfo/ValSubtotSTI | SiiDte:Dec16_2Type | No | 0..1 | xs:decimal | 0.01 | 9999999999999999.99 | - | - | [DTE choice opción 1: Documento] Valor de la línea de subtotal | totalDigits=18; fractionDigits=2; Monto con 16 Digitos de Cuerpo y 2 Decimales |
| DTE/Documento/SubTotInfo/LineasDeta | anonymous | No | 0..60 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] TABLA de Líneas de Detalle que se agrupan en el Subtotal | - |
| DTE/Documento/DscRcgGlobal | anonymous | No | 0..20 | - | - | - | - | - | [DTE choice opción 1: Documento] Descuentos y/o Recargos que afectan al total del Documento | - |
| DTE/Documento/DscRcgGlobal/NroLinDR | xs:positiveInteger | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Numero Secuencial de Linea | - |
| DTE/Documento/DscRcgGlobal/TpoMov | anonymous | Sí | 1..1 | xs:string | 1 | 1 | - | D, R | [DTE choice opción 1: Documento] Tipo de Movimiento | - |
| DTE/Documento/DscRcgGlobal/GlosaDR | anonymous | No | 0..1 | xs:string | - | 45 | - | - | [DTE choice opción 1: Documento] Descripcion del Descuento o Recargo | - |
| DTE/Documento/DscRcgGlobal/TpoValor | SiiDte:DineroPorcentajeType | Sí | 1..1 | xs:string | 1 | 1 | - | %, $ | [DTE choice opción 1: Documento] Unidad en que se Expresa el Valor | Unidad en que se expresa el Valor |
| DTE/Documento/DscRcgGlobal/ValorDR | SiiDte:Dec16_2Type | Sí | 1..1 | xs:decimal | 0.01 | 9999999999999999.99 | - | - | [DTE choice opción 1: Documento] Valor del Descuento o Recargo | totalDigits=18; fractionDigits=2; Monto con 16 Digitos de Cuerpo y 2 Decimales |
| DTE/Documento/DscRcgGlobal/ValorDROtrMnda | SiiDte:Dec14_4Type | No | 0..1 | xs:decimal | 0.0001 | 99999999999999.9999 | - | - | [DTE choice opción 1: Documento] Valor en otra moneda | totalDigits=18; fractionDigits=4; Monto con 14 Digitos de Cuerpo y 4 Decimales |
| DTE/Documento/DscRcgGlobal/IndExeDR | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | 1, 2 | [DTE choice opción 1: Documento] Indica si el D/R es No Afecto o No Facturable | - |
| DTE/Documento/Referencia | anonymous | No | 0..40 | - | - | - | - | - | [DTE choice opción 1: Documento] Identificacion de otros documentos Referenciados por Documento | - |
| DTE/Documento/Referencia/NroLinRef | anonymous | Sí | 1..1 | xs:positiveInteger | - | 99 | - | - | [DTE choice opción 1: Documento] Numero Secuencial de Linea de Referencia | - |
| DTE/Documento/Referencia/TpoDocRef | anonymous | Sí | 1..1 | xs:string | 1 | 3 | - | - | [DTE choice opción 1: Documento] Tipo de Documento de Referencia | - |
| DTE/Documento/Referencia/IndGlobal | anonymous | No | 0..1 | xs:nonNegativeInteger | - | - | - | 1 | [DTE choice opción 1: Documento] Indica que se esta Referenciando un Conjunto de Documentos | totalDigits=1 |
| DTE/Documento/Referencia/FolioRef | SiiDte:FolioRType | Sí | 1..1 | xs:string | 1 | 18 | - | - | [DTE choice opción 1: Documento] Folio del Documento de Referencia | Folio de Referencia - 18 digitos (incluye el cero) |
| DTE/Documento/Referencia/RUTOtr | SiiDte:RUTType | No | 0..1 | xs:string | 3 | 10 | - | [0-9]+-([0-9]\|K) | [DTE choice opción 1: Documento] RUT Otro Contribuyente | Rol Unico Tributario (99..99-X) |
| DTE/Documento/Referencia/FchRef | SiiDte:FechaType | Sí | 1..1 | xs:date | 2000-01-01 | 2050-12-31 | - | - | [DTE choice opción 1: Documento] Fecha de la Referencia | Fecha entre 2000-01-01 y 2050-12-31 |
| DTE/Documento/Referencia/CodRef | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | 1, 2, 3 | [DTE choice opción 1: Documento] Tipo de Uso de la Referencia | - |
| DTE/Documento/Referencia/RazonRef | anonymous | No | 0..1 | xs:string | - | 90 | - | - | [DTE choice opción 1: Documento] Razon Explicita por la que se Referencia el Documento | - |
| DTE/Documento/GeoRefEmision | anonymous | No | 0..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Ubicación | - |
| DTE/Documento/GeoRefEmision/LatitudEmision | anonymous | Sí | 1..1 | xs:string | - | 30 | - | - | [DTE choice opción 1: Documento] Latitud | - |
| DTE/Documento/GeoRefEmision/LongitudEmision | anonymous | Sí | 1..1 | xs:string | - | 30 | - | - | [DTE choice opción 1: Documento] Longitud | - |
| DTE/Documento/GeoRefEmision/SistemaReferencia | anonymous | Sí | 1..1 | xs:positiveInteger | - | 9 | - | - | [DTE choice opción 1: Documento] Sistema de Referencia | - |
| DTE/Documento/ManejoMadera | anonymous | No | 0..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Manejo de la Madera | - |
| DTE/Documento/ManejoMadera/ComunaRolOrigen | anonymous | Sí | 1..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Comuna del Rol Predial de Origen | totalDigits=6 |
| DTE/Documento/ManejoMadera/MnzRolOrigen | anonymous | Sí | 1..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Manzana del Rol Predial de Origen | totalDigits=6 |
| DTE/Documento/ManejoMadera/PrdRolOrigen | anonymous | Sí | 1..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Num. Predio del Rol Predial de Origen | totalDigits=6 |
| DTE/Documento/ManejoMadera/ComunaRolDestino | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Comuna del Rol Predial de Destino | totalDigits=6 |
| DTE/Documento/ManejoMadera/MnzRolDestino | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Manzana del Rol Predial de Destino | totalDigits=6 |
| DTE/Documento/ManejoMadera/PrdRolDestino | anonymous | No | 0..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Num. Predio del Rol Predial de Destino | totalDigits=6 |
| DTE/Documento/ManejoMadera/CodPlanConaf | anonymous | Sí | 1..1 | xs:string | - | 40 | - | - | [DTE choice opción 1: Documento] Código Plan de Manejo Conaf | - |
| DTE/Documento/ManejoMadera/AvisoEjecucionFaena | anonymous | No | 0..1 | xs:string | - | 40 | - | - | [DTE choice opción 1: Documento] Aviso Ejecución de Faena | - |
| DTE/Documento/ManejoMadera/LatitudOrigenMadera | anonymous | Sí | 1..1 | xs:string | - | 30 | - | - | [DTE choice opción 1: Documento] Latitud Origen | - |
| DTE/Documento/ManejoMadera/LongitudOrigenMadera | anonymous | Sí | 1..1 | xs:string | - | 30 | - | - | [DTE choice opción 1: Documento] Longitud Origen | - |
| DTE/Documento/ManejoMadera/LatitudDestinoMadera | anonymous | No | 0..1 | xs:string | - | 30 | - | - | [DTE choice opción 1: Documento] Latitud Destino | - |
| DTE/Documento/ManejoMadera/LongitudDestinoMadera | anonymous | No | 0..1 | xs:string | - | 30 | - | - | [DTE choice opción 1: Documento] Longitud Destino | - |
| DTE/Documento/ManejoMadera/SistemareferenciaMadera | anonymous | Sí | 1..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Sistema de Referencia | totalDigits=1 |
| DTE/Documento/Comisiones | anonymous | No | 0..20 | - | - | - | - | - | [DTE choice opción 1: Documento] Comisiones y otros cargos es obligatoria para Liquidaciones Factura | - |
| DTE/Documento/Comisiones/NroLinCom | anonymous | Sí | 1..1 | xs:positiveInteger | 1 | 20 | - | - | [DTE choice opción 1: Documento] Numero Secuencial de Linea | - |
| DTE/Documento/Comisiones/TipoMovim | anonymous | Sí | 1..1 | xs:string | 1 | - | - | C, O | [DTE choice opción 1: Documento] C (comisión) u O (otros cargos) | - |
| DTE/Documento/Comisiones/Glosa | anonymous | Sí | 1..1 | xs:string | 1 | 60 | - | - | [DTE choice opción 1: Documento] Especificación de la comisión u otro cargo | - |
| DTE/Documento/Comisiones/TasaComision | SiiDte:PctType | No | 0..1 | xs:decimal | 0.01 | 999.99 | - | - | [DTE choice opción 1: Documento] Valor porcentual de la comisión u otro cargo | totalDigits=5; fractionDigits=2; Monto de Porcentaje ( 3 y 2) |
| DTE/Documento/Comisiones/ValComNeto | SiiDte:MontoType | Sí | 1..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Valor Neto Comisiones y Otros Cargos | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Comisiones/ValComExe | SiiDte:MontoType | Sí | 1..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Val. Comis. y Otros Cargos no Afectos o Exentos | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/Comisiones/ValComIVA | SiiDte:MontoType | No | 0..1 | xs:nonNegativeInteger | - | - | - | - | [DTE choice opción 1: Documento] Valor IVA Comisiones y Otros Cargos | totalDigits=18; Monto de 18 digitos |
| DTE/Documento/TED | anonymous | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Timbre Electronico de DTE | - |
| DTE/Documento/TED/@version | anonymous | Sí | - | - | - | - | - | - | [DTE choice opción 1: Documento] | valor fijo: 1.0 |
| DTE/Documento/TED/DD | anonymous | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Datos Basicos de Documento | - |
| DTE/Documento/TED/DD/RE | SiiDte:RUTType | Sí | 1..1 | xs:string | 3 | 10 | - | [0-9]+-([0-9]\|K) | [DTE choice opción 1: Documento] RUT Emisor | Rol Unico Tributario (99..99-X) |
| DTE/Documento/TED/DD/TD | SiiDte:DTEType | Sí | 1..1 | xs:positiveInteger | - | - | - | 33, 34, 46, 52, 56, 61 | [DTE choice opción 1: Documento] Tipo DTE | Tipos de Documentos Tributarios Electronicos |
| DTE/Documento/TED/DD/F | SiiDte:FolioType | Sí | 1..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Folio DTE | totalDigits=10; Folio de DTE - 10 digitos |
| DTE/Documento/TED/DD/FE | SiiDte:FechaType | Sí | 1..1 | xs:date | 2000-01-01 | 2050-12-31 | - | - | [DTE choice opción 1: Documento] Fecha Emision DTE en Formato AAAA-MM-DD | Fecha entre 2000-01-01 y 2050-12-31 |
| DTE/Documento/TED/DD/RR | SiiDte:RUTType | Sí | 1..1 | xs:string | 3 | 10 | - | [0-9]+-([0-9]\|K) | [DTE choice opción 1: Documento] RUT Receptor | Rol Unico Tributario (99..99-X) |
| DTE/Documento/TED/DD/RSR | anonymous | Sí | 1..1 | xs:string | 1 | 40 | - | - | [DTE choice opción 1: Documento] Razon Social Receptor | - |
| DTE/Documento/TED/DD/MNT | xs:unsignedLong | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Monto Total DTE | - |
| DTE/Documento/TED/DD/IT1 | anonymous | Sí | 1..1 | xs:string | 1 | 40 | - | - | [DTE choice opción 1: Documento] Descripcion Primer Item de Detalle | - |
| DTE/Documento/TED/DD/CAF | anonymous | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Codigo Autorizacion Folios | - |
| DTE/Documento/TED/DD/CAF/@version | anonymous | Sí | - | - | - | - | - | - | [DTE choice opción 1: Documento] | valor fijo: 1.0 |
| DTE/Documento/TED/DD/CAF/DA | anonymous | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Datos de Autorizacion de Folios | - |
| DTE/Documento/TED/DD/CAF/DA/RE | SiiDte:RUTType | Sí | 1..1 | xs:string | 3 | 10 | - | [0-9]+-([0-9]\|K) | [DTE choice opción 1: Documento] RUT Emisor | Rol Unico Tributario (99..99-X) |
| DTE/Documento/TED/DD/CAF/DA/RS | anonymous | Sí | 1..1 | xs:string | 1 | 40 | - | - | [DTE choice opción 1: Documento] Razon Social Emisor | - |
| DTE/Documento/TED/DD/CAF/DA/TD | SiiDte:DTEType | Sí | 1..1 | xs:positiveInteger | - | - | - | 33, 34, 46, 52, 56, 61 | [DTE choice opción 1: Documento] Tipo DTE | Tipos de Documentos Tributarios Electronicos |
| DTE/Documento/TED/DD/CAF/DA/RNG | anonymous | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Rango Autorizado de Folios | - |
| DTE/Documento/TED/DD/CAF/DA/RNG/D | SiiDte:FolioType | Sí | 1..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Folio Inicial (Desde) | totalDigits=10; Folio de DTE - 10 digitos |
| DTE/Documento/TED/DD/CAF/DA/RNG/H | SiiDte:FolioType | Sí | 1..1 | xs:positiveInteger | - | - | - | - | [DTE choice opción 1: Documento] Folio Final (Hasta) | totalDigits=10; Folio de DTE - 10 digitos |
| DTE/Documento/TED/DD/CAF/DA/FA | SiiDte:FechaType | Sí | 1..1 | xs:date | 2000-01-01 | 2050-12-31 | - | - | [DTE choice opción 1: Documento] Fecha Autorizacion en Formato AAAA-MM-DD | Fecha entre 2000-01-01 y 2050-12-31 |
| DTE/Documento/TED/DD/CAF/DA/RSAPK | anonymous | Sí | 1..1 | - | - | - | - | - | [DTE/Documento/TED/DD/CAF/DA choice opción 1: RSAPK] Clave Publica RSA del Solicitante | - |
| DTE/Documento/TED/DD/CAF/DA/RSAPK/M | xs:base64Binary | Sí | 1..1 | - | - | - | - | - | [DTE/Documento/TED/DD/CAF/DA choice opción 1: RSAPK] Modulo RSA | - |
| DTE/Documento/TED/DD/CAF/DA/RSAPK/E | xs:base64Binary | Sí | 1..1 | - | - | - | - | - | [DTE/Documento/TED/DD/CAF/DA choice opción 1: RSAPK] Exponente RSA | - |
| DTE/Documento/TED/DD/CAF/DA/DSAPK | anonymous | Sí | 1..1 | - | - | - | - | - | [DTE/Documento/TED/DD/CAF/DA choice opción 2: DSAPK] Clave Publica DSA del Solicitante | - |
| DTE/Documento/TED/DD/CAF/DA/DSAPK/P | xs:base64Binary | Sí | 1..1 | - | - | - | - | - | [DTE/Documento/TED/DD/CAF/DA choice opción 2: DSAPK] Modulo Primo | - |
| DTE/Documento/TED/DD/CAF/DA/DSAPK/Q | xs:base64Binary | Sí | 1..1 | - | - | - | - | - | [DTE/Documento/TED/DD/CAF/DA choice opción 2: DSAPK] Entero Divisor de P - 1 | - |
| DTE/Documento/TED/DD/CAF/DA/DSAPK/G | xs:base64Binary | Sí | 1..1 | - | - | - | - | - | [DTE/Documento/TED/DD/CAF/DA choice opción 2: DSAPK] Entero f(P, Q) | - |
| DTE/Documento/TED/DD/CAF/DA/DSAPK/Y | xs:base64Binary | Sí | 1..1 | - | - | - | - | - | [DTE/Documento/TED/DD/CAF/DA choice opción 2: DSAPK] G**X mod P | - |
| DTE/Documento/TED/DD/CAF/DA/IDK | xs:long | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Identificador de Llave | - |
| DTE/Documento/TED/DD/CAF/FRMA | anonymous | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Firma Digital (RSA) del SII Sobre DA | - |
| DTE/Documento/TED/DD/TSTED | SiiDte:FechaHoraType | Sí | 1..1 | xs:dateTime | 2000-01-01T00:00:00 | 2050-12-31T23:59:59 | - | - | [DTE choice opción 1: Documento] TimeStamp de Generacion del Timbre | FechaType + hora entre 00:00 y 23:59; |
| DTE/Documento/TED/FRMT | anonymous | Sí | 1..1 | - | - | - | - | - | [DTE choice opción 1: Documento] Valor de Firma Digital sobre DD | - |
| DTE/Documento/TmstFirma | SiiDte:FechaHoraType | Sí | 1..1 | xs:dateTime | 2000-01-01T00:00:00 | 2050-12-31T23:59:59 | - | - | [DTE choice opción 1: Documento] Fecha y Hora en que se Firmo Digitalmente el Documento AAAA-MM-DDTHH:MI:SS | FechaType + hora entre 00:00 y 23:59; |
| DTE/Signature | xmldsig:SignatureType | Sí | 1..1 | - | - | - | - | - | Firma Digital sobre Documento \| Firma Digital sobre Documento | - |
| DTE/Signature/SignedInfo | anonymous | Sí | 1..1 | - | - | - | - | - | Descripcion de la Informacion Firmada y del Metodo de Firma | - |
| DTE/Signature/SignedInfo/CanonicalizationMethod | anonymous | Sí | 1..1 | - | - | - | - | - | Algoritmo de Canonicalizacion | - |
| DTE/Signature/SignedInfo/CanonicalizationMethod/@Algorithm | ds:anyURI | Sí | - | - | - | - | - | - | - | valor fijo: http://www.w3.org/TR/2001/REC-xml-c14n-20010315 |
| DTE/Signature/SignedInfo/SignatureMethod | anonymous | Sí | 1..1 | - | - | - | - | - | Algoritmo de Firma | - |
| DTE/Signature/SignedInfo/SignatureMethod/@Algorithm | anonymous | Sí | - | ds:anyURI | - | - | - | http://www.w3.org/2000/09/xmldsig#rsa-sha1, http://www.w3.org/2000/09/xmldsig#dsa-sha1 | - | - |
| DTE/Signature/SignedInfo/Reference | anonymous | Sí | 1..1 | - | - | - | - | - | Referencia a Elemento Firmado | - |
| DTE/Signature/SignedInfo/Reference/@URI | ds:anyURI | Sí | - | - | - | - | - | - | - | - |
| DTE/Signature/SignedInfo/Reference/Transforms | anonymous | No | 0..1 | - | - | - | - | - | Algoritmo de Transformacion | - |
| DTE/Signature/SignedInfo/Reference/Transforms/Transform | anonymous | Sí | 1..1 | - | - | - | - | - | - | - |
| DTE/Signature/SignedInfo/Reference/Transforms/Transform/@Algorithm | ds:anyURI | Sí | - | - | - | - | - | - | - | - |
| DTE/Signature/SignedInfo/Reference/DigestMethod | anonymous | Sí | 1..1 | - | - | - | - | - | Algoritmo de Digest | - |
| DTE/Signature/SignedInfo/Reference/DigestMethod/@Algorithm | ds:anyURI | Sí | - | - | - | - | - | - | - | valor fijo: http://www.w3.org/2000/09/xmldsig#sha1 |
| DTE/Signature/SignedInfo/Reference/DigestValue | ds:base64Binary | Sí | 1..1 | - | - | - | - | - | Valor de Digest | - |
| DTE/Signature/SignatureValue | ds:base64Binary | Sí | 1..1 | - | - | - | - | - | Valor de la Firma Digital | - |
| DTE/Signature/KeyInfo | anonymous | Sí | 1..1 | - | - | - | - | - | Informacion de Claves Publicas y Certificado | - |
| DTE/Signature/KeyInfo/KeyValue | anonymous | Sí | 1..1 | - | - | - | - | - | - | - |
| DTE/Signature/KeyInfo/X509Data | anonymous | Sí | 1..1 | - | - | - | - | - | Informacion del Certificado Publico | - |
| DTE/Signature/KeyInfo/X509Data/X509Certificate | ds:base64Binary | Sí | 1..1 | - | - | - | - | - | Certificado Publico | - |