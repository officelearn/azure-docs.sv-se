---
title: Kopiera data från Impala med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Impala till stöds sink datalager med hjälp av en kopia aktivitet i en data factory-pipelinen.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: 366d0945bfac8546aa757648b6f797c2605a43ea
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045875"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Kopiera data från Impala med hjälp av Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktiviteten i Azure Data Factory för att kopiera data från Impala. Den bygger på den [Kopieringsaktiviteten översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över aktiviteten kopia.

> [!IMPORTANT]
> Den här anslutningen är för närvarande under förhandsgranskning. Du kan prova och ge feedback. Om du vill skapa ett beroende på anslutningsappar som är i förhandsversion i din lösning kan du kontakta [Azure-supporten](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Impala till alla stöds sink-datalagret. En lista över datalager som stöds som datakällor eller sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

 Data Factory innehåller en inbyggd drivrutin att tillåta anslutningar. Därför behöver du inte manuellt installera en drivrutin om du vill använda den här anslutningen.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Impala kopplingen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Impala länkade tjänsten.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till **Impala**. | Ja |
| värd | IP-adressen eller värdnamnet namnet på servern Impala (det vill säga 192.168.222.160).  | Ja |
| port | TCP-porten som används av Impala-server för att lyssna efter anslutningar. Standardvärdet är 21050.  | Nej |
| authenticationType | Autentiseringstypen som ska användas. <br/>Tillåtna värden är **anonym**, **SASLUsername**, och **UsernameAndPassword**. | Ja |
| användarnamn | Användarnamnet som används för åtkomst till Impala-servern. Standardvärdet är anonym när du använder SASLUsername.  | Nej |
| lösenord | Det lösenord som motsvarar namnet när du använder UsernameAndPassword. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |
| enableSsl | Anger om anslutningar till servern krypteras med SSL. Standardvärdet är **FALSKT**.  | Nej |
| trustedCertPath | Den fullständiga sökvägen till PEM-filen som innehåller betrodda certifikatutfärdare som används för att verifiera servern när du ansluter via SSL. Den här egenskapen kan anges endast när du använder SSL på Self-hosted integrering Runtime. Standardvärdet är filen cacerts.pem installerades med integration körningsmiljön.  | Nej |
| useSystemTrustStore | Anger om du vill använda ett certifikat från arkivet med betrodda system eller från en angiven PEM-fil. Standardvärdet är **FALSKT**.  | Nej |
| allowHostNameCNMismatch | Anger om en Certifikatutfärdare som utfärdade SSL certifikatets namn att matcha värdnamn för servern när du ansluter via SSL. Standardvärdet är **FALSKT**.  | Nej |
| allowSelfSignedServerCert | Anger om självsignerade certifikat från servern. Standardvärdet är **FALSKT**.  | Nej |
| connectVia | Den [integrering runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Self-hosted integrering Runtime eller Azure Integration Runtime (om datalager är offentligt tillgänglig). Om inget anges används standard-Azure Integration Runtime. |Nej |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Impala dataset.

Ange typegenskapen för dataset för att kopiera data från Impala, **ImpalaObject**. Det finns ingen ytterligare typspecifika egenskap i den här typen av datauppsättningen.

**Exempel**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av typen av datakälla Impala.

### <a name="impala-as-a-source-type"></a>Impala som typ av datakälla

Om du vill kopiera data från Impala, anger du datakällan i kopieringsaktiviteten till **ImpalaSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till **ImpalaSource**. | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Ett exempel är `"SELECT * FROM MyTable"`. | Ja |

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

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).
