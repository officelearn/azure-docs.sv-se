---
title: Kopiera data från Amazon Redshift
description: Lär dig mer om hur du kopierar data från Amazon Redshift till sink-datalager som stöds med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: ce63da745fb84ebccd57b246fc934f595dd7cda1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418260"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Kopiera data från Amazon Redshift med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-amazon-redshift-connector.md)
> * [Aktuell version](connector-amazon-redshift.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från en Amazon Redshift. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Denna Amazon Redshift-kontakt stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Amazon Redshift till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Specifikt stöder den här Amazon Redshift-kontakten att hämta data från Redshift med hjälp av fråga eller inbyggt Support för Redshift UNLOAD.

> [!TIP]
> För att uppnå bästa prestanda när du kopierar stora mängder data från Redshift, överväg att använda den inbyggda Redshift LOSSa via Amazon S3. Se [Använd TA BORT för att kopiera data från Avsnittet Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) för mer information.

## <a name="prerequisites"></a>Krav

* Om du kopierar data till ett lokalt datalager med [självvärderade integrationskörning](create-self-hosted-integration-runtime.md), ger du integrationskörningstid (använd IP-adressen för datorn) åtkomsten till Amazon Redshift-klustret. Se [Auktorisera åtkomst till klustret](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) för instruktioner.
* Om du kopierar data till ett Azure-datalager läser du [Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653) för beräknings-IP-adressen och SQL-intervall som används av Azure-datacenter.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Amazon Redshift-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Amazon Redshift-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste ställas in på: **AmazonRedshift** | Ja |
| server |IP-adress eller värdnamn för Amazon Redshift-servern. |Ja |
| port |Numret på TCP-porten som Amazon Redshift-servern använder för att lyssna efter klientanslutningar. |Nej, standard är 5439 |
| databas |Namnet på Amazon Redshift-databasen. |Ja |
| användarnamn |Namn på den användare som har åtkomst till databasen. |Ja |
| password |Lösenord för användarkontot. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted Integration Runtime (om ditt datalager finns i privat nätverk). Om det inte anges används standardkörningen för Azure Integration. |Inga |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Amazon Redshift-datauppsättningen.

Så här kopierar du data från Amazon Redshift och följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen för datauppsättningen måste ställas in på: **AmazonRedshiftTable** | Ja |
| Schemat | Namnet på schemat. |Nej (om "fråga" i aktivitetskällan har angetts)  |
| tabell | Tabellens namn. |Nej (om "fråga" i aktivitetskällan har angetts)  |
| tableName | Namn på tabellen med schema. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` `table` och för ny arbetsbelastning. | Nej (om "fråga" i aktivitetskällan har angetts) |

**Exempel**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "AmazonRedshiftTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Om du `RelationalTable` använde maskinskriven datauppsättning stöds den fortfarande som den är, medan du föreslås använda den nya framöver.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Amazon Redshift-källa.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift som källa

Om du vill kopiera data från Amazon Redshift ställer du in källtypen i kopieringsaktiviteten på **AmazonRedshiftSource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen för kopieringsaktivitetskällan måste ställas in på: **AmazonRedshiftSource** | Ja |
| DocumentDB |Använd den anpassade frågan för att läsa data. Till exempel: välj * från MyTable. |Nej (om "tableName" i datauppsättningen har angetts) |
| redshiftUnloadSettings | Egenskapsgrupp när du använder Amazon Redshift UNLOAD. | Inga |
| s3LinkedServiceName | Refererar till en Amazon S3 som ska användas som en tillfällig butik genom att ange ett länkat tjänstnamn av typen "AmazonS3". | Ja om du använder LOSSNING |
| bucketName (bucketName) | Ange S3-bucketen för att lagra interimsdata. Om den inte tillhandahålls genererar Data Factory-tjänsten den automatiskt.  | Ja om du använder LOSSNING |

**Exempel: Amazon Redshift källa i kopia aktivitet med hjälp av LOSS**

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

Läs mer om hur du använder OLADDA för att kopiera data från Amazon Redshift effektivt från nästa avsnitt.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Använd TA BORT för att kopiera data från Amazon Redshift

[LOSSA](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) är en mekanism som tillhandahålls av Amazon Redshift, som kan lossa resultaten av en fråga till en eller flera filer på Amazon Simple Storage Service (Amazon S3). Det är det sätt som rekommenderas av Amazon för att kopiera stora data som från Redshift.

**Exempel: kopiera data från Amazon Redshift till Azure SQL Data Warehouse med hjälp av LOSSNING, stegvis kopia och PolyBase**

För det här exempelet är att kopiera aktivitet tar bort data från Amazon Redshift till Amazon S3 som konfigurerats i "redshiftUnloadSettings" och kopierar sedan data från Amazon S3 till Azure Blob enligt vad som anges i "stagingSettings", slutligen använda PolyBase för att läsa in data i SQL Data Warehouse. Alla interimsformat hanteras av kopieringsaktiviteten korrekt.

![Redshift till SQL DW-kopieringsarbetsflöde](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="data-type-mapping-for-amazon-redshift"></a>Datatyp kartläggning för Amazon Redshift

När data kopieras från Amazon Redshift används följande mappningar från Amazon Redshift-datatyper till Azure Data Factory interimsdatatyper. Se [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md) om du vill veta mer om hur du kopierar aktivitetsschemat och datatypen till diskhon.

| Datatyp för Amazon Redshift | Data fabrik interim datatyp |
|:--- |:--- |
| BIGINT |Int64 |
| Boolean |Sträng |
| CHAR |Sträng |
| DATE |DateTime |
| DECIMAL |Decimal |
| DUBBEL PRECISION |Double |
| INTEGER |Int32 |
| REAL |Enkel |
| SMALLINT |Int16 (int16) |
| TEXT |Sträng |
| TIMESTAMP |DateTime |
| VARCHAR |Sträng |

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
