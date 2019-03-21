---
title: Kopiera data från Amazon Redshift med Azure Data Factory | Microsoft Docs
description: Läs mer om hur du kopierar data från Amazon Redshift till datalager för mottagare som stöds med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 9e1dde57dc1903e87704bd55fb0b942b7cc349e5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010584"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Kopiera data från Amazon Redshift med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-amazon-redshift-connector.md)
> * [Aktuell version](connector-amazon-redshift.md)


Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en Amazon Redshift. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Amazon Redshift till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Amazon Redshift-anslutningsappen data hämtas från Redshift med hjälp av frågan eller inbyggt stöd för Redshift-AVLASTNING.

> [!TIP]
> För att uppnå bästa möjliga prestanda vid kopiering av stora mängder data från Redshift, Överväg att använda den inbyggda Redshift-AVLASTNING via Amazon S3. Se [Använd INAKTIVERAS för att kopiera data från Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) information.

## <a name="prerequisites"></a>Förutsättningar

* Om du kopierar data till en lokal data datalager med [lokal Integration Runtime](create-self-hosted-integration-runtime.md), ge åtkomst till Amazon Redshift-klustret för Integration Runtime (Använd IP-adressen för datorn). Se [auktorisera åtkomst till klustret](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) anvisningar.
* Om du kopierar data till ett datalager som Azure, se [IP-intervall för Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653) för Compute IP-adressen och SQL-adressintervall som används av Azure data datacenter.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Amazon Redshift-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Amazon Redshift länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **AmazonRedshift** | Ja |
| server |IP-adressen eller värdnamnet namnet på Amazon Redshift-servern. |Ja |
| port |Antalet TCP-porten som Amazon Redshift-servern använder för att lyssna efter klientanslutningar. |Nej, standard är 5439 |
| databas |Namnet på Amazon Redshift-databas. |Ja |
| användarnamn |Namnet på användaren som har åtkomst till databasen. |Ja |
| lösenord |Lösenordet för användarkontot. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om ditt datalager finns i privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Amazon Redshift-datamängd.

För att kopiera data från Amazon Redshift, ange typegenskapen på datauppsättningen till **RelationalTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **RelationalTable** | Ja |
| tableName | Namnet på tabellen i Amazon Redshift. | Nej (om ”frågan” i aktivitetskälla har angetts) |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Amazon Redshift-källan.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift som källa

För att kopiera data från Amazon Redshift, ange typ av datakälla i kopieringsaktiviteten till **AmazonRedshiftSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **AmazonRedshiftSource** | Ja |
| DocumentDB |Använd anpassad fråga för att läsa data. Till exempel: Välj * från MyTable. |Nej (om ”tableName” i datauppsättningen har angetts) |
| redshiftUnloadSettings | Egenskapsgruppen när du använder Amazon Redshift-AVLASTNING. | Nej |
| s3LinkedServiceName | Refererar till en Amazon S3 att-ska använda som en tillfällig lagring genom att ange ett namn för den länkade tjänsten av typen ”AmazonS3”. | Ja om du använder FRÅNKOPPLING |
| bucketName | Ange S3-bucket för att lagra tillfälliga data. Om du inte genererar Data Factory-tjänsten den automatiskt.  | Ja om du använder FRÅNKOPPLING |

**Exempel: Amazon Redshift-källan i en Kopieringsaktivitet med hjälp av bort från MINNET**

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

Läs mer om hur du använder INAKTIVERAS för att kopiera data från Amazon Redshift effektivt från nästa avsnitt.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Använd ta bort för att kopiera data från Amazon Redshift

[Ta bort](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) är en mekanism som tillhandahålls av Amazon Redshift, vilket kan ta bort resultatet av en fråga till en eller flera filer på Amazon Simple Storage Service (Amazon S3). Det är det sätt som rekommenderas av Amazon för att kopiera stora datamängder från Redshift.

**Exempel: kopiera data från Amazon Redshift till Azure SQL Data Warehouse med hjälp av-borttagning mellanlagrad kopiering och PolyBase**

För det här exemplet användningsfall, kopiera aktivitet tar bort data från Amazon Redshift till Amazon S3 som konfigurerats i ”redshiftUnloadSettings” och kopieringsdata från Amazon S3 till Azure Blob som anges i ”stagingSettings” slutligen använda PolyBase för att läsa in data i SQL-Data Datalager. Alla mellanliggande format hanteras korrekt av Kopieringsaktivitet.

![Redshift till SQL DW kopiera arbetsflöde](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

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
            "dataIntegrationUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Datatypsmappningen för Amazon Redshift

När du kopierar data från Amazon Redshift, används följande mappningar från Amazon Redshift-datatyper till Azure Data Factory tillfälliga-datatyper. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| Amazon Redshift-datatypen | Data factory tillfälliga datatyp |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLESKT VÄRDE |String |
| CHAR |String |
| DATE |DateTime |
| DECIMAL |Decimal |
| DUBBEL PRECISION |Double |
| HELTAL |Int32 |
| VERKLIGA |Single |
| SMALLINT |Int16 |
| TEXT |String |
| TIDSSTÄMPEL |DateTime |
| VARCHAR |String |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
