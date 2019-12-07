---
title: Kopiera data från SAP BW med Azure Data Factory
description: Lär dig hur du kopierar data från SAP Business Warehouse till mottagar data lager som stöds med hjälp av en kopierings aktivitet i en Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 26693219f44d16c8bc20ee94ae0590414ba88f73
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896351"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Kopiera data från SAP Business Warehouse med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Aktuell version](connector-sap-business-warehouse.md)

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från ett SAP Business Warehouse (BW). Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

>[!TIP]
>Om du vill lära dig mer om ADF: s övergripande support i SAP data integrations scenario, se [SAP data integration med Azure Data Factory whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) med detaljerad introduktion, comparsion och vägledning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SAP Business Warehouse-anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från SAP Business Warehouse till alla mottagar data lager som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder SAP Business Warehouse Connector:

- SAP Business Warehouse **version 7. x**.
- Kopiera data från **InfoCubes och QueryCubes** (inklusive BEX-frågor) med MDX-frågor.
- Kopiera data med grundläggande autentisering.

## <a name="prerequisites"></a>Krav

Om du vill använda den här SAP Business Warehouse Connector måste du:

- Konfigurera en egen värd Integration Runtime. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) nedan för information.
- Installera **SAP NetWeaver-biblioteket** på den integration runtime datorn. Du kan hämta SAP NetWeaver-biblioteket från din SAP-administratör eller direkt från [SAP Software Download Center](https://support.sap.com/swdc). Sök efter **SAP-Anteckningen #1025361** för att hämta hämtnings platsen för den senaste versionen. Se till att du väljer det **64-bitars** SAP NetWeaver-bibliotek som matchar din integration runtime-installation. Installera sedan alla filer som ingår i SAP NetWeaver RFC SDK enligt SAP-anteckningen. SAP NetWeaver-biblioteket ingår också i installationen av SAP-klient verktyg.

>[!TIP]
>Kontrol lera följande för att felsöka anslutnings problem till SAP BW:
>- Alla beroende bibliotek som extraheras från NetWeaver RFC SDK finns på plats i mappen%windir%\System32. Vanligt vis har den icudt34. dll, icuin34. dll, icuuc34. dll, libicudecnumber. dll, librfc32. dll, libsapucum. dll, sapcrypto. dll, sapcryto_old. dll, sapnwrfc. dll.
>- De portar som krävs för att ansluta till SAP-servern har Aktiver ATS på IR-datorn med egen värd, som vanligt vis är port 3300 och 3201.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory entiteter som är specifika för SAP Business Warehouse Connector.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten SAP Business Warehouse (BW):

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **SapBw** | Ja |
| server | Namnet på den server där SAP BW-instansen finns. | Ja |
| systemNumber | System numret för det SAP BW systemet.<br/>Tillåtet värde: tvåsiffrigt decimal tal representeras som en sträng. | Ja |
| clientId | Klient-ID för klienten i SAP W-systemet.<br/>Tillåtet värde: tre-siffrigt decimal tal representeras som en sträng. | Ja |
| userName | Namnet på den användare som har åtkomst till SAP-servern. | Ja |
| lösenord | Lösenordet för användaren. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Det krävs en egen värd Integration Runtime som anges i [krav](#prerequisites). |Ja |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP BW data uppsättning.

Om du vill kopiera data från SAP BW anger du egenskapen type för data uppsättningen till **SapBwCube**. Det finns inga typ-/regionsspecifika egenskaper som stöds för SAP BW-datauppsättningen av typen RelationalTable.

**Exempel:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "SapBwCube",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Om du använder `RelationalTable` typ av data uppsättning, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya som skickas.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP BW källa.

### <a name="sap-bw-as-source"></a>SAP BW som källa

För att kopiera data från SAP BW, stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till: **SapBwSource** | Ja |
| DocumentDB | Anger MDX-frågan för att läsa data från SAP BW-instansen. | Ja |

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
                "type": "SapBwSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Om du använder `RelationalSource` typ av källa, stöds den fortfarande som den är, medan du föreslås att du vill använda den nya som skickas.

## <a name="data-type-mapping-for-sap-bw"></a>Data typs mappning för SAP BW

När du kopierar data från SAP BW används följande mappningar från SAP BW data typer för att Azure Data Factory interimistiska data typer. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| SAP BW data typ | Data factory tillfälliga datatyp |
|:--- |:--- |
| ACCP | Int |
| CHAR | Sträng |
| CLNT | Sträng |
| CURR | Decimal |
| CUKY | Sträng |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Mottagna byte |
| INT2 | Int16 |
| INT4 | Int |
| LANG | Sträng |
| LCHR | Sträng |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | Sträng |
| ENHET | Sträng |
| DATS | Sträng |
| NUMC | Sträng |
| TIMS | Sträng |


## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
