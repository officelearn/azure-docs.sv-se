---
title: Kopiera data från SAP Business Warehouse via Open Hub
description: Lär dig hur du kopierar data från SAP Business Warehouse (BW) via Open Hub till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.date: 03/24/2020
ms.openlocfilehash: b905c75e920577e46017caeb456f8237421086b2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421214"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Kopiera data från SAP Business Warehouse via Open Hub med Azure Data Factory

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från ett SAP Business Warehouse (BW) via Open Hub. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

>[!TIP]
>Mer information om ADF:s övergripande support i SCENARIOt för SAP-dataintegrering finns i [SAP-dataintegration med hjälp av Azure Data Factory-faktablad](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) med detaljerad introduktion, jämförelse och vägledning.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här SAP Business Warehouse via Open Hub-anslutning stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från SAP Business Warehouse via Open Hub till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här SAP Business Warehouse Open Hub-kopplingen stöder:

- SAP Business Warehouse **version 7.01 eller senare (i en nyligen SAP-supportpaketstack som släpptes efter år 2015).**
- Kopiera data via Open Hub Destination lokal tabell som under kan DSO, InfoCube, MultiProvider, DataSource, etc.
- Kopiera data med hjälp av grundläggande autentisering.
- Ansluter till programservern.

## <a name="sap-bw-open-hub-integration"></a>SAP BW Open Hub-integrering 

[SAP BW Open Hub Service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) är ett effektivt sätt att extrahera data från SAP BW. Följande diagram visar en av de typiska flöden kunderna har i sitt SAP-system, i vilket fall data flödar från SAP ECC -> PSA -> DSO -> Cube.

SAP BW Open Hub Destination (OHD) definierar det mål som SAP-data vidarebefordras till. Alla objekt som stöds av SAP Data Transfer Process (DTP) kan användas som öppna navdatakällor, till exempel DSO, InfoCube, DataSource, etc. Open Hub Destination typ - där förmedlade data lagras - kan databastabeller (lokala eller fjärr) och platta filer. Den här SAP BW Open Hub-anslutningen stöder kopiering av data från OHD-lokal tabell i BW. Om du använder andra typer kan du ansluta direkt till databasen eller filsystemet med andra kopplingar.

