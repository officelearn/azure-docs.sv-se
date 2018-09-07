---
title: IoT DevKit till molnet – ansluta IoT DevKit AZ3166 till Remote Monitoring IoT lösningsaccelerator | Microsoft Docs
description: I de här självstudierna lär du dig hur du skickar status för sensorer på IoT DevKit AZ3166 till Remote Monitoring IoT solution accelerator för övervakning och visualisering.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: isacabe
ms.openlocfilehash: d3175290c1a7fca5e35f4438392f29324868f1a3
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054924"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Ansluta MXChip IoT DevKit AZ3166 till IoT-Remote Monitoring solution accelerator


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

I den här självstudien får du lära dig hur du kör en exempelapp på din DevKit att skicka sensordata till utvecklingsacceleratorn.

Den [MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino kompatibla tavla med omfattande kringutrustning och sensorer. Du kan utveckla för den med hjälp av [Visual Studio Code-tillägg för Arduino](https://aka.ms/arduino). Och det levereras med en växande [projekt catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) att guida dig prototyp Internet of Things (IoT) lösningar som utnyttjar Microsoft Azure-tjänster.

## <a name="what-you-need"></a>Vad du behöver

Gå igenom den [Kom igång-guiden](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) och **Slutför i följande avsnitt**:

* Förbered maskinvaran
* Konfigurera Wi-Fi
* Börja använda DevKit
* Förbereda utvecklingsmiljön


## <a name="open-the-remotemonitoring-sample-in-vs-code"></a>Öppna exemplet RemoteMonitoring i VS Code

1. Koppla från MXChip DevKit från datorn, om den är ansluten.

2. Starta VS Code.

3. Ansluta MXChip DevKit till din dator.

4. Använd `Ctrl+Shift+P` (Mac OS: `Cmd+Shift+P`) Skriv för att öppna kommandopaletten **Arduino**, och leta upp och välj **Arduino: exempel**.

 5. Expandera vänster **ARDUINO exempel** bläddrar du till **exempel för MXCHIP AZ3166 > AzureIoT**, och välj **RemoteMonitoringv2**. Ett nytt VS Code-fönster öppnas med en projektmapp, till exempel i den.

  > [!NOTE]
  > Om du inte ser **exempel för MXCHIP**, använda `Ctrl+Shift+P` (Mac OS: `Cmd+Shift+P`) att öppna kommandopaletten och skriv **Arduino tavla Manager**. Markera den och sedan söka efter **AZ3166** i hanteraren för tavlan. Du bör sedan att kunna upprepa steg 5 ovan och se exempel.

  ![Öppna projektet för fjärrövervakning](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)

  > [!NOTE]
  > Om du råkar stänga fönstret kan öppna du den. Använd `Ctrl+Shift+P` (Mac OS: `Cmd+Shift+P`) Skriv för att öppna kommandopaletten **Arduino**, och leta upp och välj **Arduino: exempel**.

## <a name="build-and-upload-the-device-code-to-your-mxchip"></a>Skapa och överföra kod för enhet till din MXChip

1. Använd `Ctrl+P` (Mac OS: `Cmd + P`) och skriv **uppgift config enhetsanslutning**.

  ![Välj din Azure-prenumeration och IoT-hubben](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. Terminalen frågar om du vill använda anslutningssträngen för IoT-enhet vill du använda. Välj *skapa en ny*, och klistra in den.

  ![Klistra in anslutningssträngen](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. Ibland uppmanas terminalen du att ange konfigurationsläge. Du gör detta genom att hålla ned **knappen A**, skickar sedan och släpp den **Återställ** och släpp knappen A. På skärmen visas DevKit ID och ”Configuration”.

  ![Enhetens DevKit skärm](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > Anslutningssträngen ska sparas i Urklipp om du har följt det sista avsnittet i den här självstudien. Om inte, bör du gå till Azure-portalen och Sök efter IoT-hubben på resursgruppen fjärrövervakning. Du ser det, IoT-hubben anslutna enheter och kopiera anslutningssträngen.

  ![Leta efter anslutningssträngen](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)

  Nu har du är ansluten och verifierat MXChip enheten till IoT-hubb. Om du vill se den nya fysiska enheten i avsnittet VS Code ”Azure IoT Hub-enheter”, måste du hämta den [Azure IoT Toolkit-tillägget.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) 

  Du kan nu se den nya fysiska enheten i avsnittet VS Code ”Azure IoT Hub-enheter”:

  ![Lägg märke till den nya enheten i IoT Hub](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

4. Slutligen ska du ladda upp RemoteMonitoringV2.ino kod till din MxChip att börja skicka data till din IoT-lösningsaccelerator. Använd `Ctrl + Shift + P` (Mac OS: `Cmd + Shift + P`) och skriv **Arduino överför**. VS Code sedan starta överföring av koden på din MXChip och meddelar dig när du är klar. 

## <a name="test-the-project"></a>Testa projektet

När exempelappen körs, skickar MXChip DevKit sensordata via Wi-Fi till din IoT-Lösningsacceleratorer. Följ dessa steg om du vill visa resultatet genom:

1. Gå till din IoT-lösningsaccelerator klicka sedan på **INSTRUMENTPANELEN**.

2. IoT solution accelerator-konsolen visas din MXChip DevKit sensor status. 

![Sensordata i IoT-Lösningsacceleratorer](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

Om du klickar på sensornamnet (AZ3166) öppnas en flik på höger sida av instrumentpanelen där du kan se MXChip sensorer diagrammet i realtid.


## <a name="send-a-c2d-message"></a>Skicka ett C2D-meddelande
Fjärråtkomst övervakning v2 kan du anropa fjärrmetod på enheten.
MX-Chip exempelkoden publicerar tre metoder som du kan se i avsnittet metod när sensorn har valts.

![Metoder MX-Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

Du kan ändra färg på en av de MX-Chip led: ar med metoden ”LedColor”. För att göra det, markerar du kryssrutan för enheten och klicka på knappen schema. 

![Metoder MX-Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

Välj den metod som heter ChangeColor i listrutan där alla metoder som visas, Skriv ett namn och tillämpa.

![Listrutan MX-Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

Några sekunder bör din fysiska MX-Chip ändra färg på RGB-LED (nedan av A knappen)

![LED MX-Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)

## <a name="problems-and-feedback"></a>Problem och feedback

Om du får problem kan se [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss från följande kanaler:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en enhet för DevKit till IoT-Lösningsacceleratorer och visualisera sensordata, är här nästa föreslagna steg:

* [Översikt över IoT-Lösningsacceleratorer](https://docs.microsoft.com/azure/iot-suite/)
* [Anslut en enhet för MXChip IoT DevKit till programmet Microsoft IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
* [IoT developer kit](https://microsoft.github.io/azure-iot-developer-kit/)
