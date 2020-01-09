---
title: Kopiera data från ODBC-källor med Azure Data Factory
description: Lär dig hur du kopierar data från OData-källor till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 816009bb7481d93fd53011d067ab56cecbe8e3ef
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440429"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Kopiera data från och till ODBC-datalager med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-odbc-connector.md)
> * [Aktuell version](connector-odbc.md)

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från och till ett ODBC-data lager. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här ODBC-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från ODBC-källa till alla mottagar data lager som stöds, eller kopiera från alla käll data lager som stöds till ODBC-mottagare. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder denna ODBC-anslutning kopiering av data från/till **ODBC-kompatibla data lager** med **Basic** eller **Anonym** autentisering.

## <a name="prerequisites"></a>Krav

Om du vill använda den här ODBC-anslutningen måste du:

- Konfigurera en egen värd Integration Runtime. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) nedan för information.
- Installera ODBC-drivrutinen för data lagret på den Integration Runtime datorn.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för ODBC-anslutning.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för ODBC-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **ODBC** | Ja |
| connectionString | Anslutnings strängen exklusive Credential-delen. Du kan ange anslutnings strängen med ett mönster som `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`eller använda system-DSN (data källans namn) som du har konfigurerat på den Integration Runtime datorn med `"DSN=<name of the DSN on IR machine>;"` (du behöver fortfarande ange Credential-delen i den länkade tjänsten därefter).<br>Du kan också ange ett lösen ord i Azure Key Vault och hämta `password` -konfigurationen från anslutnings strängen. Mer information finns [i lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) .| Ja |
| authenticationType | Typ av autentisering som används för att ansluta till ODBC-datalagret.<br/>Tillåtna värden är: **Basic** och **Anonymous**. | Ja |
| userName | Ange användar namn om du använder grundläggande autentisering. | Inga |
| password | Ange lösen ordet för det användar konto som du har angett för användar namnet. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| credential | Delen autentiseringsuppgifter för den anslutnings sträng som anges i drivrutinsspecifika egenskaps värde format. Exempel: `"RefreshToken=<secret refresh token>;"`. Markera det här fältet som en SecureString. | Inga |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Det krävs en egen värd Integration Runtime som anges i [krav](#prerequisites). |Ja |

**Exempel 1: använda grundläggande autentisering**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
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

**Exempel 2: använda anonym autentisering**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av ODBC-datauppsättning.

Följande egenskaper stöds för att kopiera data från/till ODBC-kompatibelt data lager:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till: **OdbcTable** | Ja |
| tableName | Namnet på tabellen i ODBC-datalagret. | Nej för källa (om "fråga" i aktivitets källan har angetts);<br/>Ja för mottagare |

**Exempel**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "OdbcTable",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

Om du använder `RelationalTable` typ av data uppsättning, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya som skickas.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av ODBC-källa.

### <a name="odbc-as-source"></a>ODBC som källa

För att kopiera data från ODBC-kompatibelt data lager, stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **OdbcSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om ”tableName” i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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
                "type": "OdbcSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Om du använder `RelationalSource` typ av källa, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya som skickas.

### <a name="odbc-as-sink"></a>ODBC som mottagare

Om du vill kopiera data till ODBC-kompatibelt data lager ställer du in mottagar typen i kopierings aktiviteten till **OdbcSink**. Följande egenskaper stöds i kopieringsaktiviteten **mottagare** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till: **OdbcSink** | Ja |
| writeBatchTimeout |Vänte tid för att infoga batch-åtgärden ska slutföras innan tids gränsen uppnåddes.<br/>Tillåtna värden är: TimeSpan. Exempel ”: 00: 30:00” (30 minuter). |Inga |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize.<br/>Tillåtna värden är: heltal (antal rader). |Nej (Standardvärdet är 0 – identifieras automatiskt) |
| preCopyScript |Ange en SQL-fråga för kopierings aktivitet som ska köras innan data skrivs till data lagret i varje körning. Du kan använda den här egenskapen för att rensa förinlästa data. |Inga |

> [!NOTE]
> För "writeBatchSize", om den inte har angetts (identifieras automatiskt), identifierar kopierings aktiviteten först om driv rutinen stöder batch-åtgärder och anger den till 10000 om den gör det, eller så anger du den till 1 om den inte gör det. Om du anger något annat värde än 0, följer kopierings aktiviteten värdet och Miss lyckas vid körning om driv rutinen inte stöder batch-åtgärder.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="sap-hana-sink"></a>SAP HANA mottagare

>[!NOTE]
>Information om hur du kopierar data från SAP HANA data lager finns i intern [SAP HANA-anslutning](connector-sap-hana.md). Om du vill kopiera data till SAP HANA ska du följa den här instruktionen för att använda ODBC-anslutning. Observera att de länkade tjänsterna för SAP HANA koppling och ODBC-koppling är av olika typ, så att de inte kan återanvändas.
>

Du kan kopiera data till SAP HANA Database med hjälp av den allmänna ODBC-anslutningen.

Konfigurera en egen värd Integration Runtime på en dator med åtkomst till ditt data lager. Integration Runtime använder ODBC-drivrutinen för SAP HANA för att ansluta till data lagret. Installera därför driv rutinen om den inte redan är installerad på samma dator. Mer information finns i avsnittet [krav](#prerequisites) .

Innan du använder SAP HANA Sink i en Data Factory-lösning kontrollerar du om Integration Runtime kan ansluta till data lagret med hjälp av anvisningarna i avsnittet [Felsöka anslutnings problem](#troubleshoot-connectivity-issues) .

Skapa en ODBC-länkad tjänst för att länka ett SAP HANA data lager till en Azure-datafabrik som visas i följande exempel:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
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

I artikeln från början finns en detaljerad översikt över hur du använder ODBC-datalager som data lager/Sink-datalager i en kopierings åtgärd.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).


## <a name="troubleshoot-connectivity-issues"></a>Felsök anslutnings problem

Använd fliken **diagnostik** i **integration runtime Configuration Manager**för att felsöka anslutnings problem.

1. Starta **Integration Runtime Configuration Manager**.
2. Växla till fliken **diagnostik** .
3. Under avsnittet "Testa anslutning" väljer du **typ** av data lager (länkad tjänst).
4. Ange **anslutnings strängen** som används för att ansluta till data lagret, Välj **autentiseringen** och ange **användar namn**, **lösen ord**och/eller **autentiseringsuppgifter**.
5. Klicka på **Testa anslutning** för att testa anslutningen till data lagret.

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
