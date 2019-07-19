---
title: Skapa Azure IoT Central Device Bridge | Microsoft Docs
description: Skapa IoT Central enhets brygga för att ansluta andra IoT-moln (Sigfox, partikel, nätverk osv.) till din IoT Central-app.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: c98574bc3447482429d7a028c6b47197e08e2e38
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850224"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Skapa IoT Central Device Bridge för att ansluta andra IoT-moln till IoT Central

*Det här avsnittet gäller för administratörer.*

IoT Central Device Bridge är en lösning med öppen källkod som ansluter dina Sigfox, partiklar, nätverk och andra moln till din IoT Central-app. Oavsett om du använder enheter för till gångs spårning som är anslutna till Sigfox med låg energi kvalitet eller om du använder luft kvalitets övervaknings enheter i molnet i molnet, eller använder jord vatten övervaknings enheter på TTN, kan du direkt dra nytta av kraften i IoT Central med IoT Central enhets bryggan. Enhets bryggan ansluter andra IoT-moln med IoT Central genom att vidarebefordra de data som enheterna skickar till de andra molnen via till din IoT Central-app. I din IoT Central-app kan du skapa regler och köra analyser på dessa data, skapa arbets flöden i Microsoft Flow och Azure Logic Apps, exportera dessa data och mycket mer. Hämta [IoT Central enhets bryggan](https://aka.ms/iotcentralgithubdevicebridge) från GitHub

## <a name="what-is-it-and-how-does-it-work"></a>Vad är det och hur fungerar det?
IoT Central Device Bridge är en lösning med öppen källkod i GitHub. Det är dags att börja med en "distribuera till Azure"-knapp som distribuerar en anpassad Azure Resource Manager-mall med flera Azure-resurser till din Azure-prenumeration. Resurserna omfattar:
-   Azure Function-app
-   Azure Storage-konto
-   Förbrukningsplan
-   Azure Key Vault

Function-appen är enhets bryggans kritiska del. Den tar emot HTTP POST-förfrågningar från andra IoT-plattformar eller anpassade plattformar via en enkel webhook-integrering. Vi har tillhandahållit exempel som visar hur du ansluter till Sigfox-, partikel-och TTN-moln. Du kan enkelt utöka den här lösningen för att ansluta till ditt anpassade IoT-moln om din plattform kan skicka HTTP POST-förfrågningar till din Function-app.
Funktionen app transformerar data i ett format som accepteras av IoT Central och vidarebefordrar det tillsammans via DPS-API: er.

![Skärm bild för Azure Functions](media/howto-build-iotc-device-bridge/azfunctions.png)

Om din IoT Central app känner igen enheten efter enhets-ID i det vidarebefordrade meddelandet visas en ny mätning för enheten. Om enhets-ID: t aldrig har visats av din IoT Central-app, försöker din Function-app registrera en ny enhet med det enhets-ID: t, så visas den som en "associerad enhet" i IoT Central-appen. 

## <a name="how-do-i-set-it-up"></a>Hur gör jag för att konfigurerar du den?
Anvisningarna visas i detalj i README-filen i GitHub-lagrings platsen. 

## <a name="pricing"></a>Prissättning
Azure-resurserna kommer att finnas i din Azure-prenumeration. Du kan lära dig mer om prissättning i [Readme-filen](https://aka.ms/iotcentralgithubdevicebridge).

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du skapar IoT Central enhets bryggan är det här det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Hantera dina enheter](howto-manage-devices.md)