![SAP BW Öppna Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Utvinningsflöde för delta

ADF SAP BW Open Hub Connector `excludeLastRequest` `baseRequestId` erbjuder två valfria egenskaper: och som kan användas för att hantera deltabelastning från Open Hub. 

- **excludeLastRequestId**: Om posterna för den senaste begäran ska uteslutas. Standardvärdet är sant. 
- **baseRequestId**: ID för begäran om delta lastning. När den har angetts hämtas endast data med requestId som är större än värdet för den här egenskapen. 

Totalt sett består extraheringen från SAP InfoProviders till Azure Data Factory (ADF) av 2 steg: 

1. **SAP BW-dataöverföringsprocess (DTP)** Det här steget kopierar data från en SAP BW InfoProvider till en SAP BW Open Hub-tabell 

1. **ADF-datakopia** I det här steget läss open hub-tabellen av ADF-anslutningen 

![Utvinningsflöde för delta](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

I det första steget körs en DTP. Varje körning skapar ett nytt SAP-begärande-ID. Begärande-ID:et lagras i tabellen Öppna hubb och används sedan av ADF-anslutningen för att identifiera deltat. De två stegen körs asynkront: DTP utlöses av SAP och ADF-datakopian utlöses via ADF. 

Som standard läser ADF inte det senaste deltat från tabellen Öppna hubb (alternativet "uteslut sista begäran" är sant). Härmed är data i ADF inte 100% uppdaterade med data i open hub-tabellen (det sista deltat saknas). I gengäld säkerställer den här proceduren att inga rader försvinner orsakade av den asynkrona extraktionen. Det fungerar bra även när ADF läser Open Hub tabellen medan DTP fortfarande skriver i samma tabell. 

Du lagrar vanligtvis max kopierat id-kort i den senaste körningen av ADF i ett mellanlagringsdatalager (till exempel Azure Blob i diagrammet ovan). Därför läss inte samma begäran en andra gång av ADF i den efterföljande körningen. Under tiden, notera data inte automatiskt tas bort från Open Hub tabellen.

För korrekt deltahantering är det inte tillåtet att ha begärande-ID:er från olika DTPs i samma Open Hub-tabell. Därför får du inte skapa mer än en DTP för varje MÅL för öppna hubb (OHD). När du behöver Fullständig och Delta utvinning från samma InfoProvider, bör du skapa två OHDs för samma InfoProvider. 

## <a name="prerequisites"></a>Krav

Om du vill använda den här SAP Business Warehouse Open Hub-kopplingen måste du:

- Konfigurera en självvärdad integrationskörning med version 3.13 eller högre. Mer information finns i artikeln Integration Runtime för [självvärd.](create-self-hosted-integration-runtime.md)

- Hämta **64-bitars [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html) ** från SAP:s webbplats och installera den på den självvärderade IR-datorn. När du installerar, i det valfria installationsstegsfönstret, kontrollerar du att du väljer alternativet **Installera sammansättningar till GAC** enligt följande bild. 

    ![Installera SAP .NET-anslutning](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- SAP-användare som används i Data Factory BW-anslutningen måste ha följande behörigheter: 

    - Auktorisering för RFC och SAP BW. 
    - Behörigheter till "Kör" aktivitet av auktoriseringsobjekt "S_SDSAUTH".

- Skapa SAP Open Hub Måltyp som **databastabell** med alternativet "Teknisk nyckel" markerat.  Vi rekommenderar också att du lämnar borttagningsdata från tabellen som avmarkerade även om det inte krävs. Utnyttja DTP (direkt köra eller integrera i befintlig processkedja) för att landa data från källobjekt (till exempel kub) som du har valt till måltabellen för öppna nav.

## <a name="getting-started"></a>Komma igång

> [!TIP]
>
> En genomgång av att använda SAP BW Open Hub-anslutning finns [i Läsa in data från SAP Business Warehouse (BW) med hjälp av Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för SAP Business Warehouse Open Hub-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för SAP Business Warehouse Open Hub-länkad tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **SapOpenHub** | Ja |
| server | Namn på den server där SAP BW-instansen finns. | Ja |
| systemNummer | Systemnummer för SAP BW-systemet.<br/>Tillåtet värde: tvåsiffrigt decimaltal representerat som en sträng. | Ja |
| ClientID | Klient-ID för klienten i SAP W-systemet.<br/>Tillåtet värde: Tresiffrigt decimaltal representerat som en sträng. | Ja |
| language | Språk som SAP-systemet använder. | Nej (standardvärdet är **EN**)|
| userName | Namn på den användare som har åtkomst till SAP-servern. | Ja |
| password | Lösenordet för användaren. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. En självvärdad integrationskörning krävs som nämns i [Förutsättningar](#prerequisites). |Ja |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av SAP BW Open Hub-datauppsättningen.

Om du vill kopiera data från och till SAP BW Open Hub anger du egenskapen type för datauppsättningen till **SapOpenHubTable**. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till **SapOpenHubTable**.  | Ja |
| openHubDestinationName | Namnet på mål för Öppna hubb som data ska kopieras från. | Ja |

Om du `excludeLastRequest` har `baseRequestId` ställt in och i datauppsättningen stöds den fortfarande som den är, medan du föreslås använda den nya modellen i aktivitetskällan framöver.

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av SAP BW Open Hub-källan.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub som källa

Om du vill kopiera data från SAP BW Open Hub stöds följande egenskaper i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för kopians aktivitetskälla måste anges till **SapOpenHubSource**. | Ja |
| excludeLastRequest | Om posterna för den senaste begäran ska uteslutas. | Nej (standard är **sant)** |
| baseRequestId (på) | ID för begäran om deltainläsning. När den har angetts hämtas endast data med requestId som är **större än** värdet för den här egenskapen.  | Inga |

>[!TIP]
>Om din Open Hub-tabell bara innehåller data som genereras av ett enda begärande-ID, till exempel, gör du alltid fullständig belastning och skriver över befintliga data i tabellen, eller om du bara kör DTP en gång för test, kom ihåg att avmarkera alternativet "excludeLastRequest" för att kopiera data ut.

För att påskynda datainläsningen [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kan du ställa in kopieringsaktiviteten för att läsa in data från SAP BW Open Hub parallellt. Om du till `parallelCopies` exempel anger fyra kör Data Factory samtidigt fyra RFC-anrop och varje RFC-anrop hämtar en del data från din SAP BW Open Hub-tabell som partitioneras av DTP-begärande-ID och paket-ID. Detta gäller när antalet unika DTP-begärande-ID + paket-ID är större än värdet för `parallelCopies`. När du kopierar data till filbaserat datalager, är det också recommanded att skriva till en mapp som flera filer (ange bara mappnamn), i vilket fall prestanda är bättre än att skriva till en enda fil.

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

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Datatypsmappning för SAP BW Open Hub

Vid kopiering av data från SAP BW Open Hub används följande mappningar från SAP BW-datatyper till Azure Data Factory interimsdatatyper. Se [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md) om du vill veta mer om hur du kopierar aktivitetsschemat och datatypen till diskhon.

| SAP ABAP-typ | Data fabrik interim datatyp |
|:--- |:--- |
| C (sträng) | String |
| I (heltal) | Int32 |
| F (Flottör) | Double |
| D (Datum) | String |
| T (Tid) | String |
| P (BCD-packad, valuta, decimal, ant) | Decimal |
| N (Numc) | String |
| X (binär och rå) | String |

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="troubleshooting-tips"></a>Felsökningstips

**Symptom:** Om du kör SAP BW på HANA och observerar endast delmängd av data kopieras över med ADF-kopieringsaktivitet (1 miljon rader), är den möjliga orsaken att du aktiverar alternativet "SAP HANA Execution" i din DTP, i vilket fall ADF bara kan hämta den första databatchen.

**Upplösning:** Inaktivera alternativet "SAP HANA Execution" i DTP, bearbeta om data och försök sedan köra kopieringsaktiviteten igen.

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
