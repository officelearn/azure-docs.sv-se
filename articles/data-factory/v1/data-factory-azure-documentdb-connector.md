---
title: Flytta data till och från Azure Cosmos DB | Microsoft Docs
description: Lär dig hur du ska flytta data till och från Azure Cosmos DB-samling med Azure Data Factory
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: af528f210f4464c973d88fab7eeb1572effc87bc
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330406"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Flytta data till och från Azure Cosmos DB med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-azure-documentdb-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Azure Cosmos DB-anslutningsapp i V2](../connector-azure-cosmos-db.md).

Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data till och från Azure Cosmos DB (SQL-API). Den bygger på den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten.

Du kan kopiera data från alla dataarkiv till Azure Cosmos DB eller från Azure Cosmos DB till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell.

> [!IMPORTANT]
> Azure Cosmos DB-anslutningsapp stöder endast SQL-API.

Kopiera data som – är till och från JSON-filer eller en annan Cosmos DB-samling finns i [Import/Export JSON-dokument](#importexport-json-documents).

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data till och från Azure Cosmos DB med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Se [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik.
2. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen.
3. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format. Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från Cosmos DB finns [JSON-exempel](#json-examples) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för Cosmos DB:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för Azure Cosmos DB-länkad tjänst.

| **Egenskap** | **Beskrivning** | **Krävs** |
| --- | --- | --- |
| typ |Type-egenskapen måste anges till: **DocumentDb** |Ja |
| connectionString |Ange information som behövs för att ansluta till Azure Cosmos DB-databas. |Ja |

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
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av (Azure SQL, Azure-blob, Azure-tabell osv.).

Avsnittet typeProperties är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. TypeProperties avsnittet för datauppsättningen av typen **DocumentDbCollection** har följande egenskaper.

| **Egenskap** | **Beskrivning** | **Krävs** |
| --- | --- | --- |
| Samlingsnamn |Namnet på Cosmos DB-dokumentsamling. |Ja |

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
### <a name="schema-by-data-factory"></a>Schemat av Data Factory
För schemafria datalager som Azure Cosmos DB härleder scheman i Data Factory-tjänsten på något av följande sätt:

1. Om du anger strukturen för data med hjälp av den **struktur** -egenskapen i definitionen för datauppsättningen, Data Factory-tjänsten godkänner den här strukturen som schema. I det här fallet ges en rad inte innehåller ett värde för en kolumn, ett null-värde för den.
2. Om du inte anger strukturen för data med hjälp av den **struktur** -egenskapen i definitionen för datauppsättningen, Data Factory-tjänsten härleder scheman med hjälp av den första raden i data. I det här fallet om den första raden inte innehåller fullständig schemat, blir vissa kolumner saknas i resultatet för kopieringsåtgärden.

Därför för schemafria datakällor, bästa praxis är att ange strukturen för data med hjälp av den **struktur** egenskapen.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdata tabeller och principen är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktivitet tar bara en inmatning och ger endast en utdata.

Egenskaper som är tillgängliga i avsnittet typeProperties aktivitetens å andra sidan varierar med varje aktivitetstyp av och vid Kopieringsaktivitet de varierar beroende på vilka typer av källor och mottagare.

Vid Kopieringsaktivitet när källan är av typen **DocumentDbCollectionSource** följande egenskaper är tillgängliga i **typeProperties** avsnittet:

| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Krävs** |
| --- | --- | --- | --- |
| DocumentDB |Ange fråga för att läsa data. |Frågesträng som stöds av Azure Cosmos DB. <br/><br/>Exempel: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nej <br/><br/>Om inte anges, SQL-instruktionen som körs: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Specialtecken som visar att dokumentet är kapslade |Valfritt tecken. <br/><br/>Azure Cosmos DB är en NoSQL-lagring av JSON-dokument, där kapslade strukturer är tillåtna. Azure Data Factory kan användaren att ange hierarki via nestingSeparator, vilket är ””. i ovanstående exempel. Med avgränsaren, Kopieringsaktivitet genererar ”Name”-objekt med tre underordnade element först mellan- och efternamn enligt ”Name.First”, ”Name.Middle” och ”Name.Last” i tabelldefinitionen. |Nej |

**DocumentDbCollectionSink** har stöd för följande egenskaper:

| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Krävs** |
| --- | --- | --- | --- |
| nestingSeparator |Specialtecken i källkolumnsnamnet som visar det kapslade dokumentet krävs. <br/><br/>Till exempel ovan: `Name.First` i utdata tabell ger följande JSON-strukturen i Cosmos DB-dokument:<br/><br/>”Name”: {<br/>    ”Första”: "John"<br/>}, |Tecken som används för att avgränsa kapslingsnivåer.<br/><br/>Standardvärdet är `.` (punkt). |Tecken som används för att avgränsa kapslingsnivåer. <br/><br/>Standardvärdet är `.` (punkt). |
| WriteBatchSize |Antalet parallella begäranden till Azure Cosmos DB-tjänsten för att skapa dokument.<br/><br/>Du kan finjustera prestanda när du kopierar data till/från Cosmos DB med hjälp av den här egenskapen. Du kan förvänta dig en bättre prestanda om du ökar writeBatchSize eftersom flera parallella förfrågningar till Cosmos DB skickas. Men genererar du måste undvika begränsning som ett felmeddelande: ”Förfrågan är stor”.<br/><br/>Begränsning avgörs av ett antal faktorer, bland annat dokument, antalet villkoren i dokument storlek, indexeringspolicy målsamlingen osv. För kopieringsåtgärder, du kan använda en bättre samling (t.ex. S3) ha de dataflöden som är tillgängliga (2 500 begäran begärandeenheter/sekund). |Integer |Nej (standard: 5) |
| writeBatchTimeout |Vänta tills åtgärden har slutförts innan tidsgränsen uppnås. |Tidsintervall<br/><br/> Exempel: ”00: 30:00” (30 minuter). |Nej |

## <a name="importexport-json-documents"></a>Import/Export JSON-dokument
Den här Cosmos DB-anslutningen kan du enkelt

* Importera JSON-dokument från olika källor till Cosmos DB, såsom Azure Blob, Azure Data Lake, lokala filsystem eller andra filbaserade butiker som stöds av Azure Data Factory.
* Exportera JSON-dokument från Cosmos DB collecton till olika filbaserade lager.
* Migrera data mellan två Cosmos DB-samlingar som – är.

Få kopian schemaoberoende
* När du använder Kopieringsguiden, kontrollera den **”exportera som – är att JSON-filer eller Cosmos DB-samling”** alternativet.
* När med hjälp av JSON-redigering, inte anger ”struktur”-avsnittet i Cosmos DB-datauppsättningar och inte heller ”nestingSeparator”-egenskapen i Cosmos DB källa/mottagare i kopieringsaktiviteten. Om du vill importera från / exportera till JSON-filer, i filen store datauppsättningen anger formattyp av som ”JsonFormat”, config ”filePattern” och hoppa över inställningarna för rest-format, se [JSON-format](data-factory-supported-file-and-compression-formats.md#json-format) avsnittet med information.

## <a name="json-examples"></a>JSON-exempel
I följande exempel får exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure Cosmos DB och Azure Blob Storage. Men du kan kopiera data **direkt** från källor till någon av de mottagare som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) använda Kopieringsaktivitet i Azure Data Factory.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Exempel: Kopiera data från Azure Cosmos DB till Azure Blob
Exemplet nedan visar:

1. En länkad tjänst av typen [DocumentDb](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [DocumentDbCollection](#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [DocumentDbCollectionSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data i Azure Cosmos DB till Azure Blob. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

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
**Azure Blob storage-länkade tjänst:**

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
**Azure Document DB datauppsättningen för indata:**

Exemplet förutsätter att du har en samling som heter **Person** i en Azure Cosmos DB-databas.

Ange ”external”: ”true” och ange externalData principinformation Azure Data Factory-tjänsten att tabellen är extern till datafabriken och inte kommer från en aktivitet i data factory.

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

**Utdatauppsättning för Azure Blob:**

Data kopieras till en ny blob varje timme med sökvägen för den blob som uppfyller specifika datum/tid med timme kornighet.

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
Exemplet JSON-dokument i samlingen Person i en Cosmos DB-databas:

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
Cosmos DB stöder förfrågningar till dokument med hjälp av SQL-liknande syntax via hierarkisk JSON-dokument.

Exempel:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Följande pipeline kopierar data från mängden Person i Azure Cosmos DB-databasen till en Azure-blob. Som en del av kopieringsaktiviteten indata och utdata har datauppsättningar angetts.

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
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Exempel: Kopiera data från Azure-Blob till Azure Cosmos DB
Exemplet nedan visar:

1. En länkad tjänst av typen [DocumentDb](#azure-documentdb-linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [DocumentDbCollection](#azure-documentdb-dataset-type-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).

Exemplet kopierar data från Azure blob till Azure Cosmos DB. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Azure Blob storage-länkade tjänst:**

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
**Indatauppsättning för Azure-Blobb:**

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
**Utdatauppsättning för Azure Cosmos DB:**

Exemplet kopierar data till en samling som heter ”Person”.

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
Följande pipeline kopierar data från Azure Blob till samlingen Person i Cosmos DB. Som en del av kopieringsaktiviteten indata och utdata har datauppsättningar angetts.

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
Om blob Exempelindata som

```
1,John,,Doe
```
Sedan ska utdata JSON i Cosmos DB som:

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
Azure Cosmos DB är en NoSQL-lagring av JSON-dokument, där kapslade strukturer är tillåtna. Azure Data Factory kan användaren att ange hierarki via **nestingSeparator**, vilket är ””. i det här exemplet. Med avgränsaren, Kopieringsaktivitet genererar ”Name”-objekt med tre underordnade element först mellan- och efternamn enligt ”Name.First”, ”Name.Middle” och ”Name.Last” i tabelldefinitionen.

## <a name="appendix"></a>Bilaga
1. **Fråga:** Stöd för Kopieringsaktiviteten uppdatering av befintliga poster?

    **Svar:** Nej.
2. **Fråga:** Hur ett nytt försök med en kopia till Azure Cosmos DB behandlar redan kopierade posterna?

    **Svar:** Om poster har ett ”ID”-fält och kopieringen försöker infoga en post med samma ID, genererar ett fel i kopieringsåtgärden.
3. **Fråga:** Har stöd för Data Factory [intervall eller hash-baserade Datapartitionering](../../cosmos-db/sql-api-partition-data.md)?

    **Svar:** Nej.
4. **Fråga:** Kan jag ange mer än en Azure Cosmos DB-samling för en tabell?

    **Svar:** Nej. Endast en samling kan anges just nu.

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
