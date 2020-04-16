---
title: Kopiera data till och från Azure Table-lagring
description: Lär dig hur du kopierar data från källarkiv som stöds till Azure Table-lagring eller från tabelllagring till sink-butiker som stöds med hjälp av Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/27/2019
ms.openlocfilehash: 6edd32f8f3579238d1f08f55ce9fb1528fa5d211
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417482"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Kopiera data till och från Azure Table-lagring med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-azure-table-connector.md)
> * [Aktuell version](connector-azure-table-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att kopiera data till och från Azure Table Storage. Den bygger på översiktsartikeln [Kopiera aktivitet](copy-activity-overview.md) som ger en allmän översikt över kopieringsaktivitet.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Table-lagringsappen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från alla källdatalager som stöds till Tabelllagring. Du kan också kopiera data från tabelllagring till alla sink-datalager som stöds. En lista över datalager som stöds som källor eller sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Specifikt stöder den här Azure Table-anslutningsappen kopiering av data med hjälp av kontonyckel och tjänstautentiseringar med delad åtkomstsignatur.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för tabelllagring.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

### <a name="use-an-account-key"></a>Använda en kontonyckel

Du kan skapa en Azure Storage-länkad tjänst med hjälp av kontonyckeln. Det ger datafabriken global åtkomst till lagring. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till **AzureTableStorage**. |Ja |
| Connectionstring | Ange den information som behövs för att ansluta till lagring för egenskapen connectionString. <br/>Du kan också placera kontonyckeln i `accountKey` Azure Key Vault och hämta konfigurationen ur anslutningssträngen. Mer information finns i följande exempel och [Store-autentiseringsuppgifter i](store-credentials-in-key-vault.md) azure key vault-artikeln. |Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted Integration Runtime (om ditt datalager finns i ett privat nätverk). Om det inte anges används standardkörningen för Azure Integration. |Inga |

>[!NOTE]
>Om du använde "AzureStorage" typ länkad tjänst, stöds den fortfarande som den är, medan du föreslås använda den här nya "AzureTableStorage"-länkade tjänsttypen framöver.

**Exempel:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: store-kontonyckel i Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
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

### <a name="use-shared-access-signature-authentication"></a>Använda autentisering av signatur för delad åtkomst

Du kan också skapa en lagringslänkad tjänst med hjälp av en signatur för delad åtkomst. Det ger datafabriken begränsad/tidsbunden åtkomst till alla/specifika resurser i lagringen.

En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagringskonto. Du kan använda den för att bevilja en klient begränsad behörighet till objekt i ditt lagringskonto under en angiven tid och med en angiven uppsättning behörigheter. Du behöver inte dela dina kontoåtkomstnycklar. Signaturen för delad åtkomst är en URI som i frågeparametrarna omfattar all information som krävs för autentiserat åtkomst till en lagringsresurs. För att komma åt lagringsresurser med signaturen för delad åtkomst behöver klienten bara skicka in signaturen för delad åtkomst till lämplig konstruktor eller metod. Mer information om signaturer för delad åtkomst finns i Signaturer för [delad åtkomst: Förstå signaturmodellen för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Data Factory stöder nu både **signaturer för delad åtkomst till tjänsten** och **signaturer för delad åtkomst till kontot**. Mer information om signaturer för delad åtkomst finns i [Bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SAS (Shared Access Signatures).](../storage/common/storage-sas-overview.md) 

> [!TIP]
> Om du vill generera en signatur för delad åtkomst för tjänsten för ditt lagringskonto kan du köra följande PowerShell-kommandon. Ersätt platshållarna och bevilja nödvändig behörighet.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Om du vill använda autentisering av signatur för delad åtkomst stöds följande egenskaper.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till **AzureTableStorage**. |Ja |
| sasuri (samma sak) | Ange SAS-URI för signaturen URI för delad åtkomst i tabellen. <br/>Markera det här fältet som en SecureString för att lagra det säkert i Data Factory. Du kan också placera SAS-token i Azure Key Vault för att utnyttja automatisk rotation och ta bort tokendelen. Mer information finns i följande exempel och [Store-autentiseringsuppgifter i](store-credentials-in-key-vault.md) azure key vault-artikeln. | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller self-hosted Integration Runtime (om ditt datalager finns i ett privat nätverk). Om det inte anges används standardkörningen för Azure Integration. |Inga |

>[!NOTE]
>Om du använde "AzureStorage" typ länkad tjänst, stöds den fortfarande som den är, medan du föreslås använda den här nya "AzureTableStorage"-länkade tjänsttypen framöver.

**Exempel:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<account>.table.core.windows.net/<table>?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: store-kontonyckel i Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<account>.table.core.windows.net/<table>>"
            },
            "sasToken": { 
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

När du skapar en URI för delad åtkomst-signatur bör du tänka på följande:

- Ange lämpliga läs-/skrivbehörigheter för objekt baserat på hur den länkade tjänsten (läsa, skriva, läsa/skriva) används i din datafabrik.
- Ställ **in utgångstid** på lämpligt sätt. Kontrollera att åtkomsten till Lagringsobjekt inte upphör att gälla inom den aktiva perioden för pipelinen.
- URI-uri:n ska skapas på rätt tabellnivå baserat på behovet.

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Table-datauppsättningen.

Om du vill kopiera data till och från Azure Table anger du egenskapen type för datauppsättningen till **AzureTable**. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för datauppsättningen måste anges till **AzureTable**. |Ja |
| tableName |Namnet på tabellen i databasinstansen Tabelllagring som den länkade tjänsten refererar till. |Ja |

**Exempel:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schema efter datafabrik

För schemafria datalager som Azure Table härleder Data Factory schemat på något av följande sätt:

* Om du anger kolumnmappningen i kopieringsaktivitet använder Data Factory kolumnlistan för källsidan för att hämta data. I det här fallet, om en rad inte innehåller ett värde för en kolumn, anges ett null-värde för den.
* Om du inte anger kolumnmappningen i kopieringsaktiviteten härleds schemat genom att använda den första raden i data. I det här fallet, om den första raden inte innehåller det fullständiga schemat (t.ex. vissa kolumner har null-värde), saknas vissa kolumner i resultatet av kopieringen.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Table-källan och diskhon.

### <a name="azure-table-as-a-source-type"></a>Azure-tabell som källtyp

Om du vill kopiera data från Azure Table anger du källtypen i kopieringsaktiviteten till **AzureTableSource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet.**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till **AzureTableSource**. |Ja |
| azureTableSourceQuery |Använd den anpassade tabelllagringsfrågan för att läsa data. Se exempel i följande avsnitt. |Inga |
| azureTableSourceIgnoreTableNotFound |Anger om det inte finns något undantag för tabellen.<br/>Tillåtna värden är **Sant** och **Falskt** (standard). |Inga |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery exempel

>[!NOTE]
>Azure Table-frågeåtgärden time out på 30 sekunder som [tillämpas av Azure Table Service](https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations). Lär dig hur du [optimerar](../storage/tables/table-storage-design-for-query.md) frågan från Design för att fråga artikel.

Om du vill filtrera data mot en kolumn för datetime-typ i Azure Data Factory läser du det här exemplet:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Om du vill filtrera data mot en strängtypskolumn läser du det här exemplet:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Om du använder pipelineparametern castar du datetime-värdet till korrekt format enligt föregående exempel.

### <a name="azure-table-as-a-sink-type"></a>Azure-tabell som en sink-typ

Om du vill kopiera data till Azure Table anger du sink-typen i kopieringsaktiviteten till **AzureTableSink**. Följande egenskaper stöds i avsnittet kopiera **aktivitetsmottagare.**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för copy activity sink måste anges till **AzureTableSink**. |Ja |
| azureTableDefaultPartitionKeyValue |Standardvärdet för partitionsnyckeln som kan användas av diskhon. |Inga |
| azureTablePartitionKeyName |Ange namnet på den kolumn vars värden används som partitionsnycklar. Om det inte anges används "AzureTableDefaultPartitionKeyValue" som partitionsnyckel. |Inga |
| azureTableRowKeyName |Ange namnet på den kolumn vars kolumnvärden används som radnyckel. Om inget anges använder du ett GUID för varje rad. |Inga |
| azureTableInsertType |Läget för att infoga data i Azure Table. Den här egenskapen styr om befintliga rader i utdatatabellen med matchande partitions- och radnycklar har sina värden ersatta eller sammanfogade. <br/><br/>Tillåtna värden **sammanfogas** (standard) och **ersätts**. <br/><br> Den här inställningen gäller på radnivå, inte tabellnivån. Inget av alternativen tar bort rader i utdatatabellen som inte finns i indata. Mer information om hur kopplings- och ersättningsinställningarna fungerar finns i [Infoga eller sammanfoga entitet](https://msdn.microsoft.com/library/azure/hh452241.aspx) och [Infoga eller ersätta entiteten](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Inga |
| skriverBatchSize |Infogar data i Azure Table när writeBatchSize eller writeBatchTimeout träffas.<br/>Tillåtna värden är heltal (antal rader). |Nej (standard är 10 000) |
| skriverBatchTimeout |Infogar data i Azure Table när writeBatchSize eller writeBatchTimeout träffas.<br/>Tillåtna värden är tidsspann. Ett exempel är "00:20:00" (20 minuter). |Nej (standard är 90 sekunder, lagringsklientens standardtidsutgång) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

Mappa en källkolumn till en målkolumn med egenskapen **"översättare"** innan du kan använda målkolumnen som azureTablePartitionKeyName.

I följande exempel mappas källkolumnen DivisionID till målkolumnen DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID" anges som partitionsnyckel.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mappning av datatyp för Azure-tabell

När du kopierar data från och till Azure Table används följande mappningar från Azure Table-datatyper till interimsdatatyper för Data Factory. Mer information om hur kopieringsaktiviteten mappar källschemat och datatypen till diskhon finns i [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md).

När du flyttar data till och från Azure Table används följande [mappningar som definieras av Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) OData-typer till .NET-typ och vice versa.

| Datatyp för Azure Tabell | Data Factory interimdatatyp | Information |
|:--- |:--- |:--- |
| Edm.Binary (Edm.Binary) |byte[] |En matris med byte upp till 64 kB. |
| Edm.Boolean |bool |Ett booleskt värde. |
| Edm.DateTime |DateTime |Ett 64-bitarsvärde uttryckt som Coordinated Universal Time (UTC). DateTime-intervallet som stöds börjar midnatt den 1 januari 1601 e.Kr. (C.E.), UTC. Intervallet slutar 31 december 9999. |
| Edm.Double |double |Ett 64-bitars flyttalsvärde. |
| Edm.Guid (edm.guid) |GUID |En 128-bitars globalt unik identifierare. |
| Edm.Int32 |Int32 |Ett 32-bitars heltal. |
| Edm.Int64 |Int64 |Ett 64-bitars heltal. |
| Edm.String |Sträng |Ett UTF-16-kodat värde. Strängvärden kan vara upp till 64 kB. |

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Data Factory finns i [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
