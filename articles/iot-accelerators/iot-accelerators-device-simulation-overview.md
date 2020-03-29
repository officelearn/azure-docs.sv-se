---
title: Översikt över enhetssimulering – Azure | Microsoft-dokument
description: En beskrivning av device simulation-lösningsacceleratorn och dess funktioner.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: f58eb05ed582cf18157a76f4d637d72a228f4e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65464875"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Översikt över lösningsacceleratorn för enhetssimulering

I en molnbaserad IoT-lösning ansluter dina enheter till en molnslutpunkt för att skicka telemetri som temperatur, plats och status. Din lösning förbrukar den här telemetrin, så att du kan vidta åtgärder eller härleda insikter från den.

När du utvecklar en IoT-lösning är experiment och testning viktiga delar av den processen. Simulering är ett viktigt verktyg under hela denna process. Med Enhetssimulering kan du:

* Snabbt få en prototyp igång och sedan iterera genom att justera simulerade enhetens beteende i farten. Denna process kan du bevisa idén innan du investerar i dyr hårdvara. Du kan skapa anpassade enheter via webbgränssnittet för att generera en prototypenhet på några sekunder.
* Validera lösningen fungerar som förväntat från enhet till lösning genom att simulera verkliga enhetsbeteenden. Du kan skript komplexa enhetsbeteenden med JavaScript för att generera realistiska simulerad telemetri.
* Skala testa din lösning genom att simulera normal, topp och bortom toppbelastningsförhållanden. Skala tester hjälper dig också att rätt storlek på Azure-resurser som behövs för att köra din lösning.

![Exempel på drönarsimulering](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Med Enhetssimulering kan du definiera enhetsmodeller för att simulera dina riktiga enheter. Den här modellen innehåller meddelandeformat, dubbla egenskaper och metoder. Du kan också simulera komplexa enhetsbeteenden med JavaScript.

Du kan köra simuleringar för en till tusentals enheter som ansluter till alla IoT-hubb. Du kan hjälpa till med testningen genom att distribuera en IoT-hubb tillsammans med Enhetssimulering för en fristående miljö.

Enhetssimulering är gratis. Enhetssimulering distribuerar dock till din Azure-prenumeration i molnet och förbrukar Azure-resurser. Om enhetssimuleringen inte uppfyller dina krav [är källkoden också tillgänglig på GitHub](https://github.com/Azure/device-simulation-dotnet) för att du ska kunna kopiera och ändra.

## <a name="sample-simulations"></a>Exempelsimuleringar

När du distribuerar Enhetssimulering får du några exempelsimuleringar och exempelenheter. Du kan använda dessa exempel för att lära dig hur du använder Enhetssimulering. För att komma igång, kör en [exempelsimulering som simulerar 10 lastbilar](quickstart-device-simulation-deploy.md). Du kan också [skapa en egen simulering med hjälp av en av de många exempelenheter som tillhandahålls](iot-accelerators-device-simulation-create-simulation.md).

![Simuleringskonfiguration](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Anpassade simulerade enheter

Du kan använda enhetssimuleringen för att [skapa anpassade enhetsmodeller](iot-accelerators-device-simulation-create-custom-device.md) som ska användas i dina simuleringar. Du kan till exempel definiera en ny modell för kylskåpsenhet som skickar telemetri för temperatur och luftfuktighet. Anpassade simulerade enheter är idealiska för enkla enhetsbeteenden med slumpmässiga, stegvisa eller minskning av telemetrivärden.

![Skapa enhetsmodell](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Avancerade simulerade enheter

När du behöver mer kontroll över telemetrivärdena som en enhet skickar kan du använda en avancerad enhetsmodell. Avancerade enhetsmodeller aktiverar JavaScript-stöd för att manipulera de skickade telemetrivärdena. Till exempel kan du simulera den inre temperaturen på en parkerad bil på en varm solig dag - som den yttre temperaturen stiger, den inre temperaturen ökar exponentiellt.

Med avancerade enhetsmodeller kan du [skapa och ladda upp egna enhetsmodeller](iot-accelerators-device-simulation-advanced-device.md) som består av en JSON-enhetsdefinitionsfil och motsvarande JavaScript-filer.

Med avancerade enhetsmodeller kan du:

* Ange det meddelandeformat som skickas från enheten tillsammans med telemetrityperna.
* Använd anpassade skript för att generera telemetrivärden som upprätthåller enhetens tillstånd över tid.
* Använd anpassade skript för att ange hur den simulerade enheten svarar på metoder.

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig om device simulation-lösningsacceleratorn och dess funktioner. För att komma igång med lösningsacceleratorn fortsätter du till snabbstarten:

> [!div class="nextstepaction"]
> [Distribuera och köra en IoT-enhetssimulering i Azure](quickstart-device-simulation-deploy.md)
