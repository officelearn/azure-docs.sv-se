---
title: Feltolerans för kopieringsaktivitet i Azure Data Factory
description: Lär dig mer om hur du lägger till feltolerans för att kopiera aktivitet i Azure Data Factory genom att hoppa över de inkompatibla raderna.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: 42c637839172dab09a8721a93a67785a748afd2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75708910"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Feltolerans för kopieringsaktivitet i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Aktuell version](copy-activity-fault-tolerance.md)

Kopieringsaktiviteten i Azure Data Factory erbjuder två sätt att hantera inkompatibla rader när du kopierar data mellan käll- och sink-datalager:

- Du kan avbryta och misslyckas med kopieringsaktiviteten när inkompatibla data påträffas (standardbeteende).
- Du kan fortsätta att kopiera alla data genom att lägga till feltolerans och hoppa över inkompatibla datarader. Dessutom kan du logga de inkompatibla raderna i Azure Blob storage eller Azure Data Lake Store. Du kan sedan undersöka loggen för att lära dig orsaken till felet, åtgärda data på datakällan och försöka kopiera aktiviteten igen.

## <a name="supported-scenarios"></a>Scenarier som stöds
Kopieringsaktivitet stöder tre scenarier för identifiering, hoppa över och logga inkompatibla data:

- **Inkompatibilitet mellan källdatatypen och den inbyggda sink-typen**. 

    Kopiera data från en CSV-fil i Blob-lagring till en SQL-databas med en schemadefinition som innehåller tre INT-typkolumner. Csv-filraderna som innehåller numeriska data, till exempel 123 456 789 kopieras framgångsrikt till sink-arkivet. Raderna som innehåller icke-numeriska värden, till exempel 123 456, abc identifieras dock som inkompatibla och hoppas över.

- **Matchar inte antalet kolumner mellan källan och diskhon**.

    Kopiera data från en CSV-fil i Blob-lagring till en SQL-databas med en schemadefinition som innehåller sex kolumner. Csv-filraderna som innehåller sex kolumner kopieras framgångsrikt till sink-arkivet. Csv-filraderna som innehåller fler än sex kolumner identifieras som inkompatibla och hoppas över.

- **Fel vid primärnyckel när du skriver till SQL Server/Azure SQL Database/Azure Cosmos DB**.

    Kopiera data från en SQL-server till en SQL-databas. En primärnyckel definieras i sink SQL-databasen, men ingen sådan primärnyckel definieras i källan SQL-servern. De duplicerade rader som finns i källan kan inte kopieras till diskhon. Kopiera aktivitet kopierar bara den första raden i källdata till diskhon. De efterföljande källraderna som innehåller det duplicerade primärnyckelvärdet identifieras som inkompatibla och hoppas över.

>[!NOTE]
>- För inläsning av data i SQL Data Warehouse med PolyBase konfigurerar du PolyBases inbyggda inställningar för feltolerans genom att ange avvisningsprinciper via "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" i kopieringsaktivitet. Du kan fortfarande aktivera omdirigering av PolyBase inkompatibla rader till Blob eller ADLS som vanligt enligt nedan.
>- Den här funktionen gäller inte när kopieringsaktiviteten är konfigurerad för att anropa [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Den här funktionen gäller inte när kopieringsaktivitet är konfigurerad för att anropa en [lagrad procedur från en SQL-diskho](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink).

## <a name="configuration"></a>Konfiguration
I följande exempel finns en JSON-definition för att konfigurera hoppa över de inkompatibla raderna i Kopieringsaktivitet:

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
enableSkipIncompatibleRow | Anger om inkompatibla rader ska hoppas under kopian eller inte. | True<br/>False (standard) | Inga
redirectIncompatibleRowSettings | En grupp egenskaper som kan anges när du vill logga de inkompatibla raderna. | &nbsp; | Inga
linkedServiceName | Den länkade tjänsten [för Azure Storage](connector-azure-blob-storage.md#linked-service-properties) eller Azure Data Lake [Store](connector-azure-data-lake-store.md#linked-service-properties) för att lagra loggen som innehåller de överhoppade raderna. | Namnet på `AzureStorage` en `AzureDataLakeStore` eller typlänkd tjänst, som refererar till den instans som du vill använda för att lagra loggfilen. | Inga
path | Sökvägen till loggfilen som innehåller de överhoppade raderna. | Ange den sökväg som du vill använda för att logga inkompatibla data. Om du inte anger en sökväg skapar tjänsten en behållare åt dig. | Inga

## <a name="monitor-skipped-rows"></a>Övervaka överhoppade rader
När kopieringsaktiviteten har körts är klar kan du se antalet överhoppade rader i utdata från kopieringsaktiviteten:

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
Om du konfigurerar för att logga de inkompatibla raderna `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`hittar du loggfilen på den här sökvägen: . 

Loggfilerna kan bara vara csv-filerna. De ursprungliga data som hoppas över loggas med kommatecken som kolumn avgränsare om det behövs. Vi lägger till ytterligare två kolumner "ErrorCode" och "ErrorMessage" i tillägg till den ursprungliga källdata i loggfilen, där du kan se orsaken till inkompatibiliteten. ErrorCode och ErrorMessage kommer att citeras med dubbla citattecken. 

Ett exempel på loggfilsinnehållet är följande:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Nästa steg
Se de andra artiklarna för kopieringsaktivitet:

- [Kopiera aktivitetsöversikt](copy-activity-overview.md)
- [Kopiera aktivitetsprestanda](copy-activity-performance.md)


