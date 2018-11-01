---
title: Feltolerans för kopieringsaktiviteten i Azure Data Factory | Microsoft Docs
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
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: jingwang
ms.openlocfilehash: 3f207cdb3af3f7e328cd5843053240bbbe15980e
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418351"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Feltolerans för kopieringsaktiviteten i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Aktuell version](copy-activity-fault-tolerance.md)

Kopieringsaktivitet i Azure Data Factory erbjuder två metoder för att hantera inkompatibla rader vid kopiering av data mellan datalager för källa och mottagare:

- Du kan avbryta och växla kopian aktivitet när det inkompatibla data påträffades (standardinställning).
- Du kan fortsätta att kopiera alla data genom att lägga till feltolerans och hoppar över inkompatibel datarader. Du kan också logga inkompatibla rader i Azure Blob storage eller Azure Data Lake Store. Du kan sedan Kontrollera loggen om du vill lära dig orsaken till felet, rätta data på datakällan och försök kopieringsaktiviteten.

## <a name="supported-scenarios"></a>Scenarier som stöds
Kopieringsaktivitet stöder tre scenarier för att identifiera, hoppar över och loggning inkompatibla data:

- **Inkompatibilitet mellan källdatatyp och interna Mottagartyp**. 

    Till exempel: kopiera data från en CSV-fil i Blob storage till en SQL-databas med en schemadefinitionen som innehåller tre kolumner av typen INT. Rader för CSV-fil som innehåller numeriska data, till exempel 123,456,789 är har kopierats till arkivet mottagare. Men de rader som innehåller icke-numeriska värden, till exempel 123,456, abc identifieras som inkompatibel och hoppas över.

- **Matchar inte antalet kolumner mellan källan och mottagaren**.

    Till exempel: kopiera data från en CSV-fil i Blob storage till en SQL-databas med en schemadefinitionen som innehåller sex kolumner. Rader för CSV-fil som innehåller sex kolumner är har kopierats till arkivet mottagare. Rader för CSV-fil som innehåller fler eller färre än sex kolumner identifieras som inkompatibel och hoppas över.

- **Primär felet när du skriver till SQL Server/Azure SQL Database-/ Azure Cosmos DB**.

    Till exempel: kopiera data från en SQLServer till en SQL-databas. En primärnyckel har definierats i mottagare SQL-databasen, men ingen primär nyckel har definierats i käll-SQL-servern. Duplicerade raderna som finns i källan kan inte kopieras till mottagaren. Kopieringsaktiviteten kopierar endast den första raden i källdata till mottagaren. Efterföljande källraderna som innehåller duplicerade primärnyckelvärdet identifieras som inkompatibel och hoppas över.

>[!NOTE]
>- För att läsa in data till SQL Data Warehouse med PolyBase, konfigurera Polybases interna fel inställningarna genom att ange avvisa villkorsprinciper via ”[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)” i kopieringsaktiviteten. Du kan fortfarande aktivera dirigerar om PolyBase inkompatibla rader till Blob eller ADLS som vanligt enligt nedan.
>- Den här funktionen gäller inte när kopieringsaktiviteten är konfigurerad för att anropa [Amazon Redshift-avlastning](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).


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
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Egenskap  | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Anger om du vill hoppa över inkompatibla rader vid kopiering eller inte. | True<br/>FALSKT (standard) | Nej
redirectIncompatibleRowSettings | En grupp egenskaper som kan anges när du vill logga inkompatibla rader. | &nbsp; | Nej
linkedServiceName | Den länkade tjänsten av [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) eller [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) att lagra loggen som innehåller raderna hoppades över. | Namnet på en `AzureStorage` eller `AzureDataLakeStore` skriver länkad tjänst som refererar till den instans som du vill använda för att lagra loggfilen. | Nej
sökväg | Sökvägen till loggfilen som innehåller raderna hoppades över. | Ange sökvägen som du vill använda för att logga inkompatibla data. Om du inte anger en sökväg, skapar tjänsten en behållare. | Nej

## <a name="monitor-skipped-rows"></a>Övervaka hoppades över rader
När körningen av kopieringsaktiviteten är klar ser du hur många hoppades över rader i utdata för kopieringsaktiviteten:

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
Om du konfigurerar för att logga inkompatibla rader, hittar du i loggfilen på den här sökvägen: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Loggfilerna kan bara vara csv-filer. Den ursprungliga informationen som hoppas över loggas med kommatecken som avgränsare för kolumnen vid behov. Vi lägga till två kolumner ”ErrorCode” och ”ErrorMessage” i ytterligare ursprungliga källdata i loggfilen, där du kan se roten orsakerna till inkompatibilitet. Felkod och ErrorMessage innehålla citattecken med dubbla citattecken. 

Ett exempel på log filinnehållet är följande:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Nästa steg
Se de andra artiklarna i Kopieringsaktiviteten:

- [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
- [Kopiera aktivitet prestanda](copy-activity-performance.md)


