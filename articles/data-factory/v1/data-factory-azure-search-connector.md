---
title: Skicka data till Sök index med hjälp av Data Factory
description: Lär dig mer om att skicka data till Azure Search index med Azure Data Factory.
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
ms.openlocfilehash: 09b891ba753291511bb1f203b7ac4437e6b2c542
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683114"
---
# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Skicka data till ett Azure Search-index med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-azure-search-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-search.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [Azure Search Connector i v2](../connector-azure-search.md).

Den här artikeln beskriver hur du använder kopierings aktiviteten för att skicka data från ett käll data lager som stöds till Azure Search index. Käll data lager som stöds visas i kolumnen källa i tabellen med [källor som stöds och tabellen Sinks](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Den här artikeln bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) , som innehåller en allmän översikt över data förflyttning med kombinationer kopiera aktivitet och stöd för data lager.

## <a name="enabling-connectivity"></a>Aktivera anslutning
Om du vill tillåta Data Factory tjänst att ansluta till ett lokalt data lager, installerar du Data Management Gateway i din lokala miljö. Du kan installera gateway på samma dator som är värd för käll data lagret eller på en annan dator för att undvika att resurser i data lagret undviks.

Data Management Gateway ansluter lokala data källor till moln tjänster på ett säkert och hanterat sätt. Mer information om Data Management Gateway finns i artikeln [Flytta data mellan lokalt och i molnet](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopierings aktivitet som skickar data från ett käll data lager till Azure Search index med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda **guiden Kopiera**. Se [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genom gång av hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager mall**, .net- **API**och **REST API**. Mer information om hur du skapar en pipeline med en kopierings aktivitet finns i [själv studie kursen kopiera aktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Oavsett om du använder verktygen eller API: erna utför du följande steg för att skapa en pipeline som flyttar data från ett käll data lager till ett mottagar data lager:

1. Skapa **länkade tjänster** för att länka indata och utdata från data lager till din data fabrik.
2. Skapa data **uppsättningar** som representerar indata och utdata för kopierings åtgärden.
3. Skapa en **pipeline** med en kopierings aktivitet som tar en data uppsättning som indata och en data uppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory entiteter (länkade tjänster, data uppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API: er (förutom .NET API) definierar du dessa Data Factory entiteter med hjälp av JSON-formatet.  Ett exempel med JSON-definitioner för Data Factory entiteter som används för att kopiera data till Azure Search index finns i [JSON-exempel: kopiera data från lokala SQL Server till Azure Search index](#json-example-copy-data-from-on-premises-sql-server-to-azure-search-index) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter som är speciella för Azure Search index:

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande tabell innehåller beskrivningar av JSON-element som är speciella för den länkade tjänsten Azure Search.

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| typ | Egenskapen Type måste anges till: **AzureSearch**. | Ja |
| url | URL för Azure Searchs tjänsten. | Ja |
| key | Administratörs nyckel för Azure Searchs tjänsten. | Ja |

## <a name="dataset-properties"></a>Egenskaper för data mängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [skapa data uppsättningar](data-factory-create-datasets.md) . Avsnitt som struktur, tillgänglighet och princip för en data uppsättnings-JSON liknar samma för alla data uppsättnings typer. Avsnittet **typeProperties** är olika för varje typ av data uppsättning. Avsnittet typeProperties för en data uppsättning av typen **AzureSearchIndex** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| typ | Egenskapen Type måste anges till **AzureSearchIndex**.| Ja |
| indexName | Namnet på Azure Search indexet. Data Factory skapar inte indexet. Indexet måste finnas i Azure Search. | Ja |


## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [skapa pipeliner](data-factory-create-pipelines.md) . Egenskaper som namn, beskrivning, indata och utdata-tabeller och olika principer är tillgängliga för alla typer av aktiviteter. De egenskaper som är tillgängliga i avsnittet typeProperties varierar med varje aktivitets typ. För kopierings aktivitet varierar de beroende på typerna av källor och mottagare.

För kopierings aktivitet är följande egenskaper tillgängliga i avsnittet typeProperties när mottagaren är av typen **AzureSearchIndexSink**:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Anger om du vill sammanfoga eller ersätta när ett dokument redan finns i indexet. Se [egenskapen WriteBehavior](#writebehavior-property).| Sammanfoga (standard)<br/>Ladda upp| Nej |
| writeBatchSize | Överför data till Azure Search index när buffertstorleken når writeBatchSize. Mer information finns i [WriteBatchSize-egenskapen](#writebatchsize-property) . | 1 till 1 000. Standardvärdet är 1000. | Nej |

### <a name="writebehavior-property"></a>WriteBehavior-egenskap
AzureSearchSink upsertar när data skrivs. Med andra ord, om dokument nyckeln redan finns i Azure Search-index, Azure Search uppdaterar det befintliga dokumentet i stället för att ett konflikt undantag utlöses.

AzureSearchSink innehåller följande två upsert-beteenden (med hjälp av AzureSearch SDK):

- **Sammanslagning**: kombinera alla kolumner i det nya dokumentet med det befintliga. För kolumner med null-värde i det nya dokumentet bevaras värdet i det befintliga.
- **Ladda upp**: det nya dokumentet ersätter det befintliga. För kolumner som inte anges i det nya dokumentet anges värdet null om det inte finns något värde som inte är null i det befintliga dokumentet eller inte.

Standard beteendet **slås samman**.

### <a name="writebatchsize-property"></a>WriteBatchSize-egenskap
Azure Search-tjänsten stöder skrivning av dokument som en batch. En batch kan innehålla 1 till 1 000-åtgärder. En åtgärd hanterar ett dokument för att utföra uppladdnings-/sammanslagnings åtgärden.

### <a name="data-type-support"></a>Data typs stöd
I följande tabell anges om en Azure Search data typ stöds eller inte.

| Azure Search data typ | Stöds i Azure Search-mottagare |
| ---------------------- | ------------------------------ |
| Sträng | Y |
| Int32 | Y |
| Int64 | Y |
| Dubbelklicka | Y |
| Boolesk | Y |
| DataTimeOffset | Y |
| Sträng mat ris | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>JSON-exempel: kopiera data från lokala SQL Server till Azure Search index

Följande exempel visar:

1. En länkad tjänst av typen [AzureSearch](#linked-service-properties).
2. En länkad tjänst av typen [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. En indata- [datauppsättning](data-factory-create-datasets.md) av typen [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
4. En utdata- [datauppsättning](data-factory-create-datasets.md) av typen [AzureSearchIndex](#dataset-properties).
4. En [pipeline](data-factory-create-pipelines.md) med en kopierings aktivitet som använder [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) och [AzureSearchIndexSink](#copy-activity-properties).

Exemplet kopierar Time Series-data från en lokal SQL Server-databas till ett Azure Search-index varje timme. De JSON-egenskaper som används i det här exemplet beskrivs i avsnitten som följer efter exemplen.

Det första steget är att konfigurera data Management Gateway på den lokala datorn. Anvisningarna finns i [Flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artiklar.

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

**SQL Server länkad tjänst**

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

**SQL Server indata-datauppsättning**

Exemplet förutsätter att du har skapat en tabell "Tabell" i SQL Server och den innehåller en kolumn med namnet "timestampcolumn" för Time Series-data. Du kan fråga över flera tabeller i samma databas med en enda data uppsättning, men en enda tabell måste användas för data uppsättningens tableName-typeProperty.

Inställningen "External": "true" informerar Data Factory tjänsten om att data uppsättningen är extern i data fabriken och inte produceras av en aktivitet i data fabriken.

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

**Azure Search Data uppsättning för utdata:**

Exemplet kopierar data till ett Azure Search index med namnet **Products**. Data Factory skapar inte indexet. Om du vill testa exemplet skapar du ett index med det här namnet. Skapa Azure Search indexet med samma antal kolumner som i data uppsättningen för indata. Nya poster läggs till i Azure Search-indexet varje timme.

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

**Kopiera aktivitet i en pipeline med SQL-källa och Azure Search index mottagare:**

Pipelinen innehåller en kopierings aktivitet som har kon figurer ATS för att använda data uppsättningar för indata och utdata och är schemalagda att köras varje timme. I JSON-definitionen för pipelinen är **käll** typen inställt på **SqlSource** och **mottagar** typ är inställd på **AzureSearchIndexSink**. SQL-frågan som angetts för egenskapen **SqlReaderQuery** väljer data under den senaste timmen som ska kopieras.

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

Om du kopierar data från ett moln data lager till Azure Search, krävs `executionLocation` egenskap. Följande JSON-kodfragment visar den ändring som krävs under kopierings aktivitet `typeProperties` som ett exempel. Markera avsnittet [Kopiera data mellan moln data lager](data-factory-data-movement-activities.md#global) om du vill ha stöd för värden som stöds och mer information.

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


## <a name="copy-from-a-cloud-source"></a>Kopiera från en moln källa
Om du kopierar data från ett moln data lager till Azure Search, krävs `executionLocation` egenskap. Följande JSON-kodfragment visar den ändring som krävs under kopierings aktivitet `typeProperties` som ett exempel. Markera avsnittet [Kopiera data mellan moln data lager](data-factory-data-movement-activities.md#global) om du vill ha stöd för värden som stöds och mer information.

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

Du kan också mappa kolumner från käll data uppsättningen till kolumner från Sink-datauppsättningen i kopierings aktivitets definitionen. Mer information finns i [mappa data mängds kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [prestanda-och justerings guiden för kopierings aktiviteter](data-factory-copy-activity-performance.md) för att lära dig mer om viktiga faktorer som påverkar prestanda för data förflyttning (kopierings aktivitet) och olika sätt att optimera den.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

* [Själv studie kursen om kopierings aktiviteter](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopierings aktivitet.
