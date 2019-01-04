---
title: Kopiera data till och från Azure Cosmos DB (SQL-API) med hjälp av Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från datalager som stöds till eller från Azure Cosmos DB (SQL-API) till mottagarens datalager med Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: d927842dfc15c089225531c9718145ab20e329dc
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53808867"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopiera data till och från Azure Cosmos DB (SQL-API) med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuell version](connector-azure-cosmos-db.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från och till Azure Cosmos DB (SQL-API). Artikeln bygger vidare på [Kopieringsaktivitet i Azure Data Factory](copy-activity-overview.md), som anger en allmän översikt över Kopieringsaktivitet.

>[!NOTE]
>Den här anslutningen endast stöder kopiera data till/från Cosmos DB SQL API. MongoDB API finns i [Cosmos DB MongoDB API-kopplingen](connector-azure-cosmos-db-mongodb-api.md). Andra API-typer stöds inte nu.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Azure Cosmos DB (SQL-API) till alla mottagare som stöds eller kopiera data från alla dataarkiv till Azure Cosmos DB (SQL-API). En lista över data lagrar att det stöder Kopieringsaktiviteten som källor och mottagare, finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Du kan använda Azure Cosmos DB (SQL API)-anslutningen till:

- Kopiera data från och till Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Skriva till Azure Cosmos DB som **infoga** eller **upsert**.
- Importera och exportera JSON-dokument som – är, eller kopiera data från eller till en datauppsättning i tabellformat. Exempel: en SQL-databas och en CSV-fil. Dokument som att kopiera-är till eller från JSON-filer eller till eller från en annan Azure Cosmos DB-samling finns i [Import eller export JSON-dokument](#importexport-json-documents).

Data Factory kan integreras med den [Azure Cosmos DB bulk executor biblioteket](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) att ge bästa möjliga prestanda när du skriver till Azure Cosmos DB.

> [!TIP]
> Den [datamigrering video](https://youtu.be/5-SRNiC_qOU) vägleder dig genom stegen för att kopiera data från Azure Blob storage till Azure Cosmos DB. Videon beskriver också prestandajustering överväganden för att föra in data till Azure Cosmos DB i allmänhet.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory-entiteter som är specifika för Azure Cosmos DB (SQL-API).

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Azure Cosmos DB (SQL-API) länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen måste anges till **CosmosDb**. | Ja |
| connectionString |Ange information som krävs för att ansluta till Azure Cosmos DB-databasen.<br /><br />**Obs!** Du måste ange databasinformation i anslutningssträngen som visas i följande exempel. Markera det här fältet som en **SecureString** Skriv för att lagra den på ett säkert sätt i Data Factory. Du kan också [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [Integreringskörningen](concepts-integration-runtime.md) för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en lokal integration runtime (om ditt datalager finns i ett privat nätverk). Om egenskapen inte anges används standard Azure Integration Runtime. |Nej |

**Exempel**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

Det här avsnittet innehåller en lista över egenskaper som har stöd för Azure Cosmos DB (SQL API)-datauppsättningen. 

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). 

För att kopiera data från eller till Azure Cosmos DB (SQL-API), ange den **typ** egenskapen på datauppsättningen till **DocumentDbCollection**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskap måste anges till **DocumentDbCollection**. |Ja |
| Samlingsnamn |Namnet på Azure Cosmos DB-dokumentsamling. |Ja |

**Exempel**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schemat av Data Factory

För schemafria datalager som Azure Cosmos DB härleder Kopieringsaktiviteten schemat i ett av de sätt som beskrivs i följande lista. Om du inte vill [importera eller exportera JSON-dokument som – är](#import-or-export-json-documents), är bra att anger strukturen för data i den **struktur** avsnittet.

* Om du anger strukturen för data med hjälp av den **struktur** -egenskapen i definitionen för datauppsättningen, Data Factory godkänner den här strukturen som schema. 

    Om en rad inte innehåller ett värde för en kolumn, har ett null-värde angetts för värdet i kolumnen.
* Om du inte anger strukturen för data med hjälp av den **struktur** -egenskapen i definitionen för datauppsättningen, Data Factory-tjänsten härleder scheman med hjälp av den första raden i data. 

    Om den första raden inte innehåller fullständig schemat kan saknas vissa kolumner i resultatet för kopieringsåtgärden.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som har stöd för Azure Cosmos DB (SQL API)-källa och mottagare.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL-API) som källa

För att kopiera data från Azure Cosmos DB (SQL-API), ange den **källa** typ i Kopieringsaktiviteten till **DocumentDbCollectionSource**. 

Följande egenskaper stöds i Kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen för aktiviteten kopieringskälla måste anges till **DocumentDbCollectionSource**. |Ja |
| DocumentDB |Ange Azure Cosmos DB-fråga för att läsa data.<br/><br/>Exempel:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nej <br/><br/>Om inte anges, körs den här SQL-instruktionen: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Specialtecken som anger att dokumentet är kapslade och hur du platta ut resultatet.<br/><br/>Exempel: om en Azure Cosmos DB-fråga returnerar det kapslade `"Name": {"First": "John"}`, Kopieringsaktivitet identifierar kolumnnamnet som `Name.First`, med värdet ”John”, när den **nestedSeparator** värdet är **.** (punkt). |Nej<br />(standardvärdet är **.** (punkt)) |

**Exempel**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL-API) som mottagare

För att kopiera data till Azure Cosmos DB (SQL-API), ange den **mottagare** typ i Kopieringsaktiviteten till **DocumentDbCollectionSink**. 

Följande egenskaper stöds i Kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen för mottagare för Kopieringsaktivitet måste anges till **DocumentDbCollectionSink**. |Ja |
| WriteBehavior |Beskriver hur du skriver data till Azure Cosmos DB. Tillåtna värden: **infoga** och **upsert**.<br/><br/>Beteendet för **upsert** är att ersätta dokumentet om ett dokument med samma ID redan finns, annars Infoga dokumentet.<br /><br />**Obs!** Data Factory genererar automatiskt ett ID för ett dokument om ett ID inte har angetts i det ursprungliga dokumentet eller genom kolumnmappning. Det innebär att måste du se till att, för **upsert** för att fungera som förväntat, dokumentet har ett ID. |Nej<br />(standardvärdet är **infoga**) |
| WriteBatchSize | Data Factory använder den [Azure Cosmos DB bulk executor biblioteket](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) att skriva data till Azure Cosmos DB. Den **writeBatchSize** egenskapen styr storleken på dokument med ADF i biblioteket. Du kan prova att öka värdet för **writeBatchSize** att förbättra prestanda och minska värdet om dokumentet storlek vara stora – se nedan tips. |Nej<br />(standardvärdet är **10 000**) |
| nestingSeparator |Specialtecken i den **källa** kolumnnamn som anger att ett kapslade dokument krävs. <br/><br/>Till exempel `Name.First` i datauppsättningen för utdata struktur genererar följande JSON-strukturen i Azure Cosmos DB dokumentera när den **nestedSeparator** är **.** (punkt): `"Name": {"First": "[value maps to this column from source]"}`  |Nej<br />(standardvärdet är **.** (punkt)) |

>[!TIP]
>Cosmos DB begränsar storleken på enskild begäran till 2MB. Formeln är begära storlek = enda dokumentstorlek * skriva batchstorlek. Om du stöter på fel som säger **”begär är för stor”.** , **minska den `writeBatchSize` värdet** i konfigurationen för mottagare av kopia.

**Exempel**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-or-export-json-documents"></a>Importera eller exportera JSON-dokument

Du kan använda den här Azure Cosmos DB (SQL API)-kopplingen till enkelt:

* Importera JSON-dokument från olika källor till Azure Cosmos DB, såsom från Azure Blob storage, Azure Data Lake Store och andra filbaserade butiker som har stöd för Azure Data Factory.
* Exportera JSON-dokument från en Azure Cosmos DB-samling till olika filbaserade lager.
* Kopiera mellan två Azure Cosmos DB-samlingar som – är.

Få schemaoberoende kopia:

* När du använder verktyget kopieringsdata, Välj den **exportera som – är att JSON-filer eller Cosmos DB-samling** alternativet.
* När du använder aktiviteten redigering kan inte ange den **struktur** (kallas även *schemat*) avsnitt i Azure Cosmos DB-datauppsättningen. Dessutom inte anger den **nestingSeparator** -egenskapen i Azure Cosmos DB-källan eller mottagaren i Kopieringsaktiviteten. När du importerar från eller exportera till JSON-filer i den motsvarande filen lagra datauppsättning, ange den **format** skriver som **JsonFormat** och konfigurera den **filePattern** som beskrivs i den [JSON-format](supported-file-formats-and-compression-codecs.md#json-format) avsnittet. Sedan kan du inte anger den **struktur** avsnittet och hoppa över resten av formatinställningarna för.

## <a name="next-steps"></a>Nästa steg

En lista över datalager som Kopieringsaktiviteten som källor och egenskaperna i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
