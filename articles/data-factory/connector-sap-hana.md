---
title: Kopiera data från SAP HANA
description: Lär dig hur du kopierar data från SAP HANA till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: 74462b68bea38e4d84219adeedb7c3bb0893bbb4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417227"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopiera data från SAP HANA med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-sap-hana-connector.md)
> * [Aktuell version](connector-sap-hana.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från en SAP HANA-databas. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

>[!TIP]
>Mer information om ADF:s övergripande support i SCENARIOt för SAP-dataintegrering finns i [SAP-dataintegration med hjälp av Azure Data Factory-faktablad](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) med detaljerad introduktion, jämförelse och vägledning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SAP HANA-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från SAP HANA-databasen till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här SAP HANA-anslutningen stöder:

- Kopiera data från valfri version av SAP HANA-databasen.
- Kopiera data från **hana-informationsmodeller** (t.ex. analytiska vyer och beräkningsvyer) och **rad-/kolumntabeller**.
- Kopiera data med **grundläggande** autentisering eller Windows-autentisering. **Windows**
- Parallellkopiering från en SAP HANA-källa. Mer information finns i avsnittet [Parallellkopia från SAP HANA.](#parallel-copy-from-sap-hana)

> [!TIP]
> Om du vill kopiera data **till** SAP HANA-datalager använder du den allmänna ODBC-anslutningen. Se [SAP HANA-diskbänk](connector-odbc.md#sap-hana-sink) med detaljer. Observera att de länkade tjänsterna för SAP HANA-anslutning och ODBC-anslutning är med olika typ och kan därför inte återanvändas.

## <a name="prerequisites"></a>Krav

Om du vill använda den här SAP HANA-anslutningen måste du:

- Konfigurera en självvärderad integrationskörning. Mer information finns i artikeln Integration Runtime för [självvärd.](create-self-hosted-integration-runtime.md)
- Installera SAP HANA ODBC-drivrutinen på integrationskörningsdatorn. Du kan hämta SAP HANA ODBC-drivrutinen från [SAP Software Download Center](https://support.sap.com/swdc). Sök med nyckelordet **SAP HANA CLIENT for Windows**.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för SAP HANA-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för SAP HANA-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste ställas in på: **SapHana** | Ja |
| Connectionstring | Ange information som behövs för att ansluta till SAP HANA med hjälp av **antingen grundläggande autentisering** eller **Windows-autentisering**. Se följande exempel.<br>I anslutningssträngen är server/port obligatorisk (standardporten är 30015) och användarnamn och lösenord är obligatoriska när du använder grundläggande autentisering. Ytterligare avancerade inställningar finns i [SAP HANA ODBC Connection Properties](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>Du kan också placera lösenord i Azure Key Vault och hämta lösenordskonfigurationen ur anslutningssträngen. Mer information finns [i Store-autentiseringsuppgifterna i](store-credentials-in-key-vault.md) azure key vault-artikeln. | Ja |
| userName | Ange användarnamn när du använder Windows-autentisering. Exempel: `user@domain.com` | Inga |
| password | Ange lösenord för användarkontot. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. En självvärdad integrationskörning krävs som nämns i [Förutsättningar](#prerequisites). |Ja |

**Exempel: använd grundläggande autentisering**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel: använda Windows-autentisering**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

Om du använde SAP HANA-länkad tjänst med följande nyttolast stöds den fortfarande som den är, medan du föreslås använda den nya framöver.

**Exempel:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av SAP HANA-datauppsättningen.

Så här kopierar du data från SAP HANA stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste ställas in på: **SapHanaTable** | Ja |
| Schemat | Namnet på schemat i SAP HANA-databasen. | Nej (om "fråga" i aktivitetskällan har angetts) |
| tabell | Namn på tabellen i SAP HANA-databasen. | Nej (om "fråga" i aktivitetskällan har angetts) |

**Exempel:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Om du `RelationalTable` använde maskinskriven datauppsättning stöds den fortfarande som den är, medan du föreslås använda den nya framöver.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av SAP HANA-källa.

### <a name="sap-hana-as-source"></a>SAP HANA som källa

>[!TIP]
>Om du vill använda data från SAP HANA effektivt med hjälp av datapartitionering kan du läsa mer från [avsnittet Parallellkopia från SAP HANA.](#parallel-copy-from-sap-hana)

Om du vill kopiera data från SAP HANA stöds följande egenskaper i avsnittet kopiera **aktivitetskälla:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till: **SapHanaSource** | Ja |
| DocumentDB | Anger den SQL-fråga som ska läsas data från SAP HANA-instansen. | Ja |
| partitionOptions | Anger de datapartitionsalternativ som används för att använda data från SAP HANA. Läs mer från [Parallellkopia från SAP HANA-avsnittet.](#parallel-copy-from-sap-hana)<br>Tillåt värden är: **Inga** (standard), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. Läs mer från [Parallellkopia från SAP HANA-avsnittet.](#parallel-copy-from-sap-hana) `PhysicalPartitionsOfTable`kan bara användas när data kopieras från en tabell men inte fråga. <br>När ett partitionsalternativ är aktiverat (det vill ha), `None`styrs graden av parallellitet till samtidig inläsning av data från SAP HANA av [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) inställningen på kopieringsaktiviteten. | False |
| partitionSätta | Ange gruppen för inställningarna för datapartitionering.<br>Använd när partitionsalternativet är `SapHanaDynamicRange`. | False |
| partitionColumnName | Ange namnet på källkolumnen som ska användas av partitionen för parallellkopia. Om inget anges identifieras indexet eller primärnyckeln för tabellen automatiskt och används som partitionskolumn.<br>Använd när partitionsalternativet är `SapHanaDynamicRange`. Om du använder en fråga för `?AdfHanaDynamicRangePartitionCondition` att hämta källdata ansluter du WHERE-satsen. Se exempel i [Parallellkopia från AVSNITTET SAP HANA.](#parallel-copy-from-sap-hana) | Ja när `SapHanaDynamicRange` du använder partitionen. |
| packetSize | Anger nätverkspaketstorleken (i Kilobytes) för att dela upp data till flera block. Om du har stora mängder data att kopiera kan öka paketstorleken öka läshastigheten från SAP HANA i de flesta fall. Prestandatestning rekommenderas när paketstorleken justeras. | Nej.<br>Standardvärdet är 2048 (2 MB). |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Om du `RelationalSource` använde den maskinskrivna kopieringskällan stöds den fortfarande som den är, medan du föreslås använda den nya framåt.

## <a name="parallel-copy-from-sap-hana"></a>Parallell kopia från SAP HANA

Data Factory SAP HANA-anslutningen tillhandahåller inbyggd datapartitionering för att kopiera data från SAP HANA parallellt. Du hittar alternativ för datapartitionering i **källtabellen** för kopieringsaktiviteten.

![Skärmbild av partitionsalternativ](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

När du aktiverar partitionerad kopia kör Data Factory parallella frågor mot DIN SAP HANA-källa för att hämta data efter partitioner. Den parallella graden [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) styrs av inställningen på kopieringsaktiviteten. Om du till `parallelCopies` exempel anger fyra genererar och kör Data Factory samtidigt fyra frågor baserat på det angivna partitionsalternativet och inställningarna, och varje fråga hämtar en del data från DIN SAP HANA.

Du föreslås aktivera parallellkopiering med datapartitionering, särskilt när du intar stora mängder data från DIN SAP HANA. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till filbaserat datalager rekommenderas att du skriver till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

| Scenario                                           | Inställningar för förslag                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Full belastning från stort bord.                        | **Partition alternativ**: Fysiska partitioner av tabellen. <br><br/>Under körningen identifierar Data Factory automatiskt den fysiska partitionstypen för den angivna SAP HANA-tabellen och väljer motsvarande partitionsstrategi:<br>- **Områdespartitioning:** Hämta partitionskolumnen och partitionsområdena som definierats för tabellen och kopiera sedan data efter område. <br>- **Hash-partitionering:** Använd hash-partitionsnyckel som partitionskolumn och partitionera och kopiera data baserat på beräknade ADF-intervall. <br>- **Partitionering av round-robin** eller **ingen partition**: Använd primärnyckeln som partitionskolumn och partitionera och kopiera data baserat på beräknade ADF-intervall. |
| Läs in stora mängder data med hjälp av en anpassad fråga. | **Partition alternativ:** Dynamiskt omfång partition.<br>**Fråga** `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`: .<br>**Partitionskolumnen**: Ange den kolumn som används för att använda partition med dynamiskt omfång. <br><br>Under körningen beräknar Data Factory först värdeområdena för den angivna partitionskolumnen, genom att fördela raderna jämnt i ett antal buckets enligt antalet `?AdfHanaDynamicRangePartitionCondition` olika partitionskolumnvärden och ADF-inställningen för parallellkopiering, ersätter sedan med filtrering av partitionskolumnvärdet för varje partition och skickar till SAP HANA.<br><br>Om du vill använda flera kolumner som partitionskolumn kan du sammanfoga värdena för varje kolumn som en kolumn `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`i frågan och ange den som partitionskolumn i ADF, till exempel . |

**Exempel: fråga med fysiska partitioner i en tabell**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exempel: fråga med partition för dynamiskt omfång**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>Mappning av datatyp för SAP HANA

Vid kopiering av data från SAP HANA används följande mappningar från SAP HANA-datatyper till Azure Data Factory interimsdatatyper. Se [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md) om du vill veta mer om hur du kopierar aktivitetsschemat och datatypen till diskhon.

| SAP HANA-datatyp | Data fabrik interim datatyp |
| ------------------ | ------------------------------ |
| ALFA-NUMMER           | Sträng                         |
| BIGINT             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | Sträng                         |
| Blob               | Byte[]                         |
| Bool               | Byte                           |
| Clob               | Sträng                         |
| DATE               | DateTime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| Flyta              | Double                         |
| INTEGER            | Int32                          |
| NCLOB (på andra sätt)              | Sträng                         |
| Nvarchar           | Sträng                         |
| REAL               | Enkel                         |
| ANDRADATE         | DateTime                       |
| KORTTEXT          | Sträng                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16 (int16)                          |
| STGEOMETRYTYP     | Byte[]                         |
| STPOINTTYP        | Byte[]                         |
| TEXT               | Sträng                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | Sträng                         |
| TIMESTAMP          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
