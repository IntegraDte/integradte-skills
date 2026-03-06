# Boleta 39 - Boleta Electrónica

- **Tipo DTE:** `39`
- **Nombre:** Boleta Electrónica
- **Fuente:** `EnvioBOLETA_v11.xsd` + `xmldsignature_v10.xsd`
- **Nota:** la estructura del XSD para boleta `39` y `41` es la misma; la diferencia explícita del schema es el valor permitido de `Documento/Encabezado/IdDoc/TipoDTE` y los nodos TED/CAF relacionados.

## Restricción específica del tipo

| Campo | Regla |
|---|---|
| `Documento/Encabezado/IdDoc/TipoDTE` | Debe ser `39` |
| `Documento/TED/DD/TD` | Debe ser `39` |
| `Documento/TED/DD/CAF/DA/TD` | Debe ser `39` |

## Campos

| Campo / Ruta | Tipo XSD | Req. | Ocurrencias | Min | Max | TotalDigits | FractionDigits | Descripción | Notas |
|---|---|---:|---|---:|---:|---:|---:|---|---|
| `Documento` | `complexType` | Sí | `1..1` | — | — | — | — | Informacion Tributaria de la Boleta | — |
| `Documento/Encabezado` | `complexType` | Sí | `1..1` | — | — | — | — | Identificacion y Totales del Documento | — |
| `Documento/Encabezado/IdDoc` | `complexType` | Sí | `1..1` | — | — | — | — | Identificacion del DTE | — |
| `Documento/Encabezado/IdDoc/TipoDTE` | `DTEType` | Sí | `1..1` | — | — | — | — | Tipo de Boleta | valor esperado en este documento: 39 |
| `Documento/Encabezado/IdDoc/Folio` | `FolioType` | Sí | `1..1` | — | — | 10 | — | Folio del Documento Electronico | — |
| `Documento/Encabezado/IdDoc/FchEmis` | `date` | Sí | `1..1` | — | — | — | — | Fecha Emision Contable del DTE (AAAA-MM-DD) | — |
| `Documento/Encabezado/IdDoc/IndServicio` | `positiveInteger` | Sí | `1..1` | — | — | — | — | Indica el Tipo de Transaccion | enum: 1, 2, 3, 4 |
| `Documento/Encabezado/IdDoc/IndMntNeto` | `integer` | No | `0..1` | — | — | — | — | Indica el Uso de Montos Netos en Detalle | enum: 2 |
| `Documento/Encabezado/IdDoc/PeriodoDesde` | `date` | No | `0..1` | — | — | — | — | Periodo de Facturacion - Desde (AAAA-MM-DD) | — |
| `Documento/Encabezado/IdDoc/PeriodoHasta` | `date` | No | `0..1` | — | — | — | — | Periodo Facturacion - Hasta (AAAA-MM-DD) | — |
| `Documento/Encabezado/IdDoc/FchVenc` | `date` | No | `0..1` | — | — | — | — | Fecha de Vencimiento del Pago (AAAA-MM-DD) | — |
| `Documento/Encabezado/IdDoc/MedioPago` | `positiveInteger` | No | `0..1` | — | — | — | — | Indica el Medio de Pago utilizado | enum: 1, 2, 3, 4, 5 |
| `Documento/Encabezado/Emisor` | `complexType` | Sí | `1..1` | — | — | — | — | Datos del Emisor | — |
| `Documento/Encabezado/Emisor/RUTEmisor` | `RUTType` | Sí | `1..1` | 3 | 10 | — | — | RUT del Emisor del DTE | pattern: [0-9]+-([0-9]\|K) |
| `Documento/Encabezado/Emisor/RznSocEmisor` | `string` | No | `0..1` | — | 100 | — | — | Nombre o Razon Social del Emisor | — |
| `Documento/Encabezado/Emisor/GiroEmisor` | `string` | No | `0..1` | — | 80 | — | — | Giro del Emisor que Corresponde a la Transaccion | — |
| `Documento/Encabezado/Emisor/CdgSIISucur` | `positiveInteger` | No | `0..1` | — | — | 9 | — | Codigo de Sucursal Entregado por el SII | — |
| `Documento/Encabezado/Emisor/DirOrigen` | `string` | No | `0..1` | — | 70 | — | — | Direccion de Origen o Emision | — |
| `Documento/Encabezado/Emisor/CmnaOrigen` | `string` | No | `0..1` | — | 20 | — | — | Comuna de Origen | — |
| `Documento/Encabezado/Emisor/CiudadOrigen` | `string` | No | `0..1` | — | 20 | — | — | Ciudad de Origen | — |
| `Documento/Encabezado/Receptor` | `complexType` | Sí | `1..1` | — | — | — | — | Datos del Receptor | — |
| `Documento/Encabezado/Receptor/RUTRecep` | `RUTType` | Sí | `1..1` | 3 | 10 | — | — | RUT del Receptor del DTE | pattern: [0-9]+-([0-9]\|K) |
| `Documento/Encabezado/Receptor/CdgIntRecep` | `string` | No | `0..1` | — | 20 | — | — | Codigo Interno del Receptor | — |
| `Documento/Encabezado/Receptor/RznSocRecep` | `string` | No | `0..1` | — | 100 | — | — | Nombre o Razon Social del Receptor | — |
| `Documento/Encabezado/Receptor/Contacto` | `string` | No | `0..1` | — | 80 | — | — | Telefono o E-mail de Contacto del Receptor | — |
| `Documento/Encabezado/Receptor/CorreoRecep` | `MailType` | No | `0..1` | — | 80 | — | — | Correo Elect. de contacto en empresa del receptor | — |
| `Documento/Encabezado/Receptor/TelefonoRecep` | `FonoType` | No | `0..1` | — | 20 | — | — | Telefono de contacto en empresa del receptor | — |
| `Documento/Encabezado/Receptor/DirRecep` | `string` | No | `0..1` | — | 70 | — | — | Direccion en la Cual se Envian los Productos o se Prestan los Servicios | — |
| `Documento/Encabezado/Receptor/CmnaRecep` | `string` | No | `0..1` | — | 20 | — | — | Comuna de Recepcion | — |
| `Documento/Encabezado/Receptor/CiudadRecep` | `string` | No | `0..1` | — | 20 | — | — | Ciudad de Recepcion | — |
| `Documento/Encabezado/Receptor/DirPostal` | `string` | No | `0..1` | — | 70 | — | — | Direccion Postal | — |
| `Documento/Encabezado/Receptor/CmnaPostal` | `string` | No | `0..1` | — | 20 | — | — | Comuna Postal | — |
| `Documento/Encabezado/Receptor/CiudadPostal` | `string` | No | `0..1` | — | 20 | — | — | Ciudad Postal | — |
| `Documento/Encabezado/RUTProvSW` | `RUTType` | No | `0..1` | 3 | 10 | — | — | RUT Proveedor | pattern: [0-9]+-([0-9]\|K) |
| `Documento/Encabezado/Totales` | `complexType` | Sí | `1..1` | — | — | — | — | Montos Totales del DTE | — |
| `Documento/Encabezado/Totales/MntNeto` | `MontoType` | No | `0..1` | — | — | 18 | — | Monto Neto | — |
| `Documento/Encabezado/Totales/MntExe` | `MontoType` | No | `0..1` | — | — | 18 | — | Monto Exento | — |
| `Documento/Encabezado/Totales/IVA` | `MontoType` | No | `0..1` | — | — | 18 | — | Monto de IVA | — |
| `Documento/Encabezado/Totales/MntTotal` | `MontoType` | Sí | `1..1` | — | — | 18 | — | Monto Total | — |
| `Documento/Encabezado/Totales/MontoNF` | `ValorType` | No | `0..1` | — | — | 18 | — | Monto No Facturable - Corresponde a Bienes o Servicios Facturados Previamente | — |
| `Documento/Encabezado/Totales/TotalPeriodo` | `ValorType` | No | `0..1` | — | — | 18 | — | Total de Ventas o Servicios del Periodo | — |
| `Documento/Encabezado/Totales/SaldoAnterior` | `ValorType` | No | `0..1` | — | — | 18 | — | Saldo Anterior - Puede ser Negativo o Positivo | — |
| `Documento/Encabezado/Totales/VlrPagar` | `ValorType` | No | `0..1` | — | — | 18 | — | Valor a Pagar Total del Documento | — |
| `Documento/Detalle` | `complexType` | No | `0..1000` | — | — | — | — | Detalle de Itemes del Documento | — |
| `Documento/Detalle/NroLinDet` | `positiveInteger` | Sí | `1..1` | 1 | 1000 | — | — | Numero Secuencial de Linea | — |
| `Documento/Detalle/CdgItem` | `complexType` | No | `0..5` | — | — | — | — | Codificacion del Item | — |
| `Documento/Detalle/CdgItem/TpoCodigo` | `string` | Sí | `1..1` | — | 10 | — | — | Tipo de Codificacion | — |
| `Documento/Detalle/CdgItem/VlrCodigo` | `string` | Sí | `1..1` | — | 35 | — | — | Valor del Codigo de Item, para la Codificacion Particular | — |
| `Documento/Detalle/IndExe` | `positiveInteger` | No | `0..1` | — | — | — | — | Indicador de Exencion/Facturacion | enum: 1, 2, 6 |
| `Documento/Detalle/ItemEspectaculo` | `positiveInteger` | No | `0..1` | — | 2 | 2 | — | Indica si el ítem es : 01: TICKET 02: VALOR SERVICIO | enum: 01, 02 |
| `Documento/Detalle/RUTMandante` | `RUTType` | No | `0..1` | 3 | 10 | — | — | Rut de la Empresa Mandante de la Boleta | pattern: [0-9]+-([0-9]\|K) |
| `Documento/Detalle/NmbItem` | `string` | Sí | `1..1` | — | 80 | — | — | Nombre del Item | — |
| `Documento/Detalle/InfoTicket` | `complexType` | No | `0..1` | — | — | — | — | Informacion de la entrada | — |
| `Documento/Detalle/InfoTicket/FolioTicket` | `positiveInteger` | Sí | `1..1` | — | — | 6 | — | Corresponde a la numeración única para el evento. | — |
| `Documento/Detalle/InfoTicket/FchGenera` | `dateTime` | Sí | `1..1` | 2010-03-10T00:00:00 | 2050-12-31T23:59:59 | — | — | Corresponde a la fecha y hora de generación del ticket (AAAA-MM-DDThh:mm:ss) | — |
| `Documento/Detalle/InfoTicket/NmbEvento` | `string` | Sí | `1..1` | — | 80 | — | — | Nombre del Espectáculo | — |
| `Documento/Detalle/InfoTicket/TpoTicket` | `string` | Sí | `1..1` | — | 10 | — | — | Tipo de ticket, Por ejemplo: Adulto, Niño, etc | — |
| `Documento/Detalle/InfoTicket/CdgEvento` | `string` | Sí | `1..1` | — | 5 | — | — | Código asociado al Evento | — |
| `Documento/Detalle/InfoTicket/FchEvento` | `dateTime` | Sí | `1..1` | 2010-03-10T00:00:00 | 2050-12-31T23:59:59 | — | — | Fecha y hora de realización del evento AAAA-MM-DDThh:mm:ss) | — |
| `Documento/Detalle/InfoTicket/LugarEvento` | `string` | Sí | `1..1` | — | 80 | — | — | Dirección o identificación del recinto donde se realizará el Espectáculo | — |
| `Documento/Detalle/InfoTicket/UbicEvento` | `string` | Sí | `1..1` | — | 20 | — | — | Sector/Sección de la ubicación en el evento | — |
| `Documento/Detalle/InfoTicket/FilaUbicEvento` | `string` | No | `0..1` | — | 3 | — | — | Fila correspondiente a la Ubicación en el evento | — |
| `Documento/Detalle/InfoTicket/AsntoUbicEvento` | `string` | No | `0..1` | — | 3 | — | — | N° de Asiento correspondiente a la Ubicación en el evento | — |
| `Documento/Detalle/DscItem` | `string` | No | `0..1` | — | 1000 | — | — | Descripcion del Item | — |
| `Documento/Detalle/QtyItem` | `Dec5Type` | No | `0..1` | 0.000001 | 999999999999.999999 | 18 | 6 | Cantidad del Item | — |
| `Documento/Detalle/UnmdItem` | `string` | No | `0..1` | — | 4 | — | — | Unidad de Medida | — |
| `Documento/Detalle/PrcItem` | `Dec5Type` | No | `0..1` | 0.000001 | 999999999999.999999 | 18 | 6 | Precio Unitario del Item en Pesos | — |
| `Documento/Detalle/DescuentoPct` | `PctType` | No | `0..1` | 0.00 | 100.00 | 5 | 2 | Porcentaje de Descuento | — |
| `Documento/Detalle/DescuentoMonto` | `nonNegativeInteger` | No | `0..1` | — | — | 18 | — | Monto de Descuento | — |
| `Documento/Detalle/RecargoPct` | `PctType` | No | `0..1` | 0.00 | 100.00 | 5 | 2 | Porcentaje de Recargo | — |
| `Documento/Detalle/RecargoMonto` | `nonNegativeInteger` | No | `0..1` | — | — | 18 | — | Monto de Recargo | — |
| `Documento/Detalle/MontoItem` | `MontoType` | Sí | `1..1` | — | — | 18 | — | Monto por Linea de Detalle. Corresponde al Monto Bruto, a menos que IndMntNeto Indique lo Contrario | — |
| `Documento/SubTotInfo` | `complexType` | No | `0..20` | — | — | — | — | Subtotales Informativos | — |
| `Documento/SubTotInfo/NroSTI` | `positiveInteger` | Sí | `1..1` | 1 | 20 | — | — | Número de Subtotal | — |
| `Documento/SubTotInfo/GlosaSTI` | `string` | No | `0..1` | — | 80 | — | — | Glosa | — |
| `Documento/SubTotInfo/OrdenSTI` | `positiveInteger` | No | `0..1` | — | 99 | — | — | Ubicación para Impresión | — |
| `Documento/SubTotInfo/SubTotNetoSTI` | `Dec1Type` | No | `0..1` | 0.01 | 9999999999999999.99 | 18 | 2 | Valor Neto del Subtotal | — |
| `Documento/SubTotInfo/SubTotIVASTI` | `Dec1Type` | No | `0..1` | 0.01 | 9999999999999999.99 | 18 | 2 | Valor del IVA del Subtotal | — |
| `Documento/SubTotInfo/SubTotAdicSTI` | `Dec1Type` | No | `0..1` | 0.01 | 9999999999999999.99 | 18 | 2 | Valor de los Impuestos adicionales o específicos del Subtotal | — |
| `Documento/SubTotInfo/SubTotExeSTI` | `Dec1Type` | No | `0..1` | 0.01 | 9999999999999999.99 | 18 | 2 | Valor no Afecto o Exento del Subtotal | — |
| `Documento/SubTotInfo/ValSubtotSTI` | `Dec1Type` | No | `0..1` | 0.01 | 9999999999999999.99 | 18 | 2 | Valor de la línea de subtotal | — |
| `Documento/SubTotInfo/LineasDeta` | `positiveInteger` | No | `0..1000` | — | 1000 | — | — | TABLA de Líneas de Detalle que se agrupan en el Subtotal | — |
| `Documento/DscRcgGlobal` | `complexType` | No | `0..20` | — | — | — | — | Descuentos y/o Recargos que afectan al total del Documento | — |
| `Documento/DscRcgGlobal/NroLinDR` | `positiveInteger` | Sí | `1..1` | 1 | 20 | — | — | Numero Secuencial de Linea | — |
| `Documento/DscRcgGlobal/TpoMov` | `string` | Sí | `1..1` | — | — | — | — | Tipo de Movimiento | enum: D, R |
| `Documento/DscRcgGlobal/GlosaDR` | `string` | No | `0..1` | — | 45 | — | — | Descripcion del Descuento o Recargo | — |
| `Documento/DscRcgGlobal/TpoValor` | `string` | Sí | `1..1` | — | — | — | — | Unidad en que se Expresa el Valor | enum: %, $ |
| `Documento/DscRcgGlobal/ValorDR` | `Dec1Type` | Sí | `1..1` | 0.01 | 9999999999999999.99 | 18 | 2 | Valor del Descuento o Recargo | — |
| `Documento/DscRcgGlobal/IndExeDR` | `positiveInteger` | No | `0..1` | — | — | — | — | Indica si el Descuento o Recargo Afecta a Itemes Exentos o No Facturables | enum: 1, 2 |
| `Documento/Referencia` | `complexType` | No | `0..40` | — | — | — | — | Identificacion de otros documentos Referenciados por Documento | — |
| `Documento/Referencia/NroLinRef` | `positiveInteger` | Sí | `1..1` | 1 | 40 | — | — | Numero Secuencial de Linea de Referencia | — |
| `Documento/Referencia/TpoDocRef` | `string` | No | `0..1` | — | 3 | — | — | Tipo Documento de referencia | — |
| `Documento/Referencia/FolioRef` | `string` | No | `0..1` | — | 18 | — | — | Folio de Referencia | — |
| `Documento/Referencia/CodRef` | `string` | No | `0..1` | — | 18 | — | — | Codigo Interno del Tipo de Referencia | — |
| `Documento/Referencia/RazonRef` | `string` | No | `0..1` | — | 90 | — | — | Razon Explicita por la que se Referencia el Documento | — |
| `Documento/Referencia/CodVndor` | `string` | No | `0..1` | — | 8 | — | — | Código del Vendedor establecido por la Empresa. Puede estar asociado a INTERNET | — |
| `Documento/Referencia/CodCaja` | `string` | No | `0..1` | — | 8 | — | — | Código de la caja establecido por la Empresa | — |
| `Documento/GeoRefEmision` | `complexType` | No | `0..1` | — | — | — | — | Ubicación | — |
| `Documento/GeoRefEmision/LatitudEmision` | `string` | Sí | `1..1` | — | 30 | — | — | Latitud | — |
| `Documento/GeoRefEmision/LongitudEmision` | `string` | Sí | `1..1` | — | 30 | — | — | Longitud | — |
| `Documento/GeoRefEmision/SistemaReferencia` | `positiveInteger` | Sí | `1..1` | — | 9 | — | — | Sistema de Referencia | — |
| `Documento/TED` | `complexType` | Sí | `1..1` | — | — | — | — | Timbre Electronico de la Boleta | — |
| `Documento/TED/DD` | `complexType` | Sí | `1..1` | — | — | — | — | Datos Basicos de Documento | — |
| `Documento/TED/DD/RE` | `RUTType` | Sí | `1..1` | 3 | 10 | — | — | RUT Emisor | pattern: [0-9]+-([0-9]\|K) |
| `Documento/TED/DD/TD` | `DTEType` | Sí | `1..1` | — | — | — | — | Tipo DTE | valor esperado en este documento: 39 |
| `Documento/TED/DD/F` | `FolioType` | Sí | `1..1` | — | — | 10 | — | Folio DTE | — |
| `Documento/TED/DD/FE` | `date` | Sí | `1..1` | — | — | — | — | Fecha Emision DTE en Formato AAAA-MM-DD | — |
| `Documento/TED/DD/RR` | `RUTType` | Sí | `1..1` | 3 | 10 | — | — | RUT Receptor | pattern: [0-9]+-([0-9]\|K) |
| `Documento/TED/DD/RSR` | `string` | Sí | `1..1` | — | 40 | — | — | Razon Sociall Receptor | — |
| `Documento/TED/DD/MNT` | `unsignedLong` | Sí | `1..1` | — | — | — | — | Monto Total DTE | — |
| `Documento/TED/DD/IT1` | `string` | Sí | `1..1` | — | 40 | — | — | Descripcion Primer Item de Detalle | — |
| `Documento/TED/DD/CAF` | `complexType` | Sí | `1..1` | — | — | — | — | Codigo Autorizacion Folios | — |
| `Documento/TED/DD/CAF/DA` | `complexType` | Sí | `1..1` | — | — | — | — | Datos de Autorizacion de Folios | — |
| `Documento/TED/DD/CAF/DA/RE` | `RUTType` | Sí | `1..1` | 3 | 10 | — | — | RUT Emisor | pattern: [0-9]+-([0-9]\|K) |
| `Documento/TED/DD/CAF/DA/RS` | `string` | Sí | `1..1` | — | 40 | — | — | Razon Social Emisor | — |
| `Documento/TED/DD/CAF/DA/TD` | `DTEType` | Sí | `1..1` | — | — | — | — | Tipo DTE | valor esperado en este documento: 39 |
| `Documento/TED/DD/CAF/DA/RNG` | `complexType` | Sí | `1..1` | — | — | — | — | Rango Autorizado de Folios | — |
| `Documento/TED/DD/CAF/DA/RNG/D` | `FolioType` | Sí | `1..1` | — | — | 10 | — | Folio Inicial (Desde) | — |
| `Documento/TED/DD/CAF/DA/RNG/H` | `FolioType` | Sí | `1..1` | — | — | 10 | — | Folio Final (Hasta) | — |
| `Documento/TED/DD/CAF/DA/FA` | `date` | Sí | `1..1` | — | — | — | — | Fecha Autorizacion en Formato AAAA-MM-DD | — |
| `Documento/TED/DD/CAF/DA/IDK` | `long` | Sí | `1..1` | — | — | — | — | Identificador de Llave | — |
| `Documento/TED/DD/CAF/FRMA` | `complexType` | Sí | `1..1` | — | — | — | — | Firma Digital (RSA) del SII Sobre DA | — |
| `Documento/TED/DD/CAF/@version` | `attribute` | Sí | `1..1` | — | — | — | — | Atributo XML | fixed: 1.0 |
| `Documento/TED/DD/TSTED` | `dateTime` | Sí | `1..1` | — | — | — | — | TimeStamp de Generacion del Timbre | — |
| `Documento/TED/FRMT` | `complexType` | Sí | `1..1` | — | — | — | — | Valor de Firma Digital sobre DD | — |
| `Documento/TED/@version` | `attribute` | Sí | `1..1` | — | — | — | — | Atributo XML | fixed: 1.0 |
| `Documento/TmstFirma` | `dateTime` | Sí | `1..1` | — | — | — | — | Fecha y Hora en que se Firmo Digitalmente el Documento AAAA-MM-DDTHH:MI:SS | — |
| `Documento/@ID` | `ID` | Sí | `1..1` | — | — | — | — | Atributo XML | — |
| `ds:Signature` | `complexType` | Sí | `1..1` | — | — | — | — | — | — |
| `@version` | `decimal` | Sí | `1..1` | — | — | — | — | Atributo XML | fixed: 1.0 |

