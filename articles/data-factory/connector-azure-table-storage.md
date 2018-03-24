---
title: Kopiera data till och från Azure Table storage med hjälp av Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från stöds källa lagrar till Azure Table storage, eller från tabellagring stöds sink butiker med hjälp av Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: ef43037ff33b693256c82459eec2e4b3beab4d9a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Kopiera data till och från Azure Table storage med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – Allmänt tillgänglig](v1/data-factory-azure-table-connector.md)
> * [Version 2 – förhandsversion](connector-azure-table-storage.md)

Den här artikeln beskrivs hur du använder Kopieringsaktiviteten i Azure Data Factory för att kopiera data till och från Azure Table storage. Den bygger på den [Kopieringsaktiviteten översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över Kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory som är allmänt tillgänglig, se [Table storage connector i version 1](v1/data-factory-azure-table-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från alla datalager stöds källa till Table storage. Du kan också kopiera data från tabellagring till alla stöds sink-datalagret. En lista över datalager som stöds som datakällor eller sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt delat den här Azure-tabellen anslutningen stöder kopierar data med hjälp av kontonyckel och tjänsten åtkomst signatur autentiseringar.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Table storage.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

### <a name="use-an-account-key"></a>Använd en kontonyckel

Du kan skapa en länkad Azure Storage-tjänst genom att använda för kontot. Det ger datafabriken global åtkomst till lagring. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till **AzureStorage**. |Ja |
| connectionString | Ange information som behövs för att ansluta till lagring för egenskapen connectionString. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integrering runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i ett privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-service-shared-access-signature-authentication"></a>Använda tjänsten autentisering med signatur för delad åtkomst

Du kan också skapa en länkad Storage-tjänst genom att använda en signatur för delad åtkomst. Det ger datafabriken begränsad/Tidsbundna åtkomst till alla utvalda resurser i lagringen.

En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagringskonto. Du kan använda den för att ge en klient begränsade behörigheter till objekt i ditt lagringskonto för en viss tid och på en viss uppsättning behörigheter. Du behöver inte dela åtkomstnycklarna för ditt konto. Signatur för delad åtkomst är en URI som omfattar all information som krävs för autentiserad åtkomst till en lagringsresurs i dess Frågeparametrar. För att komma åt lagringsresurser med signatur för delad åtkomst, måste klienten endast ska skickas i signatur för delad åtkomst till lämplig konstruktor eller metod. Mer information om signaturer för delad åtkomst finns [signaturer för delad åtkomst: Förstå delad åtkomst signatur modellen](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!IMPORTANT]
> Data Factory stöder nu endast service delade åtkomstsignaturer men inte kontot delade åtkomstsignaturer. Mer information om dessa två typer och hur du skapar dem finns [typer av signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). Delad åtkomst signatur-URL som genereras från Azure-portalen eller Azure Lagringsutforskaren är ett konto signatur för delad åtkomst, vilket inte stöds.

> [!TIP]
> Du kan köra följande PowerShell-kommandon för att generera en signatur för delad åtkomst av tjänsten för ditt lagringskonto. Ersätt platshållarna och bevilja behörighet som krävs.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Om du vill använda autentisering med signatur för delad åtkomst service stöds följande egenskaper.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till **AzureStorage**. |Ja |
| sasUri | Ange signaturen för delad åtkomst URI till lagringsresurser, till exempel blob, behållare eller tabellen. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integrering runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i ett privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

När du skapar en signatur för delad åtkomst URI, bör följande saker övervägas:

- Ange lämpliga Läs-/ skrivbehörigheter för objekt baserat på hur den länkade tjänsten (läsa, skriva, läsning och skrivning) används i din data factory.
- Ange **förfallotiden** på lämpligt sätt. Kontrollera att åtkomsten till lagringsobjekt inte går ut inom den aktiva perioden för pipelinen.
- URI: N ska skapas på den högra tabellnivån utifrån behov.

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Table-datauppsättningen.

Ange typegenskapen för dataset för att kopiera data till och från Azure Table, **AzureTable**. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till **AzureTable**. |Ja |
| tableName |Namnet på tabellen i Table storage-databasinstansen som den länkade tjänsten refererar till. |Ja |

**Exempel:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schema som Data Factory

För schemafria data butiker, till exempel Azure Table härleder Data Factory schemat på något av följande sätt:

* Om du anger strukturen för data med hjälp av den **struktur** egenskap i datauppsättningsdefinitionen Data Factory godkänner den här strukturen som schema. Om en rad inte innehåller ett värde för en kolumn kan anges i det här fallet ett null-värde för den.
* Om du inte anger strukturen för data med hjälp av den **struktur** egenskap i datauppsättningsdefinitionen Data Factory härleder schemat med hjälp av den första raden i data. Om den första raden inte innehåller fullständig schemat kan missas i det här fallet vissa kolumner i resultatet av kopieringen.

För schemafria datakällor, det bästa sättet är att ange strukturen för data med hjälp av den **struktur** egenskapen.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Table källa och mottagare.

### <a name="azure-table-as-a-source-type"></a>Azure-tabellen som en typ av datakälla

Om du vill kopiera data från Azure Table, anger du källa i kopieringsaktiviteten till **AzureTableSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till **AzureTableSource**. |Ja |
| azureTableSourceQuery |Använd anpassade Table storage fråga för att läsa data. Se exemplen i följande avsnitt. |Nej |
| azureTableSourceIgnoreTableNotFound |Anger om du vill tillåta undantag på tabellen som inte finns.<br/>Tillåtna värden är **SANT** och **FALSKT** (standard). |Nej |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery-exempel

Om Azure Table-kolumnen är av typen datetime:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Om Azure Table-kolumnen är av typen:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Om du använder parametern pipeline omvandla datetime-värdet till rätt format enligt tidigare exemplen.

### <a name="azure-table-as-a-sink-type"></a>Azure-tabellen som en Mottagartypen

Om du vill kopiera data till Azure Table, anger du sink i kopieringsaktiviteten till **AzureTableSink**. Följande egenskaper stöds i kopieringsaktiviteten **sink** avsnitt.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för kopiera aktivitet sink måste anges till **AzureTableSink**. |Ja |
| azureTableDefaultPartitionKeyValue |Standard partition nyckelvärdet som kan användas av sink. |Nej |
| azureTablePartitionKeyName |Ange namnet på kolumnen som har värden som används som partitionsnycklar. Om inget anges används ”AzureTableDefaultPartitionKeyValue” som partitionsnyckel. |Nej |
| azureTableRowKeyName |Ange namnet på kolumnen vars kolumnvärdena används som Radnyckeln. Om inget annat anges, kan du använda ett GUID för varje rad. |Nej |
| azureTableInsertType |Läget att infoga data i Azure Table. Den här egenskapen anger om befintliga rader i utdatatabellen med matchande partition och radnycklar få sina värden bytas ut eller samman. <br/><br/>Tillåtna värden är **merge** (standard) och **ersätta**. <br/><br> Den här inställningen gäller på nivån raden inte tabellnivå. Varken alternativet tar bort rader i utdatatabellen som inte finns i indata. Läs om hur dokument och Ersätt inställningarna fungerar i [Insert- eller merge entiteten](https://msdn.microsoft.com/library/azure/hh452241.aspx) och [infoga eller ersätta entiteten](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Nej |
| writeBatchSize |Infogar data i Azure Table när writeBatchSize eller writeBatchTimeout namn.<br/>Tillåtna värden är heltal (antalet rader). |Nej (standardvärdet är 10 000-tal) |
| writeBatchTimeout |Infogar data i Azure Table när writeBatchSize eller writeBatchTimeout namn.<br/>Tillåtna värden är timespan. Ett exempel är ”00: 20:00” (20 minuter). |Nej (standardvärdet är 90 sekunder, lagring klientens standardtimeout) |

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

Mappa en källkolumn till en målkolumn med den **”översättare”** egenskapen innan du kan använda målkolumnen som azureTablePartitionKeyName.

I följande exempel källkolumnen DivisionID mappas till målkolumnen DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

”DivisionID” har angetts som partitionsnyckel.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Datatypsmappningen för Azure Table

När du kopierar data från och till Azure Table, används följande mappningar från Azure Table-datatyper till mellanliggande Data Factory-datatyper. Läs om hur kopieringsaktiviteten mappar källtypen schema och data till sink i [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md).

När du flyttar data till och från Azure Table följande [mappningar som definieras av Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) som används från Azure Table OData-typer till .NET-typ och vice versa.

| Azure Table-datatyp | Data Factory tillfälliga datatyp | Information |
|:--- |:--- |:--- |
| Edm.Binary |byte |En matris med byte upp till 64 KB. |
| Edm.Boolean |bool |Ett booleskt värde. |
| Edm.DateTime |DateTime |En 64-bitars värdet uttrycks som Coordinated Universal Time (UTC). Det tillåtna intervallet för DateTime börjar midnatt 1 januari, 1601 e. kr. (C.E.) UTC. Intervallet slutar den 31 December 9999. |
| Edm.Double |dubbla |En 64-bitars flytande punktvärdet. |
| Edm.Guid |GUID |En 128-bitars globalt unik identifierare. |
| Edm.Int32 |Int32 |En 32-bitars heltal. |
| Edm.Int64 |Int64 |En 64-bitars heltal. |
| Edm.String |Sträng |Ett värde för UTF-16-kodad. Strängvärden kan vara upp till 64 KB. |

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).
