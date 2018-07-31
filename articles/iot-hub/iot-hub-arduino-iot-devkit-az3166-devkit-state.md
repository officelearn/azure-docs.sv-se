---
title: Använda Azure enhetstvillingar för att styra MXChip IoT DevKit användaren LED | Microsoft Docs
description: I de här självstudierna lär du dig hur du övervakar DevKit tillstånd och styra användaren LED med Azure IoT Hub enhetstvillingar.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: 6bc1255c5bbb9cf74c97b88600f34e7fcd90ae4f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343167"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

Du kan använda det här exemplet att övervaka MXChip IoT DevKit WiFi information och sensorn tillstånd och styra färgen på användare-LED använder enhetstvillingar för Azure IoT Hub.

## <a name="what-you-learn"></a>Detta får du får lära dig

- Så här övervakar du MXChip IoT DevKit sensorn tillstånd.

- Hur du använder Azure enhetstvillingar för att styra färgen för DevKit RGB-LED.

## <a name="what-you-need"></a>Vad du behöver

- Konfigurera din utvecklingsmiljö genom att följa den [Kom igång-guiden](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

- Skriv följande kommandon från GitBash terminalfönstret (eller andra Git-kommandoradsgränssnittet):

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Etablera Azure-tjänster

1. Klicka på den **uppgifter** nedrullningsbara menyn i Visual Studio Code och välj **kör uppgift...**   -  **molnet etablera**.

2. Förloppet visas under den **TERMINAL** fliken den **Välkommen** panelen.

3. När du uppmanas med meddelandet *vilken prenumeration du vill välja*, Välj en prenumeration.

4. Välj eller välj en resursgrupp. 
 
   > [!NOTE]
   > Om du redan har en kostnadsfri IoT Hub kan du hoppa över det här steget.

5. När du uppmanas med meddelandet *vilken IoT-hubb skulle du vilja välja*väljer eller skapar en IoT-hubb.

6. Resultatet ungefär *funktionsapp: funktionsappens namn: xxx*, visas. Skriv ned namnet på funktionen; den används i ett senare steg.

7. Vänta tills Azure Resource Manager malldistributionen är klar, vilket är anges när meddelandet *Resource Manager för malldistribution: klar* visas.

## <a name="deploy-function-app"></a>Distribuera Funktionsappen

1. Klicka på den **uppgifter** nedrullningsbara menyn i Visual Studio Code och välj **kör uppgift...**   -  **molnet – distribuera**.

2. Vänta tills funktionskod laddar upp processen avslutas. meddelandet *funktionsapp distribuerar: klar* visas.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Konfigurera anslutningssträngen för IoT Hub-enhet i DevKit

1. Ansluta MXChip IoT DevKit till din dator.

2. Klicka på den **uppgifter** nedrullningsbara menyn i Visual Studio Code och välj **kör uppgift...**   -  **config enhetsanslutning**

3. På MXChip IoT DevKit, tryck på och håll ned knappen **A**, tryck på den **återställa** knappen och sedan på knappen versionen **A** att göra DekKit ange konfigurationsläge.

4. Vänta tills anslutningen sträng konfigurationsprocessen ska slutföras.

## <a name="upload-arduino-code-to-devkit"></a>Ladda upp Arduino kod till DevKit

Ansluten till datorn med MXChip IoT DevKit:

1. Klicka på den **uppgifter** nedrullningsbara menyn i Visual Studio Code och välj **kör Skapa uppgift...** Arduino skissen samlas in och överförs till DevKit.

2. När skissen har överförts, en *Build & Överför skiss: lyckad* meddelande.

## <a name="monitor-devkit-state-in-browser"></a>Tillstånd för Övervakare för DevKit i webbläsare

1. I en webbläsare, öppna den `DevKitState\web\index.html` filen som skapades under den [vad du behöver](#whatyouneed) steg.

2. På följande webbsida visas:![Ange namnet på funktionen.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Ange funktionsappens namn du skrev ned tidigare.

4. Klicka på den **Connect** knappen

5. Inom några sekunder sidan uppdateras och visar den DevKit Wi-Fi-anslutningsstatus och status för var och en av de inbyggda sensorerna.

## <a name="control-the-devkits-user-led"></a>Kontrollera den DevKit användaren Indikator

1. Klicka på användare LED bilden på bilden webbsida.

2. Inom några sekunder skärmen uppdateras och visar den aktuella Färgstatusen för användaren LED.

3. Försök att ändra värdet för RGB-LED genom att klicka på olika platser på RGB-skjutreglage.

## <a name="example-operation"></a>Exempel-åtgärden

![Testa exempelprocedur](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Du kan se rådata för enhetstvilling i Azure portal: IoT Hub -\> IoT-enheter –\> *\<enheten\>*  - \> Enhetstvillingen.

## <a name="next-steps"></a>Nästa steg

Har du lärt dig hur du:
- Anslut en enhet för MXChip IoT DevKit till din Azure IoT lösningsacceleratorn för fjärrövervakning.
- Funktionen Azure IoT device twins kan känna av och styra färgen för DevKit RGB-LED.

Här följer nästa föreslagna steg:

* [IoT-Remote Monitoring solution accelerator översikt över Azure](https://docs.microsoft.com/azure/iot-suite/)
* [Anslut en enhet för MXChip IoT DevKit till programmet Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
