---
title: Kopiera data från ODBC-källor med Azure Data Factory
description: Lär dig hur du kopierar data från OData-källor till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jingwang
ms.openlocfilehash: 6513cfc5432e969fc53aa72b075af194a064d178
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244373"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Kopiera data från och till ODBC-datalager med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-odbc-connector.md)
> * [Aktuell version](connector-odbc.md)

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från och till ett ODBC-datalager. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här ODBC-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från ODBC-källa till alla sink-datalager som stöds eller kopiera från ett källdatalager som stöds till ODBC-mottagare. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här ODBC-anslutningen stöder kopiering av data från/till **odbc-kompatibla datalager** med hjälp av **grundläggande** eller **anonym** autentisering. En **64-bitars ODBC-drivrutin** krävs.

## <a name="prerequisites"></a>Krav

Om du vill använda den här ODBC-kontakten måste du:

- Konfigurera en självvärderad integrationskörning. Mer information finns i artikeln Integration Runtime för [självvärd.](create-self-hosted-integration-runtime.md)
- Installera 64-bitars ODBC-drivrutinen för datalagret på integrationskörningsdatorn.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för ODBC-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för ODBC-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste ställas in på: **Odbc** | Ja |
| Connectionstring | Anslutningssträngen exklusive autentiseringsdelen. Du kan ange anslutningssträngen med mönster som `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, eller använda systemet DSN (Data `"DSN=<name of the DSN on IR machine>;"` Source Name) som du ställer in på integrationskörningsdatorn med (du måste fortfarande ange autentiseringsuppgiftersdelen i den länkade tjänsten därefter).<br>Du kan också placera ett lösenord i `password` Azure Key Vault och hämta konfigurationen ur anslutningssträngen.Se [Store-autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) med mer information.| Ja |
| authenticationType | Typ av autentisering som används för att ansluta till ODBC-datalagret.<br/>Tillåtna värden är: **Grundläggande** och **Anonym**. | Ja |
| userName | Ange användarnamn om du använder grundläggande autentisering. | Inga |
| password | Ange lösenord för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| credential | Åtkomstautentiseringsdelen av anslutningssträngen som anges i drivrutinsspecifikt egenskapsvärdeformat. Exempel: `"RefreshToken=<secret refresh token>;"`. Markera det här fältet som en SecureString. | Inga |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. En självvärdad integrationskörning krävs som nämns i [Förutsättningar](#prerequisites). |Ja |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av ODBC-datauppsättning.

Så här kopierar du data från/till ODBC-kompatibelt datalager:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste ställas in på: **OdbcTable** | Ja |
| tableName | Namnet på tabellen i ODBC-datalagret. | Nej för källan (om "fråga" i aktivitetskällan anges).<br/>Ja för diskbänk |

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

Om du `RelationalTable` använde maskinskriven datauppsättning stöds den fortfarande som den är, medan du föreslås använda den nya framöver.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av ODBC-källa.

### <a name="odbc-as-source"></a>ODBC som källa

Om du vill kopiera data från ODBC-kompatibelt datalager stöds följande egenskaper i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste ställas in på: **OdbcSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om "tableName" i datauppsättningen har angetts) |

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

Om du `RelationalSource` använde den maskinskrivna källan stöds den fortfarande som den är, medan du föreslås använda den nya framåt.

### <a name="odbc-as-sink"></a>ODBC som diskbänk

Om du vill kopiera data till ODBC-kompatibelt datalager anger du sink-typen i kopieringsaktiviteten till **OdbcSink**. Följande egenskaper stöds i avsnittet kopiera **aktivitetsmottagare:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen för kopieringsaktivitetsmottagaren måste ställas in på: **OdbcSink** | Ja |
| skriverBatchTimeout |Vänta på att batchinsatsen ska slutföras innan den har klarats upp.<br/>Tillåtna värden är: tidsspann. Exempel: "00:30:00" (30 minuter). |Inga |
| skriverBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize.<br/>Tillåtna värden är: heltal (antal rader). |Nej (standard är 0 - automatisk upptäckt) |
| preCopyScript |Ange en SQL-fråga för kopieringsaktivitet som ska köras innan data skrivs i datalagret i varje körning. Du kan använda den här egenskapen för att rensa de förinlästa data. |Inga |

> [!NOTE]
> För "writeBatchSize", om den inte är inställd (automatiskt upptäckt), identifierar kopieringsaktivitet först om drivrutinen stöder batchåtgärder och ställer in den på 10000 om den gör det, eller ställer in den på 1 om den inte gör det. Om du uttryckligen anger värdet annat än 0, kopierar aktiviteten uppfyller värdet och misslyckas vid körning om drivrutinen inte stöder batchåtgärder.

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

## <a name="sap-hana-sink"></a>SAP HANA sjunka

>[!NOTE]
>Om du vill kopiera data från SAP HANA-datalager läser du den inbyggda [SAP HANA-anslutningen](connector-sap-hana.md). Om du vill kopiera data till SAP HANA följer du den här instruktionen för att använda ODBC-kontakt. Observera att de länkade tjänsterna för SAP HANA-anslutning och ODBC-anslutning är med olika typ och kan därför inte återanvändas.
>

Du kan kopiera data till SAP HANA-databasen med den allmänna ODBC-anslutningen.

Konfigurera en självvärderad integrationskörningstid på en dator med åtkomst till ditt datalager. Integration Runtime använder ODBC-drivrutinen för SAP HANA för att ansluta till datalagret. Installera därför drivrutinen om den inte redan är installerad på samma dator. Mer information finns i avsnittet [Förutsättningar.](#prerequisites)

Innan du använder SAP HANA-diskhon i en Data Factory-lösning kontrollerar du om integrationskörningen kan ansluta till datalagret med hjälp av instruktioner i avsnittet [Felsöka anslutningsproblem.](#troubleshoot-connectivity-issues)

Skapa en ODBC-länkad tjänst för att länka ett SAP HANA-datalager till en Azure-datafabrik enligt följande exempel:

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

Läs artikeln från början för en detaljerad översikt över hur du använder ODBC-datalager som käll-/sinkdatalager i en kopiering.

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).


## <a name="troubleshoot-connectivity-issues"></a>Felsök anslutningsproblem

Om du vill felsöka anslutningsproblem använder du fliken **Diagnostik** **i Configuration Manager för Integration Runtime**.

1. Starta **Konfigurationshanteraren för integreringskörningstid**.
2. Växla till fliken **Diagnostik.**
3. Under avsnittet "Testanslutning" väljer du **typ** av datalager (länkad tjänst).
4. Ange **den anslutningssträng** som används för att ansluta till datalagret, välj **autentisering** och ange **användarnamn,** **lösenord**och/eller **autentiseringsuppgifter**.
5. Klicka på **Testa anslutning** om du vill testa anslutningen till datalagret.

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
