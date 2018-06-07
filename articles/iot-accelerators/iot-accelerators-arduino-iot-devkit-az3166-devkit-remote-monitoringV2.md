---
title: 'IoT DevKit till molnet: ansluta IoT DevKit AZ3166 till fjärråtkomst övervakning IoT solution accelerator | Microsoft Docs'
description: I den här kursen lär du dig hur du skickar status för sensorer på IoT DevKit AZ3166 till fjärråtkomst övervakning IoT solution accelerator för övervakning och visualisering.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: isacabe
ms.openlocfilehash: 8aa4d660fbf785a4caf99bcdeddc86aa9929c50d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627747"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Ansluta MXChip IoT DevKit AZ3166 till IoT Fjärrövervaknings solution accelerator


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Lär dig hur du kör en exempelapp på din DevKit sensordata ska skickas till din lösningsaccelerator i den här självstudiekursen.

Den [MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino kompatibel skiva med sensorer och omfattande kringutrustning. Du kan utveckla för den med hjälp av [Visual Studio Code-tillägget för Arduino](https://aka.ms/arduino). Och det ingår en växande [projekt katalogen](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) att hjälpa dig prototyp Sakernas Internet (IoT) lösningar som utnyttjar Microsoft Azure-tjänster.

## <a name="what-you-need"></a>Vad du behöver

Slutför den [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) till:

* Har din DevKit ansluten till Wi-Fi
* Förbereda utvecklingsmiljön


## <a name="open-the-remotemonitoring-sample"></a>Öppna RemoteMonitoring-exempel

1. Koppla från DevKit från datorn, om den är ansluten.

2. Starta VS-kod.

3. Anslut DevKit till datorn. VS kod identifierar din DevKit automatiskt och öppnar följande sidor:
  * DevKit startsidan.
  * Arduino exempel: Praktiska exempel för att komma igång med DevKit.

4. Expandera vänster **ARDUINO exempel** bläddrar du till **exempel MXCHIP AZ3166 > AzureIoT**, och välj **RemoteMonitoringv2**. Ett nytt fönster för VS kod öppnas med en projektmapp i den.

  ![Öppna Fjärrövervaknings projekt](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)


  > [!NOTE]
  > Om du råkar stänga fönstret kan du öppna det igen. Använd `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) för att öppna paletten kommando skriver **Arduino**, och sedan söka efter och välj **Arduino: exempel**.

## <a name="add-a-new-physical-device"></a>Lägg till en ny fysisk enhet

I portalen, går du till **enheter** avsnittet och där klickar du på i den **+ ny enhet** knappen. 

![Lägga till en ny enhet](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device.png)

Den *ny enhet formuläret* bör fyllas i.
1. Klicka på **fysiska** i den *enhetstyp* avsnitt.
2. Definiera egna enhets-ID (till exempel *MXChip* eller *AZ3166*).
3. Välj **automatiskt generera nycklar** i den *autentiseringsnyckel* avsnitt.
4. Klicka på *tillämpa* knappen.

![Lägga till ett nytt formulär för enhet](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-new-device-form.png)

Vänta tills portalen är klar etableringen av den nya enheten.

![Etablera en ny enhet ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device-provisioning.png)


Konfigurationen av den nya enheten visas.
Kopiera den **anslutningssträngen** genereras.

![Anslutningssträngen för enhet](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-new-device-connstring.png)


Den här anslutningssträngen används i nästa avsnitt.





## <a name="build-and-upload-the-device-code"></a>Skapa och skicka kod för enheten

Gå tillbaka till Visual Studio-koden: 

1. Använd `Ctrl+P` (macOS: `Cmd + P`) och skriv **uppgift config enhetsanslutning**.

  ![Välj Azure-prenumeration och din IoT-hubb](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. Terminalen frågar om du vill använda anslutningssträngen för IoT-enhet vill du använda. Välj *skapa nya*, och klistra in den.

  ![Klistra in anslutningssträngen](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. Ibland uppmanas terminalen du att ange konfigurationsläge. Om du vill göra det, hålla ned A, och sedan push och släpp återställningsknappen och sedan knappen A. Skärmen visar DevKit ID och ”Configuration”.

  ![Enhetens DevKit skärm](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > Anslutningssträngen ska sparas i Urklipp om du har följt den sista delen av kursen. Om inte, bör du gå till Azure-portalen och Sök efter IoT-hubb för resursgruppen Fjärrövervaknings. Det, kan du se IoT-hubben anslutna enheter och kopiera anslutningssträngen för enheten.

  ![Leta efter anslutningssträngen](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)


Nu kan du se den nya fysiska enheten i avsnittet VS kod ”Azure IoT Hub-enheter”:

![Lägg märke till de nya IoT Hub-enhet](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

## <a name="test-the-project"></a>Testa projektet

När exempelappen körs, skickar DevKit sensordata via Wi-Fi till din IoT-Lösningsacceleratorer. Följ dessa steg om du vill visa resultatet:

1. Gå till din IoT-lösningsaccelerator och på **INSTRUMENTPANELEN**.

2. På konsolen IoT solution accelerator visas din DevKit sensor status. 

![Sensordata i IoT solution Accelerator](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

Om du klickar på sensornamnet (AZ3166) öppnas en flik på höger sida av instrumentpanelen, där du kan se MX-Chip sensorer diagram i realtid.


## <a name="send-a-c2d-message"></a>Skicka ett meddelande om C2D
Fjärråtkomst övervakning v2 kan du anropa remote metod på enheten.
Exempelkod MX-Chip publicerar tre metoder som du kan se i avsnittet metod när sensorn har valts.

![Metoder MX-Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

Du kan ändra färgen på en MX-Chip-indikatorer genom att använda metoden ”LedColor”. Markera kryssrutan för enheten för att göra det och klicka på knappen schema. 

![Metoder MX-Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

Välj den metod som kallas ChangeColor i listrutan där alla metoder visas, Skriv ett namn och tillämpa.

![Listrutan MX-Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

I några sekunder bör din fysiska MX-Chip ändra färg på RGB ledde (nedan i A knappen)

![Lampa MX-Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)


## <a name="change-device-id"></a>Ändra enhets-ID

Du kan ändra enhets-ID i IoT-hubb genom att följa [handboken](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).


## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem, läser du [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller nå oss från följande kanaler:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit enhet till IoT solution Accelerator-verktyg och visualisera sensordata, är här de föreslagna nästa steg:

* [IoT-lösning acceleratorer: översikt](https://docs.microsoft.com/azure/iot-suite/)
* [Anslut en MXChip IoT DevKit enhet till programmet Microsoft IoT Central](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit)
