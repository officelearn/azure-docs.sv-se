---
title: "Kopiera data från SAP BW med Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från SAP Business Warehouse till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen."
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
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 4ab0ddcc3a42ab4ebb7c9555f57bc2533989b071
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Kopiera data från SAP Business Warehouse med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-sap-business-warehouse-connector.md)
> * [Version 2 – förhandsversion](connector-sap-business-warehouse.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från en SAP Business Warehouse (BW). Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [SAP BW connector i V1](v1/data-factory-sap-business-warehouse-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från SAP Business Warehouse till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här SAP Business Warehouse-anslutningen:

- SAP Business Warehouse **version 7.x**.
- Kopiera data från **InfoCubes och QueryCubes** (inklusive BEx frågar) med hjälp av MDX-frågor.
- Kopierar data med hjälp av grundläggande autentisering.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda denna SAP Business Warehouse-anslutning måste du:

- Ställ in en Self-hosted integrering Runtime. Se [Self-hosted integrering Runtime](create-self-hosted-integration-runtime.md) artikeln för information.
- Installera den **SAP NetWeaver biblioteket** på Integration Runtime-datorn. Du kan hämta SAP Netweaver-bibliotek från SAP-administratören eller direkt från den [SAP Software Download Center](https://support.sap.com/swdc). Sök efter den **SAP Obs #1025361** få hämtningsplatsen för den senaste versionen. Kontrollera att du väljer den **64-bitars** SAP NetWeaver biblioteket, som matchar din integrering Runtime-installation. Installera alla filer som ingår i SAP NetWeaver RFC SDK enligt SAP-kommentar. Bibliotek för SAP NetWeaver ingår också i klientverktyg för SAP-installationen.

> [!TIP]
> Placera DLL: er som extraheras från NetWeaver RFC SDK i mappen system32.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till SAP Business Warehouse-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för SAP Business Warehouse (BW) länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **SapBw** | Ja |
| server | Namnet på den server som SAP BW-instansen finns. | Ja |
| systemNumber | Systemnummer för SAP BW-system.<br/>Tillåtna värdet: två siffror decimaltal representeras som en sträng. | Ja |
| clientId | Klient-ID för klienten i systemets SAP-W.<br/>Tillåtna värdet: tre siffror decimaltal representeras som en sträng. | Ja |
| Användarnamn | Namnet på den användare som har åtkomst till SAP-server. | Ja |
| lösenord | Lösenord för användaren. Markera det här fältet som en SecureString. | Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. En Self-hosted integrering Runtime krävs enligt [krav](#prerequisites). |Ja |

**Exempel:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP BW dataset.

Ange typegenskapen för dataset för att kopiera data från SAP BW, **RelationalTable**. När det finns inga typspecifika egenskaper som stöds för SAP BW datauppsättningen av skriver RelationalTable.

**Exempel:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP BW källa.

### <a name="sap-bw-as-source"></a>SAP BW som källa

Om du vill kopiera data från SAP BW anger källa i kopieringsaktiviteten för **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **RelationalSource** | Ja |
| DocumentDB | Anger MDX-fråga för att läsa data från SAP BW-instans. | Ja |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw"></a>Datatypsmappningen för SAP BW

När du kopierar data från SAP BW, används följande mappningar från SAP BW-datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

| Datatypen för SAP BW | Data factory tillfälliga datatyp |
|:--- |:--- |
| ACCP | Int |
| CHAR | Sträng |
| CLNT | Sträng |
| AKTUELLT DATUM | Decimal |
| CUKY | Sträng |
| DEC | Decimal |
| FLTP | dubbla |
| INT1 | Mottagna byte |
| INT2 | Int16 |
| INT4 | Int |
| LANG | Sträng |
| LCHR | Sträng |
| LRAW | byte] |
| PREC | Int16 |
| QUAN | Decimal |
| RÅDATA | byte] |
| RAWSTRING | byte] |
| STRÄNG | Sträng |
| ENHET | Sträng |
| DATS | Sträng |
| NUMC | Sträng |
| TIMS | Sträng |


## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).