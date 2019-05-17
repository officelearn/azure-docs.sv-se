---
title: Azure Stream Analytics diagnostiklogg datafel
description: Den här artikeln beskrivs olika indata och utdata datafel som kan uppstå när du använder Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: b00eb12092838746f4bfe16f00eac55df9224b09
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65607235"
---
# <a name="azure-stream-analytics-data-errors"></a>Fel med Azure Stream Analytics-data

När det finns en diskrepans i de data som bearbetas av Azure Stream Analytics-jobb, skickar en fel inträffat till diagnostikloggarna i Stream Analytics. Stream Analytics skriver detaljerad information och exempel på händelser, till dess diagnostikloggar när datafel uppstår. En sammanfattning av den här informationen är också tillgängligt via portalaviseringarna för vissa fel.

Den här artikeln beskrivs olika feltyper, orsaker och diagnostiklogg information om fel i inkommande och utgående data.

## <a name="diagnostic-log-schema"></a>Diagnostiklogg schema

Se [felsöka Azure Stream Analytics med hjälp av diagnostikloggar](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) att visa schemat för diagnostikloggar. Följande JSON är ett exempelvärde för den **egenskaper** i diagnostiklogg för ett datafel.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Indata-fel

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Orsak: Den inkommande Komprimeringstypen valt matchar inte data.
* Portal-meddelande tillhandahålls: Ja
* Diagnostiklogg nivå: Varning!
* Logginformation
   * Ange meddelandeidentifieraren. Identifieraren är den PartitionId, förskjutningen och sekvensnumret för Event Hub.

**Felmeddelande**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Orsak: Rubriken för indata är ogiltigt. Till exempel har en CSV-fil kolumner med samma namn.
* Portal-meddelande tillhandahålls: Ja
* Diagnostiklogg nivå: Varning!
* Logginformation
   * Ange meddelandeidentifieraren. 
   * Faktiska nyttolast upp till få kilobyte.

**Felmeddelande**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Orsak: Indatakolumnerna definieras med CREATE TABLE eller via TIMESTAMP BY finns inte.
* Portal-meddelande tillhandahålls: Ja
* Diagnostiklogg nivå: Varning!
* Logginformation
   * Ange meddelandeidentifieraren. 
   * Namnen på de kolumner som saknas. 
   * Faktiska nyttolast upp till några få kilobyte.

**Felmeddelanden**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Orsak: Det går inte att konvertera indata till typen i CREATE TABLE-instruktion.
* Portal-meddelande tillhandahålls: Ja
* Diagnostiklogg nivå: Varning!
* Logginformation
   * Ange meddelandeidentifieraren. 
   * Namnet på kolumnen och förväntade typen.

**Felmeddelanden**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Orsak: Indata är inte i rätt format. Till exempel indata är inte giltig JSON.
* Portal-meddelande tillhandahålls: Ja
* Diagnostiklogg nivå: Varning!
* Logginformation
   * Ange meddelandeidentifieraren. 
   * Faktiska nyttolast upp till få kilobyte.

**Felmeddelanden**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Orsak: Värdet för TIMESTAMP BY-uttryck kan inte konverteras till datetime.
* Portal-meddelande tillhandahålls: Ja
* Diagnostiklogg nivå: Varning!
* Logginformation
   * Ange meddelandeidentifieraren. 
   * Felmeddelande. 
   * Faktiska nyttolast upp till få kilobyte.

**Felmeddelande**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Orsak: Värdet för TIMESTAMP BY OVER timestampColumn är NULL.
* Portal-meddelande tillhandahålls: Ja
* Diagnostiklogg nivå: Varning!
* Logginformation
   * Den faktiska nyttolasten upp till få kilobyte.

**Felmeddelande**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Orsak: Skillnaden mellan programmet och ankomst tiden är större än sent ankomst tolerans fönster.
* Portal-meddelande tillhandahålls: Nej
* Diagnostiklogg nivå: Information
* Logginformation
   * Tid för program och ankomsttid. 
   * Faktiska nyttolast upp till få kilobyte.

**Felmeddelande**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Orsak: Skillnaden mellan programmet och ankomst tiden är större än 5 minuter.
* Portal-meddelande tillhandahålls: Nej
* Diagnostiklogg nivå: Information
* Logginformation
   * Tid för program och ankomsttid. 
   * Faktiska nyttolast upp till få kilobyte.

**Felmeddelande**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Orsak: Händelsen anses oordnade enligt fönstret oordnade tolerans som definierats.
* Portal-meddelande tillhandahålls: Nej
* Diagnostiklogg nivå: Information
* Logginformation
   * Faktiska nyttolast upp till få kilobyte.

**Felmeddelande**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Datafel för utdata

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Orsak: Kolumnen som krävs för utdata finns inte. Till exempel en kolumn som har definierats som Azure Table PartitionKey does't finns.
* Portal-meddelande tillhandahålls: Ja
* Diagnostiklogg nivå: Varning!
* Logginformation
   * Namnet på kolumnen och antingen index eller en del av posten.

**Felmeddelande**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Orsak: Värdet i kolumnen stämmer inte överens med med utdata. Kolumnnamnet är till exempel inte en giltig Azure tabellkolumn.
* Portal-meddelande tillhandahålls: Ja
* Diagnostiklogg nivå: Varning!
* Logginformation
   * Namnet på kolumnen och antingen post-ID eller en del av posten.

**Felmeddelande**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Orsak: En kolumn kan inte konverteras till en giltig typ i utdata. Till exempel är värdet för kolumnen inkompatibel med begränsningar eller typ som definierats i SQL-tabell.
* Portal-meddelande tillhandahålls: Ja
* Diagnostiklogg nivå: Varning!
* Logginformation
   * Namnet på kolumnen.
   * Post-ID eller en del av posten.

**Felmeddelande**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Orsak: Värdet för meddelandet är större än storleken som stöds utdata. Till exempel är en post större än 1 MB för en Event Hub-utdata.
* Portal-meddelande tillhandahålls: Ja
* Diagnostiklogg nivå: Varning!
* Logginformation
   * Post-ID eller en del av posten.

**Felmeddelande**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Orsak: En post innehåller redan en kolumn med samma namn som en systemkolumn. Till exempel CosmosDB-utdata med en kolumn med namnet ID när ID-kolumnen är till en annan kolumn.
* Portal-meddelande tillhandahålls: Ja
* Diagnostiklogg nivå: Varning!
* Logginformation
   * Namnet på kolumnen.
   * Post-ID eller en del av posten.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Nästa steg

* [Felsöka Azure Stream Analytics med hjälp av diagnostikloggar](stream-analytics-job-diagnostic-logs.md)

* [Förstå övervakning av Stream Analytics-jobb och övervaka frågor](stream-analytics-monitoring.md)
