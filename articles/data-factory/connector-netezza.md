---
title: Kopiera data från Netezza med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Netezza till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 8e2b65f83395c9e8991338864d2037d0572dd269
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078014"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Kopiera data från Netezza med hjälp av Azure Data Factory 

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från Netezza. Artikeln bygger vidare på [Kopieringsaktivitet i Azure Data Factory](copy-activity-overview.md), som anger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Netezza till alla datalager för mottagare som stöds. En lista över data lagrar att det stöder Kopieringsaktiviteten som källor och mottagare, finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory tillhandahåller en inbyggd drivrutin om du vill aktivera anslutningen. Du behöver inte installera en drivrutin för att använda den här anslutningen manuellt.

## <a name="get-started"></a>Kom igång

Du kan skapa en pipeline som använder en Kopieringsaktivitet med hjälp av .NET SDK, Python SDK, Azure PowerShell, REST API eller en Azure Resource Manager-mall. Se den [Kopieringsaktiviteten självstudien](quickstart-create-data-factory-dot-net.md) stegvisa instruktioner om hur du skapar en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory-entiteter som är specifika för Netezza-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Netezza länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen måste anges till **Netezza**. | Ja |
| connectionString | En ODBC-anslutningssträng att ansluta till Netezza. Markera det här fältet som en **SecureString** Skriv för att lagra den på ett säkert sätt i Data Factory. Du kan också [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [Integreringskörningen](concepts-integration-runtime.md) för att ansluta till datalagret. Du kan välja en lokal Integration Runtime eller Azure Integration Runtime (om ditt datalager är offentligt tillgänglig). Om den inte anges används standard Azure Integration Runtime. |Nej |

En typisk anslutningssträng är `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. I följande tabell beskrivs fler egenskaper som du kan ange:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| SecurityLevel | Nivån på security (SSL/TLS) som drivrutinen använder för anslutning till datalagret. Exempel: `SecurityLevel=preferredSecured`. Värden som stöds är:<br/>- **Endast oskyddad** (**onlyUnSecured**): drivrutinen inte använda SSL.<br/>- **Önskad oskyddat (preferredUnSecured) (standard)**: om servern innehåller ett val, drivrutinen inte använda SSL. <br/>- **Önskad säker (preferredSecured)**: om servern innehåller ett val, drivrutinen använder SSL. <br/>- **Endast skyddas (onlySecured)**: drivrutinen inte går att ansluta såvida inte en SSL-anslutning är tillgänglig. | Nej |
| CUCertfil | Den fullständiga sökvägen till SSL-certifikatet som används av servern. Exempel: `CaCertFile=<cert path>;`| Ja, om SSL är aktiverat |

**Exempel**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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

Det här avsnittet innehåller en lista över egenskaper som har stöd för Netezza-datauppsättningen.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar](concepts-datasets-linked-services.md). 

Om du vill kopiera data från Netezza, ange den **typ** egenskapen på datauppsättningen till **NetezzaTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **NetezzaTable** | Ja |
| tableName | Namnet på tabellen. | Nej (om ”frågan” i aktivitetskälla har angetts) |

**Exempel**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som har stöd för Netezza-källan.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). 

### <a name="netezza-as-source"></a>Netezza som källa

Om du vill kopiera data från Netezza, ange den **källa** typ i Kopieringsaktiviteten till **NetezzaSource**. Följande egenskaper stöds i Kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen för Kopieringsaktiviteten källan måste anges till **NetezzaSource**. | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Exempel: `"SELECT * FROM MyTable"` | Nej (om ”tableName” i datauppsättningen har angetts) |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
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

En lista över datalager som Kopieringsaktiviteten som källor och egenskaperna i Azure Data Factory finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
