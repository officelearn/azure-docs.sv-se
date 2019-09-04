---
title: Kopiera data från SAP Business Warehouse via öppen hubb med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från SAP Business Warehouse (BW) via öppna hubb till mottagar data lager med stöd för en kopierings aktivitet i en Azure Data Factory pipeline.
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
ms.openlocfilehash: d82f843cb5cdd7b910c734f26a93144374061b74
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274502"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Kopiera data från SAP Business Warehouse via öppen hubb med Azure Data Factory

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från ett SAP Business Warehouse (BW) via öppen hubb. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

>[!TIP]
>Om du vill lära dig mer om ADF: s övergripande support i SAP data integrations scenario, se [SAP data integration med Azure Data Factory whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) med detaljerad introduktion, comparsion och vägledning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från SAP Business Warehouse via öppna hubbar till alla mottagar data lager som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder detta SAP Business Warehouse Open Hub Connector:

- SAP Business Warehouse **version 7,01 eller senare (i en senaste SAP-support paket stack som publicerats efter året 2015)** .
- Kopiera data via den lokala mål destinations tabellen som under kan vara DSO, InfoCube, multitillhandahållare, DataSource osv.
- Kopiera data med grundläggande autentisering.
- Ansluter till program Server.

## <a name="sap-bw-open-hub-integration"></a>SAP BW integrering med öppen hubb 

[SAP BW Open Hub service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) är ett effektivt sätt att extrahera data från SAP BW. I följande diagram visas en av de vanligaste flödes kunderna i sina SAP-system, i vilket fall data flödar från SAP ECC-> PSA-> DSO-> Cube.

SAP BW OHD (Open Hub destination) definierar målet som SAP-data vidarebefordras till. Alla objekt som stöds av SAP Dataöverföring process (DTP) kan användas som data källor med öppen hubb, till exempel DSO, InfoCube, DataSource osv. Mål typ för öppen hubb – där vidarebefordrade data lagras – kan vara databas tabeller (lokala eller fjärranslutna) och flata filer. Det här SAP BW öppna Hub Connector-stöd för att kopiera data från den lokala OHD-tabellen i BW. Om du använder andra typer kan du ansluta direkt till databasen eller fil systemet med andra anslutningar.

