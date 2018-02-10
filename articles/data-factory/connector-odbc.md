---
title: "Kopiera data från ODBC-källor med hjälp av Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från OData-datakällor till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen."
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: ab29338b5ce2090166e75f3860744562aa9f6b01
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Kopieringsdata från och till ODBC-datalager med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-odbc-connector.md)
> * [Version 2 – förhandsversion](connector-odbc.md)

Den här artikeln beskriver hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från och till en ODBC-datalagret. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [ODBC-anslutning i V1](v1/data-factory-odata-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från ODBC-datakällan till alla stöds sink-datalagret eller kopiera från alla datalager stöds källa till ODBC-mottagare. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt denna ODBC-anslutning har stöd för kopiering av data från/till **alla ODBC-kompatibel datalager** med **grundläggande** eller **anonym** autentisering.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda denna ODBC-anslutning måste du:

- Ställ in en Self-hosted integrering Runtime. Se [Self-hosted integrering Runtime](create-self-hosted-integration-runtime.md) artikeln för information.
- Installera ODBC-drivrutinen för datalagret på Integration Runtime-datorn.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till ODBC-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för ODBC länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **Odbc** | Ja |
| connectionString | Anslutningssträngen undantaget del av autentiseringsuppgifter. Du kan ange anslutningssträngen med mönster som `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, eller använda systemets DSN (Data Source Name) som du ställer in på Integration Runtime-dator med `"DSN=<name of the DSN on IR machine>;"` (du måste fortfarande ange autentiseringsuppgifter del i den länkade tjänsten därefter).<br>Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md).| Ja |
| AuthenticationType | Typ av autentisering som används för att ansluta till ODBC-datalagret.<br/>Tillåtna värden är: **grundläggande** och **anonym**. | Ja |
| userName | Ange användarnamnet om du använder grundläggande autentisering. | Nej |
| lösenord | Ange lösenordet för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| autentiseringsuppgifter | Åtkomst autentiseringsuppgifter del av den angivna anslutningssträngen i drivrutinsspecifika egenskapsvärdet format. Exempel: `"RefreshToken=<secret refresh token>;"`. Markera det här fältet som en SecureString. | Nej |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. En Self-hosted integrering Runtime krävs enligt [krav](#prerequisites). |Ja |

**Exempel 1: använder grundläggande autentisering**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
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

**Exempel 2: använder anonym autentisering**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av ODBC-datauppsättningen.

Ange typegenskapen för dataset för att kopiera data från/till datalagret för ODBC-kompatibel, **RelationalTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **RelationalTable** | Ja |
| tableName | Namnet på tabellen i ODBC-datakällan. | Nej för källa (om ”fråga” i aktivitetskälla har angetts).<br/>Ja för sink |

**Exempel**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "RelationalTable",
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

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av ODBC-datakällan.

### <a name="odbc-as-source"></a>ODBC som källa

Om du vill kopiera data från datalagret för ODBC-kompatibel, anger du källa i kopieringsaktiviteten till **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **RelationalSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om ”tabellnamn” i datamängden har angetts) |

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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="odbc-as-sink"></a>ODBC som mottagare

Om du vill kopiera data till datalagret för ODBC-kompatibel, anger du sink i kopieringsaktiviteten till **OdbcSink**. Följande egenskaper stöds i kopieringsaktiviteten **sink** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för kopiera aktivitet sink måste anges till: **OdbcSink** | Ja |
| writeBatchTimeout |Vänta tills batch insert-åtgärden ska slutföras innan tidsgränsen uppnås.<br/>Tillåtna värden är: timespan. Exempel ”: 00: 30:00” (30 minuter). |Nej |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize.<br/>Tillåtna värden är: heltal (antalet rader). |Nej (standardvärdet är 0 - automatisk upptäckt) |
| preCopyScript |Ange en SQL-fråga för Kopieringsaktiviteten ska köras innan skrivningen av data i datalagret i varje körning. Du kan använda den här egenskapen för att rensa förinstallerade data. |Nej |

> [!NOTE]
> För ”writeBatchSize” om det inte har angetts (upptäcks automatiskt), identifierar kopieringsaktiviteten först om drivrutinen har stöd för batchåtgärder, Ställ in den på 10000 om den gör och anger värdet 1 om den inte. Om du uttryckligen ställa in värde än 0 kopieringsaktiviteten godkänner värdet och misslyckas under körning om drivrutinen inte stöd för batchåtgärder.

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

## <a name="ibm-informix-source"></a>IBM Informix-källa

Du kan kopiera data från IBM Informix-databas med hjälp av den generiska ODBC-anslutningen.

Ställ in ett Self-hosted integrering för körning på en dator med tillgång till ditt datalager. Integration Runtime använder ODBC-drivrutin för Informix för att ansluta till datalagret. Därför installera drivrutinen om den redan inte är installerad på samma dator. Du kan till exempel använda drivrutinen ”IBM INFORMIX ODBC-drivrutinen (64-bitars)”. Se [krav](#prerequisites) information.

Innan du använder Informix-källan i en Data Factory-lösning, kontrollera om integrering körning kan ansluta till datalagret med hjälp av anvisningarna i [felsökning av anslutningsproblem](#troubleshoot-connectivity-issues) avsnitt.

Skapa en ODBC-länkad tjänst om du vill länka en IBM Informix-databas till en Azure data factory som visas i följande exempel:

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<Informix connection string or DSN>"
            },
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

Läs artikeln från början en detaljerad översikt av med hjälp av ODBC data lagras som källor/mottagare datalager i en kopieringsåtgärd.

## <a name="microsoft-access-source"></a>Microsoft Access-källa

Du kan kopiera data från Microsoft Access-databas med hjälp av den generiska ODBC-anslutningen.

Ställ in ett Self-hosted integrering för körning på en dator med tillgång till ditt datalager. Integration Runtime använder ODBC-drivrutinen för Microsoft Access för att ansluta till datalagret. Därför installera drivrutinen om den redan inte är installerad på samma dator. Se [krav](#prerequisites) information.

Innan du använder Microsoft Access-källan i en Data Factory-lösning, kontrollera om integrering körning kan ansluta till datalagret med hjälp av anvisningarna i [felsökning av anslutningsproblem](#troubleshoot-connectivity-issues) avsnitt.

Skapa en ODBC-länkad tjänst om du vill länka en Microsoft Access-databas till en Azure data factory som visas i följande exempel:

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
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

Läs artikeln från början en detaljerad översikt av med hjälp av ODBC data lagras som källor/mottagare datalager i en kopieringsåtgärd.

## <a name="ge-historian-source"></a>GE Historian källa

Du kan kopiera data från GE Historian använda allmän ODBC-anslutningen.

Ställ in ett Self-hosted integrering för körning på en dator med tillgång till ditt datalager. Integration Runtime använder ODBC-drivrutin för GE Historian för att ansluta till datalagret. Därför installera drivrutinen om den redan inte är installerad på samma dator. Se [krav](#prerequisites) information.

Innan du använder GE Historian-källan i en Data Factory-lösning, kontrollera om integrering körning kan ansluta till datalagret med hjälp av anvisningarna i [felsökning av anslutningsproblem](#troubleshoot-connectivity-issues) avsnitt.

Skapa en ODBC-länkad tjänst om du vill länka en Microsoft Access-databas till en Azure data factory som visas i följande exempel:

```json
{
    "name": "HistorianLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<GE Historian store connection string or DSN>"
            },
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

Läs artikeln från början en detaljerad översikt av med hjälp av ODBC data lagras som källor/mottagare datalager i en kopieringsåtgärd.

## <a name="sap-hana-sink"></a>SAP HANA sink

>[!NOTE]
>Om du vill kopiera data från datalagret för SAP HANA, referera till intern [SAP HANA connector](connector-sap-hana.md). Följ den här instruktionen att använda ODBC-kopplingen för att kopiera data till SAP HANA. Observera de länkade tjänsterna för SAP HANA-koppling och ODBC-anslutningen med annan typ, kan vilket inte återanvändas.
>

Du kan kopiera data till SAP HANA-databas med den allmänna ODBC-anslutningen.

Ställ in ett Self-hosted integrering för körning på en dator med tillgång till ditt datalager. Integration Runtime använder ODBC-drivrutinen för SAP HANA för att ansluta till datalagret. Därför installera drivrutinen om den redan inte är installerad på samma dator. Se [krav](#prerequisites) information.

Innan du använder SAP HANA-mottagare i en Data Factory-lösning, kontrollera om integrering körning kan ansluta till datalagret med hjälp av anvisningarna i [felsökning av anslutningsproblem](#troubleshoot-connectivity-issues) avsnitt.

Skapa en ODBC-länkad tjänst om du vill länka en SAP HANA-databas till en Azure data factory som visas i följande exempel:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015"
            },
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

Läs artikeln från början en detaljerad översikt av med hjälp av ODBC data lagras som källor/mottagare datalager i en kopieringsåtgärd.

## <a name="troubleshoot-connectivity-issues"></a>Felsökning av problem med nätverksanslutningen

Felsökning av anslutningsproblem med använder den **diagnostik** fliken **integrering Runtime Configuration Manager**.

1. Starta **integrering Runtime Configuration Manager**.
2. Växla till den **diagnostik** fliken.
3. Under avsnittet ”Testa anslutning” Välj den **typen** av data lagras (länkade tjänst).
4. Ange den **anslutningssträngen** som används för att ansluta till datalagret, Välj den **autentisering** och ange **användarnamn**, **lösenord**, och/eller **autentiseringsuppgifter**.
5. Klicka på **Anslutningstestet** att testa anslutningen till datalagret.

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).