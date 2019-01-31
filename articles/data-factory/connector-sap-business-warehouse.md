---
title: Kopiera data från SAP BW med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från SAP Business Warehouse till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: jingwang
ms.openlocfilehash: 9a0abcd70b4aeb2369604bafa924136122206e0a
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022299"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Kopiera data från SAP Business Warehouse med Azure Data Factory
> [!div class="op_single_selector" title1="Välj vilken version av Data Factory-tjänsten du använder:"]
> * [Version 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Aktuell version](connector-sap-business-warehouse.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en SAP Business Warehouse (BW). Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från SAP Business Warehouse till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här SAP Business Warehouse-anslutningen:

- SAP Business Warehouse **version 7.x**.
- Kopiera data från **InfoCubes och QueryCubes** (inklusive BEx frågor) med MDX-frågor.
- Kopiera data med hjälp av grundläggande autentisering.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda denna SAP Business Warehouse-anslutning måste du:

- Konfigurera en lokal Integration Runtime. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) nedan för information.
- Installera den **SAP NetWeaver-biblioteket** på Integration Runtime-datorn. Du kan hämta SAP Netweaver-biblioteket från SAP-administratören eller direkt från den [SAP Software Download Center](https://support.sap.com/swdc). Sök efter den **SAP Note #1025361** att hitta hämtningsplatsen för den senaste versionen. Se till att du väljer den **64-bitars** SAP NetWeaver-biblioteket som matchar din Integration Runtime-installation. Installera sedan alla filer som ingår i SAP NetWeaver RFC-SDK enligt SAP Note. SAP NetWeaver-biblioteket ingår också i klientverktyg för SAP-installationen.

>[!TIP]
>Om du vill felsöka anslutningsproblemet till SAP BW kan du se till att:
>- Alla beroende bibliotek som extraheras från NetWeaver RFC-SDK finns i mappen %windir%\system32. Vanligtvis har icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- De nödvändiga portarna som används för att ansluta till SAP-Server är aktiverade på den lokal IR-datorn, som vanligtvis är port 3300 och 3201.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifik för SAP Business Warehouse-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för SAP Business Warehouse (BW) länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **SapBw** | Ja |
| server | Namnet på den server som SAP BW-instansen finns. | Ja |
| systemNumber | Systemnummer för SAP BW-system.<br/>Tillåtna värdet: tvåsiffrig decimaltal representeras som en sträng. | Ja |
| ClientId | Klient-ID för klienten i SAP W systemet.<br/>Tillåtna värdet: tresiffrig decimaltal representeras som en sträng. | Ja |
| Användarnamn | Namnet på den användare som har åtkomst till SAP-server. | Ja |
| lösenord | Lösenordet för användaren. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. En lokal Integration Runtime krävs enligt [krav](#prerequisites). |Ja |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP BW-datauppsättningen.

Om du vill kopiera data från SAP BW, ange typegenskapen på datauppsättningen till **RelationalTable**. Det finns inga typspecifika egenskaper som stöds för SAP BW-datauppsättningen för skriver RelationalTable.

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP BW-källa.

### <a name="sap-bw-as-source"></a>SAP BW som källa

Om du vill kopiera data från SAP BW, ange typ av datakälla i kopieringsaktiviteten till **RelationalSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **RelationalSource** | Ja |
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

När du kopierar data från SAP BW, används följande mappningar från SAP BW-datatyper till Azure Data Factory tillfälliga-datatyper. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| SAP BW-datatypen | Data factory tillfälliga datatyp |
|:--- |:--- |
| ACCP | Int |
| CHAR | String |
| CLNT | String |
| CURR | Decimal |
| CUKY | String |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | Int |
| LANG | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | String |
| UNIT | String |
| DATS | String |
| NUMC | String |
| TIMS | String |


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
