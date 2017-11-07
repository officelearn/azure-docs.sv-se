---
title: "Kopiera data till/från Azure Table Storage med hjälp av Data Factory | Microsoft Docs"
description: "Lär dig att kopiera data från stöds källa lagrar till Azure Table Storage (eller) från tabellagring stöds sink butiker med hjälp av Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 69115945929f1dc93369ee000d78bceead8c109c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-to-or-from-azure-table-using-azure-data-factory"></a>Kopiera data till och från Azure-tabellen med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-azure-table-connector.md)
> * [Version 2 – förhandsversion](connector-azure-table-storage.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data till och från Azure Table. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Azure Table Storage connector i V1](v1/data-factory-azure-table-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från alla datalager stöds källa till Azure Table eller kopiera data från Azure Table till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt den här Azure Table-anslutningen har stöd för kopiering av data med hjälp av både **kontonyckel** och **Service SAS** autentiseringar (signatur för delad åtkomst).

## <a name="get-started"></a>Kom igång
Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Azure Table Storage.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

### <a name="using-account-key"></a>Med hjälp av kontonyckel

Du kan skapa en länkad Azure Storage-tjänst genom att använda för kontot som ger data factory med global åtkomst till Azure Storage. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **AzureStorage** |Ja |
| connectionString | Ange information som krävs för att ansluta till Azure-lagring för egenskapen connectionString. Markera det här fältet som SecureString. |Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

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
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-sas-authentication"></a>Med tjänsten SAS-autentisering

Du kan också skapa en länkad Azure Storage-tjänst genom att använda en delad signatur åtkomst (SAS), som ger data factory med begränsad/Tidsbundna åtkomst till alla utvalda resurser i lagringen.

En delad signatur åtkomst (SAS) ger delegerad åtkomst till resurser i ditt lagringskonto. Det gör att du kan ge en klient begränsade behörigheter till objekt i ditt lagringskonto för en angiven tidsperiod och med en angiven uppsättning behörigheter, utan att behöva dela åtkomstnycklarna för ditt konto. SAS är en URI som omfattar all information som krävs för autentiserad åtkomst till en lagringsresurs i dess Frågeparametrar. För att komma åt lagringsresurser med SAS måste klienten endast att skicka in SAS till lämplig konstruktor eller metod. Detaljerad information om SAS finns [signaturer för delad åtkomst: Förstå SAS-modellen](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Azure Data Factory nu har bara stöd för **Service SAS** men inte kontots SAS. Se [typer av signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) mer information om dessa två typer och hur du skapar. SAS-URL generable från Azure-portalen eller Lagringsutforskaren är ett konto-SAS, vilket inte stöds.
>

Om du vill använda tjänsten SAS-autentisering stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **AzureStorage** |Ja |
| sasUri | Ange URI för delad åtkomst-signaturen i Azure Storage-resurser, till exempel blob, behållare eller tabellen. Markera det här fältet som SecureString. |Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

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

När du skapar en **SAS URI**, med tanke på följande:

- Ange lämpliga läsning och skrivning **behörigheter** på objekt baserat på hur den länkade tjänsten (läsa, skriva, läsning och skrivning) används i din data factory.
- Ange **förfallotiden** på lämpligt sätt. Kontrollera åtkomst till Azure Storage objekt inte upphör inom den aktiva perioden för pipelinen.
- URI: N ska skapas på den högra tabellnivån utifrån behov.

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Table dataset.

Ange typegenskapen för dataset för att kopiera data till/från Azure Table, **AzureTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **AzureTable** |Ja |
| tableName |Namnet på tabellen i Azure Table databasinstansen som den länkade tjänsten refererar till. |Ja |

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

För schemafria data butiker, till exempel Azure Table härleder Data Factory-tjänsten schemat på något av följande sätt:

1. Om du anger strukturen för data med hjälp av den **struktur** egenskap i datauppsättningsdefinitionen Data Factory-tjänsten godkänner den här strukturen som schema. I det här fallet ges en rad inte innehåller ett värde för en kolumn, ett null-värde för den.
2. Om du inte anger strukturen för data med hjälp av den **struktur** egenskap i datauppsättningsdefinitionen Data Factory härleder schemat med hjälp av den första raden i data. Om den första raden inte innehåller fullständig schemat missas i det här fallet vissa kolumner i resultatet av kopieringsåtgärden.

Därför för schemafria datakällor, det bästa sättet är att ange hur dina data med hjälp av den **struktur** egenskapen.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Table källa och mottagare.

### <a name="azure-table-as-source"></a>Azure-tabellen som källa

Om du vill kopiera data från Azure Table, anger du källa i kopieringsaktiviteten till **AzureTableSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **AzureTableSource** |Ja |
| azureTableSourceQuery |Använd anpassade Azure table-fråga för att läsa data. Se exemplen i nästa avsnitt. |Nej |
| azureTableSourceIgnoreTableNotFound |Ange om swallow undantag av tabellen inte finns.<br/>Tillåtna värden är: **SANT**, och **FALSKT** (standard). |Nej |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery-exempel

Om Azure Table kolumnen är av strängtypen:

```json
"azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', <datetime parameter>)"
```

Om Azure Table kolumnen är av typen datetime:

```json
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', <datetime parameter>, <datetime parameter>)"
```

### <a name="azure-table-as-sink"></a>Azure-tabellen som mottagare

Om du vill kopiera data från Azure Table, anger du källa i kopieringsaktiviteten till **AzureTableSink**. Följande egenskaper stöds i kopieringsaktiviteten **sink** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **AzureTableSink** |Ja |
| azureTableDefaultPartitionKeyValue |Standard partitionsnyckelvärde som kan användas av sink. |Nej |
| azureTablePartitionKeyName |Ange namnet på den kolumn som används som partitionsnycklar. Om inget anges används ”AzureTableDefaultPartitionKeyValue” som partitionsnyckel. |Nej |
| azureTableRowKeyName |Ange namnet på den kolumn vars kolumnvärdena används som radnyckel. Om inget annat anges, kan du använda ett GUID för varje rad. |Nej |
| azureTableInsertType |Läget att infoga data i Azure-tabellen. Den här egenskapen anger om befintliga rader i utdatatabellen med matchande partition och radnycklar få sina värden bytas ut eller samman. <br/><br/>Tillåtna värden är: **merge** (standard), och **ersätta**. <br/><br> Den här inställningen gäller på radnivå inte tabellnivån, varken alternativet tar bort rader i utdatatabellen som inte finns i indata. Läs om hur dessa inställningar (dokument och Ersätt) fungerar i [Insert- eller Merge-entiteten](https://msdn.microsoft.com/library/azure/hh452241.aspx) och [infoga eller ersätta entiteten](https://msdn.microsoft.com/library/azure/hh452242.aspx) avsnitt. |Nej |
| writeBatchSize |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout namn.<br/>Tillåtna värden är: heltal (antalet rader) |Nej (standard är 10000) |
| writeBatchTimeout |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout namn.<br/>Tillåtna värden är: timespan. Exempel ”: 00: 20:00” (20 minuter) |Nej (standardvärdet är 90 sek - lagring klientens standardtimeout) |

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

Mappa en källkolumn till en målkolumn med egenskapen ”översättare” innan du kan använda målkolumnen som azureTablePartitionKeyName.

I följande exempel källkolumnen DivisionID mappas till målkolumnen DivisionID.

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

När du kopierar data från/till Azure Table, används följande mappningar från Azure Table-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

När du flyttar data till och från Azure Table följande [mappningar som definierats av Azure Table-tjänsten](https://msdn.microsoft.com/library/azure/dd179338.aspx) som används från Azure Table OData-typer till .NET-typ och vice versa.

| Azure Table-datatyp | Data factory tillfälliga datatyp | Information |
|:--- |:--- |:--- |
| Edm.Binary |byte] |En matris med byte upp till 64 KB. |
| Edm.Boolean |bool |Ett booleskt värde. |
| Edm.DateTime |Datum och tid |En 64-bitars värdet uttrycks som Coordinated Universal Time (UTC). Det intervall som stöds för den DateTime som börjar från midnatt, 1 januari, 1601 e. kr. (C.E.) UTC. Intervallet slutar vid den 31 December 9999. |
| Edm.Double |dubbla |En 64-bitars flytande punktvärdet. |
| Edm.Guid |GUID |En 128-bitars globalt unik identifierare. |
| Edm.Int32 |Int32 |En 32-bitars heltal. |
| Edm.Int64 |Int64 |En 64-bitars heltal. |
| Edm.String |Sträng |Ett värde för UTF-16-kodad. Strängvärden kan vara upp till 64 KB. |

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).