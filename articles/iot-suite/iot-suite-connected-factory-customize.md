---
title: "Anpassa lösningen anslutna factory - Azure | Microsoft Docs"
description: "En beskrivning av hur du anpassar anslutna fabriken förkonfigurerade lösningen."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 09b146740413e74e3030bf3a6cb660a3cfabd239
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Anpassa hur lösningen anslutna factory visar data från dina OPC UA-servrar

Anslutna factory lösningen sammanställer och visar data från OPC UA-servrar som är anslutna till lösningen. Du kan bläddra och skicka kommandon till OPC UA-servrar i din lösning. Mer information om OPC UA finns i [Vanliga frågor och svar om ansluten fabrik](iot-suite-faq-cf.md).

Exempel på sammanställda data i lösningen är den övergripande utrustning effektivitet (OEE) och nyckeln (nyckeltal) som kan visas på instrumentpanelen på fabriken, rad och station nivåer. Följande skärmbild visar OEE och KPI-värden för den **sammansättningen** station på **produktion rad 1**i den **München** fabriken:

![Exempel på OEE och KPI-värden i lösningen][img-oee-kpi]

Den här lösningen kan du visa detaljerad information från specifika dataobjekt från OPC UA-servrar kallas *stationer*. Följande skärmbild visar områden av antalet tillverkade artiklar från en specifik station:

![Områden av antal tillverkade artiklar][img-manufactured-items]

Om du klickar på diagrammet kan du utforska data med ytterligare tid serien insikter (TSD):

![Utforska data med hjälp av tid serien insikter][img-tsi]

Den här artikeln beskrivs:

- Hur data ska få tillgång till de olika vyerna i lösningen.
- Visar hur du kan anpassa hur lösningen data.

## <a name="data-sources"></a>Datakällor

Den anslutna factory-lösningen visar data från OPC UA-servrar som är anslutna till lösningen. Standardinstallationen omfattar flera OPC UA-servrar som kör en fabrik simulering. Du kan lägga till egna OPC UA-servrar som [ansluta via en gateway] [ lnk-connect-cf] i lösningen.

Du kan bläddra dataobjekt som en anslutna OPC UA-servern kan skicka din lösning på instrumentpanelen:

1. Navigera till den **väljer du en OPC UA server** vy:

    ![Navigera till Markera en vy som OPC UA][img-select-server]

1. Välj en server och klicka på **Anslut**. Klicka på **Fortsätt** när säkerhetsvarningen visas.

    > [!NOTE]
    > Den här varningen visas en gång för varje server endast och upprättar en förtroenderelation mellan lösning instrumentpanelen och servern.

1. Du kan nu bläddra dataobjekt som servern kan skicka till lösningen. Objekt som skickas till lösningen har en grön bock:

    ![Publicerade objekt][img-published]

1. Om du är en *administratör* i lösningen kan du välja att publicera ett dataobjekt att göra den tillgänglig i den anslutna factory-lösningen. Som administratör kan du också ändra värdet för dataobjekt och anropa metoder i OPC UA-server.

## <a name="map-the-data"></a>Mappa data

Den anslutna factory-lösningen mappar och sammanställer publicerade dataobjekt från servern OPC UA till de olika vyerna i lösningen. Anslutna factory-lösningen distribueras till din Azure-konto när du etablerar lösningen. En JSON-fil i Visual Studio anslutna factory lösningen lagrar mappningsinformationen. Du kan visa och ändra den här JSON-konfigurationsfil i anslutna fabriken Visual Studio-lösning. Du kan distribuera lösningen igen när du har gjort en ändring.

Du kan använda konfigurationsfilen till:

- Redigera den befintliga simulerade fabriker och produktion rader stationer.
- Mappa data från verkliga OPC UA-servrar som du ansluter till lösningen.

Använd följande kommando i git för att klona en kopia av anslutna fabriken Visual Studio-lösning:

`git clone https://github.com/Azure/azure-iot-connected-factory.git`

Filen **ContosoTopologyDescription.json** definierar mappningen från OPC UA server dataobjekt till vyerna i instrumentpanelen för anslutna factory-lösning. Du hittar den här konfigurationsfilen i den **Contoso\Topology** mapp i den **WebApp** projekt i Visual Studio-lösning.

Innehållet i JSON-filen har ordnats som en hierarki av fabriken, produktionen och station noder. Den här hierarkin definierar navigeringshierarkin i instrumentpanelen för anslutna fabriken. Värdena på varje nod i hierarkin avgör vilken information som visas i instrumentpanelen. JSON-filen innehåller till exempel följande värden för München fabriken:

```json
"Guid": "73B534AE-7C7E-4877-B826-F1C0EA339F65",
"Name": "Munich",
"Description": "Braking system",
"Location": {
    "City": "Munich",
    "Country": "Germany",
    "Latitude": 48.13641,
    "Longitude": 11.57754
},
"Image": "munich.jpg"
```

