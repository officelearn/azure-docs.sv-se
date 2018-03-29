---
title: Feltolerans kopiera aktivitet i Azure Data Factory | Microsoft Docs
description: Läs mer om hur du lägger till feltolerans för att kopiera aktivitet i Azure Data Factory genom att hoppa över inkompatibla rader.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: jingwang
ms.openlocfilehash: 4fef9a9d30adb48f8f68d34e35a7436c04b63125
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Feltolerans kopiera aktivitet i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Version 2 – förhandsversion](copy-activity-fault-tolerance.md)

Kopieringsaktiviteten i Azure Data Factory finns två sätt att hantera inkompatibla rader vid kopiering av data mellan käll- och mottagarnoderna datalager:

- Du kan avbryta och misslyckas kopieringen aktivitet när inkompatibla data påträffades (standardinställning).
- Du kan fortsätta att kopiera alla data genom att lägga till feltolerans och hoppar över inkompatibla datarader. Dessutom kan du logga inkompatibla raderna i Azure Blob storage eller Azure Data Lake Store. Du kan sedan Kontrollera loggen om du vill veta orsaken till felet, rätta data på datakällan och försök kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [kopiera feltolerans för aktiviteten i V1](v1/data-factory-copy-activity-fault-tolerance.md).


 ## <a name="supported-scenarios"></a>Scenarier som stöds
Kopieringsaktiviteten stöder tre scenarier för identifiering, hoppar över och loggning inkompatibla data:

- **Inkompatibilitet mellan källdatatyp och interna Mottagartypen**. 

    Till exempel: kopiera data från en CSV-fil i Blob storage till en SQL-databas med en schemadefinition som innehåller tre kolumner av typen INT. CSV-filen rader som innehåller numeriska data, till exempel 123,456,789 har kopierats till arkivet sink. Men de rader som innehåller icke-numeriska värden, till exempel 123,456, abc identifieras som inkompatibel och hoppas över.

- **Matchningsfel i antalet kolumner mellan käll- och sink**.

    Till exempel: kopiera data från en CSV-fil i Blob storage till en SQL-databas med en schemadefinition som innehåller sex kolumner. Rader för CSV-fil som innehåller sex kolumner har kopierats till arkivet mottagare. CSV-filen rader som innehåller fler eller färre än sex kolumner identifieras som inkompatibel och hoppas över.

- **Primärnyckelfel vid skrivning till SQL Server-/ Azure SQL Database-/ Azure Cosmos DB**.

    Till exempel: kopiera data från en SQLServer till en SQL-databas. En primär nyckel har definierats i sink SQL-databasen, men ingen primär nyckel har definierats i källan SQLServer. Duplicerade rader som finns i källan kan inte kopieras till sink. Kopieringsaktiviteten kopieras bara den första raden i källdata till sink. Efterföljande källraderna som innehåller duplicerade primärnyckelvärdet identifieras som inkompatibel och hoppas över.

>[!NOTE]
>Den här funktionen gäller inte när kopieringsaktiviteten är konfigurerad för att anropa externa datainläsning mekanism inklusive [Azure SQL Data Warehouse PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) eller [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift). För att läsa in data i SQL Data Warehouse med PolyBase använda Polybases interna feltolerans stöd genom att ange ”[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)” i en Kopieringsaktivitet.

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
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Anger om du vill hoppa över inkompatibla raderna vid kopiering eller inte. | True<br/>FALSKT (standard) | Nej
redirectIncompatibleRowSettings | En grupp egenskaper som kan anges när du vill logga inkompatibla rader. | &nbsp; | Nej
linkedServiceName | Den länkade tjänsten av [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) eller [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) att lagra loggen som innehåller raderna hoppades över. | Namnet på en `AzureStorage` eller `AzureDataLakeStore` skriver länkade tjänst som refererar till den instans som du vill använda för att lagra loggfilen. | Nej
sökväg | Sökvägen till loggfilen som innehåller raderna hoppades över. | Ange den sökväg som du vill använda för att logga inkompatibla data. Om du inte anger en sökväg, skapas en behållare av tjänsten. | Nej

## <a name="monitor-skipped-rows"></a>Övervaka överhoppade rader
När kopieringsaktiviteten kör har slutförts kan se du antalet överhoppade rader i utdata för kopieringsaktiviteten:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Om du konfigurerar för att logga inkompatibla rader du hittar filen på den här sökvägen: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Loggfilerna kan bara vara csv-filer. Den ursprungliga informationen hoppas loggas med kommatecken som avgränsare för kolumnen vid behov. Vi lägga till två kolumner ”ErrorCode” och ”ErrorMessage” i ytterligare till den ursprungliga källinformationen i loggfilen, där du kan se roten orsak till inkompatibilitet. ErrorCode och ErrorMessage ska anges med dubbla citattecken. 

Ett exempel på filinnehåll loggen är följande:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Nästa steg
Finns de andra Kopieringsaktiviteten artiklarna:

- [Aktiviteten-kopia – översikt](copy-activity-overview.md)
- [Kopiera aktivitet prestanda](copy-activity-performance.md)


