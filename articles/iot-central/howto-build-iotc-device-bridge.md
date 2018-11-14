---
title: Skapa Azure IoT Central enhet bryggan | Microsoft Docs
description: Skapa IoT Central enhet brygga för att ansluta andra IoT-moln (Sigfox, partikeln, The saker nätverk osv.) till din IoT Central-app.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 11/8/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 83f053a8815f31803f536920497fdc42e72d2a2d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629274"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Skapa IoT Central enhet brygga för att ansluta andra IoT-moln till IoT Central

*Det här avsnittet gäller för administratörer.*

Enhet-brygga IoT Central är en lösning för öppen källkod som ansluter din Sigfox, partikeln, saker nätverket och andra moln till din IoT Central-app. Om du använder enheter som är anslutna till Sigfoxs låg Power hela nätverket av tillgångar eller air kvalitet övervakning enheter på partikeln enhet moln eller med hjälp av jord fukt övervaka enheter på TTN kan du direkt dra nytta av IoT Central använder IoT Central enhet bryggan. Enheten bryggan ansluter andra IoT-moln med IoT Central som vidarebefordrar data enheterna skickar till de andra molnen via till din IoT Central-app. I din IoT Central-app kan du skapa regler och köra analyser på dessa data, skapa arbetsflöden i Microsoft Flow och Azure Logic apps, exportera dessa data och mycket mer. Hämta den [IoT Central enhet brygga](https://aka.ms/iotcentralgithubdevicebridge) från Github

## <a name="what-is-it-and-how-does-it-work"></a>Vad är det och hur fungerar det?
Enhet-brygga IoT Central är en lösning för öppen källkod i Github. Det är redo att sätta igång med en ”distribuera till Azure”-knapp som distribuerar en anpassad Azure Resource Manager-mall med flera Azure-resurser i din Azure-prenumeration. Resurserna är:
-   Azure Functions-app
-   Azure Storage-konto
-   App Service-Plan (S1-nivå)
-   Azure Key Vault funktionsappen är viktiga delar av enheten bryggan. Den tar emot HTTP POST-förfrågningar från andra IoT-plattformar eller valfri anpassad plattformar via ett enkelt webhook-integrering. Vi har lagt till exempel som visar hur du ansluter till Sigfox och partikeln TTN moln. Du kan enkelt utöka den här lösningen för att ansluta till din anpassade IoT-molnet om din plattform kan skicka HTTP POST-förfrågningar till din funktionsapp.
Funktionsappen omvandlar data till ett format som accepteras av IoT Central och vidarebefordrar det längs DPS-API: er.

![Skärmbild av Azure functions](media/howto-build-iotc-device-bridge/azfunctions.png)

Om din IoT Central-app kan identifiera enheten av enhets-ID i det vidarebefordrade meddelandet, visas ett nytt mått för enheten. Om enhets-ID har aldrig observerats av din IoT Central-app, funktionsappen försöker registrera en ny enhet med enhets-ID och den visas som en ”olänkade enhet” i din IoT Central-app. 

## <a name="how-do-i-set-it-up"></a>Hur konfigurerar jag det?
Anvisningarna finns i detalj i README-filen i Github-lagringsplatsen. 

## <a name="pricing"></a>Prissättning
Det finns alla i din Azure-prenumeration. Merparten av den uppskattade kostnaden för de etablerade resurserna kommer från den [priset för en standard Apptjänstplan]( https://azure.microsoft.com/en-us/pricing/details/app-service/windows/). Du kan läsa mer om detta och potentiella sätt att minska det i filen Viktigt.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar IoT Central enhet brygga, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Hantera dina enheter](howto-manage-devices.md)