## Tipos base relevantes

| Tipo | Base | Min | Max | MinLength | MaxLength | TotalDigits | FractionDigits | Pattern / Enum |
|---|---|---:|---:|---:|---:|---:|---:|---|
| `RUTType` | `string` | — | — | 3 | 10 | — | — | pattern: [0-9]+-([0-9]|K) |
| `DTEType` | `positiveInteger` | — | — | — | — | — | — | enum: 39, 41 |
| `FolioType` | `positiveInteger` | — | — | — | — | 10 | — | — |
| `MontoType` | `nonNegativeInteger` | — | — | — | — | 18 | — | — |
| `ValorType` | `integer` | — | — | — | — | 18 | — | — |
| `Dec1Type` | `decimal` | 0.01 | 9999999999999999.99 | — | — | 18 | 2 | — |
| `Dec5Type` | `decimal` | 0.000001 | 999999999999.999999 | — | — | 18 | 6 | — |
| `PctType` | `decimal` | 0.00 | 100.00 | — | — | 5 | 2 | — |
| `MailType` | `string` | — | — | — | 80 | — | — | — |
| `FonoType` | `string` | — | — | — | 20 | — | — | — |

## Observaciones

- `Documento/Detalle` permite hasta **1000** líneas.
- `Documento/SubTotInfo` permite hasta **20** repeticiones.
- `Documento/DscRcgGlobal` permite hasta **20** repeticiones.
- `Documento/Referencia` permite hasta **40** repeticiones.
- `Documento` incluye `TED`, `TmstFirma` y `ds:Signature` dentro del mismo DTE.
- Este material documenta restricciones del **XSD**; no reemplaza validaciones de negocio del SII que no estén modeladas directamente en el schema.