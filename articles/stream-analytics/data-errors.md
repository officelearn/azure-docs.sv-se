---
title: Diagnostikloggdatafel i Azure Stream Analytics
description: I den här artikeln beskrivs de olika indata- och utdatafel som kan uppstå när Du använder Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 0546464b4d1bcc9eaa4fbffe265486985d9c58f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465025"
---
# <a name="azure-stream-analytics-data-errors"></a>Azure Stream Analytics-datafel

Datafel är fel som uppstår vid bearbetning av data.  Dessa fel uppstår oftast under datade serialisering, serialisering och skrivåtgärder.  När datafel inträffar skriver Stream Analytics detaljerad information och exempelhändelser till diagnostikloggarna.  I vissa fall kan även en sammanfattning av denna information tillhandahållas via portalmeddelanden.

I den här artikeln beskrivs de olika feltyperna, orsakerna och diagnostikloggens information för in- och utdatafel.

## <a name="diagnostic-log-schema"></a>Schema för diagnostikloggar

Se [Felsöka Azure Stream Analytics med hjälp av diagnostikloggar](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) för att se schemat för diagnostikloggar. Följande JSON är ett exempelvärde för fältet **Egenskaper** i en diagnostiklogg för ett datafel.

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

## <a name="input-data-errors"></a>Indatafel

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Orsak: Den valda indatakomprimeringstypen matchar inte data.
* Portalmeddelande: Ja
* Diagnostisk loggnivå: Varning
* Effekt: Meddelanden med eventuella deserialiseringsfel, inklusive ogiltig komprimeringstyp, tas bort från indata.
* Logga information
   * Indatameddelandeidentifierare. För Event Hub är identifieraren PartitionId, Offset och Sequence Number.

**Felmeddelande**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Orsak: Rubriken för indata är ogiltigt. En CSV har till exempel kolumner med dubblettnamn.
* Portalmeddelande: Ja
* Diagnostisk loggnivå: Varning
* Effekt: Meddelanden med eventuella deserialiseringsfel, inklusive ogiltigt huvud, tas bort från indata.
* Logga information
   * Indatameddelandeidentifierare. 
   * Faktisk nyttolast upp till några kilobyte.

**Felmeddelande**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Orsak: Indatakolumnerna som definierats med SKAPA TABELL eller via TIMESTAMP BY finns inte.
* Portalmeddelande: Ja
* Diagnostisk loggnivå: Varning
* Effekt: Händelser med saknade kolumner tas bort från indata.
* Logga information
   * Indatameddelandeidentifierare. 
   * Namn på de kolumner som saknas. 
   * Faktisk nyttolast upp till några kilobyte.

**Felmeddelanden**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Orsak: Det gick inte att konvertera indata till den typ som anges i CREATE TABLE-satsen.
* Portalmeddelande: Ja
* Diagnostisk loggnivå: Varning
* Effekt: Händelser med typkonverteringsfel tas bort från indata.
* Logga information
   * Indatameddelandeidentifierare. 
   * Namn på kolumnen och förväntad typ.

**Felmeddelanden**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Orsak: Indata är inte i rätt format. Indata är till exempel inte giltig JSON.
* Portalmeddelande: Ja
* Diagnostisk loggnivå: Varning
* Effekt: Alla händelser i meddelandet efter att ett ogiltigt datafel har påträffats tas bort från indata.
* Logga information
   * Indatameddelandeidentifierare. 
   * Faktisk nyttolast upp till några kilobyte.

**Felmeddelanden**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>OgiltiginputTimeStamp

* Orsak: Värdet för TIMESTAMP BY-uttrycket kan inte konverteras till datetime.
* Portalmeddelande: Ja
* Diagnostisk loggnivå: Varning
* Effekt: Händelser med ogiltig indatatidsstämpel tas bort från indata.
* Logga information
   * Indatameddelandeidentifierare. 
   * Felmeddelande. 
   * Faktisk nyttolast upp till några kilobyte.

**Felmeddelande**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>OgiltigInputTimeStampKey

* Orsak: Värdet för TIMESTAMP BY OVER timestampColumn är NULL.
* Portalmeddelande: Ja
* Diagnostisk loggnivå: Varning
* Effekt: Händelser med ogiltig tidsstämpelnyckel tas bort från indata.
* Logga information
   * Den faktiska nyttolasten upp till några kilobyte.

