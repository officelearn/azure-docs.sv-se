---
title: Anpassa lösningen connected factory – Azure | Microsoft-dokument
description: En beskrivning av hur du anpassar beteendet hos lösningsacceleratorn ansluten fabrik.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 6062f8b3992732e0e0f9bbdae9549e69c393f4ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67080490"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Anpassa hur Connected Factory-lösningen visar data från dina OPC UA-servrar

Lösningen Connected Factory sammanställer och visar data från OPC UA-servrar som är anslutna till lösningen. Du kan bläddra bland och skicka kommandon till OPC UA-servrarna i din lösning. Mer information om OPC UA finns i [vanliga frågor om Connected Factory](iot-accelerators-faq-cf.md).

Exempel på aggregerade data i lösningen är de kpi:er (Overall Equipment Efficiency) och KPI:er (Key Performance Indicators) som du kan visa i instrumentpanelen på fabriks-, linje- och stationsnivå. Följande skärmbild visar OEE- och KPI-värdena för **monteringsstationen,** på **produktionslinje 1**, i **Münchenfabriken:**

![Exempel på OEE- och KPI-värden i lösningen][img-oee-kpi]

Lösningen gör att du kan visa detaljerad information från specifika dataobjekt från OPC UA-servrar, så kallade *stationer*. Följande skärmbild visar diagram över antalet tillverkade artiklar från en viss station:

![Tomter av antal tillverkade artiklar][img-manufactured-items]

Om du klickar på något av diagrammen kan du utforska data ytterligare med Hjälp av TSD (Time Series Insights):

![Utforska data med hjälp av Time Series Insights][img-tsi]

I den här artikeln beskrivs:

- Hur uppgifterna görs tillgängliga för de olika vyerna i lösningen.
- Hur du kan anpassa hur lösningen visar data.

## <a name="data-sources"></a>Datakällor

Lösningen Connected Factory visar data från OPC UA-servrar som är anslutna till lösningen. Standardinstallationen innehåller flera OPC UA-servrar som kör en fabrikssimulering. Du kan lägga till dina egna UA-servrar för OPC som [ansluter via en gateway][lnk-connect-cf] till din lösning.

Du kan bläddra bland de dataobjekt som en ansluten OPC UA-server kan skicka till din lösning på instrumentpanelen:

1. Välj **Webbläsare** för att navigera till **välja en OPC UA-servervy:**

    ![Navigera till välja en OPC UA-servervy][img-select-server]

1. Välj en server och klicka på **Anslut**. Klicka på **Fortsätt** när säkerhetsvarningen visas.

    > [!NOTE]
    > Den här varningen visas bara en gång för varje server och upprättar en förtroenderelation mellan lösningsinstrumentpanelen och servern.

1. Du kan nu bläddra bland de dataobjekt som servern kan skicka till lösningen. Objekt som skickas till lösningen är markerat:

    ![Publicerade objekt][img-published]

1. Om du är *administratör* i lösningen kan du välja att publicera ett dataobjekt för att göra det tillgängligt i lösningen Ansluten fabrik. Som administratör kan du också ändra värdet på dataobjekt och anropsmetoder på OPC UA-servern.

## <a name="map-the-data"></a>Mappa data

Lösningen Connected Factory mappar och sammanställer publicerade dataobjekt från OPC UA-servern till de olika vyerna i lösningen. Connected Factory-lösningen distribueras till ditt Azure-konto när du etablerar lösningen. En JSON-fil i Visual Studio Connected Factory-lösningen lagrar den här mappningsinformationen. Du kan visa och ändra den här JSON-konfigurationsfilen i lösningen Connected Factory Visual Studio. Du kan distribuera om lösningen när du har gjort en ändring.

Du kan använda konfigurationsfilen för att:

- Redigera befintliga simulerade fabriker, produktionslinjer och stationer.
- Kartdata från riktiga OPC UA-servrar som du ansluter till lösningen.

Mer information om hur du mappar och sammanställer data för att uppfylla dina specifika krav finns i Så här konfigurerar du [lösningsacceleratorn för Connected Factory ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Distribuera ändringarna

När du är klar med att göra ändringar i **filen ContosoTopologyDescription.json** måste du distribuera om lösningen Ansluten fabrik till ditt Azure-konto.

Den **azure-iot-anslutna** fabrikslagringsplatsen innehåller ett **build.ps1** PowerShell-skript som du kan använda för att återskapa och distribuera lösningen.

## <a name="next-steps"></a>Efterföljande moment

Läs mer om lösningsacceleratorn för Connected Factory genom att läsa följande artiklar:

* [Behörigheter på azureiotsolutions.com webbplats][lnk-permissions]
* [Vanliga frågor om ansluten fabrik](iot-accelerators-faq-cf.md)
* [Faq][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md