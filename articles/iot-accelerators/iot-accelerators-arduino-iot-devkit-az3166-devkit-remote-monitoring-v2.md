---
title: Anslut IoT DevKit till lösningen för fjärrövervakning – Azure | Microsoft-dokument
description: I den här guiden för hur du ska guida får du lära dig hur du skickar telemetri från sensorerna på IoT DevKit AZ3166-enheten till remote monitoring-lösningsacceleratorn för övervakning och visualisering.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 6e9f9c89cf2e5e40d37a1532e688490aae294181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888869"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Ansluta en IoT DevKit-enhet till lösningsacceleratorn för fjärrövervakning

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här guiden visar hur du kör ett exempelprogram på din IoT DevKit-enhet. Exempelkoden skickar telemetri från sensorerna på DevKit-enheten till din lösningsaccelerator.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino kompatibel styrelse med rik kringutrustning och sensorer. Du kan utveckla för det med Azure [IoT Device Workbench](https://aka.ms/iot-workbench) eller [Azure IoT Tools](https://aka.ms/azure-iot-tools) tilläggspaket i Visual Studio Code. [Projektkatalogen](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) innehåller exempelprogram som hjälper dig att prototypen av IoT-lösningar.

## <a name="before-you-begin"></a>Innan du börjar

Gör så här för att slutföra stegen i den här självstudien:

* Förbered din DevKit genom att följa stegen i [Connect IoT DevKit AZ3166 till Azure IoT Hub i molnet](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="open-sample-project"></a>Öppna exempelprojekt

Så här öppnar du exempel på fjärrövervakning i VS-kod:

1. Kontrollera att din IoT DevKit inte är till din dator. Starta VS-koden först och anslut sedan DevKit till datorn.

1. Klicka `F1` här om du vill öppna kommandopaletten, skriv och välj **Azure IoT Device Workbench: Öppna exempel...**. Välj sedan **IoT DevKit** som anslagstavla.

1. Sök **efter fjärrövervakning** och klicka på **Öppna exempel**. Ett nytt VS-kodfönster öppnas med projektmappen:

   ![IoT Workbench, välj exempel på fjärrövervakning](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Konfigurera enheten

Så här konfigurerar du anslutningssträngen för IoT Hub-enheten på din DevKit-enhet:

1. Växla IoT DevKit till **konfigurationsläge:**

    * Håll ned knapp **A**.
    * Tryck och släpp **återställningsknappen.**

1. På skärmen visas DevKit `Configuration`ID och .

    ![Konfigurationsläge för IoT DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Tryck på **F1** för att öppna kommandopaletten, skriv och välj **Azure IoT Device Workbench: Konfigurera enhetsinställningar... > Config Device Connection String**.

1. Klistra in anslutningssträngen som du kopierade tidigare och tryck på **Retur** för att konfigurera enheten.

## <a name="build-the-code"></a>Skapa koden

Så här skapar och laddar du upp enhetskoden:

1. Tryck `F1` för att öppna kommandopaletten, skriv och välj **Azure IoT Device Workbench: Ladda upp enhetskod:**

1. VS-kod sammanställer och laddar upp koden till din DevKit-enhet:

    ![IoT Workbench: Enhet - > uppladdad](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. DevKit-enheten startar om och kör koden du laddade upp.

## <a name="test-the-sample"></a>Testa provet

Så här kontrollerar du att exempelprogrammet som du laddade upp till DevKit-enheten fungerar:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Visa telemetrin som skickas till lösningen för fjärrövervakning

När exempelappen körs skickar DevKit-enheten telemetri från sina sensordata via Wi-Fi till din lösningsaccelerator. Så här visar du telemetrin:

1. Gå till instrumentpanelen för lösningen och klicka på **Enhetsutforskaren**.

1. Klicka på enhetsnamnet på din DevKit-enhet. på den högra fliken kan du se telemetrin från DevKit i realtid:

    ![Sensordata i Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Styr DevKit-enheten

Med lösningsacceleratorn för fjärrövervakning kan du fjärrstyra enheten. Exempelkoden implementerar tre metoder som du kan se i avsnittet **Metod** när du väljer enheten på sidan **Enhetsutforskaren:**

![IoT DevKit-metoder](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Om du vill ändra färg på en av DevKit-lysdioderna använder du **LedColor-metoden:**

1. Välj enhetsnamnet från enhetslistan och klicka på **jobben:**

    ![Skapa ett jobb](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Konfigurera jobb med hjälp av följande värden och klicka på **Använd:**

   * Välj jobb: **Kör metod**
   * Metodnamn: **LedColor**
   * Jobbnamn: **ChangeLedColor**

     ![Jobbinställningar](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Efter ett par sekunder ändras färgen på RGB-lysdioden (under knappen A) på din DevKit:

    ![IoT DevKit rödledd](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att gå vidare till självstudierna låter du acceleratorn för fjärrövervakningslösningen vara distribuerad.

Om du inte längre behöver lösningsacceleratorn tar du bort den från sidan Etablerade lösningar genom att markera den och sedan klicka på Ta bort lösning:

![Ta bort lösningen](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på några problem, se vanliga frågor om [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss via följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackspill](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit-enhet till din lösningsaccelerator för fjärrövervakning följer följande steg:

* [Översikt över Azure IoT-lösningsacceleratorer](https://docs.microsoft.com/azure/iot-accelerators/)
* [Anpassa användargränssnittet](iot-accelerators-remote-monitoring-customize.md)
* [Anslut IoT DevKit till ditt Azure IoT Central-program](../iot-central/core/howto-connect-devkit.md)