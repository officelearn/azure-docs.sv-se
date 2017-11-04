---
title: "Skicka data till sökindexet med hjälp av Data Factory | Microsoft Docs"
description: "Lär dig mer om hur du överför data till Azure Search Index med hjälp av Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 2415850491018f4e27c5ec930b688026cc12b41a
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Skicka data till ett Azure Search-index med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-azure-search-connector.md)
> * [Version 2 – förhandsversion](../connector-azure-search.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Azure Search-kopplingen i V2](../connector-azure-search.md).

Den här artikeln beskriver hur du använder aktiviteten kopiera för pusha data från ett dataarkiv som stöds källan till Azure Search-index. Stöds källa datalager listas i kolumnen källa för den [källor och sänkor stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Den här artikeln bygger på den [data movement aktiviteter](data-factory-data-movement-activities.md) artikel som ger en allmän översikt över dataflyttning stöds data store kombinationer och Kopieringsaktivitet.

## <a name="enabling-connectivity"></a>Aktivera anslutning
Om du vill tillåta Data Factory-tjänsten ansluta till ett lokalt datalager kan installera du Data Management Gateway i din lokala miljö. Du kan installera gatewayen på samma dator som värd källdata lagra eller på en separat dator för att undvika konkurrerar om resurser med datalagret.

Data Management Gateway ansluter lokala datakällor till molntjänster i en säker och hanterad sätt. Se [flytta data mellan lokalt och i molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln för information om Data Management Gateway.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som skickar data från ett dataarkiv som källa till Azure Search index med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare: 

1. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory.
2. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. 
3. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. 

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till Azure Search index finns [JSON-exempel: kopiera data från lokala SQL Server till Azure Search index](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera entiteter i Data Factory för Azure Search Index:

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande tabell innehåller beskrivningar för JSON-element som är specifika för Azure Search länkade tjänsten.

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| typ | Egenskapen type måste anges till: **AzureSearch**. | Ja |
| URL: en | URL för Azure Search-tjänsten. | Ja |
| key | Admin-nyckel för Azure Search-tjänsten. | Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningar och avsnitt, finns det [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av datauppsättningen. Den **typeProperties** avsnittet är olika för varje typ av datauppsättningen. TypeProperties avsnittet för en dataset av typen **AzureSearchIndex** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| typ | Egenskapen type måste anges till **AzureSearchIndex**.| Ja |
| Indexnamn | Namnet på det Azure Search-indexet. Data Factory kan inte skapa indexet. Index måste finnas i Azure Search. | Ja |


## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar pipelines](data-factory-create-pipelines.md) artikel. Egenskaper, till exempel namn, beskrivning, indata och utdatatabeller och olika principer är tillgängliga för alla typer av aktiviteter. De egenskaper som är tillgängliga i avsnittet typeProperties varierar beroende på varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

För Kopieringsaktiviteten när sink är av typen **AzureSearchIndexSink**, följande egenskaper finns i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Anger om du vill slå samman eller ersätta ett dokument som redan finns i indexet. Finns det [WriteBehavior egenskapen](#writebehavior-property).| sammanfoga (standard)<br/>Ladda upp| Nej |
| writeBatchSize | Överför data till Azure Search index när buffertstorleken når writeBatchSize. Finns det [WriteBatchSize egenskapen](#writebatchsize-property) mer information. | 1 till 1 000. Standardvärdet är 1000. | Nej |

### <a name="writebehavior-property"></a>Egenskapen WriteBehavior
AzureSearchSink upserts när data skrivs. När du skriver ett dokument, om dokumentnyckeln finns redan i Azure Search-index kan uppdateras med andra ord Azure Search befintligt dokument i stället för att en konflikt undantag.

AzureSearchSink innehåller följande två upsert beteenden (med hjälp av AzureSearch SDK):

- **Sammanfoga**: kombinera alla kolumner i det nya dokumentet med den befintliga. Värdet i den befintliga versionen bevaras i kolumner med null-värde i det nya dokumentet.
- **Överför**: nytt dokument ersätter den befintliga versionen. För kolumner som inte har angetts i det nya dokumentet har värdet null om det finns ett icke-null-värde i det befintliga dokumentet eller inte.

Standardbeteendet är **sammanfoga**.

### <a name="writebatchsize-property"></a>Egenskapen WriteBatchSize
Azure Search-tjänsten stöder skrivning dokument som en batch. En grupp kan innehålla 1 till 1 000 åtgärder. En åtgärd som hanterar ett dokument för att genomföra överföringen/merge-operation.

### <a name="data-type-support"></a>Stöd för datatypen
I följande tabell anger om en Azure Search-datatyp stöds eller inte.

| Azure Search-datatyp | Stöds i Azure Search Sink |
| ---------------------- | ------------------------------ |
| Sträng | Y |
| Int32 | Y |
| Int64 | Y |
| dubbla | Y |
| Booleskt värde | Y |
| DataTimeOffset | Y |
| Strängmatris | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>JSON-exempel: kopiera data från lokala SQL Server till Azure Search index

I följande exempel visas:

1.  En länkad tjänst av typen [AzureSearch](#linked-service-properties).
2.  En länkad tjänst av typen [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3.  Indata [dataset](data-factory-create-datasets.md) av typen [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4.  Utdata [dataset](data-factory-create-datasets.md) av typen [AzureSearchIndex](#dataset-properties).
4.  En [pipeline](data-factory-create-pipelines.md) med en kopia-aktivitet som använder [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) och [AzureSearchIndexSink](#copy-activity-properties).

Exemplet kopierar time series-data från en lokal SQL Server-databas till ett Azure Search-index per timme. JSON-egenskaper som används i det här exemplet beskrivs i exemplen i följande avsnitt.

Som ett första steg bör du konfigurera data management gateway på din lokala dator. Anvisningarna är i den [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikel.

**Azure Search länkad tjänst:**

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

**SQL Server som är länkad tjänst**

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

**SQL Server inkommande dataset**

Exemplet förutsätter att du har skapat en tabell ”mytable” som prefix i SQL Server och den innehåller en kolumn med namnet ”timestampcolumn” för tid series-data. Du kan fråga över flera tabeller i samma databas med hjälp av en enda dataset, men en enskild tabell måste användas för att datauppsättningen tableName typeProperty.

Inställningen ”externa”: ”true” informerar Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

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

**Azure Search utdatauppsättningen:**

Exemplet kopierar data till ett Azure Search-index med namnet **produkter**. Data Factory kan inte skapa indexet. Testa exemplet genom att skapa ett index med det här namnet. Skapa Azure Search-index med samma antal kolumner som den inkommande datamängden. Nya poster läggs till i Azure Search index varje timme.

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

**Kopiera aktivitet i en pipeline med SQL-källa och mottagare för Azure Search Index:**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **SqlSource** och **sink** är inställd på **AzureSearchIndexSink**. SQL-frågan som angetts för den **SqlReaderQuery** egenskapen väljer vilka data under den senaste timmen att kopiera.

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

Om du vill kopiera data från ett moln-datalager i Azure Search `executionLocation` egenskapen måste anges. Följande JSON-fragment visar behövs under Kopieringsaktiviteten `typeProperties` som exempel. Kontrollera [kopiera data mellan moln datalager](data-factory-data-movement-activities.md#global) avsnitt för mer information och de värden som stöds.

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


## <a name="copy-from-a-cloud-source"></a>Kopiera från en källa för molnet
Om du vill kopiera data från ett moln-datalager i Azure Search `executionLocation` egenskapen måste anges. Följande JSON-fragment visar behövs under Kopieringsaktiviteten `typeProperties` som exempel. Kontrollera [kopiera data mellan moln datalager](data-factory-data-movement-activities.md#global) avsnitt för mer information och de värden som stöds.

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

Du kan också mappa kolumner från källan dataset till kolumner från sink datamängd i aktivitetsdefinitionen kopia. Mer information finns i [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering  
Finns det [prestandajustering guide och Kopieringsaktivitet prestanda](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) och olika sätt att optimera den.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* [Kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) stegvisa instruktioner för att skapa en pipeline med en kopia-aktivitet.
