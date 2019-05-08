---
title: Kopiera data från Cassandra med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Cassandra till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
ms.openlocfilehash: 743dad6032547f8f535543413adff416efb56ac0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640097"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Kopiera data från Cassandra med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Aktuell version](connector-cassandra.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en Cassandra-databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Cassandra-databas till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Cassandra-anslutningen:

- Cassandra **versioner 2.x och 3.x**.
- Kopiera data med hjälp av **grundläggande** eller **anonym** autentisering.

>[!NOTE]
>För aktiviteter som körs på lokal Integration Runtime, Cassandra 3.x stöds sedan IR version 3.7 och senare.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill kopiera data från en Cassandra-databas som inte är allmänt tillgänglig, måste du konfigurera en lokal Integration Runtime. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) artikeln om du vill få mer detaljerad information. Integreringskörningen innehåller en inbyggd Cassandra-drivrutin, måste du därför inte installera några drivrutinen manuellt när du kopierar data från/till Cassandra.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Cassandra-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Cassandra länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type |Type-egenskapen måste anges till: **Cassandra** |Ja |
| host |En eller flera IP-adresser eller värdnamn för Cassandra-servrar.<br/>Ange en kommaavgränsad lista med IP-adresser eller värdnamn för att ansluta till alla servrar samtidigt. |Ja |
| port |TCP-port som Cassandra-servern använder för att lyssna efter klientanslutningar. |Nej (standard är 9042) |
| authenticationType | Typ av autentisering som används för att ansluta till Cassandra-databasen.<br/>Tillåtna värden är: **Grundläggande**, och **anonym**. |Ja |
| username |Ange användarnamn för användarkontot. |Ja, om authenticationType anges till Basic. |
| password |Ange lösenordet för användarkontot. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja, om authenticationType anges till Basic. |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda lokal Integration Runtime eller Azure Integration Runtime (om ditt datalager är offentligt tillgänglig). Om den inte anges används standard Azure Integration Runtime. |Nej |

>[!NOTE]
>För närvarande stöds inte anslutning till Cassandra med hjälp av SSL.

**Exempel:**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Cassandra-datauppsättningen.

Om du vill kopiera data från Cassandra, ange typegenskapen på datauppsättningen till **CassandraTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för datauppsättningen måste anges till: **CassandraTable** | Ja |
| keyspace |Namnet på keyspace eller schema i Cassandra-databasen. |Nej (om ”query” för ”CassandraSource” har angetts) |
| tableName |Namnet på tabellen i Cassandra-databas. |Nej (om ”query” för ”CassandraSource” har angetts) |

**Exempel:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet


En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Cassandra-källa.

### <a name="cassandra-as-source"></a>Cassandra som källa

Om du vill kopiera data från Cassandra, ange typ av datakälla i kopieringsaktiviteten till **CassandraSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **CassandraSource** | Ja |
| query |Använd anpassad fråga för att läsa data. SQL-92 fråga eller CQL-fråga. Se [CQL referens](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>När du använder SQL-fråga, ange **keyspace name.table namn** som representerar den tabell som du vill fråga. |Nej (om ”tableName” och ”keyspace” i datauppsättningen har angetts). |
| consistencyLevel |Konsekvensnivån som anger hur många kopior måste svara på en läsbegäran innan det returneras data till klientprogrammet. Cassandra kontrollerar det angivna antalet repliker för data för att tillgodose läsförfrågan. Se [konfigurera datakonsekvens](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) mer information.<br/><br/>Tillåtna värden är: **EN**, **två**, **tre**, **KVORUM**, **alla**, **LOCAL_QUORUM**, **EACH_QUORUM**, och **LOCAL_ONE**. |Nej (standard är `ONE`) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
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
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Datatypen mappning för Cassandra

När du kopierar data från Cassandra, används följande mappningar från Cassandra-datatyper till Azure Data Factory tillfälliga-datatyper. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| Cassandra-datatypen | Data factory tillfälliga datatyp |
|:--- |:--- |
| ASCII |String |
| BIGINT |Int64 |
| BLOB |Byte[] |
| BOOLEAN |Boolean |
| DECIMAL |Decimal |
| DOUBLE |Double |
| FLOAT |Single |
| INET |String |
| INT |Int32 |
| TEXT |String |
| TIMESTAMP |DateTime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |String |
| VARINT |Decimal |

> [!NOTE]
> Samlingen finns typer (karta, set, lista, osv.), [arbeta med Cassandra samlingstyper med hjälp av virtuella tabellen](#work-with-collections-using-virtual-table) avsnittet.
>
> Användardefinierade typer stöds inte.
>
> Längden på Binär kolumn och strängkolumn längd får inte vara större än 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Arbeta med samlingar med virtuella tabellen

Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från Cassandra-databas. För samlingstyper inklusive kartan, uppsättning och lista, renormalizes drivrutinen data till motsvarande virtuella tabeller. Om en tabell innehåller några kolumner för samlingen, genererar drivrutinen mer specifikt kan följande virtuella tabeller:

* En **bastabellen**, som innehåller samma data som den verkliga tabellen utom samling kolumner. Bastabellen använder samma namn som den verkliga tabell som representerar.
* En **virtuella tabellen** för varje samling-kolumn som utökar den kapslade data. Virtuella tabeller som representerar samlingar namnges med namnet på tabellen verkliga avgränsare ”*vt*” och namnet på kolumnen.

Virtuella tabellerna hänvisar till data i tabellen verkliga aktiverar drivrutinen att komma åt den Avnormaliserade data. Se exempel avsnittet för information. Du kan komma åt innehållet i Cassandra samlingar genom att fråga och ansluta till virtuella tabeller.

### <a name="example"></a>Exempel

Till exempel är följande ”ExampleTable” en Cassandra-databastabell som innehåller ett heltal primärnyckelkolumnen med namnet ”pk_int”, en textkolumn namngivet värde, en kolumn, en karta kolumn och en uppsättning kolumn (med namnet ”StringSet”).

| pk_int | Värde | Visa lista | Karta | StringSet |
| --- | --- | --- | --- | --- |
| 1 |”exempelvärde 1” |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |”exempelvärde 3” |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Drivrutinen skulle generera flera virtuella tabeller som representerar en enda tabell. Sekundärnyckelskolumnerna i virtuella tabeller refererar till primärnyckelskolumnerna i verkliga tabellen och anger vilka verkliga tabellrad som den virtuella tabellraden motsvarar.

Den första virtuella tabellen är bastabellen med namnet ”ExampleTable” visas i följande tabell: 

| pk_int | Värde |
| --- | --- |
| 1 |”exempelvärde 1” |
| 3 |”exempelvärde 3” |

Bastabellen innehåller samma data som den ursprungliga databastabellen förutom de samlingar som är utelämnas från den här tabellen och expanderas i andra virtuella tabeller.

Följande tabeller visar de virtuella tabeller som renormera data från listan och kartan StringSet kolumner. Kolumner med namn som slutar med ”_index” eller ”_nyckel” anger data i den ursprungliga listan eller kartan position. Kolumner med namn som slutar med ”_value” innehåller utökade data från samlingen.

**Tabell ”ExampleTable_vt_List”:**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**Tabell ”ExampleTable_vt_Map”:**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

**Table "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
