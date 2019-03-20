---
title: Kopiera data från SAP Business Warehouse via Open-hubb med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från SAP Business Warehouse (BW) via öppna hubben till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: jingwang
ms.openlocfilehash: c64842dc89c9519c738701558f510940f4cc148d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103918"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Kopiera data från SAP Business Warehouse via Open-hubb med Azure Data Factory

Den här artikeln beskriver hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en SAP Business Warehouse (BW) via öppna Hub. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från SAP Business Warehouse via öppna hubben till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här öppna hubben för SAP Business Warehouse-anslutningen:

- SAP Business Warehouse **version 7.01 eller senare (i en senaste SAP Support paketet Stack lanseras efter år 2015)**.
- Kopiering av data via öppna Hub lokala måltabell som under kan vara DSO, InfoCube, MultiProvider, datakällan, osv.
- Kopiera data med hjälp av grundläggande autentisering.
- Ansluta till programservern.

## <a name="sap-bw-open-hub-integration"></a>SAP BW Open Hub-integrering 

[SAP BW Open Hub Service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) är ett effektivt sätt att extrahera data från SAP BW. I följande diagram visas ett av de vanliga flöden som kunder har i sina SAP-system, i vilket fall dataflöden från SAP ECC -> PSA -> DSO -> kuben.

SAP BW Open Hub mål (OHD) definierar målet som SAP-data är vidarebefordrande. Alla objekt som stöds av SAP (DTP Data Transfer processen) kan användas som öppna hub datakällor, till exempel DSO, InfoCube, datakällan, osv. Öppna Hub måltypen - där vidarebefordrande data lagras - kan vara databastabeller (lokal eller fjärransluten) och flata filer. Den här öppna hubben för SAP BW connector stöd kopierar data från OHD lokala tabellen i BW. Om du använder andra typer, kan du ansluta direkt till databasen eller filen systemet med andra anslutningsappar.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Delta extrahering flöde

ADF-SAP BW Open Hub-Anslutningsappen erbjuder två valfria egenskaper: `excludeLastRequest` och `baseRequestId` som kan användas för att hantera delta belastningen från öppna Hub. 

- **excludeLastRequestId**: Om du vill exkludera poster i den senaste begäran. Standardvärdet är sant. 
- **baseRequestId**: ID för begäran om deltainläsningen. När den har angetts kommer att hämta endast data med requestId som är större än värdet för den här egenskapen. 

Övergripande består extraheringen från SAP InfoProviders till Azure Data Factory (ADF) av 2 steg: 

1. **SAP BW Data Transfer processen (DTP)** det här steget kopierar data från en SAP BW InfoProvider till en SAP BW Open Hub-tabell 

1. **ADF datakopiering** i det här steget tabellen öppna Hub läses av ADF-anslutningen 

![Delta extrahering flöde](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

I det första steget körs en DTP. Varje körning skapar en ny SAP begärande-ID. Begäran-ID lagras i tabellen öppna Hub och sedan används av ADF-anslutningen för att identifiera deltadata. De två stegen körs asynkront: DTP utlöses av SAP och Datakopieringen ADF utlöses av ADF. 

Som standard ADF inte läsa den senaste listan från tabellen öppna Hub (alternativet ”exkludera senaste begäran” är SANT). Data i ADF är härmed, inte 100% uppdaterade med data i tabellen öppna Hub (senaste delta saknas). I utbyte kan säkerställer den här proceduren att inga rader hämta förlorat orsakade av asynkron extraheringen. Det fungerar bra även när ADF läser tabellen öppna Hub medan DTP är fortfarande skrivs i samma tabell. 

Du kan vanligtvis lagra max kopierade begäran-ID i den senaste körningen av ADF i ett datalager för mellanlagring (till exempel Azure Blob i ovanför diagrammet). Samma begäran är därför inte läsa en gång av ADF i efterföljande körningen. Observera under tiden kan data inte tas bort automatiskt från tabellen öppna Hub.

För rätt delta är hantera det. inte tillåtet att ha begäran-ID från olika DTPs i samma öppna Hub tabell. Du måste därför inte skapa fler än en DTP för varje öppen Hub mål (OHD). Om du behöver fullständig och Förändringsspecifik extrahering från samma InfoProvider, bör du skapa två OHDs för samma InfoProvider. 

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda denna anslutning för SAP Business Warehouse öppna Hub måste du:

- Konfigurera en lokal Integration Runtime med version 3.13 eller senare. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) nedan för information.

