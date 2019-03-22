---
title: Kopiera data från ODBC-datakällor som använder Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från OData-källor till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
ms.openlocfilehash: f14c8f8ef9f0e59ac35dd7346bf37cc07f2cfb19
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58163862"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Kopieringsdata från och till ODBC-datalager med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-odbc-connector.md)
> * [Aktuell version](connector-odbc.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från och till ett ODBC-datalager. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från ODBC-källan till alla mottagarens datalager eller kopieras från valfri dataarkiv till ODBC-mottagare. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här ODBC-anslutningsprogram kopiering av data från/till **alla datalager för ODBC-kompatibel** med **grundläggande** eller **anonym** autentisering.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här ODBC-anslutningsprogram, måste du:

- Konfigurera en lokal Integration Runtime. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) nedan för information.
- Installera ODBC-drivrutinen för datalagret på Integration Runtime-datorn.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för ODBC-anslutningsprogram.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för ODBC-länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **Odbc** | Ja |
| connectionString | Anslutningssträngen undantaget del som autentiseringsuppgifter. Du kan ange anslutningssträngen med mönster som `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, eller använda systemet-DSN (Data Source Name) du har konfigurerat på Integration Runtime-dator med `"DSN=<name of the DSN on IR machine>;"` (du måste fortfarande ange credential-delen i den länkade tjänsten i enlighet med detta).<br>Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md).| Ja |
| authenticationType | Typ av autentisering som används för att ansluta till ODBC-datalager.<br/>Tillåtna värden är: **Grundläggande** och **anonym**. | Ja |
| Användarnamn | Ange användarnamnet om du använder grundläggande autentisering. | Nej |
| lösenord | Ange lösenord för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| credential | Åtkomst till autentiseringsuppgifter delen av anslutningssträngen som angetts i drivrutinsspecifika egenskapsvärdet format. Exempel: `"RefreshToken=<secret refresh token>;"`. Markera det här fältet som en SecureString. | Nej |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. En lokal Integration Runtime krävs enligt [krav](#prerequisites). |Ja |

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

För att kopiera data från/till datalagret för ODBC-kompatibel, ange typegenskapen på datauppsättningen till **RelationalTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **RelationalTable** | Ja |
| tableName | Namnet på tabellen i ODBC-datalager. | Nej för källan (om ”fråga” i aktivitetskälla har angetts).<br/>Ja för mottagare |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av ODBC-källan.

### <a name="odbc-as-source"></a>ODBC som källa

För att kopiera data från ODBC-kompatibel datalager, ange typ av datakälla i kopieringsaktiviteten till **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **RelationalSource** | Ja |
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

Om du vill kopiera data till datalagret för ODBC-kompatibel, ange Mottagartyp i kopieringsaktiviteten till **OdbcSink**. Följande egenskaper stöds i kopieringsaktiviteten **mottagare** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till: **OdbcSink** | Ja |
| writeBatchTimeout |Väntetid för batch insert-åtgärden ska slutföras innan tidsgränsen uppnås.<br/>Tillåtna värden är: timespan. Exempel: ”00: 30:00” (30 minuter). |Nej |
| WriteBatchSize |Infogar data i SQL-tabell när buffertstorleken når writeBatchSize.<br/>Tillåtna värden är: heltal (antal rader). |Nej (standardvärdet är 0 - automatiskt upptäckt) |
| preCopyScript |Ange en SQL-fråga för Kopieringsaktiviteten till att köra innan du skriver data till datalagret i varje körning. Du kan använda den här egenskapen för att rensa tidigare inlästa data. |Nej |

> [!NOTE]
> För ”writeBatchSize” om det inte har angetts (upptäcks automatiskt), identifierar Kopieringsaktivitet först om drivrutinen har stöd för batch-åtgärder och ange den till 10000 om den finns eller angetts till 1 om den inte. Om du uttryckligen ställa in värde än 0, Kopieringsaktivitet godkänner värdet och misslyckas vid körning om drivrutinen inte stöder batchåtgärder.

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

Du kan kopiera data från IBM Informix-databas med hjälp av generiska ODBC-anslutningsprogram.

Konfigurera en lokal Integration Runtime på en dator med åtkomst till ditt datalager. Integration Runtime använder ODBC-drivrutinen för Informix för att ansluta till datalagret. Därför installera drivrutinen om den redan inte är installerad på samma dator. Du kan till exempel använda drivrutinen ”IBM INFORMIX ODBC-drivrutin (64-bitars)”. Se [krav](#prerequisites) information.

Innan du använder Informix-källan i en Data Factory-lösning kan du kontrollera om Integration Runtime kan ansluta till datalagret med hjälp av anvisningarna i [Felsöka anslutningsproblem](#troubleshoot-connectivity-issues) avsnittet.

Skapa en ODBC-länkad tjänst för att länka ett IBM Informix-datalager till en Azure-datafabrik som visas i följande exempel:

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

Läs artikeln från början en detaljerad översikt över med hjälp av ODBC data lagras som datalager för källa/mottagare på en kopieringsåtgärd.

## <a name="microsoft-access-source"></a>Microsoft Access-källa

Du kan kopiera data från Microsoft Access-databas med hjälp av generiska ODBC-anslutningsprogram.

Konfigurera en lokal Integration Runtime på en dator med åtkomst till ditt datalager. Integration Runtime använder ODBC-drivrutinen för Microsoft Access för att ansluta till datalagret. Därför installera drivrutinen om den redan inte är installerad på samma dator. Se [krav](#prerequisites) information.

Innan du använder Microsoft Access-källan i en Data Factory-lösning kan du kontrollera om Integration Runtime kan ansluta till datalagret med hjälp av anvisningarna i [Felsöka anslutningsproblem](#troubleshoot-connectivity-issues) avsnittet.

Skapa en ODBC-länkad tjänst för att länka en Microsoft Access-databas till en Azure-datafabrik som visas i följande exempel:

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

Läs artikeln från början en detaljerad översikt över med hjälp av ODBC data lagras som datalager för källa/mottagare på en kopieringsåtgärd.

## <a name="sap-hana-sink"></a>SAP HANA-mottagare

>[!NOTE]
>För att kopiera data från SAP HANA-datalager, referera till intern [SAP HANA-anslutningsappen](connector-sap-hana.md). Följ den här instruktionen för att använda ODBC-anslutningsprogram för att kopiera data till SAP HANA. Observera de länkade tjänsterna för SAP HANA-anslutningsappen och ODBC-anslutningsprogram med annan typ kan därför inte får återanvändas.
>

Du kan kopiera data till SAP HANA-databas med den allmänna ODBC-anslutningsprogram.

Konfigurera en lokal Integration Runtime på en dator med åtkomst till ditt datalager. Integration Runtime använder ODBC-drivrutinen för SAP HANA för att ansluta till datalagret. Därför installera drivrutinen om den redan inte är installerad på samma dator. Se [krav](#prerequisites) information.

Innan du använder SAP HANA-mottagare i en Data Factory-lösning kan du kontrollera om Integration Runtime kan ansluta till datalagret med hjälp av anvisningarna i [Felsöka anslutningsproblem](#troubleshoot-connectivity-issues) avsnittet.

Skapa en ODBC-länkad tjänst för att länka ett SAP HANA-datalager till en Azure-datafabrik som visas i följande exempel:

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

Läs artikeln från början en detaljerad översikt över med hjälp av ODBC data lagras som datalager för källa/mottagare på en kopieringsåtgärd.

## <a name="troubleshoot-connectivity-issues"></a>Felsöka anslutningsproblem

Felsök problem med anslutningen genom att använda den **diagnostik** fliken **Konfigurationshanteraren för Integration Runtime**.

1. Starta **Konfigurationshanteraren för Integration Runtime**.
2. Växla till den **diagnostik** fliken.
3. Under avsnittet ”Testa anslutning” väljer du den **typ** lagra data (länkad tjänst).
4. Ange den **anslutningssträngen** som används för att ansluta till datalagret, Välj den **autentisering** och ange **användarnamn**, **lösenord**, och/eller **autentiseringsuppgifter**.
5. Klicka på **Testanslutning** att testa anslutningen till datalagret.

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
