---
title: "Kopiera data från HBase med hjälp av Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från HBase till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen."
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
ms.date: 02/28/2018
ms.author: jingwang
ms.openlocfilehash: 543d0ec5d0c94b793b1e825d44356039b366908a
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="copy-data-from-hbase-using-azure-data-factory"></a>Kopiera data från HBase med hjälp av Azure Data Factory 

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från HBase. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Kopieringsaktiviteten i V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från HBase till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Azure Data Factory innehåller en inbyggd drivrutin att tillåta anslutningar, måste du därför inte att manuellt installera en drivrutin med den här anslutningen.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till HBase-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för HBase länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **HBase** | Ja |
| värd | IP-adressen eller värdnamnet namnet på HBase-server. (i.e. 192.168.222.160, [clustername].azurehdinsight.net)  | Ja |
| port | TCP-porten som HBase-instans som används för att lyssna efter anslutningar. Standardvärdet är 9090.  | Nej |
| httpPath | Partiell URL som motsvarar HBase-server. (i.e. /gateway/sandbox/hbase/version)  | Nej |
| AuthenticationType | Autentiseringsmekanism för att ansluta till HBase-servern. <br/>Tillåtna värden är: **anonym**, **grundläggande** | Ja |
| användarnamn | Användarnamnet som används för att ansluta till HBase-instans.  | Nej |
| lösenord | Lösenordet för användarnamnet. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| enableSsl | Anger om anslutningar till servern krypteras med SSL. Standardvärdet är false.  | Nej |
| trustedCertPath | Den fullständiga sökvägen till PEM-filen som innehåller certifikat för betrodda Certifikatutfärdare för att verifiera servern när du ansluter via SSL. Den här egenskapen kan bara anges när du använder SSL på själva värdbaserade IR. Standardvärdet är filen cacerts.pem installeras med IR.  | Nej |
| allowHostNameCNMismatch | Anger om en Certifikatutfärdare som utfärdade SSL certifikatets namn att matcha värdnamn för servern när du ansluter via SSL. Standardvärdet är false.  | Nej |
| allowSelfSignedServerCert | Anger om självsignerade certifikat från servern. Standardvärdet är false.  | Nej |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Self-hosted integrering Runtime eller Azure Integration Runtime (om datalager är offentligt tillgänglig). Om inget anges används standard-Azure Integration Runtime. |Nej |

**Exempel för HDInsights HBase:**

```json
{
    "name": "HBaseLinkedService",
    "properties": {
        "type": "HBase",
        "typeProperties": {
            "host" : "<cluster name>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "<e.g. hbaserest>",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "enableSsl" : true
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel för allmän HBase:**

```json
{
    "name": "HBaseLinkedService",
    "properties": {
        "type": "HBase",
        "typeProperties": {
            "host" : "<host e.g. 192.168.222.160>",
            "port" : "<port>",
            "httpPath" : "<e.g. /gateway/sandbox/hbase/version>",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "enableSsl" : true,
            "trustedCertPath" : "<trustedCertPath>",
            "allowHostNameCNMismatch" : true,
            "allowSelfSignedServerCert" : true
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av HBase dataset.

Ange typegenskapen för dataset för att kopiera data från HBase, **HBaseObject**. Det finns ingen ytterligare typspecifika egenskap i den här typen av datauppsättningen.

**Exempel**

```json
{
    "name": "HBaseDataset",
    "properties": {
        "type": "HBaseObject",
        "linkedServiceName": {
            "referenceName": "<HBase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av HBase-källa.

### <a name="hbasesource-as-source"></a>HBaseSource som källa

Om du vill kopiera data från HBase, anger du källa i kopieringsaktiviteten till **HBaseSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **HBaseSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Ja |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromHBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HBase input dataset name>",
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
                "type": "HBaseSource",
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
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).
