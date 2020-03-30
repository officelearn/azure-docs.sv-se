---
title: Kopiera data från Cassandra med Azure Data Factory
description: Lär dig hur du kopierar data från Cassandra till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 9339fff820c0a0d915258ce3a0bc5371242ad50d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892835"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Kopiera data från Cassandra med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Aktuell version](connector-cassandra.md)

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från en Cassandra-databas. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Cassandra-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Cassandra-databasen till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här Cassandra-kontakten stöder:

- Cassandra **versionerna 2.x och 3.x**.
- Kopiera data med **grundläggande** eller **anonym** autentisering.

>[!NOTE]
>För aktiviteter som körs på Självvärderade Integration Runtime stöds Cassandra 3.x sedan IR version 3.7 och senare.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime tillhandahåller en inbyggd Cassandra-drivrutin, därför behöver du inte installera någon drivrutin manuellt när du kopierar data från/till Cassandra.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för Cassandra-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Cassandra-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen Type måste ställas in på: **Cassandra** |Ja |
| värd |En eller flera IP-adresser eller värdnamn för Cassandra-servrar.<br/>Ange en kommaavgränsad lista med IP-adresser eller värdnamn som ska anslutas till alla servrar samtidigt. |Ja |
| port |TCP-porten som Cassandra-servern använder för att lyssna efter klientanslutningar. |Nej (standard är 9042) |
| authenticationType | Typ av autentisering som används för att ansluta till Cassandra-databasen.<br/>Tillåtna värden är: **Grundläggande**och **Anonym**. |Ja |
| användarnamn |Ange användarnamn för användarkontot. |Ja, om authenticationType är inställt på Basic. |
| password |Ange lösenord för användarkontot. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja, om authenticationType är inställt på Basic. |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om det inte anges används standardkörningen för Azure Integration. |Inga |

>[!NOTE]
>För närvarande stöds inte anslutning till Cassandra med SSL.

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Cassandra-datauppsättning.

Om du vill kopiera data från Cassandra anger du egenskapen typ för datauppsättningen till **CassandraTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste anges till: **CassandraTable** | Ja |
| nyckelutrymme |Namn på nyckelutrymmet eller schemat i Cassandra-databasen. |Nej (om "fråga" för "CassandraSource" har angetts) |
| tableName |Namnet på tabellen i Cassandra-databasen. |Nej (om "fråga" för "CassandraSource" har angetts) |

**Exempel:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet


En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Cassandra-källan.

### <a name="cassandra-as-source"></a>Cassandra som källa

Om du vill kopiera data från Cassandra anger du källtypen i kopieringsaktiviteten till **CassandraSource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type property för kopians aktivitet måste anges till: **CassandraSource** | Ja |
| DocumentDB |Använd den anpassade frågan för att läsa data. SQL-92-fråga eller CQL-fråga. Se [CQL-referens](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>När du använder SQL-frågan anger du **namnet keyspace name.table** som ska representera den tabell som du vill fråga. |Nej (om "tableName" och "keyspace" i datauppsättningen anges). |
| konsekvensNivå |Konsekvensnivån anger hur många repliker som måste svara på en läsbegäran innan data returneras till klientprogrammet. Cassandra kontrollerar det angivna antalet repliker för data för att uppfylla läsbegäran. Mer information [finns i Konfigurera datakonsekvens.](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html)<br/><br/>Tillåtna värden är: **ETT**, **TVÅ**, **TRE**, **KVORUM**, **ALLA**, **LOCAL_QUORUM**, **EACH_QUORUM**och **LOCAL_ONE**. |Nej (standard `ONE`är) |

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

## <a name="data-type-mapping-for-cassandra"></a>Mappning av datatyp för Cassandra

När du kopierar data från Cassandra används följande mappningar från Cassandra-datatyper till Azure Data Factory interimsdatatyper. Se [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md) om du vill veta mer om hur du kopierar aktivitetsschemat och datatypen till diskhon.

| Cassandra-datatyp | Data fabrik interim datatyp |
|:--- |:--- |
| ASCII |String |
| BIGINT |Int64 |
| Blob |Byte[] |
| Boolean |Boolean |
| DECIMAL |Decimal |
| DOUBLE |Double |
| Flyta |Enkel |
| Inet |String |
| INT |Int32 |
| TEXT |String |
| TIMESTAMP |DateTime |
| TIMEUUID (PÅ LÄNGE) |GUID |
| Uuid |GUID |
| VARCHAR |String |
| VARINT (OLIKA) |Decimal |

> [!NOTE]
> För samlingstyper (karta, uppsättning, lista osv.) finns [i Samlingstyper för Arbete med Cassandra med hjälp av avsnittet virtual table.](#work-with-collections-using-virtual-table)
>
> Användardefinierade typer stöds inte.
>
> Längden på längderna binärkolumn och strängkolumn kan inte vara större än 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Arbeta med samlingar med hjälp av virtuell tabell

Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från cassandra-databasen. För samlingstyper, inklusive karta, uppsättning och lista, renormalizes drivrutinen till motsvarande virtuella tabeller. Om en tabell innehåller några samlingskolumner genererar drivrutinen följande virtuella tabeller:

* En **bastabell**som innehåller samma data som den verkliga tabellen utom samlingskolumnerna. Bastabellen använder samma namn som den verkliga tabellen som den representerar.
* En **virtuell tabell** för varje samlingskolumn, som utökar de kapslade data. De virtuella tabeller som representerar samlingar namnges med namnet på den verkliga tabellen, en avgränsare "*vt*" och namnet på kolumnen.

Virtuella tabeller refererar till data i den verkliga tabellen, vilket gör att drivrutinen kan komma åt denormaliserade data. Mer information finns i avsnittet Exempel. Du kan komma åt innehållet i Cassandra-samlingar genom att fråga och ansluta till de virtuella tabellerna.

### <a name="example"></a>Exempel

Följande "Exempeltabell" är till exempel en Cassandra-databastabell som innehåller en heltalsnyckelkolumn med namnet "pk_int", en textkolumn med namnet värde, en listkolumn, en kartkolumn och en uppsättningskolumn (med namnet "StringSet").

| pk_int | Värde | Visa lista | Karta | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"provvärde 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"provvärde 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Drivrutinen skulle generera flera virtuella tabeller för att representera den här enstaka tabellen. Kolumnerna för sekundärnyckel i de virtuella tabellerna refererar till primärnyckelkolumnerna i den verkliga tabellen och anger vilken verklig tabellrad den virtuella tabellraden motsvarar.

Den första virtuella tabellen är bastabellen "ExampleTable" visas i följande tabell: 

| pk_int | Värde |
| --- | --- |
| 1 |"provvärde 1" |
| 3 |"provvärde 3" |

Bastabellen innehåller samma data som den ursprungliga databastabellen förutom samlingarna, som utelämnas från den här tabellen och expanderas i andra virtuella tabeller.

I följande tabeller visas de virtuella tabeller som renormalize data från kolumnerna Lista, Karta och StringSet. Kolumnerna med namn som slutar med "_index" eller "_key" anger datas position i den ursprungliga listan eller kartan. Kolumnerna med namn som slutar med "_value" innehåller expanderade data från samlingen.

**Tabell "ExampleTable_vt_List":**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**Tabell "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

**Tabell "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
