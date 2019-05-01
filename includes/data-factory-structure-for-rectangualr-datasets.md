---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309205"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Ange strukturdefinition för rektangulära datauppsättningar
Avsnittet strukturen i datauppsättningar JSON är en **valfritt** för rektangulär tabeller (med rader och kolumner) och innehåller en uppsättning kolumner för tabellen. Du använder avsnittet struktur för antingen givande anger du följande information typkonverteringar eller göra kolumnmappningarna. I följande avsnitt beskrivs de här funktionerna i detalj. 

Varje kolumn innehåller följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Namnet på kolumnen. |Ja |
| typ |Datatypen för kolumnen. Se typen konverteringar avsnittet nedan finns mer information om när ska du ange typinformation |Nej |
| kultur |.NET baserade språkmiljö som ska användas när typ har angetts och .NET-typen Datetime eller Datetimeoffset. Standardvärdet är ”en-us”. |Nej |
| Format |Formatera strängen som ska användas när typ har angetts och är .NET typen Datetime eller Datetimeoffset. |Nej |

I följande exempel visas avsnittet struktur JSON för en tabell med tre kolumner användar-ID, namn och lastlogindate.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Använd följande riktlinjer för när med ”strukturera” information och vad som ska ingå i den **struktur** avsnittet.

* **För strukturerade datakällor** att spara data schema- och typ av information tillsammans med själva (källor som SQL Server, Oracle, Azure-tabell osv), bör du ange avsnittet ”struktur” om du vill att informationen inte kolumnmappning av viss källa kolumner till specifika kolumner i mottagare och deras namn är inte samma (Mer information finns i kolumnen mappning nedan). 
  
    Som nämnts ovan är är informationen valfri i avsnittet ”struktur”. För strukturerade datakällor typinformation finns redan som en del av definitionen av datauppsättningen i datalagret, så du får inte innehålla anger du följande information när du inkluderar ”struktur”-avsnittet.
* **För schema vid läsning datakällor (speciellt Azure blob)** kan du lagra data utan att behöva lagra någon schema eller typ information med data. För dessa typer av datakällor ska du inkludera ”struktur” i följande fall 2:
  * Du vill göra kolumnmappning.
  * När datauppsättningen är en källa i en Kopieringsaktivitet, du kan ange anger du följande information i ”strukturen” och data factory ska använda den här typinformation för konvertering till inbyggda typer för mottagaren. Se [flytta data till och från Azure Blob](../articles/data-factory/v1/data-factory-azure-blob-connector.md) artikeln för mer information.

### <a name="supported-net-based-types"></a>Stöd för. NET-baserade typer
Data factory stöder följande CLS kompatibla .NET baserat typvärden för att tillhandahålla anger du följande information i ”strukturen” för schemat på läsdata källor som Azure blob.

* Int16
* Int32 
* Int64
* Single
* Double
* Decimal
* Byte[]
* Booleskt
* String 
* Guid
* DateTime
* Datetimeoffset
* Tidsintervall 

Du kan även ange ”kultur” & ”format” sträng för att underlätta tolkning av din anpassade Datetime-sträng för Datetime & Datetimeoffset. Se exempel för typkonvertering nedan.