- Ladda ned den **64-bitars [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)**  från SAP: s webbplats och installera den på den lokala IR-datorn. När installation i fönstret inställningar för valfria steg gör att du väljer den **installera sammansättningar GAC** enligt i följande bild. 

    ![Installera SAP .NET Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- SAP-användare som används i Data Factory BW-anslutningsappen måste ha följande behörigheter: 

    - Auktorisering för RFC och SAP BW. 
    - Behörigheter för att aktiviteten ”kör” för auktorisering objektet ”S_SDSAUTH”.

- Skapa SAP öppna Hub måltypen som **databastabell** med ”teknisk Key”-alternativet är markerat.  Vi rekommenderar också att lämna Data tas bort från tabellen som avmarkerat även om det inte krävs. Utnyttja DTP (direkt köra eller integrera befintliga processen kedja) hamnar data från källobjektet (till exempel kub) som du har valt att öppna hub måltabellen.

## <a name="getting-started"></a>Komma igång

> [!TIP]
>
> En genomgång för att använda anslutningstjänsten SAP BW Open Hub finns i [läsa in data från SAP Business Warehouse (BW) med hjälp av Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifik för SAP Business Warehouse öppna Hub connector.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för SAP Business Warehouse öppna hubben som är länkad tjänst:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **SapOpenHub** | Ja |
| server | Namnet på den server som SAP BW-instansen finns. | Ja |
| systemNumber | Systemnummer för SAP BW-system.<br/>Tillåtna värdet: tvåsiffrig decimaltal representeras som en sträng. | Ja |
| ClientId | Klient-ID för klienten i SAP W systemet.<br/>Tillåtna värdet: tresiffrig decimaltal representeras som en sträng. | Ja |
| language | Språk som använder SAP-system. | Nej (standardvärdet är **EN**)|
| Användarnamn | Namnet på den användare som har åtkomst till SAP-server. | Ja |
| lösenord | Lösenordet för användaren. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. En lokal Integration Runtime krävs enligt [krav](#prerequisites). |Ja |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Salesforce-datauppsättning.

För att kopiera data från och till SAP BW Open Hub, ange typegenskapen på datauppsättningen till **SapOpenHubTable**. Följande egenskaper stöds.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **SapOpenHubTable**.  | Ja |
| openHubDestinationName | Namnet på Öppna Hub målet att kopiera data från. | Ja |
| excludeLastRequest | Om du vill exkludera poster i den senaste begäran. | Nej (standard är **SANT**) |
| baseRequestId | ID för begäran om deltainläsningen. När den har angetts, endast data med requestId **större än** värdet för den här egenskapen ska hämtas.  | Nej |

>[!TIP]
>Om öppen Hub tabellen bara innehåller data som genereras av enskild begäran-ID, till exempel du alltid full laddning och skriva över befintliga data i tabellen eller du bara köra DTP en gång för testning, Kom ihåg att avmarkera alternativet ”excludeLastRequest” för att kopiera d ATA ut.

**Exempel:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av SAP BW Open Hub källa.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub som källa

För att kopiera data från SAP BW Open Hub, ange typ av datakälla i kopieringsaktiviteten till **SapOpenHubSource**. Det finns inga ytterligare typspecifika egenskaper som krävs i kopieringsaktiviteten **källa** avsnittet.

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
                "type": "SapOpenHubSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Datatypsmappningen för SAP BW Open Hub

När du kopierar data från SAP BW Open Hub, används följande mappningar från SAP BW-datatyper till Azure Data Factory tillfälliga-datatyper. Se [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md) vill veta mer om hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren.

| SAP ABAP-typ | Data factory tillfälliga datatyp |
|:--- |:--- |
| C (sträng) | String |
| Jag (heltal) | Int32 |
| F (Float) | Double |
| D (datum) | String |
| T (tid) | String |
| P (BCD späckad, valuta, Decimal, kvantitet) | Decimal |
| N (Numc) | String |
| X (binär och Raw) | String |

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
