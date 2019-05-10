---
title: Simulering enhetsöversikt – Azure | Microsoft Docs
description: En beskrivning av lösningsaccelerator Enhetssimulering och dess funktioner.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: f58eb05ed582cf18157a76f4d637d72a228f4e96
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464875"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Översikt över lösningsacceleratorn för enhetssimulering

I en molnbaserad IoT-lösning ansluter dina enheter till en molnslutpunkt för att skicka telemetri, till exempel temperatur, plats och status. Lösningen använder den här telemetrin, så att du kan vidta åtgärder eller slutsatser från den.

När du utvecklar en IoT-lösning, är experimentering och testning viktiga delar av den här processen. Simulering är ett viktigt verktyg i hela den här processen. Med Enhetssimulering kan du:

* Hämta en prototyp snabbt igång och sedan iterera genom att justera simulerade enhetsbeteende direkt. Den här processen kan du prova tanken innan du investerar i kostsamma maskinvara. Du kan skapa anpassade enheter via webbgränssnittet för att generera en prototyp-enhet på några sekunder.
* Validera att lösningen fungerar som förväntat från enhet till lösning genom att simulera verkliga enhetsbeteenden. Du kan skapa skript komplexa enhetsbeteenden som använder JavaScript för att generera realistisk simulerad telemetri.
* Skala Testa lösningen genom simulering normal belastning och mycket mer högsta belastningstillstånd. Skala tester också hur du till rätt storlek på Azure-resurser för att köra din lösning.

![Exemplet drönare simulering](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Med Enhetssimulering kan du definiera enhetsmodeller för att simulera verkliga-enheter. Den här modellen innehåller meddelandeformat, tvillingegenskaper och metoder. Du kan också simulera komplexa enhetsbeteenden med JavaScript.

Du kan köra simuleringar för en till tusentals enheter som ansluter till alla IoT-hubb. För att hjälpa till med testning, kan alternativt du distribuera en IoT hub tillsammans med Enhetssimulering som en fristående miljö.

Enhetssimulering är kostnadsfri. Enhetssimulering distribuerar på Azure-prenumerationen i molnet och att använda Azure-resurser. Om Enhetssimulering inte uppfyller dina krav på [källkoden finns också på GitHub](https://github.com/Azure/device-simulation-dotnet) som du kan kopiera och ändra.

## <a name="sample-simulations"></a>Exemplet simuleringar

När du distribuerar Enhetssimulering kan få du vissa exempel simuleringar och exempelenheter. Du kan använda de här exemplen för att lära dig hur du använder Enhetssimulering. Kom igång genom att köra en [exempel simulering som simulerar 10 lastbilar](quickstart-device-simulation-deploy.md). Du kan också [skapa dina egna simulering med någon av de många exempelenheter som tillhandahålls](iot-accelerators-device-simulation-create-simulation.md).

![Simuleringskonfiguration](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Anpassade simulerade enheter

Du kan använda Enhetssimulering till [skapa anpassade modeller](iot-accelerators-device-simulation-create-custom-device.md) ska användas i dina simuleringar. Exempelvis kan definiera du en ny kylskåp enhetsmodell som skickar telemetri om temperatur och fuktighet. Anpassade simulerade enheter är idealiska för enkel enhetsbeteenden med slumpmässiga, öka eller minska telemetri värde.

![Skapa enhetsmodell](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Avancerade simulerade enheter

När du behöver mer kontroll över telemetrivärden som en enhet skickar kan använda du en avancerad enhetsmodell. Avancerade enhetsmodeller Aktivera JavaScript-stöd för att manipulera värdena skickat telemetri. Exempel: du kan simulera inre temperaturen för en parkerat bil på frekvent skiner, som ökar yttre temperatur, inre temperaturen ökar exponentiellt.

Avancerade modeller kan du [skapa och ladda upp egna enhetsmodeller](iot-accelerators-device-simulation-advanced-device.md) som består av en enhetsdefinition i JSON-filen och motsvarande JavaScript-filer.

Avancerade modeller kan du:

* Ange meddelandeformatet som skickas från enheten tillsammans med typerna av telemetri.
* Använd anpassade skript för att generera telemetrivärden som underhåller tillståndet för enheten med tiden.
* Använd anpassade skript för att ange hur den simulerade enheten svarar på metoder.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om lösningsaccelerator Enhetssimulering och dess funktioner. Kom igång med solution accelerator, fortsätter du till Snabbstart:

> [!div class="nextstepaction"]
> [Distribuera och köra en IoT-enhetssimulering i Azure](quickstart-device-simulation-deploy.md)
