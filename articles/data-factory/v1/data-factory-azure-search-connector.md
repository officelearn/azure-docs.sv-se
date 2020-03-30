---
title: Skicka data till sökindex med hjälp av Data Factory
description: Lär dig mer om hur du skickar data till Azure Cognitive Search Index med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5b1170f721cf8521cfe1762df0cc616c938ddf28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281566"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>Skicka data till ett Azure Cognitive Search-index med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-azure-search-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-search.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Azure Cognitive Search-anslutningsappen i V2](../connector-azure-search.md).

I den här artikeln beskrivs hur du använder kopieringsaktiviteten för att skicka data från ett källdatalager som stöds till ett Azure Cognitive Search-index. Källdatalager som stöds visas i kolumnen Källa i tabellen [Källor och sänkor som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Den här artikeln bygger på [dataförflyttningsaktiviteter,](data-factory-data-movement-activities.md) som ger en allmän översikt över dataförflyttning med Kopiera aktivitet och datalagerkombinationer som stöds.

## <a name="enabling-connectivity"></a>Aktivera anslutning
Om du vill tillåta datafabrikstjänst ansluta till ett lokalt datalager installerar du Data Management Gateway i din lokala miljö. Du kan installera gateway på samma dator som är värd för källdatalagret eller på en separat dator för att undvika att konkurrera om resurser med datalagret.

Data Management Gateway ansluter lokala datakällor till molntjänster på ett säkert och hanterat sätt. Mer information om Data Management Gateway finns i [Flytta data mellan lokal och molnartikel.](data-factory-move-data-between-onprem-and-cloud.md)

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som skickar data från ett källdatalager till ett sökindex med hjälp av olika verktyg/API:er.

Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen.
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet.  Ett exempel med JSON-definitioner för datafabrikentiteter som används för att kopiera data till sökindex finns i [JSON-exempel: Kopiera data från lokal SQL Server till ett Azure Cognitive Search-indexavsnitt](#json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för ett sökindex:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

I följande tabell finns beskrivningar av JSON-element som är specifika för azure Cognitive Search-länkade tjänsten.

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| typ | Typegenskapen måste anges till: **AzureSearch**. | Ja |
| url | URL för söktjänsten. | Ja |
| key | Administratörsnyckel för söktjänsten. | Ja |

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper. Avsnittet **typeProperties** är olika för varje typ av datauppsättning. Avsnittet typeProperties för en datauppsättning av typen **AzureSearchIndex** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| typ | Typegenskapen måste anges till **AzureSearchIndex**.| Ja |
| indexNamn | Namn på sökindexet. Data Factory skapar inte indexet. Indexet måste finnas i Azure Cognitive Search. | Ja |


## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatatabeller och olika principer är tillgängliga för alla typer av aktiviteter. Medan egenskaper som är tillgängliga i avsnittet typeProperties varierar beroende på varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

För Kopieringsaktivitet, när diskhon är av typen **AzureSearchIndexIndexSink,** är följande egenskaper tillgängliga i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| -------- | ----------- | -------------- | -------- |
| SkrivBeteende | Anger om det ska slås samman eller ersätta när det redan finns ett dokument i indexet. Se [egenskapen WriteBehavior](#writebehavior-property).| Sammanfoga (standard)<br/>Ladda upp| Inga |
| SkrivBatchSize | Överför data till sökindexet när buffertstorleken når writeBatchSize. Mer information finns i [egenskapen WriteBatchSize.](#writebatchsize-property) | 1 till 1000. Standardvärdet är 1000. | Inga |

### <a name="writebehavior-property"></a>WriteBehavior egendom
AzureSearchSink upserts när du skriver data. Med andra ord, när du skriver ett dokument, om dokumentnyckeln redan finns i sökindexet, uppdaterar Azure Cognitive Search det befintliga dokumentet i stället för att utlösa ett konfliktundantag.

AzureSearchSink innehåller följande två upsert-beteenden (med hjälp av AzureSearch SDK):

- **Sammanfoga:** kombinera alla kolumner i det nya dokumentet med det befintliga dokumentet. För kolumner med null-värde i det nya dokumentet bevaras värdet i det befintliga.
- **Ladda upp**: Det nya dokumentet ersätter det befintliga dokumentet. För kolumner som inte anges i det nya dokumentet anges värdet till null om det finns ett värde som inte är null i det befintliga dokumentet eller inte.

Standardbeteendet är **Koppla**.

### <a name="writebatchsize-property"></a>Egenskapen WriteBatchSize
Azure Cognitive Search-tjänsten stöder att skriva dokument som en batch. En batch kan innehålla 1 till 1 000 åtgärder. En åtgärd hanterar ett dokument för att utföra överföringen/kopplingen.

### <a name="data-type-support"></a>Stöd för datatyp
Följande tabell anger om en Azure Cognitive Search-datatyp stöds eller inte.

| Datatyp för Azure Cognitive Search | Stöds i Azure Cognitive Search Sink |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| Boolean | Y |
| DataTimeOffset | Y |
| Strängmatris | N |
| GeografiPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-cognitive-search-index"></a>JSON-exempel: Kopiera data från lokalt SQL Server till Azure Cognitive Search-index

Följande exempel visar:

1. En länkad tjänst av typen [AzureSearch](#linked-service-properties).
2. En länkad tjänst av typen [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureSearchIndex](#dataset-properties).
4. En [pipeline](data-factory-create-pipelines.md) med en kopieringsaktivitet som använder [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) och [AzureSearchIndexSink](#copy-activity-properties).

Exemplet kopierar tidsseriedata från en lokal SQL Server-databas för att söka index varje timme. De JSON-egenskaper som används i det här exemplet beskrivs i avsnitt som följer proverna.

Som ett första steg konfigurerar du datahanteringsgatewayen på den lokala datorn. Instruktionerna finns i [flyttdata mellan lokala platser och molnartikel.](data-factory-move-data-between-onprem-and-cloud.md)

**Azure Cognitive Search länkad tjänst:**

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

**SQL Server-länkad tjänst**

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

**SQL Server-indatauppsättning**

Exemplet förutsätter att du har skapat en tabell "MyTable" i SQL Server och den innehåller en kolumn som kallas "tidsstämpelpcolumn" för tidsseriedata. Du kan fråga över flera tabeller i samma databas med hjälp av en enda datauppsättning, men en enda tabell måste användas för datauppsättningens tabellNamnstypFel.

Inställningen "extern": "true" informerar Data Factory-tjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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

**Utdatauppsättning för Azure Cognitive Search:Azure Cognitive Search output dataset: Azure Cognitive Search output dataset: Azure Cognitive**

Exemplet kopierar data till ett Azure Cognitive Search-index med namnet **produkter**. Data Factory skapar inte indexet. Testa exemplet genom att skapa ett index med det här namnet. Skapa sökindexet med samma antal kolumner som i indatauppsättningen. Nya poster läggs till i sökindexet varje timme.

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

**Kopiera aktivitet i en pipeline med SQL-källa och Azure Cognitive Search Index sink:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I pipeline JSON-definitionen anges **källtypen** till **SqlSource** och **sink-typen** är inställd på **AzureSearchIndexSink**. Sql-frågan som angetts för egenskapen **SqlReaderQuery** väljer de data som ska kopieras under den senaste timmen.

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

Om du kopierar data från ett molndatalager `executionLocation` till Azure Cognitive Search krävs egenskap. Följande JSON-kodavsnitt visar den ändring `typeProperties` som behövs under Kopiera aktivitet som ett exempel. Kontrollera [Kopiera data mellan avsnittet molndatalager](data-factory-data-movement-activities.md#global) för värden som stöds och mer information.

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
Om du kopierar data från ett molndatalager `executionLocation` till Azure Cognitive Search krävs egenskap. Följande JSON-kodavsnitt visar den ändring `typeProperties` som behövs under Kopiera aktivitet som ett exempel. Kontrollera [Kopiera data mellan avsnittet molndatalager](data-factory-data-movement-activities.md#global) för värden som stöds och mer information.

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

Du kan också mappa kolumner från källdatauppsättning till kolumner från sink-datauppsättning i definitionen av kopieringsaktivitet. Mer information finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [prestanda- och justeringsguiden för kopieringsaktivitet](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar datarörelsens prestanda (kopiera aktivitet) och olika sätt att optimera den.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* [Kopiera självstudiekurs](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för aktivitet för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.
