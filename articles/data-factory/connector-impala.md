---
title: "Kopiera data från Apache Impala med hjälp av Azure Data Factory (Beta) | Microsoft Docs"
description: "Lär dig hur du kopierar data från Apache Impala till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen."
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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: 4766e19b1823bdb737be8a90b3e2e2bfe4e48ab9
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-apache-impala-using-azure-data-factory-beta"></a>Kopiera data från Apache Impala med hjälp av Azure Data Factory (Beta)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från Apache Impala. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Kopieringsaktiviteten i V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Den här anslutningen är för närvarande i betaversion. Du kan prova och ge feedback. Använd den inte i produktionsmiljöer.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Apache Impala till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Azure Data Factory innehåller en inbyggd drivrutin att tillåta anslutningar, måste du därför inte att manuellt installera en drivrutin med den här anslutningen.

## <a name="getting-started"></a>Komma igång

Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter i Apache Impala koppling.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Apache Impala länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **Apache Impala** | Ja |
| värden | IP-adressen eller värdnamnet namnet på servern Apache Impala. (som är 192.168.222.160)  | Ja |
| port | TCP-porten som använder Apache Impala servern att lyssna efter anslutningar. Standardvärdet är 21050.  | Nej |
| AuthenticationType | Autentiseringstypen som ska användas. <br/>Tillåtna värden är: **anonym**, **SASLUsername**, **UsernameAndPassword** | Ja |
| användarnamn | Användarnamnet som används för åtkomst till Apache Impala-servern. Standardvärdet är anonym när du använder SASLUsername.  | Nej |
| lösenord | Det lösenord som motsvarar namnet när du använder UsernameAndPassword. Du kan välja att markera det här fältet som en SecureString att lagra den säkert i ADF eller lagra lösenord i Azure Key Vault och låta kopieringsaktiviteten hämtar därifrån vid kopiering av data - mer information från [lagra autentiseringsuppgifter i Nyckelvalvet](store-credentials-in-key-vault.md). | Nej |
| enableSsl | Anger om anslutningar till servern krypteras med SSL. Standardvärdet är false.  | Nej |
| trustedCertPath | Den fullständiga sökvägen till PEM-filen som innehåller certifikat för betrodda Certifikatutfärdare för att verifiera servern när du ansluter via SSL. Den här egenskapen kan bara anges när du använder SSL på själva värdbaserade IR. Standardvärdet är filen cacerts.pem installeras med IR.  | Nej |
| useSystemTrustStore | Anger om du vill använda ett certifikat från arkivet med betrodda system eller från en angiven PEM-fil. Standardvärdet är false.  | Nej |
| allowHostNameCNMismatch | Anger om en Certifikatutfärdare som utfärdade SSL certifikatets namn att matcha värdnamn för servern när du ansluter via SSL. Standardvärdet är false.  | Nej |
| allowSelfSignedServerCert | Anger om självsignerade certifikat från servern. Standardvärdet är false.  | Nej |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Self-hosted integrering Runtime eller Azure Integration Runtime (om datalager är offentligt tillgänglig). Om inget anges används standard-Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "Apache ImpalaLinkedService",
    "properties": {
        "type": "Apache Impala",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Apache Impala dataset.

Ange typegenskapen för dataset för att kopiera data från Apache Impala, **Apache ImpalaObject**. Det finns ingen ytterligare typspecifika egenskap i den här typen av datauppsättningen.

**Exempel**

```json
{
    "name": "Apache ImpalaDataset",
    "properties": {
        "type": "Apache ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Apache Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Apache Impala källa.

### <a name="apache-impalasource-as-source"></a>Apache ImpalaSource som källa

Om du vill kopiera data från Apache Impala, anger du datakällan i kopieringsaktiviteten till **Apache ImpalaSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **Apache ImpalaSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Ja |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromApache Impala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Apache Impala input dataset name>",
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
                "type": "Apache ImpalaSource",
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
En lista över data lagras stöds datalager i Azure Data Factory, finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).
