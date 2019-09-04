---
title: Kopiera data från Hive med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Hive till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 40f97c3b31a7e49c9a5ecc790e3cc762572ecaa3
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276358"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Kopiera data från Hive med Azure Data Factory 

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från Hive. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Hive till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Azure Data Factory tillhandahåller en inbyggd drivrutin för att aktivera anslutning, måste du därför inte att manuellt installera en drivrutin som använder den här anslutningen.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Hive-anslutningen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Hive länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type måste anges till: **Hive** | Ja |
| host | IP-adressen eller värdnamnet namnet på Hive-servern, avgränsade med ””; för flera värdar (bara när serviceDiscoveryMode är aktivera).  | Ja |
| port | TCP-porten som Hive-servern använder för att lyssna efter klientanslutningar. Ange porten som 443 om du ansluter till Azure HDInsights. | Ja |
| serverType | Typ av Hive-servern. <br/>Tillåtna värden är: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Nej |
| thriftTransportProtocol | Transportprotokollet ska användas i Thrift-lagret. <br/>Tillåtna värden är: **Binary**, **SASL**, **HTTP** | Nej |
| authenticationType | Den autentiseringsmetod som används för att få åtkomst till Hive-servern. <br/>Tillåtna värden är: **Anonym**, **username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Ja |
| serviceDiscoveryMode | TRUE om du vill ange med tjänsten ZooKeeper false inte.  | Nej |
| zooKeeperNameSpace | Namnområdet på ZooKeeper under vilken Hive Server 2 noder har lagts till.  | Nej |
| useNativeQuery | Anger om drivrutinen använder HiveQL internfrågor eller konverterar dem till ett motsvarande formulär i HiveQL.  | Nej |
| username | Användarnamnet som används för att få åtkomst till Hive-Server.  | Nej |
| password | Lösenordet för användaren. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| httpPath | Partiell URL som motsvarar Hive-servern.  | Nej |
| enableSsl | Anger om anslutningar till servern krypteras med SSL. Standardvärdet är FALSKT.  | Nej |
| trustedCertPath | Den fullständiga sökvägen till filen .pem som innehåller certifikat från betrodda Certifikatutfärdare för att verifiera servern när du ansluter via SSL. Den här egenskapen kan bara anges när du använder SSL på lokal IR. Standardvärdet är filen cacerts.pem installerad med i IR.  | Nej |
| useSystemTrustStore | Anger om du vill använda ett CA-certifikat från arkivet med betrodda system eller från en angiven PEM-fil. Standardvärdet är FALSKT.  | Nej |
| allowHostNameCNMismatch | Anger om en CA-utfärdade SSL-certifikatnamnet att matcha värdnamnet för servern när du ansluter via SSL. Standardvärdet är FALSKT.  | Nej |
| allowSelfSignedServerCert | Anger om du vill tillåta självsignerade certifikat från servern. Standardvärdet är FALSKT.  | Nej |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Läs mer från avsnittet [krav](#prerequisites) . Om den inte anges används standard Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Hive-datauppsättningen.

Om du vill kopiera data från Hive, ange typegenskapen på datauppsättningen till **HiveObject**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Data uppsättningens typ-egenskap måste anges till: **HiveObject** | Ja |
| schema | Schemats namn. |Nej (om ”query” i aktivitetskälla har angetts)  |
| table | Namnet på tabellen. |Nej (om ”query” i aktivitetskälla har angetts)  |
| tableName | Namnet på tabellen inklusive schema del. Den här egenskapen stöds för bakåtkompatibilitet. Använd `schema` och`table`för ny arbets belastning. | Nej (om ”query” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Hive-källa.

### <a name="hivesource-as-source"></a>HiveSource som källa

Om du vill kopiera data från Hive, ange typ av datakälla i kopieringsaktiviteten till **HiveSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Typ egenskapen för kopierings aktivitets källan måste anges till: **HiveSource** | Ja |
| query | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Nej (om ”tableName” i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
