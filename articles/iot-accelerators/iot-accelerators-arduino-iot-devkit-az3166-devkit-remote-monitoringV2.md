---
title: IoT DevKit till molnet – ansluta IoT DevKit AZ3166 till Remote Monitoring IoT lösningsaccelerator | Microsoft Docs
description: I de här självstudierna lär du dig hur du skickar status för sensorer på IoT DevKit AZ3166 till Remote Monitoring IoT solution accelerator för övervakning och visualisering.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: isacabe
ms.openlocfilehash: 35ef6ef02e5ae8a4b9231121615f44e0dc00ad15
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378745"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Ansluta MXChip IoT DevKit AZ3166 till IoT-Remote Monitoring solution accelerator

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

I den här självstudien får du lära dig hur du kör en exempelapp på din DevKit att skicka sensordata till utvecklingsacceleratorn.

Den [MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino kompatibla tavla med omfattande kringutrustning och sensorer. Du kan utveckla för den med hjälp av [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) i Visual Studio Code. Och det levereras med en växande [projekt catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) att guida dig prototyp Internet of Things (IoT) lösningar som utnyttjar Microsoft Azure-tjänster.

## <a name="what-you-need"></a>Vad du behöver

Gå igenom den [Kom igång-guiden](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) och **Slutför i följande avsnitt**:

* Förbered maskinvaran
* Konfigurera Wi-Fi
* Börja använda DevKit
* Förbereda utvecklingsmiljön


## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Skapa en Azure IoT lösningsacceleratorn för fjärrövervakning

AZ3166 enheten som du skapar i den här självstudien skickar data till en instans av lösningsacceleratorn för fjärrövervakning. Om du inte ännu har etablerat en instans i ditt Azure-konto, följer du de [snabbstarten](https://docs.microsoft.com/azure/iot-accelerators/quickstart-remote-monitoring-deploy) guide om hur du gör.

När distributionen för lösningen för fjärrövervakning är klar öppnar du instrumentpanelen för lösningen.

![Instrumentpanelen för lösningen](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard-info.png)

## <a name="add-a-device-to-the-remote-monitoring-solution"></a>Lägga till en enhet i lösningen för fjärrövervakning

1. Gå till instrumentpanelen för **enheter** och klicka på **ny enhet**.
   ![Att lägga till en ny enhet](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-add-device.png)

2. Konfigurera enheten med hjälp av informationen nedan och klicka på **tillämpa**.
  * Enhetstyp: **fysiska**
  * Enhets-ID: **AZ3166**
  * Autentiseringstyp: **symmetrisk nyckel**
  * Autentiseringsnyckel: **generera nycklar automatiskt**
  
  ![Att lägga till ett nytt formulär för enhet](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-add-new-device-form.png)

3. När den nya enheten har skapats kan du kopiera den **anslutningssträngen primärnyckel**. Det här är den enhet som du nyss skapade i Azure IoT Hub under.
  
  ![Enhetens anslutningssträng](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-new-device-connstring.png)

## <a name="open-the-remote-monitoring-sample-in-vs-code"></a>Öppna exemplet fjärrövervakning i VS Code

1. Kontrollera att din IoT DevKit är **inte ansluten** till datorn. Starta VS Code först och sedan ansluta DevKit till din dator.

1. Klicka på `F1` att öppna kommandopaletten, typ och väljer **IoT Workbench: exempel**. Välj sedan **IoT DevKit** som tavla.

1. Hitta **fjärrövervakning** och klicka på **öppna exemplet**. Ett nytt VS Code-fönster öppnas med projektmapp, till exempel i den.
  ![IoT Workbench, Välj exempel för fjärrövervakning](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-iot-hub-device-connection-string"></a>Konfigurera anslutningssträngen för IoT Hub-enhet

1. Växla IoT DevKit till **konfigurationsläge**. Gör så här:
   * Håll ned knappen **A**.
   * Push- och släpp den **återställa** knappen.

2. På skärmen visas DevKit ID och ”Configuration”.
   
  ![IoT DevKit konfigurationsläge](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

3. Klicka på `F1` att öppna kommandopaletten, typ och väljer **IoT Workbench: enhet > Config Enhetsinställningar**.

4. Klistra in anslutningssträngen som du kopierade Klicka `Enter` att konfigurera den.

## <a name="build-and-upload-the-device-code"></a>Skapa och överföra kod för enhet

1. Klicka på `F1` att öppna kommandopaletten, typ och väljer **IoT Workbench: enhet > enheten ladda upp**.
  ![IoT Workbench: Enhet – > Överför](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-upload.png)

1. VS Code och sedan startar när koden kompileras och ladda upp koden till din DevKit.
  ![IoT Workbench: Enhet – > laddats upp](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

DevKit startar om och börjat köra koden.

## <a name="test-the-project"></a>Testa projektet

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Visa telemetri som skickas till lösningen för fjärrövervakning

När exempelappen körs, skickar DevKit sensordata via Wi-Fi till lösningen för fjärrövervakning. Följ dessa steg om du vill visa resultatet genom:

1. Gå till instrumentpanelen för lösningen och klicka **enheter**.

1. Klicka på namnet på enheten (AZ3166) öppnas en flik på höger sida av instrumentpanelen där du kan se status för sensor på DevKit i realtid.
  ![Sensordata i Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="send-a-c2d-message"></a>Skicka ett C2D-meddelande

Remote Monitoring-lösningen kan du anropa fjärrmetod på enheten. Koden sxample publicerar tre metoder som du ser i den **metoden** avsnittet när sensorn har valts.

![IoT DevKit metoder](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Låt oss försöka ändra färg på en av DevKit led: ar med hjälp av metoden ”LedColor”.

1. Välj den **AZ3166** från listan över enheter och klicka på den **jobb**.

  ![Skapa ett jobb](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

2. Konfigurera jobb enligt nedan och klicka på **tillämpa**.
  * Välj jobb: **metoden Kör**
  * Metodnamn: **LedColor**
  * Jobbnamnet: **ChangeLedColor**
  
  ![Inställningar för](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

Några sekunder bör din DevKit ändra färg på RGB-LED (under knappen A).

![IoT DevKit red ledde](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att gå vidare till självstudierna låter du Fjärrövervakning-lösningsacceleratorn vara distribuerad.

Om du inte längre behöver lösningsaccelerator kan ta bort den från sidan etablerade lösningar genom att markera den och sedan klicka på Ta bort lösningen:

![Ta bort lösningen](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problem och feedback

Om du får problem kan se [IoT DevKit vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss med hjälp av följande kanaler:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit enhet till din Azure IoT lösningsacceleratorn för fjärrövervakning och visualisera sensordata, är här nästa föreslagna steg:

* [Azure IoT acceleratorer lösningsöversikt](https://docs.microsoft.com/azure/iot-suite/)
* [Anpassa användargränssnittet](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)
* [Ansluta IoT DevKit till programmet Azure IoT Central](../iot-central/howto-connect-devkit.md)