---
title: Kopiera data från Amazon Redshift med hjälp av Azure Data Factory | Microsoft Docs
description: Läs mer om hur du kopierar data från Amazon Redshift till stöds sink datalager med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 5787f944211c5dd6c0c83e7dc10170d8a0cd9a23
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34616618"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Kopiera data från Amazon Redshift med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-amazon-redshift-connector.md)
> * [Version 2 – förhandsversion](connector-amazon-redshift.md)


Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från en Amazon Redshift. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Amazon Redshift connnector i V1](v1/data-factory-amazon-redshift-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Amazon Redshift till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Den här Amazon Redshift anslutningen har stöd hämtar data från Redshift med hjälp av frågan eller inbyggt stöd för Redshift bort från MINNET.

> [!TIP]
> Om du vill uppnå bästa prestanda vid kopiering av stora mängder data från Redshift, Överväg att använda den inbyggda Redshift INAKTIVERAS via Amazon S3. Se [Använd INAKTIVERAS för att kopiera data från Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) information.

## <a name="prerequisites"></a>Förutsättningar

* Om du kopierar data till en lokal data lagra med [Self-hosted integrering Runtime](create-self-hosted-integration-runtime.md), ge åtkomst till Amazon Redshift kluster för integrering Runtime (Använd IP-adress för datorn). Se [auktorisera åtkomst till klustret](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) anvisningar.
* Om du kopierar data till ett Azure datalager, se [Azure Data Center IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653) för Compute IP-adressen och SQL-adressintervall som används av Azure data datacenter.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Amazon Redshift connector.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Amazon Redshift länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **AmazonRedshift** | Ja |
| server |IP-adressen eller värdnamnet namnet på Amazon Redshift-servern. |Ja |
| port |Antalet TCP-porten som Amazon Redshift-servern använder för att lyssna efter anslutningar. |Nej, standard är 5439 |
| databas |Namnet på Amazon Redshift-databasen. |Ja |
| användarnamn |Namnet på användaren som har åtkomst till databasen. |Ja |
| lösenord |Lösenordet för användarkontot. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Amazon Redshift dataset.

Ange typegenskapen för dataset för att kopiera data från Amazon Redshift, **RelationalTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **RelationalTable** | Ja |
| tableName | Namnet på tabellen i Amazon Redshift. | Nej (om ”fråga” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Amazon Redshift källa.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift som källa

Om du vill kopiera data från Amazon Redshift, anger du källa i kopieringsaktiviteten till **AmazonRedshiftSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **AmazonRedshiftSource** | Ja |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: Välj * från mytable prefix. |Nej (om ”tabellnamn” i datamängden har angetts) |
| redshiftUnloadSettings | Egenskapsgrupp när du använder Amazon Redshift bort från MINNET. | Nej |
| s3LinkedServiceName | Refererar till en Amazon S3 till-ska används som en mellanliggande arkivet genom att ange ett namn för länkade tjänsten av typen ”AmazonS3”. | Ja om du använder INAKTIVERAS |
| bucketName | Ange S3-bucket för lagring av tillfälliga data. Om inte genererar Data Factory-tjänsten den automatiskt.  | Ja om du använder INAKTIVERAS |

**Exempel: Amazon Redshift källa i kopieringsaktiviteten med bort från MINNET**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

Mer information om hur du använder INAKTIVERAS för att kopiera data från Amazon Redshift effektivt från nästa avsnitt.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Använd ta bort för att kopiera data från Amazon Redshift

[Ta bort](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) är en mekanism som tillhandahålls av Amazon Redshift, vilket kan ta bort resultatet av en fråga till en eller flera filer på Amazon enkla Storage-tjänst (Amazon S3). Det är på sätt som rekommenderas av Amazon för kopiering av stora datamängder från Redshift.

**Exempel: kopiera data från Amazon Redshift till Azure SQL Data Warehouse med hjälp av-borttagning mellanlagrade kopia och PolyBase**

För det här exemplet användningsfall, aktiviteten tar bort data från Amazon Redshift för Amazon S3 som konfigurerats i ”redshiftUnloadSettings” och sedan kopieringsdata från Amazon S3 till Azure Blob som anges i ”stagingSettings” slutligen använda PolyBase för att läsa in data till SQL-Data Datalager. Alla mellanliggande formatet hanteras av kopieringsaktiviteten korrekt.

![Redshift till SQL DW kopiera arbetsflöde](media\copy-data-from-amazon-redshift\redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Datatypsmappningen för Amazon Redshift

När du kopierar data från Amazon Redshift, används följande mappningar från Amazon Redshift datatyper för Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| Amazon Redshift datatyp | Data factory tillfälliga datatyp |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLESKT VÄRDE |Sträng |
| CHAR |Sträng |
| DATE |DateTime |
| DECIMAL |Decimal |
| DUBBEL PRECISION |dubbla |
| HELTAL |Int32 |
| VERKLIG |Enkel |
| SMALLINT |Int16 |
| TEXT |Sträng |
| TIDSSTÄMPEL |DateTime |
| VARCHAR |Sträng |

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).