Namn, beskrivning och plats visas på den här vyn i instrumentpanelen:

![München data på instrumentpanelen][img-munich]

Varje fabriken, produktionen och station har ett image-egenskapen. Du hittar dessa JPEG-filer i den **Content\img** mapp i den **WebApp** projekt. Dessa bildfiler visas i instrumentpanelen för anslutna fabriken.

Varje station innehåller flera detaljerade egenskaper som definierar mappningen från OPC UA dataobjekt. De här egenskaperna beskrivs i följande avsnitt:

### <a name="opcuri"></a>OpcUri

Den **OpcUri** värdet är OPC UA programmet URI: N som unikt identifierar OPC UA-servern. Till exempel den **OpcUri** för sammansättningen station på produktion rad 1 i München ser ut som följande: **urn: scada2194:ua:munich:productionline0:assemblystation**.

Du kan visa URI: er med anslutna OPC UA-servrar i instrumentpanelen för lösningen:

![Visa OPC UA server URI: er][img-server-uris]

### <a name="simulation"></a>Simulering

Informationen i den **simuleringen** nod är specifik för OPC UA simuleringen som körs i OPC UA-servrar som tillhandahålls som standard. Den används inte för en verklig OPC UA-server.

### <a name="kpi1-and-kpi2"></a>Kpi1 och Kpi2

Dessa noder beskrivs hur data från stationen som bidrar till de två KPI-värdena i instrumentpanelen. Dessa KPI-värden stöds i en standarddistribution, enheter per timme och kWh per timme. Lösningen beräknas KPI vales på en station nivå och sammanställer dem i produktionen och factory nivåer.

Varje KPI har en lägsta, högsta och målvärdet. Varje KPI-värdet kan också definiera aviseringsåtgärder för anslutna factory-lösningen att utföra. Följande utdrag visar KPI-definitioner för sammansättningen station produktion rad 1 i München:

```json
"Kpi1": {
  "Minimum": 150,
  "Target": 300,
  "Maximum": 600
},
"Kpi2": {
  "Minimum": 50,
  "Target": 100,
  "Maximum": 200,
  "MinimumAlertActions": [
    {
      "Type": "None"
    }
  ]
}
```

Följande skärmbild visar KPI-data i instrumentpanelen.

![KPI-information på instrumentpanelen][lnk-kpi]

### <a name="opcnodes"></a>OpcNodes

Den **OpcNodes** noder identifiera publicerade dataobjekt från OPC UA-servern och ange hur att bearbeta dessa data.

Den **NodeId** värdet identifierar specifika OPC UA NodeID från OPC UA-servern. Den första noden i sammansättningen station för produktion rad 1 i München har värdet **ns = 2, i = 385**. En **NodeId** värdet anger dataobjektet att läsa från OPC UA-server och **SymbolicName** ger ett användarvänligt namn på instrumentpanelen för dessa data.

Andra värden som är kopplade till varje nod sammanfattas i följande tabell:

| Värde | Beskrivning |
| ----- | ----------- |
| Relevans  | Värdet för KPI-Indikatorn och OEE informationen bidrar till. |
| OpCode     | Hur informationen sammanställs. |
| Enheter      | Enheterna som används i instrumentpanelen.  |
| Synliga    | Om du vill visa det här värdet i instrumentpanelen. Vissa värden beräkningar men visas inte.  |
| Maximalt    | Det maximala värdet som utlöser en avisering i instrumentpanelen. |
| MaximumAlertActions | En åtgärd som svar på en avisering. Till exempel skicka ett kommando till en station. |
| ConstValue | Ett konstantvärde som används i en beräkning. |

## <a name="deploy-the-changes"></a>Distribuera ändringarna

När du är klar med att göra ändringar i den **ContosoTopologyDescription.json** fil, du måste distribuera om anslutna factory lösningen till din Azure-konto.

Den **azure iot-ansluten-fabrik** databasen innehåller en **build.ps1** PowerShell-skript som du kan använda för att återskapa och distribuera lösningen.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den anslutna factory förkonfigurerade lösningen genom att läsa följande artiklar:

* [Genomgång av den förkonfigurerade lösningen Ansluten fabrik][lnk-rm-walkthrough]
* [Distribuera en gateway för anslutna factory][lnk-connect-cf]
* [Behörigheter på webbplatsen azureiotsuite.com][lnk-permissions]
* [Vanliga frågor och svar om ansluten fabrik](iot-suite-faq-cf.md)
* [VANLIGA FRÅGOR OCH SVAR][lnk-faq]


[img-oee-kpi]: ./media/iot-suite-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-suite-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-suite-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-suite-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-suite-connected-factory-customize/published.png
[img-munich]: ./media/iot-suite-connected-factory-customize/munich.png
[img-server-uris]: ./media/iot-suite-connected-factory-customize/serveruris.png
[lnk-kpi]: ./media/iot-suite-connected-factory-customize/kpidisplay.png

[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-faq]: iot-suite-v1-faq.md