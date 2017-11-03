---
title: "Lägg till feltolerans i Azure Data Factory-Kopieringsaktiviteten genom att hoppa över inkompatibla rader | Microsoft Docs"
description: "Hur du lägger till feltolerans i Azure Data Factory-Kopieringsaktiviteten genom att hoppa över inkompatibla rader vid kopiering"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5c32d4ac2c1179a83a82bd5deb41047b82e43b7e
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Lägg till feltolerans i en Kopieringsaktivitet genom att hoppa över inkompatibla rader
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [feltolerans i en Kopieringsaktivitet i Data Factory version 2](../copy-activity-fault-tolerance.md).

Azure Data Factory [Kopieringsaktiviteten](data-factory-data-movement-activities.md) ger dig två sätt att hantera inkompatibla rader vid kopiering av data mellan käll- och mottagarnoderna datalager:

- Du kan avbryta och misslyckas kopieringen aktivitet när inkompatibla data påträffades (standardinställning).
- Du kan fortsätta att kopiera alla data genom att lägga till feltolerans och hoppar över inkompatibla datarader. Dessutom kan du logga inkompatibla raderna i Azure Blob storage. Du kan sedan Kontrollera loggen om du vill veta orsaken till felet, rätta data på datakällan och försök kopieringsaktiviteten.

## <a name="supported-scenarios"></a>Scenarier som stöds
Kopieringsaktiviteten stöder tre scenarier för identifiering, hoppar över och loggning inkompatibla data:

- **Inkompatibilitet mellan källdatatyp och interna Mottagartypen**

    Till exempel: kopiera data från en CSV-fil i Blob storage till en SQL-databas med en schemadefinition som innehåller tre **INT** kolumner av typen. CSV-filen rader som innehåller numeriska data, till exempel `123,456,789` har kopierats till arkivet mottagare. Men raderna som innehåller icke-numeriska värden som `123,456,abc` identifieras som inkompatibel och hoppas över.

- **Matchningsfel i antalet kolumner mellan käll- och sink**

    Till exempel: kopiera data från en CSV-fil i Blob storage till en SQL-databas med en schemadefinition som innehåller sex kolumner. Rader för CSV-fil som innehåller sex kolumner har kopierats till arkivet mottagare. CSV-filen rader som innehåller fler eller färre än sex kolumner identifieras som inkompatibel och hoppas över.

- **Primärnyckelfel vid skrivning till en relationsdatabas**

    Till exempel: kopiera data från en SQLServer till en SQL-databas. En primär nyckel har definierats i sink SQL-databasen, men ingen primär nyckel har definierats i källan SQLServer. Duplicerade rader som finns i källan kan inte kopieras till sink. Kopieringsaktiviteten kopieras bara den första raden i källdata till sink. Efterföljande källraderna som innehåller duplicerade primärnyckelvärdet identifieras som inkompatibel och hoppas över.

## <a name="configuration"></a>Konfiguration
I följande exempel innehåller en JSON-definitionen om du vill konfigurera hoppar över inkompatibla rader i en Kopieringsaktivitet:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },         
    "enableSkipIncompatibleRow": true,           
    "redirectIncompatibleRowSettings": {
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Aktivera hoppar inkompatibla rader under kopia eller inte. | True<br/>FALSKT (standard) | Nej |
| **redirectIncompatibleRowSettings** | En grupp egenskaper som kan anges när du vill logga inkompatibla rader. | &nbsp; | Nej |
| **linkedServiceName** | Den länkade tjänsten av Azure Storage för att lagra loggen som innehåller raderna hoppades över. | Namnet på en [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) eller [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) länkade tjänst som refererar till den instans av lagring som du vill använda för att lagra loggfilen. | Nej |
| **sökväg** | Sökvägen till loggfilen som innehåller raderna hoppades över. | Ange sökvägen för Blob-lagring som du vill använda för att logga inkompatibla data. Om du inte anger en sökväg, skapas en behållare av tjänsten. | Nej |

## <a name="monitoring"></a>Övervakning
När kopieringsaktiviteten kör har slutförts kan se du antalet överhoppade rader i avsnittet övervakning:

![Övervakaren hoppas över inkompatibla rader](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Om du konfigurerar för att logga inkompatibla rader du hittar filen på den här sökvägen: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` i loggfilen du kan se de rader som hoppas över och orsaken till inkompatibilitet.

Både den ursprungliga informationen och motsvarande fel loggas i filen. Ett exempel på filinnehåll loggen är följande:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure Data Factory-Kopieringsaktiviteten i [flytta data med hjälp av Kopieringsaktiviteten](data-factory-data-movement-activities.md).