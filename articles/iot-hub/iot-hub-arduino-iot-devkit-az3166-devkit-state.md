---
title: Använda Azure enheten twins för att styra MXChip IoT DevKit användaren Indikator | Microsoft Docs
description: I den här kursen lär du dig hur du övervakar DevKit tillstånd och styr användare Indikator med twins för Azure IoT Hub-enhet.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: ac013cf234fdc7249d4d71992435adcc27fcc64e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

Du kan använda det här exemplet att övervaka MXChip IoT DevKit WiFi information och sensor tillstånd och styra Indikator för användare med hjälp av Azure IoT Hub-enhet twins färg.

## <a name="what-you-learn"></a>Detta får du får lära dig

- Så här övervakar du MXChip IoT DevKit sensor tillstånd.
- Hur du använder Azure enheten twins för att styra färgen för DevKit RGB-Indikator.

## <a name="what-you-need"></a>Vad du behöver

- Ställ in din utvecklingsmiljö genom att följa den [komma igång](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
- Skriv följande kommandon från din Bash terminalfönster (eller andra Git-kommandoradsgränssnittet):
    - `git clone https://github.com/DevKitExamples/DevKitState.git`
    - `cd DevKitState`
    - `code .`

## <a name="provision-azure-services"></a>Etablera Azure-tjänster

1. Klicka på den **uppgifter** nedrullningsbara menyn i Visual Studio-koden och välj **kör uppgift...**   -  **moln etablera**.
2. Installationsförloppet visas under den **TERMINAL** för den **Välkommen** panelen.
3. När du uppmanas med meddelandet *vilken prenumeration du vill välja*, välja en prenumeration.
4. Välj eller välj en resursgrupp. 
 
    > [!NOTE]
    > Om du redan har en kostnadsfri IoT-hubb hoppas det här steget.

5. När du uppmanas med meddelandet *vilka IoT-hubb du vill välja*väljer eller skapar en IoT-hubb.
6. Något liknande *funktionsapp: funktionen programnamn: xxx*, visas. Skriv ned namnet på funktionen appen; den används i ett senare steg.
7. Vänta tills den Azure Resource Manager malldistribution till slut, vilket är anges när meddelandet *malldistribution för hanteraren för filserverresurser: klar* visas.

## <a name="deploy-function-app"></a>Distribuera Funktionsapp

1. Klicka på den **uppgifter** nedrullningsbara menyn i Visual Studio-koden och välj **kör uppgift...**   -  **moln distribuera**.
2. Vänta tills app Funktionskoden överför processen avslutas. meddelandet *funktionsapp distribuerar: klar* visas.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Konfigurera anslutningssträngen för IoT Hub-enhet i DevKit

1. Anslut din MXChip IoT DevKit till datorn.
2. Klicka på den **uppgifter** nedrullningsbara menyn i Visual Studio-koden och välj **kör uppgift...**   -  **config enhetsanslutning**
3. På MXChip IoT DevKit, tryck och håll knappen **A**, tryck på den **återställa** knappen och sedan på knappen versionen **A** att göra DekKit ange konfigurationsläge.
4. Vänta tills anslutningen sträng konfigurationsprocessen ska slutföras.

## <a name="upload-arduino-code-to-devkit"></a>Överför Arduino kod till DevKit

Ansluten till datorn med din MXChip IoT DevKit:
1. Klicka på den **uppgifter** nedrullningsbara menyn i Visual Studio-koden och välj **kör Build Task...** Arduino ritningarna kompileras och överförs till DevKit.
2. När ritningarna har överförts, en *Build & Överför skissa: lyckade* meddelande.

## <a name="monitor-devkit-state-in-browser"></a>Tillstånd för Övervakare för DevKit i webbläsare

1. I en webbläsare, öppnar den `DevKitState\web\index.html` filen som skapades under den [vad du behöver](#whatyouneed) steg.
2. På följande webbsida visas:![Ange namnet på funktionen appen.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)
1. Ange namnet på funktionen appen som du skrev ned tidigare.
2. Klicka på den **Anslut** knappen
3. Inom några sekunder sidan uppdateras och visar den DevKit WiFi anslutningsstatus och tillståndet för var och en av de inbyggda sensorerna.

## <a name="control-the-devkits-user-led"></a>Kontrollera den DevKit användaren Indikator

1. Klicka på användare Indikator för bilden på webbsidan bilden.
2. Inom några sekunder skärmen uppdaterar och visar den aktuella Färgstatusen för användaren Indikator.
3. Försök att ändra färgvärdet för Indikator för RGB-genom att klicka på olika ställen på skjutreglage RGB.

## <a name="example-operation"></a>Exempel åtgärden

![Procedur för test](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du:
- Anslut en MXChip IoT DevKit enhet till din Azure IoT-Fjärrövervaknings lösningsaccelerator.
- Funktionen Azure IoT-enhet twins känner av och kontrollera färgen för DevKit RGB-Indikator.

Här är de föreslagna nästa steg:

* [Azure IoT fjärråtkomst övervakning solution accelerator-översikt](https://docs.microsoft.com/azure/iot-suite/)
* [Anslut en MXChip IoT DevKit enhet till programmet Microsoft IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
