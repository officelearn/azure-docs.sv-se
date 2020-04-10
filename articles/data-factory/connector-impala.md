---
title: Kopiera data från Impala med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från Impala till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en pipeline för datafabriker.
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
ms.openlocfilehash: 3c7690390936a05dd472796eb6f50f582f652e35
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990857"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Kopiera data från Impala med hjälp av Azure Data Factory

I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att kopiera data från Impala. Den bygger på översiktsartikeln [Kopiera aktivitet](copy-activity-overview.md) som ger en allmän översikt över kopieringsaktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Impala-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Impala till alla sink-datalager som stöds. En lista över datalager som stöds som källor eller sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Data Factory tillhandahåller en inbyggd drivrutin för anslutning. Därför behöver du inte installera en drivrutin manuellt för att använda den här anslutningen.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för Impala-kopplingen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Impala-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **Impala**. | Ja |
| värd | IP-adressen eller värdnamnet för Impala-servern (det vill vara 192.168.222.160).  | Ja |
| port | TCP-porten som Impala-servern använder för att lyssna efter klientanslutningar. Standardvärdet är 21050.  | Inga |
| authenticationType | Den autentiseringstyp som ska användas. <br/>Tillåtna värden är **Anonym**, **SASLUsername**och **UsernameAndPassword**. | Ja |
| användarnamn | Användarnamnet som används för att komma åt Impala-servern. Standardvärdet är anonymt när du använder SASLUsername.  | Inga |
| password | Lösenordet som motsvarar användarnamnet när du använder UsernameAndPassword. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| enableSsl enableSsl enableSsl enableS | Anger om anslutningarna till servern krypteras med hjälp av TLS. Standardvärdet är **falskt**.  | Inga |
| betroddaCertPath | Den fullständiga sökvägen till PEM-filen som innehåller betrodda certifikatutfärdare som används för att verifiera servern när du ansluter via TLS. Den här egenskapen kan bara ställas in när du använder TLS på självvärdbaserad integrationskörning. Standardvärdet är filen cacerts.pem som är installerad med integrationskörningen.  | Inga |
| användaSystemTrustStore | Anger om ett CERTIFIKAT FRÅN systemförtroendearkivet eller från en angiven PEM-fil ska användas. Standardvärdet är **falskt**.  | Inga |
| allowHostNameCNMismatch | Anger om ett certifikatutfärdat TLS/SSL-certifikatnamn ska krävas för att matcha serverns värdnamn när du ansluter via TLS. Standardvärdet är **falskt**.  | Inga |
| tillåtVälsignedServerCert | Anger om självsignerade certifikat ska tillåtas från servern. Standardvärdet är **falskt**.  | Inga |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om det inte anges används standardkörningen för Azure Integration. |Inga |

**Exempel:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Impala-datauppsättningen.

Om du vill kopiera data från Impala anger du egenskapen type för datauppsättningen till **ImpalaObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste ställas in på: **ImpalaObject** | Ja |
| Schemat | Namnet på schemat. |Nej (om "fråga" i aktivitetskällan har angetts)  |
| tabell | Tabellens namn. |Nej (om "fråga" i aktivitetskällan har angetts)  |
| tableName | Namn på tabellen med schema. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` `table` och för ny arbetsbelastning. | Nej (om "fråga" i aktivitetskällan har angetts) |

**Exempel**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av impala-källtypen.

### <a name="impala-as-a-source-type"></a>Impala som källtyp

Om du vill kopiera data från Impala anger du källtypen i kopieringsaktiviteten till **ImpalaSource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet.**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till **ImpalaSource**. | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`. | Nej (om "tableName" i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
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
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Data Factory finns i [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
