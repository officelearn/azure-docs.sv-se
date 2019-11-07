---
title: Kopiera data till eller från Azure Cosmos DB (SQL API) med Data Factory
description: Lär dig hur du kopierar data från käll data lager som stöds till eller från Azure Cosmos DB (SQL API) till Sink-butiker som stöds med hjälp av Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 74bec61cbb69b17afddeb5abc1df16d8c1140234
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681309"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopiera data till eller från Azure Cosmos DB (SQL API) med Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuell version](connector-azure-cosmos-db.md)

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från och till Azure Cosmos DB (SQL API). Artikeln bygger på [kopierings aktivitet i Azure Data Factory](copy-activity-overview.md), som visar en översikt över kopierings aktiviteten.

>[!NOTE]
>Den här kopplingen stöder endast kopiering av data till/från Cosmos DB SQL API. För MongoDB-API, se [Connector för Azure Cosmos DB s API för MongoDB](connector-azure-cosmos-db-mongodb-api.md). Andra API-typer stöds inte nu.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Cosmos DB-anslutningen (SQL API) stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Azure Cosmos DB (SQL API) till alla mottagar data lager som stöds, eller kopiera data från alla käll data lager som stöds till Azure Cosmos DB (SQL API). En lista över data lager som kopierings aktiviteten stöder som källor och mottagare finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Du kan använda Azure Cosmos DB-anslutningen (SQL API) för att:

- Kopiera data från och till Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Skriv till Azure Cosmos DB som **insert** eller **upsert**.
- Importera och exportera JSON-dokument som-är eller kopiera data från eller till en tabell data uppsättning. Exempel på detta är en SQL-databas och en CSV-fil. Om du vill kopiera dokument som de är till eller från JSON-filer eller till eller från en annan Azure Cosmos DB samling, se Importera eller exportera JSON-dokument.

Data Factory integreras med [Azure Cosmos DB bulk utförar-biblioteket](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) för att ge bästa möjliga prestanda när du skriver till Azure Cosmos dB.

