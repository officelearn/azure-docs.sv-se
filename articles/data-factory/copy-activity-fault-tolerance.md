---
title: Feltolerans för kopieringsaktivitet i Azure Data Factory
description: Lär dig mer om hur du lägger till fel tolerans för att kopiera aktiviteter i Azure Data Factory genom att hoppa över de inkompatibla raderna.
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
ms.openlocfilehash: 766520fe44047eee76029adf8ee1683c7b8008a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417869"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Feltolerans för kopieringsaktivitet i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Aktuell version](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Kopierings aktiviteten i Azure Data Factory ger dig två sätt att hantera inkompatibla rader när du kopierar data mellan käll-och mottagar data lager:

- Du kan avbryta och avbryta kopierings aktiviteten när inkompatibla data påträffas (standard beteende).
- Du kan fortsätta att kopiera alla data genom att lägga till fel tolerans och hoppa över inkompatibla data rader. Dessutom kan du logga de inkompatibla raderna i Azure Blob Storage eller Azure Data Lake Store. Du kan sedan granska loggen för att lära dig orsaken till felet, korrigera data på data källan och försöka kopiera aktiviteten igen.

## <a name="supported-scenarios"></a>Scenarier som stöds
Kopierings aktiviteten stöder tre scenarier för att identifiera, hoppa över och logga inkompatibla data:

- **Inkompatibilitet mellan käll data typ och inbyggd typ av mottagare**. 

    Exempel: kopiera data från en CSV-fil i Blob Storage till en SQL-databas med en schema definition som innehåller tre kolumner av typen INT. CSV-filrader som innehåller numeriska data, till exempel 123 456 789, kopieras till mottagar lagret. Men raderna som innehåller icke-numeriska värden, till exempel 123 456, ABC identifieras som inkompatibla och hoppas över.

- **Antalet kolumner mellan källan och sinken matchar inte**.

    Exempel: kopiera data från en CSV-fil i Blob Storage till en SQL-databas med en schema definition som innehåller sex kolumner. CSV-filrader som innehåller sex kolumner kopieras till mottagar lagret. CSV-filrader som innehåller fler än sex kolumner identifieras som inkompatibla och hoppas över.

- **Fel vid primär nyckel vid skrivning till SQL Server/Azure SQL Database/Azure Cosmos DB**.

    Exempel: kopiera data från en SQL-Server till en SQL-databas. En primär nyckel definieras i Sink-SQL-databasen, men ingen sådan primär nyckel definieras i SQL Server-källan. Det går inte att kopiera de duplicerade raderna som finns i källan till mottagaren. Kopierings aktiviteten kopierar bara den första raden i data källan till mottagaren. Efterföljande käll rader som innehåller värdet för duplicerad primär nyckel identifieras som inkompatibla och hoppas över.

>[!NOTE]
>- För att läsa in data i SQL Data Warehouse med PolyBase konfigurerar du polybases inställningar för ursprunglig fel tolerans genom att ange principer för avvisande av "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" i kopierings aktiviteten. Du kan fortfarande aktivera omdirigering av PolyBase-inkompatibla rader till BLOB eller ADLS som normalt som visas nedan.
>- Den här funktionen gäller inte när kopierings aktiviteten har kon figurer ATS för att anropa [Amazon RedShift-inläsning](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Den här funktionen gäller inte när kopierings aktiviteten har kon figurer ATS för att anropa en [lagrad procedur från en SQL-mottagare](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink).

## <a name="configuration"></a>Konfiguration
I följande exempel visas en JSON-definition som hoppar över inkompatibla rader i kopierings aktiviteten:

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
enableSkipIncompatibleRow | Anger om inkompatibla rader ska hoppas över vid kopiering eller inte. | Sant<br/>False (standard) | Nej
redirectIncompatibleRowSettings | En grupp egenskaper som kan anges när du vill logga de inkompatibla raderna. | &nbsp; | Nej
linkedServiceName | Den länkade tjänsten [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) eller [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) för att lagra loggen som innehåller de överhoppade raderna. | Namnet på en `AzureStorage` eller `AzureDataLakeStore` typ länkad tjänst som refererar till den instans som du vill använda för att lagra logg filen. | Nej
path | Sökvägen till logg filen som innehåller de överhoppade raderna. | Ange den sökväg som du vill använda för att logga inkompatibla data. Om du inte anger en sökväg skapar tjänsten en behållare åt dig. | Nej

## <a name="monitor-skipped-rows"></a>Övervaka överhoppade rader
När körningen av kopierings aktiviteten är klar kan du se antalet rader som hoppades över i resultatet av kopierings aktiviteten:

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
Om du konfigurerar för att logga inkompatibla rader kan du hitta logg filen på den här sökvägen: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Loggfilerna kan bara vara CSV-filerna. De ursprungliga data som hoppas över kommer att loggas med kommatecken som kolumn avgränsare om det behövs. Vi lägger till två fler kolumner "ErrorCode" och "ErrorMessage" i ytterligare till de ursprungliga källdata i logg filen, där du kan se den bakomliggande orsaken till inkompatibiliteten. ErrorCode och ErrorMessage anges med dubbla citat tecken. 

Ett exempel på logg filens innehåll är följande:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Nästa steg
Se andra artiklar om kopierings aktiviteter:

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Kopiera aktivitetsprestanda](copy-activity-performance.md)


