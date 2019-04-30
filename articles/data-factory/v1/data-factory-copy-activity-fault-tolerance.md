---
title: Lägga till feltolerans i Azure Data Factory-Kopieringsaktiviteten genom att hoppa över inkompatibla rader | Microsoft Docs
description: Lär dig hur du lägger till feltolerans i Azure Data Factory-Kopieringsaktiviteten genom att hoppa över inkompatibla rader vid kopiering
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3a255b21e8bfd7d78954603e9aa6e5ca39cee95b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60566077"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Lägga till feltolerans i Kopieringsaktiviteten genom att hoppa över inkompatibla rader

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-copy-activity-fault-tolerance.md)
> * [Version 2 (aktuell version)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [feltolerans i kopieringsaktiviteten i Data Factory](../copy-activity-fault-tolerance.md).

Azure Data Factory [Kopieringsaktiviteten](data-factory-data-movement-activities.md) ger dig två sätt att hantera inkompatibla rader vid kopiering av data mellan datalager för källa och mottagare:

- Du kan avbryta och växla kopian aktivitet när det inkompatibla data påträffades (standardinställning).
- Du kan fortsätta att kopiera alla data genom att lägga till feltolerans och hoppar över inkompatibel datarader. Du kan också logga inkompatibla rader i Azure Blob storage. Du kan sedan Kontrollera loggen om du vill lära dig orsaken till felet, rätta data på datakällan och försök kopieringsaktiviteten.

## <a name="supported-scenarios"></a>Scenarier som stöds
Kopieringsaktivitet stöder tre scenarier för att identifiera, hoppar över och loggning inkompatibla data:

- **Inkompatibilitet mellan källdatatyp och interna Mottagartyp**

    Exempel: Kopiera data från en CSV-fil i Blob storage till en SQL-databas med en schemadefinitionen som innehåller tre **INT** kolumner av typen. Rader för CSV-fil som innehåller numeriska data, till exempel `123,456,789` har kopierats till arkivet mottagare. Men raderna som innehåller icke-numeriska värden, till exempel `123,456,abc` identifieras som inkompatibel och hoppas över.

- **Matchar inte antalet kolumner mellan källan och mottagaren**

    Exempel: Kopiera data från en CSV-fil i Blob storage till en SQL-databas med en schemadefinitionen som innehåller sex kolumner. Rader för CSV-fil som innehåller sex kolumner är har kopierats till arkivet mottagare. Rader för CSV-fil som innehåller fler eller färre än sex kolumner identifieras som inkompatibel och hoppas över.

- **Primär felet när du skriver till SQL Server/Azure SQL Database-/ Azure Cosmos DB**

    Exempel: Kopiera data från en SQLServer till en SQL-databas. En primärnyckel har definierats i mottagare SQL-databasen, men ingen primär nyckel har definierats i käll-SQL-servern. Duplicerade raderna som finns i källan kan inte kopieras till mottagaren. Kopieringsaktiviteten kopierar endast den första raden i källdata till mottagaren. Efterföljande källraderna som innehåller duplicerade primärnyckelvärdet identifieras som inkompatibel och hoppas över.

>[!NOTE]
>Den här funktionen gäller inte när kopieringsaktiviteten är konfigurerad för att anropa externa datainläsning mekanism inklusive [Azure SQL Data Warehouse PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) eller [Amazon Redshift-avlastning](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). För att läsa in data till SQL Data Warehouse med PolyBase kan du använda Polybases interna fault tolerance support genom att ange ”[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)” i kopieringsaktiviteten.

## <a name="configuration"></a>Konfiguration
I följande exempel innehåller en JSON-definition för att konfigurera hoppar över inkompatibla rader i Kopieringsaktiviteten:

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

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Aktivera hoppas över inkompatibla rader vid kopiering eller inte. | True<br/>FALSKT (standard) | Nej |
| **redirectIncompatibleRowSettings** | En grupp egenskaper som kan anges när du vill logga inkompatibla rader. | &nbsp; | Nej |
| **linkedServiceName** | Den länkade tjänsten av Azure Storage för att lagra loggen som innehåller raderna hoppades över. | Namnet på en [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) eller [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) länkad tjänst som refererar till den storage-instans som du vill använda för att lagra loggfilen. | Nej |
| **path** | Sökvägen till loggfilen som innehåller raderna hoppades över. | Ange sökvägen för Blob-lagring som du vill använda för att logga inkompatibla data. Om du inte anger en sökväg, skapar tjänsten en behållare. | Nej |

## <a name="monitoring"></a>Övervakning
När körningen av kopieringsaktiviteten är klar ser du hur många hoppades över rader i avsnittet övervakning:

![Övervaka hoppades över inkompatibla rader](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Om du konfigurerar för att logga inkompatibla rader, hittar du i loggfilen på den här sökvägen: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` I loggfilen ser du de rader som har hoppats över och de grundläggande orsakerna till inkompatibilitet.

Både den ursprungliga informationen och motsvarande fel loggas i filen. Ett exempel på log filinnehållet är följande:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure Data Factory Kopieringsaktivitet i [flytta data med hjälp av Kopieringsaktiviteten](data-factory-data-movement-activities.md).