> [!TIP]
> [Videomigreringen](https://youtu.be/5-SRNiC_qOU) vägleder dig genom stegen för att kopiera data från Azure Blob storage till Azure Cosmos dB. Videon beskriver också prestanda justerings överväganden för att mata in data till Azure Cosmos DB allmänt.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory entiteter som är speciella för Azure Cosmos DB (SQL API).

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten Azure Cosmos DB (SQL API):

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** måste anges till **CosmosDb**. | Ja |
| Begär |Ange information som krävs för att ansluta till Azure Cosmos DB databasen.<br />**Obs!** du måste ange databas information i anslutnings strängen som visas i exemplen som följer. <br/>Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i Data Factory. Du kan också ange konto nyckeln i Azure Key Vault och hämta `accountKey`-konfigurationen från anslutnings strängen. Se följande exempel och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. |Ja |
| connectVia | [Integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller en egen värd för integration Runtime (om ditt data lager finns i ett privat nätverk). Om den här egenskapen inte anges används standard Azure Integration Runtime. |Nej |

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

**Exempel: lagrings konto nyckel i Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;Database=<Database>"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för data mängd

Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Cosmos DB-datauppsättningen (SQL API). 

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i [data uppsättningar och länkade tjänster](concepts-datasets-linked-services.md). 

Om du vill kopiera data från eller till Azure Cosmos DB (SQL API) anger du egenskapen **Type** för data uppsättningen till **DocumentDbCollection**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens **typ** -egenskap måste anges till **DocumentDbCollection**. |Ja |
| Samling |Namnet på den Azure Cosmos DB dokument samlingen. |Ja |

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

### <a name="schema-by-data-factory"></a>Schema efter Data Factory

För schema fria data lager som Azure Cosmos DB härleds kopierings aktivitet till schemat på ett av de sätt som beskrivs i följande lista. Om du inte vill [Importera eller exportera JSON-dokument i befintligt skick](#import-or-export-json-documents)är det bästa sättet att ange data strukturen i avsnittet **struktur** .

* Om du anger data strukturen med hjälp av **struktur** egenskapen i definitions definitionen för data uppsättning, Data Factory den här strukturen som schema. 

    Om en rad inte innehåller något värde för en kolumn, anges ett null-värde för kolumnvärdet.
* Om du inte anger data strukturen med hjälp av **struktur** egenskapen i data uppsättnings definitionen data Factory, härleds schemat med hjälp av den första raden i data. 

    Om den första raden inte innehåller det fullständiga schemat, kommer vissa kolumner saknas i resultatet av kopierings åtgärden.

## <a name="copy-activity-properties"></a>Kopiera aktivitets egenskaper

Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Cosmos DB-källan (SQL API) och mottagar tjänsten.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) som källa

Om du vill kopiera data från Azure Cosmos DB (SQL API) anger du **käll** typen i kopierings aktivitet till **DocumentDbCollectionSource**. 

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Typ** egenskapen för kopierings aktivitets källan måste anges till **DocumentDbCollectionSource**. |Ja |
| query |Ange Azure Cosmos DB fråga för att läsa data.<br/><br/>Exempel:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nej <br/><br/>Om detta inte anges körs SQL-instruktionen: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Ett specialtecken som visar att dokumentet är kapslat och hur du fören klar resultat uppsättningen.<br/><br/>Om exempelvis en Azure Cosmos DB fråga returnerar det kapslade resultatet `"Name": {"First": "John"}`, identifierar kopierings aktiviteten kolumn namnet som `Name.First`, med värdet John, när värdet **nestedSeparator** är **.** (punkt). |Nej<br />(Standardvärdet är **.** (punkt)) |

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

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) som mottagare

Om du vill kopiera data till Azure Cosmos DB (SQL API) anger du **mottagar** typen i kopierings aktivitet till **DocumentDbCollectionSink**. 

Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** för kopierings aktivitetens Sink måste anges till **DocumentDbCollectionSink**. |Ja |
| WriteBehavior |Beskriver hur du skriver data till Azure Cosmos DB. Tillåtna värden: **insert** och **upsert**.<br/><br/>Beteendet för **upsert** är att ersätta dokumentet om det redan finns ett dokument med samma ID. annars infogar du dokumentet.<br /><br />**Obs!** Data Factory automatiskt genererar ett ID för ett dokument om inget ID anges i det ursprungliga dokumentet eller med kolumn mappning. Det innebär att du måste se till att ditt dokument har ett ID för att **upsert** ska fungera som förväntat. |Nej<br />(Standardvärdet är **insert**) |
| writeBatchSize | Data Factory använder [Azure Cosmos DB bulk utförar-biblioteket](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) för att skriva data till Azure Cosmos dB. Egenskapen **writeBatchSize** styr storleken på dokument som ADF tillhandahåller till biblioteket. Du kan prova att öka värdet för **writeBatchSize** för att förbättra prestandan och minska värdet om dokument storleken är stor, se tipsen nedan. |Nej<br />(Standardvärdet är **10 000**) |
| nestingSeparator |Ett specialtecken i **käll** kolumnens namn som anger att ett kapslat dokument behövs. <br/><br/>`Name.First` i strukturen för data uppsättningen för utdata genererar till exempel följande JSON-struktur i Azure Cosmos DB-dokumentet när **nestedSeparator** är **.** (punkt): `"Name": {"First": "[value maps to this column from source]"}`  |Nej<br />(Standardvärdet är **.** (punkt)) |
| disableMetricsCollection | Data Factory samlar in mått som Cosmos DB ru: er för att kopiera prestanda optimering och rekommendationer. Om du är orolig för det här beteendet anger du `true` att inaktivera det. | Nej (standard är `false`) |

>[!TIP]
>Cosmos DB begränsar storleken på en enskild begäran till 2 MB. Formeln är en begär ande storlek = enskild dokument storlek * Skriv batchstorleken. Om du klickar på fel som säger att **"begäran är för stor"** , **minskar du `writeBatchSize` svärdet** i Kopiera Sink-konfigurationen.

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
## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="import-or-export-json-documents"></a>Importera eller exportera JSON-dokument

Du kan enkelt använda den här Azure Cosmos DBs anslutningen (SQL API) för att:

* Importera JSON-dokument från olika källor till Azure Cosmos DB, inklusive från Azure Blob Storage, Azure Data Lake Store och andra filbaserade arkiv som Azure Data Factory stöder.
* Exportera JSON-dokument från en Azure Cosmos DB-samling till olika filbaserade arkiv.
* Kopiera dokument mellan två Azure Cosmos DB samlingar som de är.

För att uppnå schema-oberoende kopia:

* När du använder Kopiera data-verktyget väljer du alternativet **Exportera som är till JSON-filer eller Cosmos DB samling** .
* När du använder redigering av aktiviteter ska du inte ange **strukturen** (kallas även *schema*) i Azure Cosmos db data uppsättningen. Ange inte heller egenskapen **nestingSeparator** i Azure Cosmos DB källan eller sinken i kopierings aktiviteten. När du importerar från eller exporterar till JSON-filer anger du **format** typen som **JsonFormat** i motsvarande fil Arkiv data uppsättning och konfigurerar **FilePattern** enligt beskrivningen i avsnittet JSON- [format](supported-file-formats-and-compression-codecs.md#json-format) . Ange inte avsnittet **struktur** och hoppa över resten av format inställningarna.

## <a name="next-steps"></a>Nästa steg

En lista över data lager som kopierings aktiviteten stöder som källor och handfat i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
