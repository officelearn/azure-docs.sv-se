---
title: Kopiera data till och från Azure Table storage med hjälp av Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från stöds butiker Azure Table Storage och Table storage till mottagarens datalager med Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: jingwang
ms.openlocfilehash: b1f4ad523f84616391d4121dbf7eaabb2dfde060
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54018627"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Kopiera data till och från Azure Table storage med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-table-connector.md)
> * [Aktuell version](connector-azure-table-storage.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data till och från Azure Table storage. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från alla dataarkiv Table Storage. Du kan också kopiera data från Table storage till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt delat den här Azure-tabell anslutningsappen stöder kopiera data med hjälp av nyckeln och tjänsten åtkomst signatur autentiseringar.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för tabellagring.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

### <a name="use-an-account-key"></a>Använd en kontonyckel

Du kan skapa en länkad Azure Storage-tjänst med hjälp av kontonyckeln. Det ger data factory global åtkomst till lagring. Följande egenskaper stöds.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **AzureTableStorage**. |Ja |
| connectionString | Ange information som behövs för att ansluta till lagringsutrymmet för connectionString-egenskapen. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om ditt datalager finns i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

>[!NOTE]
>Om du använde ”AzureStorage” typen länkad tjänst, stöds det fortfarande som – är att medan du är föreslagna länkad tjänsttyp som vi rekommenderar att du använder den här nya ”AzureTableStorage”.

**Exempel:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
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

### <a name="use-shared-access-signature-authentication"></a>Använda autentisering med signatur för delad åtkomst

Du kan också skapa en länkad lagringstjänst genom att använda en signatur för delad åtkomst. Data factory skapar med begränsade/Tidsbundna åtkomst till alla utvalda resurser i lagringen.

En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt storage-konto. Du kan använda den för att ge en klient begränsad behörighet till objekt i ditt storage-konto för en specifik tidsperiod och med en angiven uppsättning behörigheter. Du behöver inte dela åtkomstnycklarna för kontot. Signatur för delad åtkomst är en URI som omfattar all information som behövs för autentiserad åtkomst till en lagringsresurs i dess Frågeparametrar. Om du vill få åtkomst till lagringsresurser med signatur för delad åtkomst, behöver klienten bara använda signatur för delad åtkomst till lämplig konstruktor nebo metodu. Mer information om signaturer för delad åtkomst finns i [signaturer för delad åtkomst: Förstå modellen](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Data Factory stöder nu både **service signaturer för delad åtkomst** och **konto signaturer för delad åtkomst**. Mer information om dessa två typer och hur du skapar dem finns i [typer av signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). 

> [!TIP]
> Du kan köra följande PowerShell-kommandon för att generera en signatur för delad åtkomst av tjänsten för ditt lagringskonto. Ersätt platshållarna och bevilja behörigheten som krävs.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Om du vill använda autentisering med signatur för delad åtkomst, stöds följande egenskaper.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **AzureTableStorage**. |Ja |
| sasUri | Ange signaturen för delad åtkomst URI till lagringsresurser, t.ex blob, behållaren eller tabellen. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller den lokala Integration Runtime (om ditt datalager finns i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

>[!NOTE]
>Om du använde ”AzureStorage” typen länkad tjänst, stöds det fortfarande som – är att medan du är föreslagna länkad tjänsttyp som vi rekommenderar att du använder den här nya ”AzureTableStorage”.

**Exempel:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
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

När du skapar en signatur för delad åtkomst URI kan du överväga följande punkter:

- Ange lämpliga Läs/Skriv-behörigheter för objekt baserat på hur den länkade tjänsten (läsa, skriva, Läs/Skriv) används i din datafabrik.
- Ange **förfallotiden** på rätt sätt. Se till att åtkomsten till lagringsobjekt inte går ut inom den aktiva perioden för pipelinen.
- URI: N ska skapas på den högra tabellnivån utifrån behov.

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Table-datauppsättningen.

För att kopiera data till och från Azure Table, ange typegenskapen på datauppsättningen till **AzureTable**. Följande egenskaper stöds.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till **AzureTable**. |Ja |
| tableName |Namnet på tabellen i Table storage-databasinstansen som den länkade tjänsten refererar till. |Ja |

**Exempel:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schemat av Data Factory

För schemafria datalager som Azure Table härleder Data Factory scheman som på något av följande sätt:

* Om du anger strukturen för data med hjälp av den **struktur** -egenskapen i definitionen för datauppsättningen, Data Factory godkänner den här strukturen som schema. Om en rad inte innehåller ett värde för en kolumn kan anges i det här fallet ett null-värde för den.
* Om du inte anger strukturen för data med hjälp av den **struktur** -egenskapen i definitionen för datauppsättningen, Data Factory härleder scheman med hjälp av den första raden i data. I det här fallet om den första raden inte innehåller fullständig schemat, är vissa kolumner missat i resultatet för kopieringsåtgärden.

För schemafria datakällor, bästa praxis är att ange strukturen för data med hjälp av den **struktur** egenskapen.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Table-källa och mottagare.

### <a name="azure-table-as-a-source-type"></a>Azure-tabell som en typ av datakälla

För att kopiera data från Azure Table, ange typ av datakälla i kopieringsaktiviteten till **AzureTableSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till **AzureTableSource**. |Ja |
| azureTableSourceQuery |Använd anpassade Table storage fråga om för att läsa data. Se exemplen i följande avsnitt. |Nej |
| azureTableSourceIgnoreTableNotFound |Anger om du vill tillåta undantag i tabellen kan inte finns.<br/>Tillåtna värden är **SANT** och **FALSKT** (standard). |Nej |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery exempel

Om Azure Table-kolumnen är av typen datetime:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Om Azure Table-kolumnen är av strängtypen:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Om du använder parametern pipeline kan du omvandla datetime-värde till rätt format enligt föregående exempel.

### <a name="azure-table-as-a-sink-type"></a>Azure-tabell som en Mottagartyp

Om du vill kopiera data till Azure Table, ange Mottagartyp i kopieringsaktiviteten till **AzureTableSink**. Följande egenskaper stöds i kopieringsaktiviteten **mottagare** avsnittet.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till **AzureTableSink**. |Ja |
| azureTableDefaultPartitionKeyValue |Standard partitionsnyckelvärdet som kan användas av mottagaren. |Nej |
| azureTablePartitionKeyName |Ange namnet på kolumnen vars värden används som partitionsnycklar. Om den inte anges används ”AzureTableDefaultPartitionKeyValue” som partitionsnyckel. |Nej |
| azureTableRowKeyName |Ange namnet på kolumnen vars kolumnvärdena används som radnyckel. Om inte anges kan du använda ett GUID för varje rad. |Nej |
| azureTableInsertType |Läget för att infoga data i Azure Table. Den här egenskapen styr om befintliga rader i utdatatabellen med matchande partition och radnycklar har sina värden ersättas eller samman. <br/><br/>Tillåtna värden är **merge** (standard) och **Ersätt**. <br/><br> Den här inställningen gäller inte på tabellnivå på radnivå. Varken alternativet tar bort rader i utdatatabellen som inte finns i aktuella indata. Läs om hur inställningarna merge och Ersätt fungerar i [Insert- eller merge-entitet](https://msdn.microsoft.com/library/azure/hh452241.aspx) och [infoga eller ersätta entitet](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Nej |
| WriteBatchSize |Infogar data i Azure Table när writeBatchSize eller writeBatchTimeout uppnås.<br/>Tillåtna värden är heltal (antal rader). |Nej (standardvärdet är 10 000) |
| writeBatchTimeout |Infogar data i Azure Table när writeBatchSize eller writeBatchTimeout uppnås.<br/>Tillåtna värden är tidsintervallet. Ett exempel är ”00: 20:00” (20 minuter). |Nej (standardvärdet är 90 sekunder, standardvärde för storage-klienten) |

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

Mappa en källkolumn till en målkolumn med hjälp av den **”translator”** egenskapen innan du kan använda målkolumnen som azureTablePartitionKeyName.

I följande exempel mappas källkolumnen DivisionID till målkolumnen DivisionID:

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

När du kopierar data från och till Azure Table, används följande mappningar från Azure Table-datatyper till Data Factory tillfälliga-datatyper. Mer information om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren, se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md).

När du flyttar data till och från Azure Table, följande [mappningar som definieras av Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) som används från Azure Table OData-typer till .NET-typ och vice versa.

| Azure tabelldatatyp | Data Factory tillfälliga datatyp | Information |
|:--- |:--- |:--- |
| Edm.Binary |byte |En matris med byte upp till 64 KB. |
| Edm.Boolean |Bool |Ett booleskt värde. |
| Edm.DateTime |DateTime |Ett 64-bitars värde uttryckt som Coordinated Universal Time (UTC). Det tillåtna intervallet för DateTime börjar midnatt 1 januari, 1601 e. kr. (C.E.), UTC. Intervallet slutar den 31 December 9999. |
| Edm.Double |double |Ett 64-bitars flytande punktvärde. |
| Edm.Guid |GUID |En globalt unik identifierare för 128-bitars. |
| Edm.Int32 |Int32 |En 32-bitars heltal. |
| Edm.Int64 |Int64 |En 64-bitars heltal. |
| Edm.String |Sträng |Ett UTF-16-kodade värde. Strängvärden kan vara upp till 64 KB. |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
