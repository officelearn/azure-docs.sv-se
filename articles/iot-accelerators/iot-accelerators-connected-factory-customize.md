---
title: Anpassa lösningen för ansluten fabrik – Azure | Microsoft Docs
description: En beskrivning av hur du anpassar lösningsaccelerator ansluten fabrik.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 7d4e95d066e191e1d5b6d083ede65843dbe73f31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450435"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Anpassa visningen av data från dina OPC UA-servrar i lösningen för ansluten fabrik

Lösningen för ansluten fabrik aggregerar och visar data från OPC UA-servrar som är anslutna till lösningen. Du kan bläddra och skicka kommandon till OPC UA-servrar i din lösning. Mer information om OPC UA finns i [Vanliga frågor och svar om Ansluten fabrik](iot-accelerators-faq-cf.md).

Exempel på sammanställda data i lösningen är de övergripande utrustning effektivitet (OEE) och OEM-tillverkare (Key Performance Indicator) som du kan visa i instrumentpanelen på factory, rad och station nivåer. Följande skärmbild visar OEE och KPI-värden för den **sammansättningen** station på **produktionslinje 1**i den **München** factory:

![Exempel på OEE och KPI-värden i lösningen][img-oee-kpi]

Den här lösningen kan du visa detaljerad information från specifika dataobjekt från OPC UA-servrar kallas *stationer*. Följande skärmbild visar områden av antalet tillverkade artiklar från en specifik station:

![Områden av antalet tillverkade artiklar][img-manufactured-items]

Om du klickar på diagrammet kan du utforska data ytterligare med Time Series Insights (TSI):

![Utforska data med hjälp av Time Series Insights][img-tsi]

Den här artikeln beskrivs:

- Hur data görs tillgänglig för de olika vyerna i lösningen.
- Hur du kan anpassa hur lösningen visar data.

## <a name="data-sources"></a>Datakällor

Lösningen för ansluten fabrik visar data från OPC UA-servrar som är anslutna till lösningen. Standardinstallationen omfattar flera OPC UA-servrar som kör en factory simulering. Du kan lägga till dina egna OPC UA-servrar som [ansluta via en gateway] [ lnk-connect-cf] i lösningen.

Du kan bläddra bland de dataobjekt som en ansluten OPC UA-servern kan skicka till din lösning på instrumentpanelen:

1. Välj **webbläsare** att navigera till den **Välj en OPC UA-server** vy:

    ![Navigera till Markera en vy för OPC UA-server][img-select-server]

1. Välj en server och klicka på **Connect**. Klicka på **Fortsätt** när säkerhetsvarningen visas.

    > [!NOTE]
    > Den här varningen bara förekommer en gång för varje server och upprättar en förtroenderelation mellan lösningens instrumentpanel och servern.

1. Du kan nu bläddra dataobjekt som servern kan skicka till lösningen. Objekt som skickas till lösningen är markerade:

    ![Publicerade objekt][img-published]

1. Om du är en *administratör* i lösningen, kan du välja att publicera ett dataobjekt så att den blir tillgänglig i lösningen för ansluten fabrik. Du kan också ändra värdet för dataobjekt och anropa metoder i OPC UA-servern som administratör.

## <a name="map-the-data"></a>Mappa data

Lösningen för ansluten fabrik mappar och läggs till de publicerade dataobjekt från OPC UA-servern till de olika vyerna i lösningen. Lösningen för ansluten fabrik distribuerar till ditt Azure-konto när du etablerar lösningen. Av mappningsinformationen lagras i en JSON-fil i Visual Studio Connected Factory-lösning. Du kan visa och ändra den här JSON-konfigurationsfil i ansluten fabrik Visual Studio-lösning. Du kan distribuera lösningen igen när du har gjort en ändring.

Du kan använda konfigurationsfilen för att:

- Redigera den befintliga simulerade fabriker och produktionsrader stationer.
- Visualisera data från verkliga OPC UA-servrar som du ansluter till lösningen.

Mer information om mappning och datainsamling för att uppfylla dina specifika krav finns i [så här konfigurerar du lösningsaccelerator ansluten fabrik ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Distribuera ändringarna

När du är klar med att göra ändringar i den **ContosoTopologyDescription.json** filen, måste du distribuera om lösningen för ansluten fabrik på Azure-kontot.

Den **azure-iot-connected-factory** databasen innehåller en **build.ps1** PowerShell-skript som du kan använda för att återskapa och distribuera lösningen.

## <a name="next-steps"></a>Nästa steg

Läs mer om ansluten fabrik lösningsaccelerator genom att läsa följande artiklar:

* [Genomgång av lösningsacceleratorn Ansluten fabrik][lnk-rm-walkthrough]
* [Distribuera en gateway för ansluten fabrik][lnk-connect-cf]
* [Behörigheter på webbplatsen azureiotsolutions.com][lnk-permissions]
* [Vanliga frågor och svar om ansluten fabrik](iot-accelerators-faq-cf.md)
* [VANLIGA FRÅGOR OCH SVAR][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-rm-walkthrough]:iot-accelerators-connected-factory-sample-walkthrough.md
[lnk-connect-cf]:iot-accelerators-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md