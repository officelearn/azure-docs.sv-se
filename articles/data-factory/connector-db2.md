---
title: Kopiera data från DB2 med Azure Data Factory
description: Lär dig hur du kopierar data från DB2 till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: jingwang
ms.openlocfilehash: 22ecac12e049e58e533cdde0078f4a25f6bb2aa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77423835"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopiera data från DB2 med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-onprem-db2-connector.md)
> * [Aktuell version](connector-db2.md)

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från en DB2-databas. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här DB2-databaskopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från DB2-databasen till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här DB2-anslutningen stöder följande IBM DB2-plattformar och versioner med DRDA-version 9, 10 och 11 (Distributed Relational Database Architecture Architecture) (Distributed Relational Database Architecture) version 9, 10 och 11:

* IBM DB2 för z/OS 12.1
* IBM DB2 för z/OS 11.1
* IBM DB2 för z/OS 10.1
* IBM DB2 för i 7.3
* IBM DB2 för i 7.2
* IBM DB2 för i 7.1
* IBM DB2 för LUW 11
* IBM DB2 för LUW 10,5
* IBM DB2 för LUW 10.1

>[!TIP]
>DB2-kontakten är byggd ovanpå Microsoft OLE DB Provider för DB2. Information om felsÃ¶kning av DB2-anslutningsfel finns i [felkoder för dataprovidern](https://docs.microsoft.com/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors).

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime tillhandahåller en inbyggd DB2-drivrutin, därför behöver du inte installera någon drivrutin manuellt när du kopierar data från DB2.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för DB2-koppling.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för DB2-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **Db2** | Ja |
| server |Namn på DB2-servern. Du kan ange portnumret efter servernamnet avgränsat med kolon `server:port`t.ex. |Ja |
| databas |Namn på DB2-databasen. |Ja |
| authenticationType |Typ av autentisering som används för att ansluta till DB2-databasen.<br/>Tillåtet värde är: **Grundläggande**. |Ja |
| användarnamn |Ange användarnamn som ska anslutas till DB2-databasen. |Ja |
| password |Ange lösenord för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| packageCollection paket | Ange under var de nödvändiga paketen skapas automatiskt av ADF när du frågar databasen. | Inga |
| certifikatKommernamn | När du använder SSL-kryptering (Secure Sockets Layer) eller TLS-kryptering (Transport Layer Security) måste du ange ett värde för certifikatets gemensamma namn. | Inga |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om det inte anges används standardkörningen för Azure Integration. |Inga |

> [!TIP]
> Om du får ett `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805`felmeddelande som anger skapas inte orsaken ett nödvändigt paket för användaren. Som standard försöker ADF skapa ett paket under samling som heter som den användare som du använde för att ansluta till DB2. Ange egenskapen paketsamling för att ange under var du vill att ADF ska skapa de paket som behövs när du frågar databasen.

**Exempel:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av DB2-datauppsättningen.

Så här kopierar du data från DB2:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste anges till: **Db2Table** | Ja |
| Schemat | Namnet på schemat. |Nej (om "fråga" i aktivitetskällan har angetts)  |
| tabell | Tabellens namn. |Nej (om "fråga" i aktivitetskällan har angetts)  |
| tableName | Namn på tabellen med schema. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` `table` och för ny arbetsbelastning. | Nej (om "fråga" i aktivitetskällan har angetts) |

**Exempel**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Om du `RelationalTable` använde maskinskriven datauppsättning stöds den fortfarande som den är, medan du föreslås använda den nya framöver.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av DB2-källa.

### <a name="db2-as-source"></a>DB2 som källa

Om du vill kopiera data från DB2 stöds följande egenskaper i avsnittet kopiera **aktivitetskälla:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till: **Db2Source** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Till exempel: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nej (om "tableName" i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Om du `RelationalSource` använde den maskinskrivna källan stöds den fortfarande som den är, medan du föreslås använda den nya framåt.

## <a name="data-type-mapping-for-db2"></a>Mappning av datatyp för DB2

Vid kopiering av data från DB2 används följande mappningar från DB2-datatyper till Azure Data Factory interimsdatatyper. Se [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md) om du vill veta mer om hur du kopierar aktivitetsschemat och datatypen till diskhon.

| Databastyp för DB2 | Data fabrik interim datatyp |
|:--- |:--- |
| Bigint |Int64 |
| Binär |Byte[] |
| Blob |Byte[] |
| Char |String |
| Clob |String |
| Datum |Datumtid |
| DB2DynArray |String |
| DbClob (D.) |String |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float (Flyttal) |Double |
| Grafisk |String |
| Integer |Int32 |
| LongVarBinary (LångVarBinary) |Byte[] |
| LongVarChar (Olikart) |String |
| LongVarGraphic (olikartade) |String |
| Numerisk |Decimal |
| Verkliga |Enkel |
| Smallint |Int16 (int16) |
| Tid |TimeSpan |
| Tidsstämpel |DateTime |
| Varbinary |Byte[] |
| Varchar |String |
| Vargrafiska |String |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
