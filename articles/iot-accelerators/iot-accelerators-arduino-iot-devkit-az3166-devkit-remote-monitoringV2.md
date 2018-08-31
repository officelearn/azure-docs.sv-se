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
ms.openlocfilehash: 92ce85a3cc94702468a13348d3a41667498c68f5
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43187399"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Ansluta MXChip IoT DevKit AZ3166 till IoT-Remote Monitoring solution accelerator


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

I den här självstudien får du lära dig hur du kör en exempelapp på din DevKit att skicka sensordata till utvecklingsacceleratorn.

Den [MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino kompatibla tavla med omfattande kringutrustning och sensorer. Du kan utveckla för den med hjälp av [Visual Studio Code-tillägg för Arduino](https://aka.ms/arduino). Och det levereras med en växande [projekt catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) att guida dig prototyp Internet of Things (IoT) lösningar som utnyttjar Microsoft Azure-tjänster.

## <a name="what-you-need"></a>Vad du behöver

Slutför den [Kom igång med](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) till:

* Har din DevKit som är ansluten till Wi-Fi
* Förbereda utvecklingsmiljön


## <a name="open-the-remotemonitoring-sample"></a>Öppna exemplet RemoteMonitoring

1. Koppla från DevKit från datorn, om den är ansluten.

2. Starta VS Code.

3. Anslut DevKit till din dator. VS Code automatiskt identifierar din DevKit och öppnas på följande sidor:
  * DevKit startsidan.
  * Arduino-exempel: Praktiska exempel för att komma igång med DevKit.

4. Expandera vänster **ARDUINO exempel** bläddrar du till **exempel för MXCHIP AZ3166 > AzureIoT**, och välj **RemoteMonitoringv2**. Ett nytt VS Code-fönster öppnas med en projektmapp, till exempel i den.

  ![Öppna projektet för fjärrövervakning](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)


  > [!NOTE]
  > Om du råkar stänga fönstret kan öppna du den. Använd `Ctrl+Shift+P` (Mac OS: `Cmd+Shift+P`) Skriv för att öppna kommandopaletten **Arduino**, och leta upp och välj **Arduino: exempel**.

## <a name="add-a-new-physical-device"></a>Lägg till en ny fysisk enhet

I portalen, går du till **enheter** och det, klicka i den **+ ny enhet** knappen. 

![Att lägga till en ny enhet](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device.png)

Den *nytt enheten formulär* bör fyllas i.
1. Klicka på **fysiska** i den *enhetstyp* avsnittet.
2. Ange ditt eget enhets-ID (till exempel *MXChip* eller *AZ3166*).
3. Välj **generera nycklar automatiskt** i den *autentiseringsnyckeln* avsnittet.
4. Klicka på *tillämpa* knappen.

![Att lägga till ett nytt formulär för enhet](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-new-device-form.png)

Vänta tills portalen har slutat att etableringen av den nya enheten.

![Etablera en ny enhet ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device-provisioning.png)


Konfigurationen av den nya enheten visas.
Kopiera den **Connection String** genereras.

![Enhetens anslutningssträng](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-new-device-connstring.png)


Den här anslutningssträngen används i nästa avsnitt.





## <a name="build-and-upload-the-device-code"></a>Skapa och överföra kod för enhet

Gå tillbaka till Visual Studio Code: 

1. Använd `Ctrl+P` (Mac OS: `Cmd + P`) och skriv **uppgift config enhetsanslutning**.

  ![Välj din Azure-prenumeration och IoT-hubben](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. Terminalen frågar om du vill använda anslutningssträngen för IoT-enhet vill du använda. Välj *skapa en ny*, och klistra in den.

  ![Klistra in anslutningssträngen](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. Ibland uppmanas terminalen du att ange konfigurationsläge. Gör du hålla ned A, och sedan push- och släpp återställningsknappen och sedan på knappen A. På skärmen visas DevKit ID och ”Configuration”.

  ![Enhetens DevKit skärm](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > Anslutningssträngen ska sparas i Urklipp om du har följt det sista avsnittet i den här självstudien. Om inte, bör du gå till Azure-portalen och Sök efter IoT-hubben på resursgruppen fjärrövervakning. Du ser det, IoT-hubben anslutna enheter och kopiera anslutningssträngen.

  ![Leta efter anslutningssträngen](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)


Du kan nu se den nya fysiska enheten i avsnittet VS Code ”Azure IoT Hub-enheter”:

![Lägg märke till den nya enheten i IoT Hub](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

## <a name="test-the-project"></a>Testa projektet

När exempelappen körs, skickar DevKit sensordata via Wi-Fi till din IoT-Lösningsacceleratorer. Följ dessa steg om du vill visa resultatet genom:

1. Gå till din IoT-lösningsaccelerator klicka sedan på **INSTRUMENTPANELEN**.

2. IoT solution accelerator-konsolen visas din DevKit sensor status. 

![Sensordata i IoT-Lösningsacceleratorer](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

Om du klickar på sensornamnet (AZ3166) öppnas en flik på höger sida av instrumentpanelen där du kan se MX-Chip sensorer diagrammet i realtid.


## <a name="send-a-c2d-message"></a>Skicka ett C2D-meddelande
Fjärråtkomst övervakning v2 kan du anropa fjärrmetod på enheten.
MX-Chip exempelkoden publicerar tre metoder som du kan se i avsnittet metod när sensorn har valts.

![Metoder MX-Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

Du kan ändra färg på en MX-Chip led: ar med hjälp av metoden ”LedColor”. För att göra det, markerar du kryssrutan för enheten och klicka på knappen schema. 

![Metoder MX-Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

Välj den metod som heter ChangeColor i listrutan där alla metoder som visas, Skriv ett namn och tillämpa.

![Listrutan MX-Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

Några sekunder bör din fysiska MX-Chip ändra färg på RGB ledde (nedan av A knappen)

![Lampa MX-Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)

## <a name="problems-and-feedback"></a>Problem och feedback

Om du får problem kan se [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss från följande kanaler:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en enhet för DevKit till IoT-Lösningsacceleratorer och visualisera sensordata, är här nästa föreslagna steg:

* [Översikt över IoT-Lösningsacceleratorer](https://docs.microsoft.com/azure/iot-suite/)
* [Anslut en enhet för MXChip IoT DevKit till programmet Microsoft IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
* [IoT developer kit](https://microsoft.github.io/azure-iot-developer-kit/)