![SAP BW öppna hubben](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Flöde för delta extrahering

ADF SAP BW Open Hub Connector erbjuder två valfria egenskaper: `excludeLastRequest` och `baseRequestId` som kan användas för att hantera delta-belastning från öppna hubb. 

- **excludeLastRequestId**: Om posterna för den senaste begäran ska uteslutas. Standardvärdet är true. 
- **baseRequestId**: ID för begäran om delta inläsning. När den har angetts hämtas endast data med requestId som är större än värdet för den här egenskapen. 

Som helhet består extraheringen från SAP InfoProviders till Azure Data Factory (ADF) av två steg: 

1. **SAP BW dataöverföring process (DTP)** I det här steget kopieras data från en SAP BW InfoProvider till en SAP BW öppen Hub-tabell 

1. **ADF-datakopia** I det här steget läses öppna Hub-tabellen av ADF-kopplingen 

![Flöde för delta extrahering](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

I det första steget körs en DTP. Varje körning skapar ett nytt ID för SAP-begäran. Fråge-ID: t lagras i den öppna Hub-tabellen och används sedan av ADF-kopplingen för att identifiera delta. De två stegen körs asynkront: DTP utlöses av SAP och data kopieringen för ADF utlöses via ADF. 

Som standard läser ADF inte den senaste förändringen från den öppna Hub-tabellen (alternativet "exkludera senaste begäran" är sant). Data i ADF är inte 100% uppdaterade med data i den öppna Hub-tabellen (det sista deltaet saknas). I retur ser den här proceduren till att inga rader går förlorade av asynkron extrahering. Det fungerar fint även när ADF läser den öppna Hub-tabellen medan DTP fortfarande skriver till samma tabell. 

Du lagrar vanligt vis det maximala kopierade förfrågnings-ID: t i den senaste körningen av ADF i ett data lager för mellanlagring (till exempel Azure blob i diagrammet ovan). Därför läses samma begäran inte en andra gång med ADF i den efterföljande körningen. Observera att data inte tas bort automatiskt från den öppna Hub-tabellen.

För korrekt delta hantering kan det inte ha fråge-ID: n från olika DTPs i samma öppna Hub-tabell. Därför får du inte skapa fler än en DTP för varje Open Hub-destination (OHD). När du behöver fullständig och delta extrahering från samma InfoProvider bör du skapa två OHDs för samma InfoProvider. 

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här SAP Business Warehouse-anslutningen med öppen hubb måste du:

- Konfigurera en egen värd Integration Runtime med version 3,13 eller senare. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) nedan för information.

- Hämta **64-bitars [SAP .net Connector 3,0](https://support.sap.com/en/product/connectors/msnet.html)**  från SAP: s webbplats och installera den på den lokala IR-datorn. När du installerar går du till fönstret valfria installations steg och kontrollerar att du väljer alternativet **Installera sammansättningar i GAC** som det visas i följande bild. 

    ![Installera SAP .NET Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- SAP-användare som används i Data Factory BW-anslutaren måste ha följande behörigheter: 

    - Auktorisering för RFC och SAP BW. 
    - Behörigheter till aktiviteten "kör" för auktoriseringsbegäran "S_SDSAUTH".

- Skapa en SAP Open Hub-måltyp som **databas tabell** med alternativet "teknisk nyckel" markerat.  Vi rekommenderar också att du lämnar kryss rutan ta bort data från tabellen som omarkerade även om det inte är obligatoriskt. Utnyttja DTP (direkt körning eller integrering i en befintlig process kedja) för att landa data från källobjektet (till exempel kub) som du har valt till mål tabellen med öppen hubb.

## <a name="getting-started"></a>Komma igång

> [!TIP]
>
> En genom gång av hur du använder SAP BW Open Hub Connector finns i [läsa in data från SAP Business Warehouse (BW) med hjälp av Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är specifika för SAP Business Warehouse Open Hub Connector.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för SAP Business Warehouse-länkad hubb:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type måste anges till: **SapOpenHub** | Ja |
| server | Namnet på den server där SAP BW-instansen finns. | Ja |
| systemNumber | System numret för det SAP BW systemet.<br/>Tillåtet värde: tvåsiffrigt decimal tal representeras som en sträng. | Ja |
| clientId | Klient-ID för klienten i SAP W-systemet.<br/>Tillåtet värde: tre-siffrigt decimal tal representeras som en sträng. | Ja |
| language | Språk som används i SAP-systemet. | Nej (Standardvärdet är **en**)|
| userName | Namnet på den användare som har åtkomst till SAP-servern. | Ja |
| password | Lösenordet för användaren. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Det krävs en egen värd Integration Runtime som anges i [krav](#prerequisites). |Ja |

**Exempel:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP BW Open Hub-datauppsättningen.

Om du vill kopiera data från och till SAP BW öppna hubben, anger du egenskapen type för data uppsättningen till **SapOpenHubTable**. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type måste anges till **SapOpenHubTable**.  | Ja |
| openHubDestinationName | Namnet på det öppna hubb målet att kopiera data från. | Ja |

Om du har `excludeLastRequest` angett `baseRequestId` och i data uppsättning stöds det fortfarande som det är, medan du föreslås att använda den nya modellen i aktivitets källan som går framåt.

**Exempel:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP BW öppna Hub-källan.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW öppna hubben som källa

Om du vill kopiera data från SAP BW öppna hubben, stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** :

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | **Typ** egenskapen för kopierings aktivitets källan måste anges till **SapOpenHubSource**. | Ja |
| excludeLastRequest | Om posterna för den senaste begäran ska uteslutas. | Nej (standard är **true**) |
| baseRequestId | ID för begäran om delta inläsning. När den har angetts hämtas endast data med requestId som är **större än** värdet för den här egenskapen.  | Nej |

>[!TIP]
>Om den öppna Hub-tabellen bara innehåller de data som genereras av ID: t för en enskild begäran, till exempel, gör du alltid fullständig belastning och skriver över befintliga data i tabellen, eller så kör du bara DTP en gång för test, kom ihåg att avmarkera alternativet "excludeLastRequest" för att kopiera d ATA ut.

För att påskynda data inläsningen kan du ställa [`parallelCopies`](copy-activity-performance.md#parallel-copy) in på kopierings aktiviteten för att läsa in data från SAP BW öppen hubb parallellt. Om du till exempel ställer in `parallelCopies` till fyra kör Data Factory samtidigt fyra RFC-anrop, och varje RFC-anrop hämtar en del av data från SAP BW öppna Hub-tabellen partitionerad med ID för DTP-begäran och paket-ID. Detta gäller när antalet unika ID för DTP-begäran och paket-ID är större än värdet för `parallelCopies`. När du kopierar data till ett filbaserat data lager, skrivs det också om att skriva till en mapp som flera filer (ange bara mappnamn), i vilket fall prestandan är bättre än att skriva till en enda fil.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Data typs mappning för SAP BW öppen hubb

När du kopierar data från SAP BW öppna hubben används följande mappningar från SAP BW data typer för att Azure Data Factory interimistiska data typer. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| Typ av SAP-ABAP | Data factory tillfälliga datatyp |
|:--- |:--- |
| C (String) | Sträng |
| I (integer) | Int32 |
| F (float) | Double |
| D (Date) | Sträng |
| T (Time) | Sträng |
| P (BCD Packed, Currency, Decimal, Qty) | Decimal |
| N (Numc) | Sträng |
| X (Binary and Raw) | Sträng |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
