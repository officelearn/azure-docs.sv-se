---
title: Lägga till feltolerans i Azure Data Factory Copy Activity genom att hoppa över inkompatibla rader
description: Lär dig hur du lägger till feltolerans i Azure Data Factory Copy Activity genom att hoppa över inkompatibla rader under kopiering
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8ffaee75154fd5fe025bdb683c89f16799d6e86b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926155"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Lägga till feltolerans i Kopieringsaktivitet genom att hoppa över inkompatibla rader

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-copy-activity-fault-tolerance.md)
> * [Version 2 (aktuell version)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [feltolerans i kopieringsaktiviteten för Data Factory](../copy-activity-fault-tolerance.md).

Azure Data Factory [Copy Activity](data-factory-data-movement-activities.md) erbjuder två sätt att hantera inkompatibla rader när du kopierar data mellan käll- och sink-datalager:

- Du kan avbryta och misslyckas med kopieringsaktiviteten när inkompatibla data påträffas (standardbeteende).
- Du kan fortsätta att kopiera alla data genom att lägga till feltolerans och hoppa över inkompatibla datarader. Dessutom kan du logga de inkompatibla raderna i Azure Blob-lagring. Du kan sedan undersöka loggen för att lära dig orsaken till felet, åtgärda data på datakällan och försöka kopiera aktiviteten igen.

## <a name="supported-scenarios"></a>Scenarier som stöds
Kopieringsaktivitet stöder tre scenarier för identifiering, hoppa över och logga inkompatibla data:

- **Inkompatibilitet mellan källdatatypen och den interna mottagartypen**

    Kopiera data från en CSV-fil i Blob-lagring till en SQL-databas med en schemadefinition som innehåller tre INT-typkolumner. **INT** Csv-filraderna som innehåller numeriska `123,456,789` data, till exempel kopieras till sink-arkivet. Raderna som innehåller icke-numeriska värden, `123,456,abc` till exempel identifieras som inkompatibla och hoppas över.

- **Antalet kolumner mellan källan och mottagaren matchar inte varandra**

    Kopiera data från en CSV-fil i Blob-lagring till en SQL-databas med en schemadefinition som innehåller sex kolumner. Csv-filraderna som innehåller sex kolumner kopieras framgångsrikt till sink-arkivet. Csv-filraderna som innehåller fler eller färre än sex kolumner identifieras som inkompatibla och hoppas över.

- **Primärnyckelsfel vid skrivning till SQL Server/Azure SQL Database/Azure Cosmos DB**

    Kopiera data från en SQL-server till en SQL-databas. En primärnyckel definieras i sink SQL-databasen, men ingen sådan primärnyckel definieras i källan SQL-servern. De duplicerade rader som finns i källan kan inte kopieras till diskhon. Kopiera aktivitet kopierar bara den första raden i källdata till diskhon. De efterföljande källraderna som innehåller det duplicerade primärnyckelvärdet identifieras som inkompatibla och hoppas över.

>[!NOTE]
>Den här funktionen gäller inte när kopieringsaktivitet är konfigurerad för att anropa extern datainläsningsmekanism, inklusive [Azure SQL Data Warehouse PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) eller Amazon [Redshift Unload](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). Om du vill läsa in data i SQL Data Warehouse med PolyBase använder du PolyBase:s ursprungliga stöd för feltolerans genom att ange "[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" i kopieringsaktivitet.

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
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Aktivera hoppa över inkompatibla rader under kopia eller inte. | True<br/>False (standard) | Inga |
| **redirectIncompatibleRowSettings** | En grupp egenskaper som kan anges när du vill logga de inkompatibla raderna. | &nbsp; | Inga |
| **linkedServiceName** | Den länkade tjänsten för Azure Storage för att lagra loggen som innehåller de överhoppade raderna. | Namnet på en [AzureStorage-](data-factory-azure-blob-connector.md#azure-storage-linked-service) eller [AzureStorageSas-länkad](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) tjänst, som refererar till den lagringsinstans som du vill använda för att lagra loggfilen. | Inga |
| **Sökvägen** | Sökvägen till loggfilen som innehåller de överhoppade raderna. | Ange den Blob-lagringssökväg som du vill använda för att logga inkompatibla data. Om du inte anger en sökväg skapar tjänsten en behållare åt dig. | Inga |

## <a name="monitoring"></a>Övervakning
När kopieringsaktiviteten har körts är klar kan du se antalet överhoppade rader i övervakningsavsnittet:

![Övervaka överhoppade inkompatibla rader](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Om du konfigurerar för att logga de inkompatibla raderna `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` kan du hitta loggfilen på den här sökvägen: I loggfilen kan du se de rader som hoppades över och grundorsaken till inkompatibiliteten.

Både de ursprungliga uppgifterna och motsvarande fel loggas i filen. Ett exempel på loggfilsinnehållet är följande:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Data Factory Copy Activity finns i [Flytta data med hjälp av Kopiera aktivitet](data-factory-data-movement-activities.md).
