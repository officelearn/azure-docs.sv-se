---
title: "Kopiera data till/från Oracle med Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från stöds källa lagrar till Oracle-databas (eller) från Oracle stöds sink butiker med hjälp av Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
ms.openlocfilehash: df9ba6367b2d2ce1b2c281ce483e63b0baa9feb6
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="copy-data-from-and-to-oracle-using-azure-data-factory"></a>Kopiera data från och till Oracle med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-onprem-oracle-connector.md)
> * [Version 2 – förhandsversion](connector-oracle.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från och till en Oracle-databas. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Oracle-anslutningen i V1](v1/data-factory-onprem-oracle-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Oracle-databas till alla stöds sink-datalagret eller kopiera data från alla datalager stöds källa till Oracle-databas. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt denna koppling Oracle stöder följande versioner av Oracle-databasen och den stöder grundläggande eller OID autentiseringar.

    - Oracle 12c R1 (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10.1, 10,2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

## <a name="prerequisites"></a>Krav

Om du vill kopiera data från/till en Oracle-databas som inte är offentligt tillgänglig, måste du konfigurera en Self-hosted integrering Runtime. Se [Self-hosted integrering Runtime](create-self-hosted-integration-runtime.md) artikeln för information om integrering av körningsmiljön. Integration Runtime innehåller en inbyggd Oracle-drivrutin, därför behöver du inte installera en drivrutin manuellt när du kopierar data från/till Oracle.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Oracle-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Oracle länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **Oracle** | Ja |
| connectionString | Ange information som behövs för att ansluta till Oracle-databasinstansen. Markera det här fältet som en SecureString. | Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Self-hosted integrering Runtime eller Azure Integration Runtime (om datalager är offentligt tillgänglig). Om inget anges används standard-Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Oracle dataset.

Ange typegenskapen för dataset för att kopiera data från/till Oracle, **OracleTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **OracleTable** | Ja |
| tableName |Namnet på tabellen i Oracle-databas som den länkade tjänsten refererar till. | Ja |

**Exempel:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Oracle-källa och mottagare.

### <a name="oracle-as-source"></a>Oracle som källa

Om du vill kopiera data från Oracle, anger du källa i kopieringsaktiviteten till **OracleSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **OracleSource** | Ja |
| oracleReaderQuery | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej |

Om du inte anger ”oracleReaderQuery” kolumner som har definierats i avsnittet ”struktur” i dataset används för att skapa en fråga (`select column1, column2 from mytable`) att köra mot Oracle-databasen. Om datauppsättningsdefinitionen inte har ”struktur”, markeras alla kolumner från tabellen.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle som mottagare

Om du vill kopiera data till Oracle, anger du sink i kopieringsaktiviteten till **OracleSink**. Följande egenskaper stöds i kopieringsaktiviteten **sink** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för kopiera aktivitet sink måste anges till: **OracleSink** | Ja |
| writeBatchSize | Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize.<br/>Tillåtna värden är: heltal (antalet rader). |Nej (standard är 10000) |
| writeBatchTimeout | Vänta tills batch insert-åtgärden ska slutföras innan tidsgränsen uppnås.<br/>Tillåtna värden är: Timespan. Exempel: 00:30:00 (30 minuter). | Nej |
| preCopyScript | Ange en SQL-fråga för Kopieringsaktiviteten ska köras innan data skrivs till Oracle i varje körning. Du kan använda den här egenskapen för att rensa förinstallerade data. | Nej |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-oracle"></a>Mappning för Oracle-datatyp

När du kopierar data från/till Oracle, används följande mappningar från Oracle-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| Oracle-datatyp | Data factory tillfälliga datatyp |
|:--- |:--- |
| BFILE |byte] |
| BLOB |byte] |
| CHAR |Sträng |
| CLOB |Sträng |
| DATUM |Datum och tid |
| FLYTTAL |Decimal, sträng (om precision > 28) |
| HELTAL |Decimal, sträng (om precision > 28) |
| LÅNG |Sträng |
| LÅNGT RÅDATA |byte] |
| NCHAR |Sträng |
| NCLOB |Sträng |
| ANTAL |Decimal, sträng (om precision > 28) |
| NVARCHAR2 |Sträng |
| RÅDATA |byte] |
| ROWID |Sträng |
| TIDSSTÄMPEL |Datum och tid |
| TIDSSTÄMPEL MED LOKALA TIDSZON |Sträng |
| TIDSSTÄMPEL MED TIDSZON |Sträng |
| OSIGNERAT HELTAL |Tal |
| VARCHAR2 |Sträng |
| XML |Sträng |

> [!NOTE]
> Datatypen intervall år, månad och intervall dag till andra stöds inte.


## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).