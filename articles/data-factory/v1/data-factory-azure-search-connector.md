---
title: Skicka data till Search-index med hjälp av Data Factory | Microsoft Docs
description: Läs mer om hur du skickar data till Azure Search-Index med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7ad328eec7e16b5368b78a0dfccbf5c09adb5c13
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330016"
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Skicka data till ett Azure Search-index med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-azure-search-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-search.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Azure Search connector i V2](../connector-azure-search.md).

Den här artikeln beskriver hur du använder Kopieringsaktiviteten för att skicka data från ett dataarkiv till Azure Search-index. Datalager som stöds visas i kolumnen källa för den [stöds källor och mottagare](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Den här artikeln bygger vidare på den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som visar en allmän översikt över dataförflyttning med Kopieringsaktivitet och data som stöds store kombinationer.

## <a name="enabling-connectivity"></a>Aktivera anslutning
För att Data Factory-tjänsten ansluta till ett lokalt datalager måste installera du Data Management Gateway i din lokala miljö. Du kan installera gatewayen på samma dator som värdar källdata lagrar eller på en separat dator att undvika konkurrerar om resurser med datalagret.

Data Management Gateway ansluter lokala datakällor till molntjänster i ett säkert och hanterat sätt. Se [flytta data mellan lokala och molnbaserade](data-factory-move-data-between-onprem-and-cloud.md) nedan för information om Data Management Gateway.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data från ett källdatalager till Azure Search-index med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Se [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik.
2. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen.
3. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format.  Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till Azure Search-index, se [JSON-exempel: Kopiera data från en lokal SQL Server till Azure Search-index](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure Search-Index:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

I följande tabell innehåller beskrivningar av JSON-element som är specifika för Azure Search länkade tjänsten.

| Egenskap  | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| typ | Type-egenskapen måste anges till: **AzureSearch**. | Ja |
| url | URL för Azure Search-tjänsten. | Ja |
| key | Admin-nyckel för Azure Search-tjänsten. | Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av. Den **typeProperties** är olika för varje typ av datauppsättning. TypeProperties avsnittet för en datauppsättning av typen **AzureSearchIndex** har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| typ | Type-egenskapen måste anges till **AzureSearchIndex**.| Ja |
| indexName | Namnet på Azure Search-index. Data Factory skapar inte indexet. Indexet måste finnas i Azure Search. | Ja |


## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdatatabeller och olika principer är tillgängliga för alla typer av aktiviteter. Medan egenskaper som är tillgängliga i avsnittet typeProperties varierar med varje aktivitetstyp av. För Kopieringsaktiviteten variera de beroende på vilka typer av källor och mottagare.

För Kopieringsaktiviteten, när mottagaren är av typen **AzureSearchIndexSink**, följande egenskaper är tillgängliga i avsnittet typeProperties:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Anger om du vill slå samman eller Ersätt när det finns redan ett dokument i indexet. Se den [WriteBehavior egenskapen](#writebehavior-property).| Sammanfoga (standard)<br/>Ladda upp| Nej |
| WriteBatchSize | Överför data till Azure Search-indexet när buffertstorleken når writeBatchSize. Se den [WriteBatchSize egenskapen](#writebatchsize-property) mer information. | 1 och 1 000. Standardvärdet är 1 000. | Nej |

### <a name="writebehavior-property"></a>WriteBehavior egenskap
AzureSearchSink upsertar när du skriver data. När du skriver ett dokument om dokumentnyckeln som redan finns i Azure Search-index, uppdaterar Azure Search med andra ord befintligt dokument i stället för att utlöste ett undantag i konflikt.

AzureSearchSink innehåller följande två upsert beteenden (med hjälp av AzureSearch SDK):

- **Sammanfoga**: kombinera alla kolumner i det nya dokumentet med den befintliga. Värdet i den befintliga påverkas bevaras för kolumner med null-värde i det nya dokumentet.
- **Ladda upp**: Det nya dokumentet ersätter den befintliga påverkas. För kolumner som inte har angetts i det nya dokumentet har värdet null om det finns ett icke-null-värde i det befintliga dokumentet eller inte.

Standardbeteendet är **sammanfoga**.

### <a name="writebatchsize-property"></a>WriteBatchSize egenskap
Azure Search-tjänsten stöder skrivning dokument som en batch. En grupp kan innehålla 1 och 1 000 åtgärder. En åtgärd hanterar ett dokument för att utföra uppladdning/merge-operation.

### <a name="data-type-support"></a>Stöd för datatypen
I följande tabell anger om en Azure Search-datatyp stöds eller inte.

| Azure Search-datatyp | Stöds i Azure Search-mottagare |
| ---------------------- | ------------------------------ |
| Sträng | Y |
| Int32 | Y |
| Int64 | Y |
| Double-värde | Y |
| Boolesk | Y |
| DataTimeOffset | Y |
| Strängmatris | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>JSON-exempel: Kopiera data från en lokal SQL Server till Azure Search-index

I följande exempel visas:

1. En länkad tjänst av typen [AzureSearch](#linked-service-properties).
2. En länkad tjänst av typen [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureSearchIndex](#dataset-properties).
4. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) och [AzureSearchIndexSink](#copy-activity-properties).

Exemplet kopierar time series-data från en lokal SQL Server-databas till ett Azure Search-index per timme. JSON-egenskaper som används i det här exemplet beskrivs i exemplen i följande avsnitt.

Konfigurera data management gateway på din lokala dator som ett första steg. Anvisningarna finns i den [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln.

**Azure Search länkade tjänsten:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**SQL Server-länkade tjänst**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**Indatauppsättning för SQL Server**

Exemplet förutsätter att du har skapat en tabell ”MyTable” i SQL Server och den innehåller en kolumn med namnet ”timestampcolumn” för time series-data. Du kan fråga över flera tabeller inom samma databas med hjälp av en enda datauppsättning, men en enskild tabell måste användas för datauppsättningens tableName typeProperty.

Ange ”external”: ”true” informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Utdatauppsättning för Azure Search:**

Exemplet kopierar data till ett Azure Search-index med namnet **produkter**. Data Factory skapar inte indexet. Testa exemplet genom att skapa ett index med det här namnet. Skapa Azure Search-index med samma antal kolumner som i datauppsättningen för indata. Nya poster läggs till Azure Search-index varje timme.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

**Kopieringsaktivitet i en pipeline med SQL-källa och mottagare för Azure Search-Index:**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **SqlSource** och **mottagare** är **AzureSearchIndexSink**. SQL-frågan som angetts för den **SqlReaderQuery** egenskapen väljer vilka data under den senaste timmen att kopiera.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

Om du kopierar data från ett datalager i molnet till Azure Search `executionLocation` egenskapen måste anges. Följande JSON-kodfragmentet visar ändringen som behövs under Kopieringsaktiviteten `typeProperties` som exempel. Kontrollera [kopiera data mellan molndatalager](data-factory-data-movement-activities.md#global) avsnittet värden som stöds och mer information.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>Kopiera från en molnkälla
Om du kopierar data från ett datalager i molnet till Azure Search `executionLocation` egenskapen måste anges. Följande JSON-kodfragmentet visar ändringen som behövs under Kopieringsaktiviteten `typeProperties` som exempel. Kontrollera [kopiera data mellan molndatalager](data-factory-data-movement-activities.md#global) avsnittet värden som stöds och mer information.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

Du kan också mappa kolumner från datauppsättningen för källan till kolumner från en datauppsättning för mottagare i aktivitetsdefinitionen kopia. Mer information finns i [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se den [Kopieringsaktiviteten prestanda- och Justeringsguiden](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataflytt (Kopieringsaktivitet) och olika sätt att optimera den.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* [Kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.
