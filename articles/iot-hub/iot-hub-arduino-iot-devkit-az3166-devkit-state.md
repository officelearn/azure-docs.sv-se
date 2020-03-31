---
title: Använd Azure-enhetstvillingar för att styra MXChip IoT DevKit-användar-LED | Microsoft-dokument
description: I den här självstudien kan du lära dig hur du övervakar DevKit-tillstånd och styr användar-LED med Azure IoT Hub-enhetstvillingar.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: deb1ea8c7b41ad48bddebfbed1b15c667ee0071a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73483939"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

Du kan använda det här exemplet för att övervaka MXChip IoT DevKit WiFi-information och sensortillstånd och för att styra färgen på användar-LED med Azure IoT Hub-enhetstvillingar.

## <a name="what-you-learn"></a>Detta får du får lära dig

- Så här övervakar du MXChip IoT DevKit-sensorns tillstånd.

- Så här använder du Azure-enhetstvillingar för att styra färgen på DevKits RGB-LED.

## <a name="what-you-need"></a>Vad du behöver

- Konfigurera utvecklingsmiljön genom att följa [komma igång-guiden](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

- Skriv följande kommandon från GitBash-terminalfönstret (eller annat Git-kommandoradsgränssnitt):

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Etablera Azure-tjänster

1. Klicka på den **nedrullningsbara** menyn Uppgifter i Visual Studio-kod och välj **Kör aktivitet...**  -  **molnetablering**.

2. Dina framsteg visas under fliken **TERMINAL** på **välkomstpanelen.**

3. När du uppmanas att göra meddelandet *Vilken prenumeration vill du välja*väljer du en prenumeration.

4. Markera eller välj en resursgrupp. 
 
   > [!NOTE]
   > Om du redan har en gratis IoT Hub kan du hoppa över det här steget.

5. När du uppmanas att göra meddelandet *Vad IoT-hubben vill välja*väljer eller skapar du en IoT Hub.

6. Något som liknar *funktionsapp: funktionsappnamn: xxx*, visas. Skriv ner funktionsappens namn. det kommer att användas i ett senare steg.

7. Vänta tills Azure Resource Manager-malldistributionen är klar, vilket indikeras när distributionen av *meddelanderesurshanterarens mall: Klar* visas.

## <a name="deploy-function-app"></a>Appen Distribuera funktion

1. Klicka på den **nedrullningsbara** menyn Uppgifter i Visual Studio-kod och välj **Kör aktivitet...**  -  **distribuera molnet**.

2. Vänta tills uppladdningsprocessen för funktionsappkod är klar. distribuerar *meddelandefunktionsappen: Klar* visas.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Konfigurera anslutningssträng för IoT Hub Device i DevKit

1. Anslut din MXChip IoT DevKit till din dator.

2. Klicka på den **nedrullningsbara** menyn Uppgifter i Visual Studio-kod och välj **Kör aktivitet...**  -  **config-device-anslutning**

3. På MXChip IoT DevKit trycker du på och håller ned knapp **A,** trycker på **återställningsknappen** och släpper sedan knappen **A** för att få DekKit att gå in i konfigurationsläge.

4. Vänta tills konfigurationsprocessen för anslutningssträngen har slutförts.

## <a name="upload-arduino-code-to-devkit"></a>Ladda upp Arduino-koden till DevKit

Med din MXChip IoT DevKit ansluten till din dator:

1. Klicka på den **nedrullningsbara** menyn Uppgifter i Visual Studio-kod och välj **Kör byggaktivitet...** Arduino-skissen sammanställs och laddas upp till DevKit.

2. När skissen har laddats upp visas ett *& Uppladdningsskiss:* framgångsmeddelande.

## <a name="monitor-devkit-state-in-browser"></a>Övervaka DevKit-tillstånd i webbläsaren

1. Öppna filen i `DevKitState\web\index.html` en webbläsare – som skapades under steget Vad du behöver.

2. Följande webbsida visas:![Ange funktionsappnamnet.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Mata in funktionsappnamnet som du skrev ned tidigare.

4. Klicka på knappen **Anslut**

5. Inom några sekunder uppdateras sidan och visar DevKits WiFi-anslutningsstatus och tillståndet för var och en av de inbyggda sensorerna.

## <a name="control-the-devkits-user-led"></a>Styr DevKits användar-LED

1. Klicka på användar-LED-grafiken på webbsidans illustration.

2. Inom några sekunder uppdateras skärmen och visar den aktuella färgstatusen för användaren LED.

3. Prova att ändra färgvärdet för RGB-LED genom att klicka på olika platser på RGB-skjutreglagen.

## <a name="example-operation"></a>Exempel på åtgärd

![Exempel på testförfarande](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> Du kan se rådata om enhetstvilling\> i Azure-portalen: IoT Hub - IoT-enheter -\> * \<enheten\> *  - \> Device Twin.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig att:
- Anslut en MXChip IoT DevKit-enhet till din Azure IoT Remote Monitoring-lösningsaccelerator.
- Använd funktionen Azure IoT-enhetstvillingar för att känna av och styra färgen på DevKits RGB-LED.

Här är de föreslagna nästa stegen:

* [Azure IoT Remote Monitoring solution accelerator översikt](https://docs.microsoft.com/azure/iot-suite/)
* [Ansluta en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program](/azure/iot-central/core/howto-connect-devkit)
