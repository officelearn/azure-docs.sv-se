---
title: Kopiera data från SAP BW
description: Lär dig hur du kopierar data från SAP Business Warehouse till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 2f8406038be10ba3bdc207bf447fecb86a376fe8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418073"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Kopiera data från SAP Business Warehouse med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Aktuell version](connector-sap-business-warehouse.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från ett SAP Business Warehouse (BW). Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

>[!TIP]
>Mer information om ADF:s övergripande support i SCENARIOt för SAP-dataintegrering finns i [SAP-dataintegration med hjälp av Azure Data Factory-faktablad](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) med detaljerad introduktion, jämförelse och vägledning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SAP Business Warehouse-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från SAP Business Warehouse till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här SAP Business Warehouse-kopplingen stöder:

- SAP Business Warehouse **version 7.x**.
- Kopiera data från **InfoCubes och QueryCubes** (inklusive BEx-frågor) med MDX-frågor.
- Kopiera data med hjälp av grundläggande autentisering.

## <a name="prerequisites"></a>Krav

Om du vill använda den här SAP Business Warehouse-kopplingen måste du:

- Konfigurera en självvärderad integrationskörning. Mer information finns i artikeln Integration Runtime för [självvärd.](create-self-hosted-integration-runtime.md)
- Installera **SAP NetWeaver-biblioteket** på integrationskörningsdatorn. Du kan hämta SAP Netweaver-biblioteket från din SAP-administratör eller direkt från [SAP Software Download Center](https://support.sap.com/swdc). Sök efter **SAP Note-#1025361** för att hämta hämtningsplatsen för den senaste versionen. Se till att du väljer **det 64-bitars** SAP NetWeaver-bibliotek som matchar installationen av Integration Runtime. Installera sedan alla filer som ingår i SAP NetWeaver RFC SDK enligt SAP Note. SAP NetWeaver-biblioteket ingår också i SAP Client Tools-installationen.

>[!TIP]
>Om du vill felsöka anslutningsproblemet till SAP BW kontrollerar du att
>- Alla beroendebibliotek som extraherats från NetWeaver RFC SDK finns på plats i mappen %windir%\system32. Vanligtvis har icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- De portar som behövs för att ansluta till SAP Server är aktiverade på den självvärderade IR-datorn, som vanligtvis är port 3300 och 3201.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för SAP Business Warehouse-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för TJÄNSTEN SAP Business Warehouse (BW):

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste ställas in på: **SapBw** | Ja |
| server | Namn på den server där SAP BW-instansen finns. | Ja |
| systemNummer | Systemnummer för SAP BW-systemet.<br/>Tillåtet värde: tvåsiffrigt decimaltal representerat som en sträng. | Ja |
| ClientID | Klient-ID för klienten i SAP W-systemet.<br/>Tillåtet värde: Tresiffrigt decimaltal representerat som en sträng. | Ja |
| userName | Namn på den användare som har åtkomst till SAP-servern. | Ja |
| password | Lösenordet för användaren. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. En självvärdad integrationskörning krävs som nämns i [Förutsättningar](#prerequisites). |Ja |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av SAP BW-datauppsättning.

Om du vill kopiera data från SAP BW anger du egenskapen type för datauppsättningen till **SapBwCube**. Det finns inga typspecifika egenskaper som stöds för SAP BW-datauppsättningen för typen RelationalTable.

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

Om du `RelationalTable` använde maskinskriven datauppsättning stöds den fortfarande som den är, medan du föreslås använda den nya framöver.

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av SAP BW-källa.

### <a name="sap-bw-as-source"></a>SAP BW som källa

Om du vill kopiera data från SAP BW stöds följande egenskaper i avsnittet kopiera **aktivitetskälla:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste ställas in på: **SapBwSource** | Ja |
| DocumentDB | Anger mdx-frågan för att läsa data från SAP BW-instansen. | Ja |

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

Om du `RelationalSource` använde den maskinskrivna källan stöds den fortfarande som den är, medan du föreslås använda den nya framåt.

## <a name="data-type-mapping-for-sap-bw"></a>Datatypsmappning för SAP BW

Vid kopiering av data från SAP BW används följande mappningar från SAP BW-datatyper till Azure Data Factory interimsdatatyper. Se [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md) om du vill veta mer om hur du kopierar aktivitetsschemat och datatypen till diskhon.

| SAP BW-datatyp | Data fabrik interim datatyp |
|:--- |:--- |
| ACCP (på andra) | Int |
| CHAR | Sträng |
| Clnt | Sträng |
| CURR (HÄRD) | Decimal |
| CUKY (K.) | Sträng |
| December | Decimal |
| FLTP (PÅ) | Double |
| INT1 (INT1) | Byte |
| INT2 (INT2) | Int16 (int16) |
| INT4 (INT4) | Int |
| Lang | Sträng |
| LCHR (på andra) | Sträng |
| LRAW (LRAW) | Byte[] |
| PREC (PREC) | Int16 (int16) |
| Quan | Decimal |
| Raw | Byte[] |
| RÅSNSTRÄNG | Byte[] |
| Sträng | Sträng |
| Enhet | Sträng |
| Dats | Sträng |
| NUMC (NUMC) | Sträng |
| TIMS (AVS) | Sträng |


## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