**Felmeddelande**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Orsak: Skillnaden mellan ansökningstid och ankomsttid är större än toleransfönstret för sen ankomst.
* Portalmeddelande: Nej
* Diagnostisk loggnivå: Information
* Effekt: Sena indatahändelser hanteras enligt inställningen "Hantera andra händelser" i avsnittet Händelsebeställning i jobbkonfigurationen. Mer information finns i [Principer för tidshantering](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Logga information
   * Ansökningstid och ankomsttid. 
   * Faktisk nyttolast upp till några kilobyte.

**Felmeddelande**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Orsak: Skillnaden mellan ansökningstid och ankomsttid är större än 5 minuter.
* Portalmeddelande: Nej
* Diagnostisk loggnivå: Information
* Effekt: Tidiga indatahändelser hanteras enligt inställningen "Hantera andra händelser" i avsnittet Händelsebeställning i jobbkonfigurationen. Mer information finns i [Principer för tidshantering](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Logga information
   * Ansökningstid och ankomsttid. 
   * Faktisk nyttolast upp till några kilobyte.

**Felmeddelande**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>UtanförOrderevent

* Orsak: Händelsen betraktas som i oordning enligt det definierade toleransfönstret i oordning.
* Portalmeddelande: Nej
* Diagnostisk loggnivå: Information
* Effekt: Händelser i oordning hanteras enligt inställningen Hantera andra händelser i avsnittet Händelsebeställning i jobbkonfigurationen. Mer information finns i [Principer för tidshantering](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Logga information
   * Faktisk nyttolast upp till några kilobyte.

**Felmeddelande**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Datafel för utdata

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Orsak: Kolumnen som krävs för utdata finns inte. En kolumn som definieras som Azure Table PartitionKey finns till exempel inte.
* Portalmeddelande: Ja
* Diagnostisk loggnivå: Varning
* Effekt: Alla datakonverteringsfel, inklusive den saknade obligatoriska kolumnen, hanteras enligt inställningen [För utdataprincip.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Logga information
   * Kolumnens namn och antingen postidentifieraren eller en del av posten.

**Felmeddelande**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Orsak: Kolumnvärdet överensstämmer inte med utdata. Kolumnnamnet är till exempel inte en giltig Azure-tabellkolumn.
* Portalmeddelande: Ja
* Diagnostisk loggnivå: Varning
* Effekt: Alla utdatakonverteringsfel inklusive ogiltigt kolumnnamn hanteras enligt inställningen [Utdataprincip.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Logga information
   * Namn på kolumnen och antingen postidentifierare eller en del av posten.

**Felmeddelande**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Orsak: Det går inte att konvertera en kolumn till en giltig typ i utdata. Värdet för kolumnen är till exempel inkompatibelt med begränsningar eller typ som definieras i SQL-tabellen.
* Portalmeddelande: Ja
* Diagnostisk loggnivå: Varning
* Effekt: Alla utdatakonverteringsfel, inklusive typkonverteringsfel, hanteras enligt inställningen [För utdataprincip.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Logga information
   * Kolumnens namn.
   * Antingen postidentifierare eller en del av posten.

**Felmeddelande**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceedEdSizeLimit

* Orsak: Värdet för meddelandet är större än utdatastorleken som stöds. En post är till exempel större än 1 MB för en eventhubb-utdata.
* Portalmeddelande: Ja
* Diagnostisk loggnivå: Varning
* Effekt: Alla konverteringsfel för utdata, inklusive post överskred storleksgränsen, hanteras enligt inställningen [för utdataprincip.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Logga information
   * Antingen postidentifierare eller en del av posten.

**Felmeddelande**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Orsak: En post innehåller redan en kolumn med samma namn som en systemkolumn. CosmosDB-utdata med en kolumn med namnet ID när ID-kolumnen är till en annan kolumn.
* Portalmeddelande: Ja
* Diagnostisk loggnivå: Varning
* Effekt: Alla utdatakonverteringsfel inklusive dubblettnyckel hanteras enligt inställningen [Utdataprincip.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Logga information
   * Kolumnens namn.
   * Antingen postidentifierare eller en del av posten.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Nästa steg

* [Felsöka Azure Stream Analytics med hjälp av diagnostikloggar](stream-analytics-job-diagnostic-logs.md)

* [Förstå Stream Analytics jobbövervakning och hur du övervakar frågor](stream-analytics-monitoring.md)
