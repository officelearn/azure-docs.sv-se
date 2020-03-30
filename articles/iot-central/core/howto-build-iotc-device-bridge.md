---
title: Skapa Azure IoT Central device bridge | Microsoft-dokument
description: Bygg IoT Central enhetsbryggan för att ansluta andra IoT-moln (Sigfox, Particle, The Things Network etc.) till din IoT Central-app.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: 6499c9c29d10a2056b0af5499b68b5edd67d82cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158426"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Bygg IoT Central enhetsbryggan för att ansluta andra IoT-moln till IoT Central

*Det här avsnittet gäller administratörer.*

IoT Central enhetsbryggan är en lösning med öppen källkod som ansluter din Sigfox, Particle, The Things Network och andra moln till din IoT Central-app. Oavsett om du använder tillgångsspårningsenheter som är anslutna till Sigfox lågeffektsomfattande områdesnätverk, eller använder enheter för övervakning av luftkvalitet på partikelenhetsmolnet, eller använder jordfuktsövervakningsenheter på TTN, kan du direkt utnyttja kraften i IoT Central med hjälp av IoT Central enhetsbrygga. Enhetsbryggan ansluter andra IoT-moln med IoT Central genom att vidarebefordra de data som dina enheter skickar till de andra molnen till din IoT Central-app. I din IoT Central-app kan du skapa regler och köra analyser på dessa data, skapa arbetsflöden i Microsoft Flow- och Azure Logic-appar, exportera dessa data och mycket mer. Hämta [IoT Central enhetsbryggan](https://aka.ms/iotcentralgithubdevicebridge) från GitHub

## <a name="what-is-it-and-how-does-it-work"></a>Vad är det och hur fungerar det?
IoT Central device bridge är en lösning med öppen källkod i GitHub. Den är klar att gå med knappen "Distribuera till Azure" som distribuerar en anpassad Azure Resource Manager-mall med flera Azure-resurser till din Azure-prenumeration. Resurserna omfattar:
-    Azure-funktionsapp
-    Azure Storage-konto
-    Förbrukningsplan
-    Azure Key Vault

Funktionsappen är den kritiska delen av enhetsbryggan. Den tar emot HTTP POST-begäranden från andra IoT-plattformar eller anpassade plattformar via en enkel webhook-integrering. Vi har gett exempel som visar hur man ansluter till Sigfox, Particle och TTN moln. Du kan enkelt utöka den här lösningen för att ansluta till ditt anpassade IoT-moln om din plattform kan skicka HTTP POST-begäranden till din funktionsapp.
Funktionsappen omvandlar data till ett format som accepteras av IoT Central och vidarebefordrar dem via DPS API:er.

![Skärmbild av Azure-funktioner](media/howto-build-iotc-device-bridge/azfunctions.png)

Om din IoT Central-app känner igen enheten efter enhets-ID i det vidarebefordrade meddelandet visas en ny mätning för den enheten. Om enhets-ID aldrig har setts av din IoT Central-app försöker din funktionsapp registrera en ny enhet med det enhets-ID:t och den visas som en "Oassocierad enhet" i din IoT Central-app. 

## <a name="how-do-i-set-it-up"></a>Hur ställer jag in den?
Instruktionerna visas i detalj i README-filen i GitHub-repo. 

## <a name="pricing"></a>Prissättning
Azure-resurserna finns i din Azure-prenumeration. Du kan läsa mer om priser i [README-filen](https://aka.ms/iotcentralgithubdevicebridge).

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du bygger IoT Central enhetsbrygga, här är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Hantera dina enheter](howto-manage-devices.md)
