---
title: Kopiera data från Phoenix med Azure Data Factory
description: Lär dig hur du kopierar data från Phoenix till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.openlocfilehash: d8f63984a5ad3717b470657aba02224794122cd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930842"
---
# <a name="copy-data-from-phoenix-using-azure-data-factory"></a>Kopiera data från Phoenix med Azure Data Factory 

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från Phoenix. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Phoenix-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Phoenix till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Azure Data Factory tillhandahåller en inbyggd drivrutin för att aktivera anslutning, därför behöver du inte installera någon drivrutin manuellt med den här anslutningen.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för Phoenix-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Phoenix-länkade tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **Phoenix** | Ja |
| värd | IP-adressen eller värdnamnet för Phoenix-servern. (det vill än 192.168.222.160)  | Ja |
| port | TCP-porten som Phoenix-servern använder för att lyssna efter klientanslutningar. Standardvärdet är 8765. Om du ansluter till Azure HDInsights anger du porten som 443. | Inga |
| httpPath (på ett sätt) | Den partiella URL som motsvarar Phoenix-servern. (det vill vara/ gateway/sandbox/phoenix/version). Ange `/hbasephoenix0` om du använder HDInsights-klustret.  | Inga |
| authenticationType | Autentiseringsmekanismen som används för att ansluta till Phoenix-servern. <br/>Tillåtna värden är: **Anonym**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Ja |
| användarnamn | Användarnamnet som används för att ansluta till Phoenix-servern.  | Inga |
| password | Lösenordet som motsvarar användarnamnet. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |
| enableSsl enableSsl enableSsl enableS | Anger om anslutningarna till servern är krypterade med SSL. Standardvärdet är false.  | Inga |
| betroddaCertPath | Den fullständiga sökvägen till PEM-filen som innehåller betrodda CERTIFIKATUTfärdare för att verifiera servern när du ansluter via SSL. Den här egenskapen kan bara ställas in när ssl används på självvärdbaserad IR. The default value is the cacerts.pem file installed with the IR.  | Inga |
| användaSystemTrustStore | Anger om ett CERTIFIKAT FRÅN systemförtroendearkivet eller från en angiven PEM-fil ska användas. Standardvärdet är false.  | Inga |
| allowHostNameCNMismatch | Anger om ett CERTIFIKATUTFÄRDAT SSL-certifikatnamn ska behövas för att matcha serverns värdnamn när du ansluter via SSL. Standardvärdet är false.  | Inga |
| tillåtVälsignedServerCert | Anger om självsignerade certifikat ska tillåtas från servern. Standardvärdet är false.  | Inga |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om det inte anges används standardkörningen för Azure Integration. |Inga |

>[!NOTE]
>Om klustret inte stöder klibbiga session `/hbasephoenix0` `/hbasephoenix`t.ex.

**Exempel:**

```json
{
    "name": "PhoenixLinkedService",
    "properties": {
        "type": "Phoenix",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "/hbasephoenix0",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Phoenix-datauppsättning.

Om du vill kopiera data från Phoenix anger du egenskapen Type för datauppsättningen till **PhoenixObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste ställas in på: **PhoenixObject** | Ja |
| Schemat | Namnet på schemat. |Nej (om "fråga" i aktivitetskällan har angetts)  |
| tabell | Tabellens namn. |Nej (om "fråga" i aktivitetskällan har angetts)  |
| tableName | Namn på tabellen med schema. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` `table` och för ny arbetsbelastning. | Nej (om "fråga" i aktivitetskällan har angetts) |

**Exempel**

```json
{
    "name": "PhoenixDataset",
    "properties": {
        "type": "PhoenixObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Phoenix linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Phoenix-källan.

### <a name="phoenix-as-source"></a>Phoenix som källa

Om du vill kopiera data från Phoenix anger du källtypen i kopieringsaktiviteten till **PhoenixSource**. Följande egenskaper stöds i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till: **PhoenixSource** | Ja |
| DocumentDB | Använd den anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om "tableName" i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromPhoenix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Phoenix input dataset name>",
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
                "type": "PhoenixSource",
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
