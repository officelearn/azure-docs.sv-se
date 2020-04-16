---
title: Kopiera data från Microsoft Access-källor
description: Lär dig hur du kopierar data från Microsoft Access-källor till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/27/2019
ms.openlocfilehash: fc2179efcda4ee11dda3b424b16a072a2bb2c26e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418192"
---
# <a name="copy-data-from-and-to-microsoft-access-data-stores-using-azure-data-factory"></a>Kopiera data från och till Microsoft Access-datalager med Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från ett Microsoft Access-datalager. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Microsoft Access-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Microsoft Access-källa till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="prerequisites"></a>Krav

Om du vill använda den här Microsoft Access-anslutningen måste du:

- Konfigurera en självvärderad integrationskörning. Mer information finns i artikeln Integration Runtime för [självvärd.](create-self-hosted-integration-runtime.md)
- Installera Microsoft Access ODBC-drivrutinen för datalagret på integrationskörningsdatorn.

>[!NOTE]
>Microsoft Access 2016-versionen av ODBC-drivrutinen fungerar inte med den här kopplingen. Använd drivrutinsversionen 2013 eller 2010 i stället.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Microsoft Access-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten i Microsoft Access:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **MicrosoftAccess** | Ja |
| Connectionstring | ODBC-anslutningssträngen exklusive autentiseringsuppgifter. Du kan ange anslutningssträngen eller använda systemet DSN (Data Source Name) som du ställer in på integrationskörningsdatorn (du måste fortfarande ange autentiseringsuppgifterna i den länkade tjänsten därefter).<br> Du kan också placera ett lösenord i `password` Azure Key Vault och hämta konfigurationen ur anslutningssträngen.Se [Store-autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) med mer information.| Ja |
| authenticationType | Typ av autentisering som används för att ansluta till Microsoft Access-datalagret.<br/>Tillåtna värden är: **Grundläggande** och **Anonym**. | Ja |
| userName | Ange användarnamn om du använder grundläggande autentisering. | Inga |
| password | Ange lösenord för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| credential | Åtkomstautentiseringsdelen av anslutningssträngen som anges i drivrutinsspecifikt egenskapsvärdeformat. Markera det här fältet som en SecureString. | Inga |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. En självvärdad integrationskörning krävs som nämns i [Förutsättningar](#prerequisites). |Ja |

**Exempel:**

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Microsoft Access",
        "typeProperties": {
            "connectionString": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Microsoft Access-datauppsättningen.

Så här kopierar du data från Microsoft Access och följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste anges till: **MicrosoftAccessTable** | Ja |
| tableName | Namnet på tabellen i Microsoft Access. | Nej för källan (om "fråga" i aktivitetskällan anges).<br/>Ja för diskbänk |

**Exempel**

```json
{
    "name": "MicrosoftAccessDataset",
    "properties": {
        "type": "MicrosoftAccessTable",
        "linkedServiceName": {
            "referenceName": "<Microsoft Access linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Microsoft Access-källa.

### <a name="microsoft-access-as-source"></a>Microsoft Access som källa

Om du vill kopiera data från Microsoft Access-kompatibelt datalager stöds följande egenskaper i avsnittet kopiera **aktivitetskälla:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till: **MicrosoftAccessSource** | Ja |
| DocumentDB | Använd den anpassade frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om "tableName" i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromMicrosoftAccess",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Microsoft Access input dataset name>",
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
                "type": "MicrosoftAccessSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
