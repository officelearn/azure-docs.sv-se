---
title: Kopiera data från SAP HANA
description: Lär dig hur du kopierar data från SAP HANA till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
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
ms.openlocfilehash: fa165c21622110bb18476efdebf3264a11e26ad7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265888"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Kopiera data från SAP HANA med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-sap-hana-connector.md)
> * [Aktuell version](connector-sap-hana.md)

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en SAP HANA-databas. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

>[!TIP]
>Om du vill lära dig mer om ADF: s övergripande support i SAP data integrations scenario, se [SAP data integration med Azure Data Factory whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) med detaljerad introduktion, comparsion och vägledning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SAP HANA anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från SAP HANA-databasen till alla mottagar data lager som stöds. En lista över data lager som stöds som källor/mottagare av kopierings aktiviteten finns i tabellen med [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Mer specifikt stöder den här SAP HANA-anslutningen:

- Kopiera data från en version av SAP HANA Database.
- Kopiera data från **Hana-informations modeller** (till exempel analys-och beräknings visningar) och **rad/kolumn-tabeller**.
- Kopiera data med **Basic** -eller **Windows** -autentisering.
- Parallell kopiering från en SAP HANA källa. Mer information finns i avsnittet [parallell kopiering från SAP HANA](#parallel-copy-from-sap-hana) .

> [!TIP]
> Om du vill kopiera data **till** SAP HANA data lager använder du allmän ODBC-anslutning. Se [SAP HANA Sink](connector-odbc.md#sap-hana-sink) med information. Observera att de länkade tjänsterna för SAP HANA koppling och ODBC-koppling är av olika typ, så att de inte kan återanvändas.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här SAP HANA-anslutningen måste du:

- Konfigurera en egen värd Integration Runtime. Mer information finns i artikeln om [egen värd integration runtime](create-self-hosted-integration-runtime.md) .
- Installera SAP HANA ODBC-drivrutinen på datorn Integration Runtime. Du kan hämta SAP HANA ODBC-drivrutinen från [SAP Software Download Center](https://support.sap.com/swdc). Sök med nyckelordet **SAP HANA-klienten för Windows**.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är speciella för SAP HANA koppling.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för SAP HANA länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **SapHana** | Ja |
| connectionString | Ange information som behövs för att ansluta till SAP HANA med hjälp av **grundläggande autentisering** eller **Windows-autentisering**. Se följande exempel.<br>I anslutnings strängen är server/port obligatorisk (standard porten är 30015) och användar namn och lösen ord är obligatoriska när du använder grundläggande autentisering. Mer avancerade inställningar finns i [SAP HANA egenskaper för ODBC-anslutning](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>Du kan också ställa in lösen ord i Azure Key Vault och hämta lösen ords konfigurationen från anslutnings strängen. Se [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja |
| userName | Ange användar namn när du använder Windows-autentisering. Exempel: `user@domain.com` | Nej |
| lösenord | Ange lösen ordet för användar kontot. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| connectVia | Den [integration runtime](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Det krävs en egen värd Integration Runtime som anges i [krav](#prerequisites). |Ja |

**Exempel: Använd grundläggande autentisering**

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

**Exempel: Använd Windows-autentisering**

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

Om du använder SAP HANA länkade tjänsten med följande nytto Last, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya vägen.

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av SAP HANA data uppsättning.

Följande egenskaper stöds för att kopiera data från SAP HANA:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **SapHanaTable** | Ja |
| schema | Namnet på schemat i SAP HANA databasen. | Nej (om ”query” i aktivitetskälla har angetts) |
| table | Namnet på tabellen i SAP HANA databasen. | Nej (om ”query” i aktivitetskälla har angetts) |

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

Om du använder `RelationalTable` typ av data uppsättning, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya som skickas.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av SAP HANA källa.

### <a name="sap-hana-as-source"></a>SAP HANA som källa

>[!TIP]
>Om du vill mata in data från SAP HANA effektivt genom att använda data partitionering kan du läsa mer från [parallell kopiering från SAP HANA](#parallel-copy-from-sap-hana) -avsnittet.

För att kopiera data från SAP HANA, stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **SapHanaSource** | Ja |
| query | Anger SQL-frågan för att läsa data från SAP HANA-instansen. | Ja |
| partitionOptions | Anger de data partitionerings alternativ som används för att mata in data från SAP HANA. Läs mer från [SAP HANA avsnittet om parallell kopiering](#parallel-copy-from-sap-hana) .<br>Tillåt värden är: **inga** (standard), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. Läs mer från [SAP HANA avsnittet om parallell kopiering](#parallel-copy-from-sap-hana) . `PhysicalPartitionsOfTable` kan endast användas när du kopierar data från en tabell men inte frågar. <br>När ett partitions alternativ är aktiverat (det vill säga inte `None`), kontrol leras graden av parallellitet för data från SAP HANA av [`parallelCopies`](copy-activity-performance.md#parallel-copy) inställningen för kopierings aktiviteten. | False |
| partitionSettings | Ange gruppen med inställningar för data partitionering.<br>Använd när alternativet partition är `SapHanaDynamicRange`. | False |
| partitionColumnName | Ange namnet på den käll kolumn som ska användas av en partition för parallell kopiering. Om detta inte anges identifieras indexet eller primär nyckeln för tabellen automatiskt och används som partition-kolumn.<br>Använd när alternativet partition är `SapHanaDynamicRange`. Om du använder en fråga för att hämta källdata, Hook- `?AdfHanaDynamicRangePartitionCondition` i WHERE-satsen. Se exempel i [parallell kopiering från SAP HANA](#parallel-copy-from-sap-hana) -avsnittet. | Ja när du använder `SapHanaDynamicRange` partition. |
| packetSize | Anger storleken på nätverks paketet (i kilobyte) för att dela data till flera block. Om du har stora mängder data som ska kopieras kan ökande paket storlek öka Läs hastigheten från SAP HANA i de flesta fall. Prestanda testning rekommenderas när du justerar paket storleken. | Nej.<br>Standardvärdet är 2048 (2 MB). |

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

Om du använder `RelationalSource` typ av kopierings källa, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya vägen.

## <a name="parallel-copy-from-sap-hana"></a>Parallell kopiering från SAP HANA

Data Factory SAP HANA-anslutningen innehåller inbyggda data partitioner för att kopiera data från SAP HANA parallellt. Du kan hitta data partitionerings alternativ i **käll** tabellen för kopierings aktiviteten.

![Skärm bild av partitionsalternativ](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

När du aktiverar partitionerad kopiering körs Data Factory parallella frågor mot din SAP HANA källa för att hämta data efter partitioner. Den parallella graden styrs av [`parallelCopies`s](copy-activity-performance.md#parallel-copy) inställningen på kopierings aktiviteten. Om du till exempel ställer in `parallelCopies` till fyra, Data Factory samtidigt genererar och kör fyra frågor baserat på ditt angivna partitionsalternativ och inställningar, och varje fråga hämtar en del av data från din SAP HANA.

Du rekommenderas att aktivera parallell kopiering med data partitionering, särskilt när du matar in stora mängder data från din SAP HANA. Följande är föreslagna konfigurationer för olika scenarier. När du kopierar data till filbaserat data lager rekommenderar vi att du skriver till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

| Scenario                                           | Föreslagna inställningar                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Fullständig belastning från stor tabell.                        | **Partitions alternativ**: fysiska partitioner i tabell. <br><br/>Under körningen identifierar Data Factory automatiskt typen av fysisk partition för den angivna SAP HANA tabellen och väljer motsvarande partitionsnyckel:<br>**partitionering**för - intervall: hämta den partitionstabell och de partitions intervall som har definierats för tabellen och kopiera sedan data efter intervall. <br>- **hash-partitionering**: Använd nyckel för hash-partition som partition och sedan partitionera och kopiera data baserat på beräknade intervall i ADF. <br>- **partitionering med resursallokering** eller **ingen partition**: Använd primär nyckel som partitionstabell och sedan partitionera och kopiera data baserat på beräknade intervall i ADF. |
| Läs in stora mängder data med hjälp av en anpassad fråga. | **Partitions alternativ**: partition med dynamiskt intervall.<br>**Fråga**: `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Partitionstabell**: Ange den kolumn som används för att använda partitionen för dynamiskt intervall. <br><br>Under körningen beräknar Data Factory först värde intervallen för den angivna partitionstabellen, genom att fördela raderna i ett antal buckets i enlighet med antalet distinkta värden för partitionstabell och inställningen för parallell kopiering, ersätter `?AdfHanaDynamicRangePartitionCondition` med att filtrera partitionens kolumn värde intervall för varje partition och skickar till SAP HANA.<br><br>Om du vill använda flera kolumner som partitionstabell kan du sammanfoga värdena i varje kolumn som en kolumn i frågan och ange den som partitions kolumn i ADF, t. ex. `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`. |

**Exempel: fråga med fysiska partitioner i en tabell**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exempel: fråga med Dynamic Range-partition**

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

## <a name="data-type-mapping-for-sap-hana"></a>Data typs mappning för SAP HANA

När du kopierar data från SAP HANA används följande mappningar från SAP HANA data typer för att Azure Data Factory interimistiska data typer. Se [mappningar av schema och data typer](copy-activity-schema-and-type-mapping.md) för att lära dig mer om hur kopierings aktiviteten mappar käll schema och datatyp till mottagaren.

| SAP HANA data typ | Data factory tillfälliga datatyp |
| ------------------ | ------------------------------ |
| ALPHANUM           | String                         |
| BIGINT             | Int64                          |
| BINARY             | Byte[]                         |
| Bintext            | String                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | String                         |
| DATE               | DateTime                       |
| DECIMAL            | decimaltal                        |
| DOUBLE             | Double-värde                         |
| FLOAT              | Double-värde                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| REAL               | Enkel                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | String                         |
| SMALLDECIMAL       | decimaltal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| INFORMATION               | String                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | String                         |
| TIMESTAMP          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Azure Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
