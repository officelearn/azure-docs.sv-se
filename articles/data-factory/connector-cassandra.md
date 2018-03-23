---
title: Kopiera data från Cassandra med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Cassandra till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen.
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
ms.openlocfilehash: 72d2eb9e6cf235a90c5b1cf1c125fb6719c65317
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Kopiera data från Cassandra med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-onprem-cassandra-connector.md)
> * [Version 2 – förhandsversion](connector-cassandra.md)

Den här artikeln beskriver hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från en Cassandra-databas. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.


> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Cassandra connector i V1](v1/data-factory-onprem-cassandra-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Cassandra databasen till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här Cassandra-anslutningen:

- Cassandra **versioner 2.X**.
- Kopiera data med hjälp av **grundläggande** eller **anonym** autentisering.

## <a name="prerequisites"></a>Förutsättningar

Om du vill kopiera data från en Cassandra-databas som inte är offentligt tillgänglig, måste du konfigurera en Self-hosted integrering Runtime. Se [Self-hosted integrering Runtime](create-self-hosted-integration-runtime.md) artikel mer information. Integration Runtime innehåller en inbyggd Cassandra drivrutin, därför behöver du inte installera en drivrutin manuellt när du kopierar data från/till Cassandra.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Cassandra connector.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Cassandra länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen type måste anges till: **Cassandra** |Ja |
| värd |En eller flera IP-adresser eller värdnamn Cassandra servrar.<br/>Ange en kommaavgränsad lista med IP-adresser eller värdnamn för att ansluta till alla servrar samtidigt. |Ja |
| port |TCP-porten som används av Cassandra-server för att lyssna efter anslutningar. |Nej (standard är 9042) |
| AuthenticationType | Typ av autentisering som används för att ansluta till Cassandra-databasen.<br/>Tillåtna värden är: **grundläggande**, och **anonym**. |Ja |
| användarnamn |Ange användarnamnet för användarkontot. |Ja, om authenticationType anges till Basic. |
| lösenord |Ange lösenordet för användarkontot. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja, om authenticationType anges till Basic. |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Self-hosted integrering Runtime eller Azure Integration Runtime (om datalager är offentligt tillgänglig). Om inget anges används standard-Azure Integration Runtime. |Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Cassandra dataset.

Ange typegenskapen för dataset för att kopiera data från Cassandra, **RelationalTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **CassandraTable** | Ja |
| keyspace |Namnet på keyspace eller schema i Cassandra databasen. |Nej (om ”frågan” för ”CassandraSource” har angetts) |
| tableName |Namnet på tabellen i Cassandra databas. |Nej (om ”frågan” för ”CassandraSource” har angetts) |

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


En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Cassandra källa.

### <a name="cassandra-as-source"></a>Cassandra som källa

Om du vill kopiera data från Cassandra, anger du källa i kopieringsaktiviteten till **CassandraSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **CassandraSource** | Ja |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-92 frågan eller CQL frågan. Se [CQL referens](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>När du använder SQL-frågan anger **keyspace name.table namn** som representerar den tabell som du vill fråga. |Nej (om ”tabellnamn” och ”keyspace” i datamängden har angetts). |
| consistencyLevel |Nivå för konsekvenskontroll anger hur många repliker måste svara på en läsbegäran innan den returnerar data till klientprogrammet. Cassandra kontrollerar det angivna antalet repliker för data att uppfylla read-förfrågan. Se [konfigurera datakonsekvens](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) mer information.<br/><br/>Tillåtna värden är: **en**, **två**, **tre**, **KVORUM**, **alla**, **LOCAL_ KVORUM**, **EACH_QUORUM**, och **LOCAL_ONE**. |Nej (standardvärdet är `ONE`) |

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

När du kopierar data från Cassandra, används följande mappningar från Cassandra datatyper för Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| Cassandra datatyp | Data factory tillfälliga datatyp |
|:--- |:--- |
| ASCII |Sträng |
| BIGINT |Int64 |
| BLOB |Byte[] |
| BOOLESKT VÄRDE |Boolesk |
| DECIMAL |Decimal |
| DUBBEL |Dubbel |
| FLYTTAL |Ogift |
| INET |Sträng |
| INT |Int32 |
| TEXT |Sträng |
| TIDSSTÄMPEL |DateTime |
| TIMEUUID |GUID |
| UUID |GUID |
| VARCHAR |Sträng |
| VARINT |Decimal |

> [!NOTE]
> Insamling av typer (karta, set, lista, etc.), referera till [arbeta med Cassandra samlingstyper med virtuella tabellen](#work-with-collections-using-virtual-table) avsnitt.
>
> Användardefinierade typer stöds inte.
>
> Längden på Binary-kolumn och strängkolumn längd får inte vara större än 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Arbeta med samlingar med virtuella tabellen

Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från databasen Cassandra. För samlingstyper inklusive karta, uppsättning och lista, renormalizes drivrutinen data i motsvarande virtuella tabeller. Om en tabell innehåller några kolumner i samlingen, genererar drivrutinen mer specifikt kan följande virtuella tabeller:

* En **bastabellen**, som innehåller samma data som verkliga tabell utom samling kolumner. Bastabellen använder samma namn som den verkliga tabell som representerar.
* En **virtuella tabellen** för varje samling-kolumn som utökar kapslade data. Virtuella tabeller som representerar samlingar namnges med namnet på tabellen verkliga avgränsare ”*vt*” och namnet på kolumnen.

Virtuella tabellerna hänvisar till data i tabellen verkliga aktiverar drivrutinen att komma åt Avnormaliserade data. Se avsnittet för information. Du kan komma åt innehållet i Cassandra samlingar genom att fråga och ansluta till virtuella tabeller.

### <a name="example"></a>Exempel

Till exempel är följande ”ExampleTable” en Cassandra databastabell som innehåller ett heltal primärnyckelkolumnen med namnet ”pk_int”, en textkolumn namngivet värde, en kolumn, en karta kolumn och en set-kolumn (med namnet ”StringSet”).

| pk_int | Värde | Visa lista | Karta | StringSet |
| --- | --- | --- | --- | --- |
| 1 |”exempelvärde 1” |["1", "2", "3"] |{”S1”: ”a”, ”S2”: ”b”} |{”A”, ”B”, ”C”} |
| 3 |”exempelvärde 3” |["100", "101", "102", "105"] |{”S1”: ”t”} |{”A”, ”E”} |

Drivrutinen skulle generera flera virtuella tabeller som representerar denna tabell. Sekundärnyckelskolumnerna i virtuella tabeller refererar till primärnyckelkolumnerna i tabellen verkliga och ange vilka verkliga tabellrad som motsvarar den virtuella tabellraden.

Den första virtuella tabellen är bastabellen med namnet ”ExampleTable” visas i följande tabell: 

| pk_int | Värde |
| --- | --- |
| 1 |”exempelvärde 1” |
| 3 |”exempelvärde 3” |

Bastabellen innehåller samma data som den ursprungliga databastabellen förutom de samlingar som utelämnas från den här tabellen och expanderas i andra virtuella tabeller.

Följande tabeller visar virtuella register som renormalize data från listan och mappa StringSet kolumner. Kolumner med namn som slutar med ”_index” eller ”_nyckelskyddsserverns” anger placeringen av data i den ursprungliga listan eller kartan. Kolumnerna med namn som slutar med ”_value” innehåller utökade data från samlingen.

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

**Table "ExampleTable_vt_Map":**

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
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).