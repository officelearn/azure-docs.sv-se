---
title: Flytta data till/från Azure Cosmos DB | Microsoft Docs
description: Lär dig hur du ska flytta data till/från Azure Cosmos DB samlingen med hjälp av Azure Data Factory
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f3ebd704129aabecffdaa2589b8b086803a2d092
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046613"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Flytta data till och från Azure Cosmos-databasen med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-azure-documentdb-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns [Azure Cosmos DB-kopplingen i V2](../connector-azure-cosmos-db.md).

Den här artikeln förklarar hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data till och från Azure Cosmos DB (SQL-API). Den bygger på den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet. 

Du kan kopiera data från alla stöds källa datalagret till Azure Cosmos DB eller Azure Cosmos DB till alla stöds sink-datalagret. En lista över datakällor som stöds som datakällor eller sänkor av kopieringsaktiviteten, finns det [stöds datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. 

> [!IMPORTANT]
> Azure DB Cosmos connector har bara stöd för SQL-API.

Att kopiera data som-är till/från JSON-filer eller en annan Cosmos DB samling finns [Import/Export JSON-dokument](#importexport-json-documents).

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data till och från Azure Cosmos DB med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall** , **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare: 

1. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory.
2. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. 
3. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. 

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från Cosmos DB finns [JSON-exempel](#json-examples) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter Cosmos DB: 

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för Azure Cosmos DB länkad tjänst.

| **Egenskap** | **Beskrivning** | **Krävs** |
| --- | --- | --- |
| typ |Egenskapen type måste anges till: **DocumentDb** |Ja |
| connectionString |Ange information som behövs för att ansluta till Azure DB som Cosmos-databasen. |Ja |

Exempel:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt hittar du den [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av dataset (Azure SQL Azure blob, Azure-tabellen, osv.).

Avsnittet typeProperties är olika för varje typ av dataset och ger information om placeringen av data i datalagret. TypeProperties avsnittet för datauppsättningen av typen **DocumentDbCollection** har följande egenskaper.

| **Egenskap** | **Beskrivning** | **Krävs** |
| --- | --- | --- |
| Samlingsnamn |Namnet på samlingen Cosmos DB dokumentet. |Ja |

Exempel:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Schema som Data Factory
För schemafria data butiker, till exempel Azure Cosmos DB härleder Data Factory-tjänsten schemat på något av följande sätt:  

1. Om du anger strukturen för data med hjälp av den **struktur** egenskap i datauppsättningsdefinitionen Data Factory-tjänsten godkänner den här strukturen som schema. Om en rad inte innehåller ett värde för en kolumn, tillhandahålls i det här fallet ett null-värde för den.
2. Om du inte anger strukturen för data med hjälp av den **struktur** egenskap i datauppsättningsdefinitionen Data Factory-tjänsten härleder schemat med hjälp av den första raden i data. I det här fallet om den första raden inte innehåller fullständig schemat kommer vissa kolumner att saknas i resultatet av kopieringsåtgärden.

Därför för schemafria datakällor, det bästa sättet är att ange hur dina data med hjälp av den **struktur** egenskapen.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över egenskaper som är tillgängliga för att definiera aktiviteter & avsnitt hittar du den [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, ingående och utgående tabeller och principen är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktiviteten tar endast en inmatning och ger en enda utdata.

Egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten å andra sidan varierar med varje aktivitetstyp och vid kopieringsaktiviteten de varierar beroende på vilka typer av datakällor och sänkor.

Vid kopieringsaktiviteten när datakällan är av typen **DocumentDbCollectionSource** följande egenskaper är tillgängliga i **typeProperties** avsnitt:

| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Krävs** |
| --- | --- | --- | --- |
| DocumentDB |Ange frågan som läser data. |Frågesträng stöds av Azure Cosmos DB. <br/><br/>Exempel: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nej <br/><br/>Om inget annat anges, SQL-instruktionen som körs: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Specialtecken som visar att dokumentet är kapslad |Valfritt tecken. <br/><br/>Azure Cosmos-DB är en NoSQL store för JSON-dokument, där kapslade strukturer är tillåtna. Azure Data Factory gör det möjligt för användaren att ange hierarkin via nestingSeparator, vilket är ””. i ovanstående exempel. Med avgränsare, kopieringsaktiviteten genererar ”Name”-objektet med tre underordnade element först mellan- och efternamn enligt ”Name.First”, ”Name.Middle” och ”Name.Last” i tabelldefinitionen. |Nej |

**DocumentDbCollectionSink** stöder följande egenskaper:

| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Krävs** |
| --- | --- | --- | --- |
| nestingSeparator |Ett specialtecken i källkolumnsnamnet att ange kapslade dokumentet krävs. <br/><br/>Till exempel ovan: `Name.First` i utdata tabell ger följande JSON-strukturen i Cosmos-DB-dokument:<br/><br/>”Name”: {<br/>    ”Första”: ”John”<br/>}, |Tecken som används för att avgränsa kapslingsnivåer.<br/><br/>Standardvärdet är `.` (punkt). |Tecken som används för att avgränsa kapslingsnivåer. <br/><br/>Standardvärdet är `.` (punkt). |
| writeBatchSize |Antalet parallella begäranden till Azure DB som Cosmos-tjänsten att skapa dokument.<br/><br/>Du kan finjustera prestanda vid kopiering av data till och från Cosmos-databas med hjälp av den här egenskapen. Du kan förvänta dig bättre prestanda om du ökar writeBatchSize eftersom flera parallella begäranden till Cosmos DB skickas. Men du behöver undvika begränsning som kan utlösa ett felmeddelande: ”begär frekvensen är stor”.<br/><br/>Begränsning bestäms av ett antal faktorer, bland annat storlek dokument, antalet villkoren i dokument, indexering princip målsamling osv. För kopieringsåtgärd, du kan använda en bättre samling (t.ex. S3) har mest genomströmning tillgänglig (2 500 begärande enheter per sekund). |Integer |Nej (standard: 5) |
| writeBatchTimeout |Vänta tills åtgärden har slutförts innan tidsgränsen uppnås. |TimeSpan<br/><br/> Exempel ”: 00: 30:00” (30 minuter). |Nej |

## <a name="importexport-json-documents"></a>Importera och exportera JSON-dokument
Den här Cosmos-DB-anslutningen kan du enkelt

* Importera JSON-dokument från olika källor till Cosmos-DB, inklusive Azure Blob Azure Data Lake, lokalt filsystem eller andra filbaserade butiker som stöds av Azure Data Factory.
* Exportera JSON-dokument från Cosmos DB collecton till olika filbaserade butiker.
* Migrera data mellan två Cosmos DB samlingar som-är.

Att uppnå kopian schema-oberoende 
* När du använder guiden Kopiera, kontrollera den **”exportera som – som JSON-filer eller Cosmos DB samling”** alternativet.
* När med JSON redigering inte anger avsnittet ”struktur” i Cosmos DB datauppsättning/ar eller egenskapen ”nestingSeparator” i Cosmos DB källor/mottagare i en Kopieringsaktivitet. Om du vill importera från / exportera till JSON-filer, ange formatet i filen store datamängden som ”JsonFormat”, config ”filePattern” och hoppa över inställningarna för rest-format, se [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format) avsnittet detaljer.

## <a name="json-examples"></a>JSON-exempel
Följande exempel ger exempel JSON definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure Cosmos DB och Azure Blob Storage. Dock datan kan kopieras **direkt** från någon av källorna till någon av sänkor anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Exempel: Kopiera data från Azure Cosmos DB till Azure-Blob
Exemplet nedan visar:

1. En länkad tjänst av typen [DocumentDb](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [DocumentDbCollection](#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med Kopieringsaktiviteten som använder [DocumentDbCollectionSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data i Azure Cosmos DB till Azure-Blob. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Azure Cosmos-DB länkade tjänsten:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Azure Blob storage länkade tjänsten:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure dokumentet DB indatauppsättning:**

Exemplet förutsätter att du har en samling med namnet **Person** i Azure DB som Cosmos-databasen.

Inställningen ”externa”: ”true” och ange externalData principinformation Azure Data Factory-tjänsten att tabellen är extern till data factory och inte kommer från en aktivitet i datafabriken.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Azure Blob utdatauppsättningen:**

Data kopieras till en ny blob varje timme med sökvägen för blobben reflektion specifika datetime med timme granularitet.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Exempel JSON-dokumentet i samlingen Person i en Cosmos-DB-databas:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
Cosmos DB stöder förfrågningar till dokument med hjälp av en SQL som syntax över hierarkiska JSON-dokument.

Exempel: 

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Följande pipeline kopierar data från samlingen Person i Azure DB som Cosmos-databasen till en Azure blob. Datauppsättningar har angetts som en del av kopieringsaktiviteten indata och utdata.  

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Exempel: Kopiera data från Azure Blob till Azure Cosmos DB 
Exemplet nedan visar:

1. En länkad tjänst av typen [DocumentDb](#azure-documentdb-linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [DocumentDbCollection](#azure-documentdb-dataset-type-properties).
5. En [pipeline](data-factory-create-pipelines.md) med Kopieringsaktiviteten som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).

Exemplet kopierar data från Azure blob till Azure Cosmos DB. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Azure Blob storage länkade tjänsten:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Cosmos-DB länkade tjänsten:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Azure Blob inkommande datamängd:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Azure Cosmos-DB utdatauppsättningen:**

Exemplet kopierar data till en samling med namnet ”Person”.

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Följande pipeline kopierar data från Azure Blob till samlingen Person i Cosmos-databasen. Datauppsättningar har angetts som en del av kopieringsaktiviteten indata och utdata.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          }
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Om blob Exempelindata som

```
1,John,,Doe
```
Utdata JSON i Cosmos DB blir som:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
Azure Cosmos-DB är en NoSQL store för JSON-dokument, där kapslade strukturer är tillåtna. Azure Data Factory gör det möjligt för användaren att ange hierarkin via **nestingSeparator**, vilket är ””. i det här exemplet. Med avgränsare, kopieringsaktiviteten genererar ”Name”-objektet med tre underordnade element först mellan- och efternamn enligt ”Name.First”, ”Name.Middle” och ”Name.Last” i tabelldefinitionen.

## <a name="appendix"></a>Bilaga
1. **Fråga:** har stöd för att uppdatera Kopieringsaktiviteten befintliga poster?

    **Svar:** Nej.
2. **Fråga:** hur redan har ett nytt försök till en kopia till Azure Cosmos DB behandlar kopieras poster?

    **Svar:** om poster har ett ”ID”-fält och kopieringen görs ett försök att infoga en post med samma ID, kopieringen genererar ett fel.  
3. **Fråga:** stöder Data Factory [intervall eller hash-baserad Datapartitionering](../../cosmos-db/sql-api-partition-data.md)?

    **Svar:** Nej.
4. **Fråga:** kan jag ange mer än en Azure DB som Cosmos-samlingen för en tabell?

    **Svar:** Nej. Endast en samling kan anges just nu.

## <a name="performance-and-tuning"></a>Prestanda och finjustering
Se [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
