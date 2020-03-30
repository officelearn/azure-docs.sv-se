---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67188013"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Ange strukturdefinition för rektangulära datauppsättningar
Strukturavsnittet i datauppsättningarna JSON är ett **valfritt** avsnitt för rektangulära tabeller (med rader & kolumner) och innehåller en samling kolumner för tabellen. Du kommer att använda strukturavsnittet för att antingen ange typinformation för typkonverteringar eller göra kolumnmappningar. I följande avsnitt beskrivs dessa funktioner i detalj. 

Varje kolumn innehåller följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Kolumnens namn. |Ja |
| typ |Datatyp för kolumnen. Se avsnittet typkonverteringar nedan för mer information om när ska du ange typinformation |Inga |
| Kultur |.NET-baserad odling som ska användas när typen anges och är .NET-typ Datetime eller Datetimeoffset. Standard är "en-us". |Inga |
| format |Formatera sträng som ska användas när typen anges och är .NET-typ Datetime eller Datetimeoffset. |Inga |

I följande exempel visas strukturavsnittet JSON för en tabell som har tre kolumner userid, namn och lastlogindate.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Använd följande riktlinjer för när du ska inkludera "struktur" **structure** information och vad som ska ingå i strukturavsnittet.

* **För strukturerade datakällor** som lagrar dataschema och skrivarinformation tillsammans med själva data (källor som SQL Server, Oracle, Azure-tabell etc.) bör du bara ange avsnittet "struktur" om du vill göra kolumnmappning av specifika källkolumner till specifika kolumner i diskhon och deras namn är inte desamma (se information i kolumnmappningsavsnittet nedan). 
  
    Som nämnts ovan är typinformationen valfri i avsnittet "struktur". För strukturerade källor är typinformation redan tillgänglig som en del av datauppsättningsdefinitionen i datalagret, så du bör inte inkludera typinformation när du inkluderar avsnittet "struktur".
* **För schema på läsdatakällor (särskilt Azure blob)** kan du välja att lagra data utan att lagra något schema eller skriva information med data. För dessa typer av datakällor bör du inkludera "struktur" i följande 2 fall:
  * Du vill göra kolumnmappning.
  * När datauppsättningen är en källa i en kopieringsaktivitet kan du ange typinformation i "struktur" och datafabriken använder den här typen av information för konvertering till inbyggda typer för diskhon. Mer information finns i Flytta data till och från Azure [Blob-artikel.](../articles/data-factory/v1/data-factory-azure-blob-connector.md)

### <a name="supported-net-based-types"></a>Stöds. NET-baserade typer
Data factory stöder följande CLS-kompatibla .NET-baserade typvärden för att tillhandahålla typinformation i "struktur" för schema på läsa datakällor som Azure blob.

* Int16 (int16)
* Int32 
* Int64
* Enkel
* Double
* Decimal
* Byte[]
* Bool
* String 
* GUID
* Datumtid
* Datumtidsdatum
* Tidsintervall 

För Datetime & Datetimeoffset kan du också ange "kultur" & "format"-sträng för att underlätta tolkning av din anpassade Datetime-sträng. Se exempel för typkonvertering nedan.

