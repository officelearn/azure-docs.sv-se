---
title: Flytta data till/från Azure Cosmos DB
description: Lär dig hur du flyttar data till/från Azure Cosmos DB-samling med Azure Data Factory
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a638184d5232de916ebd25360147301a93309dd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260519"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Flytta data till och från Azure Cosmos DB med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-azure-documentdb-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Azure Cosmos DB-anslutningsappen i V2](../connector-azure-cosmos-db.md).

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data till/från Azure Cosmos DB (SQL API). Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

Du kan kopiera data från alla källdatalager som stöds till Azure Cosmos DB eller från Azure Cosmos DB till alla sink-datalager som stöds. En lista över datalager som stöds som källor eller sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

> [!IMPORTANT]
> Azure Cosmos DB-anslutning stöder endast SQL API.

Information om hur du kopierar data enligt/från JSON-filer eller en annan Cosmos DB-samling finns i [Importera/exportera JSON-dokument](#importexport-json-documents).

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data till/från Azure Cosmos DB med hjälp av olika verktyg/API:er.

Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen.
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet. För exempel med JSON-definitioner för datafabrikentiteter som används för att kopiera data till/från Cosmos DB finns i avsnittet [JSON-exempel](#json-examples) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för Cosmos DB:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Följande tabell innehåller en beskrivning av JSON-element som är specifika för Azure Cosmos DB-länkade tjänst.

| **Egenskap** | **Beskrivning** | **Obligatoriskt** |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **DocumentDb** |Ja |
| Connectionstring |Ange information som behövs för att ansluta till Azure Cosmos DB-databas. |Ja |

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
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (Azure SQL, Azure blob, Azure-tabell osv.).

Avsnittet typeProperties är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Avsnittet typeProperties för datauppsättningen av typen **DocumentDbCollection** har följande egenskaper.

| **Egenskap** | **Beskrivning** | **Obligatoriskt** |
| --- | --- | --- |
| collectionName (samlingsnamn) |Namn på dokumentsamlingen Cosmos DB. |Ja |

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
### <a name="schema-by-data-factory"></a>Schema efter datafabrik
För schemafria datalager som Azure Cosmos DB härleder tjänsten Data Factory schemat på något av följande sätt:

1. Om du anger datastrukturen med hjälp av **egenskapen struktur** i datauppsättningsdefinitionen, respekterar datafabrikstjänsten den här strukturen som schema. I det här fallet, om en rad inte innehåller ett värde för en kolumn, kommer ett null-värde att anges för den.
2. Om du inte anger datastrukturen med hjälp av **egenskapen struktur** i datauppsättningsdefinitionen, härleder datafabrikstjänsten schemat med hjälp av den första raden i data. I det här fallet, om den första raden inte innehåller hela schemat, kommer vissa kolumner att saknas i resultatet av kopieringen.

För schemafria datakällor är därför bästa praxis att ange strukturen för data med hjälp av **egenskapen struktur.**

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatatabeller och princip är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktiviteten tar bara en indata och producerar bara en utdata.

Egenskaper som är tillgängliga i avsnittet typEgenskaper i aktiviteten å andra sidan varierar med varje aktivitetstyp och när det gäller kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

När det gäller kopieringsaktivitet när källan är av typen **DocumentDbCollectionSource** är följande egenskaper tillgängliga i avsnittet **typeProperties:**

| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Obligatoriskt** |
| --- | --- | --- | --- |
| DocumentDB |Ange frågan för att läsa data. |Frågesträng som stöds av Azure Cosmos DB. <br/><br/>Exempel: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Inga <br/><br/>Om inget anges körs SQL-uttrycket som körs:`select <columns defined in structure> from mycollection` |
| nestingSeparator |Specialtecken som anger att dokumentet är kapslat |Vilket tecken som helst. <br/><br/>Azure Cosmos DB är ett NoSQL-arkiv för JSON-dokument, där kapslade strukturer tillåts. Azure Data Factory gör det möjligt för användare att beteckna hierarki via nestingSeparator, som är "." i ovanstående exempel. Med avgränsaren genererar kopieringsaktiviteten "Namn"-objektet med tre underordnade element Först, Mitten och Sist, enligt "Name.First", "Name.Middle" och "Name.Last" i tabelldefinitionen. |Inga |

**DocumentDbCollectionSink** stöder följande egenskaper:

| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Obligatoriskt** |
| --- | --- | --- | --- |
| nestingSeparator |Ett specialtecken i källkolumnens namn för att ange att kapslat dokument behövs. <br/><br/>Till exempel `Name.First` ovan: i utdatatabellen skapas följande JSON-struktur i Cosmos DB-dokumentet:<br/><br/>"Namn": {<br/>    "Först": "John"<br/>}, |Tecken som används för att avgränsa kapslingsnivåer.<br/><br/>Standardvärdet `.` är (punkt). |Tecken som används för att avgränsa kapslingsnivåer. <br/><br/>Standardvärdet `.` är (punkt). |
| skriverBatchSize |Antal parallella begäranden till Azure Cosmos DB-tjänst för att skapa dokument.<br/><br/>Du kan finjustera prestanda när du kopierar data till/från Cosmos DB med hjälp av den här egenskapen. Du kan förvänta dig bättre prestanda när du ökar writeBatchSize eftersom fler parallella begäranden till Cosmos DB skickas. Du måste dock undvika begränsning som kan kasta felmeddelandet: "Begäranden är stor".<br/><br/>Begränsning bestäms av ett antal faktorer, inklusive storlek på dokument, antal termer i dokument, indexering politik målinsamling, etc. För kopieringsåtgärder kan du använda en bättre samling (t.ex. |Integer |Nej (standard: 5) |
| skriverBatchTimeout |Vänta på att åtgärden ska slutföras innan den har time out. |Gått<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |

## <a name="importexport-json-documents"></a>Importera/exportera JSON-dokument
Med den här Cosmos DB-kontakten kan du enkelt

* Importera JSON-dokument från olika källor till Cosmos DB, inklusive Azure Blob, Azure Data Lake, lokalt filsystem eller andra filbaserade butiker som stöds av Azure Data Factory.
* Exportera JSON-dokument från Cosmos DB-samling till olika filbaserade butiker.
* Migrera data mellan två Cosmos DB-samlingar som de är.

För att uppnå en sådan schemaa-agnostisk kopia,
* När du använder kopieringsguiden kontrollerar du alternativet **"Exportera som det är till JSON-filer eller Cosmos DB-samling".**
* När du använder JSON-redigering ska du inte ange avsnittet "struktur" i Cosmos DB-datauppsättningar eller egenskapen "nestingSeparator" på Cosmos DB-käll-/diskho i kopieringsaktivitet. Om du vill importera från/exportera till JSON-filer anger du formattypen "JsonFormat" i fillagret som "JsonFormat", config "filePattern" och hoppar över inställningarna för resten-format, se [avsnittet JSON-format](data-factory-supported-file-and-compression-formats.md#json-format) om detaljer.

## <a name="json-examples"></a>JSON exempel
Följande exempel innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure Cosmos DB och Azure Blob Storage. Data kan dock kopieras **direkt** från någon av källorna till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Exempel: Kopiera data från Azure Cosmos DB till Azure Blob
Urvalet nedan visar:

1. En länkad tjänst av typen [DocumentDb](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [DocumentDbCollection](#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med copy activity som använder [DocumentDbCollectionSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exempelet kopierar data i Azure Cosmos DB till Azure Blob. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

**Azure Cosmos DB-länkad tjänst:**

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
**Azure Blob storage-länkad tjänst:**

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
**Azure Document DB-indatauppsättning:**

Exemplet förutsätter att du har en samling med namnet **Person** i en Azure Cosmos DB-databas.

Ange "extern": "true" och ange externdataprincipinformation azure data factory-tjänsten att tabellen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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

**Azure Blob-utdatauppsättning:**

Data kopieras till en ny blob varje timme med sökvägen för bloben som återspeglar den specifika datumtiden med timmegranularitet.

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
Exempel på JSON-dokument i personsamlingen i en Cosmos DB-databas:

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
Cosmos DB stöder att fråga dokument med hjälp av en SQL-liknande syntax över hierarkiska JSON-dokument.

Exempel:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Följande pipeline kopierar data från personsamlingen i Azure Cosmos DB-databasen till en Azure-blob. Som en del av kopieringsaktiviteten har indata- och utdatauppsättningar angetts.

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
Urvalet nedan visar:

1. En länkad tjänst av typen DocumentDb.
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen DocumentDbCollection.
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och DocumentDbCollectionSink.

Exempelet kopierar data från Azure blob till Azure Cosmos DB. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

**Azure Blob storage-länkad tjänst:**

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
**Azure Cosmos DB-länkad tjänst:**

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
**Azure Blob-indatauppsättning:**

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
**Azure Cosmos DB-utdatauppsättning:**

Exemplet kopierar data till en samling med namnet "Person".

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
Följande pipeline kopierar data från Azure Blob till personsamlingen i Cosmos DB. Som en del av kopieringsaktiviteten har indata- och utdatauppsättningar angetts.

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
          },
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
Om exempelblobbingången är som

```
1,John,,Doe
```
Då produktionen JSON i Cosmos DB kommer att vara som:

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
Azure Cosmos DB är ett NoSQL-arkiv för JSON-dokument, där kapslade strukturer tillåts. Azure Data Factory gör det möjligt för användare att beteckna hierarki via **nestingSeparator**, som är "." i det här exemplet. Med avgränsaren genererar kopieringsaktiviteten "Namn"-objektet med tre underordnade element Först, Mitten och Sist, enligt "Name.First", "Name.Middle" och "Name.Last" i tabelldefinitionen.

## <a name="appendix"></a>Bilaga
1. **Fråga:** Stöder copy activity-uppdateringen av befintliga poster?

    **Svar:** Nej.
2. **Fråga:** Hur hanterar ett nytt försök av en kopia till Azure Cosmos DB redan kopierade poster?

    **Svar:** Om poster har ett "ID" fält och kopieringen försöker infoga en post med samma ID, genererar kopieringen ett fel.
3. **Fråga:** Har Data Factory [stödområde eller hash-baserad datapartitionering?](../../cosmos-db/sql-api-partition-data.md)

    **Svar:** Nej.
4. **Fråga:** Kan jag ange mer än en Azure Cosmos DB-samling för en tabell?

    **Svar:** Nej. Endast en samling kan anges just nu.

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.
