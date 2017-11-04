## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Ange struktur definition för rektangulär datauppsättningar
Avsnittet strukturen i datauppsättningar JSON är en **valfria** avsnittet för rektangulär tabeller (med rader och kolumner) och innehåller en uppsättning kolumner för tabellen. Du använder avsnittet struktur för antingen tillhandahåller typinformation för typkonverteringar eller göra kolumnmappningarna. I följande avsnitt beskrivs de här funktionerna i detalj. 

Varje kolumn innehåller följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Namnet på kolumnen. |Ja |
| typ |Datatypen för kolumnen. Se typen konverteringar avsnittet nedan för mer information om när ska du ange typinformation |Nej |
| Kultur |.NET baserat kulturen som ska användas när typ har angetts och är .NET typen Datetime eller Datetimeoffset. Standardvärdet är ”en-us”. |Nej |
| Format |Formatsträng som ska användas när typ har angetts och är .NET typen Datetime eller Datetimeoffset. |Nej |

I följande exempel visas avsnittet struktur JSON för en tabell som har tre kolumner användar-ID, namn och lastlogindate.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Använd följande riktlinjer för när du ska inkludera ”struktur” information och vad som ska ingå i den **struktur** avsnitt.

* **För strukturerade datakällor** att lagra data schema och ange information tillsammans med själva (källor som Azure tabell för SQL Server, Oracle, etc.), bör du ange avsnittet ”struktur” om du vill att informationen göra kolumnmappningen för specifika källa kolumner till specifika kolumner i mottagare och deras namn är inte samma (Mer information finns i kolumnen mappning nedan). 
  
    Som nämnts ovan är är typinformationen valfri i avsnittet ”struktur”. För strukturerade källor typinformation finns redan som en del av definitionen för datamängden i datalagret, så du bör inte inkludera typinformation när du inkluderar avsnittet ”struktur”.
* **För schemat för skrivskyddade datakällor (specifikt Azure blob)** kan du lagra data utan att spara schemat eller typ information med data. Du bör ta ”struktur” för dessa typer av datakällor i följande fall 2:
  * Du vill göra kolumnmappningen.
  * När dataset är en datakälla i en Kopieringsaktivitet kan du kan ange av typinformation i ”struktur” och data factory använder den här typinformation för konvertering till inbyggda typer för sink. Se [flytta data till och från Azure Blob](../articles/data-factory/v1/data-factory-azure-blob-connector.md) artikel för mer information.

### <a name="supported-net-based-types"></a>Stöd för. NET-baserade typer
Data factory stöder följande CLS kompatibla .NET baserat typen värden för att ange information i ”struktur” för schemat för skrivskyddade datakällor som Azure blob.

* Int16
* Int32 
* Int64
* Enskild
* dubbla
* Decimal
* byte]
* bool
* Sträng 
* GUID
* Datum och tid
* DateTimeOffset
* Tidsintervall 

Du kan också ange ”kultur” & ”format” sträng för att underlätta tolkning av din egen Datetime-sträng för Datetime & Datetimeoffset. Se exemplet för typkonvertering nedan.

