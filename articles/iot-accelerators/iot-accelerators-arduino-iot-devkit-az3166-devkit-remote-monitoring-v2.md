---
title: Ansluta IoT-DevKit till fjärr styrning Solution Accelerator – Azure | Microsoft Docs
description: I den här instruktions guiden får du lära dig hur du skickar telemetri från sensorer på IoT DevKit AZ3166-enheten till en lösnings Accelerator för fjärrövervakning för övervakning och visualisering.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 542a0780c0525ee7ceddfd36c3a4f7c348d0574f
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72930868"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Anslut en IoT DevKit-enhet till lösningen för fjärr styrnings lösning

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här instruktions guiden visar hur du kör ett exempel program på din IoT DevKit-enhet. Exempel koden skickar telemetri från sensorer på DevKit-enheten till Solution Accelerator.

[MXChip IoT-DevKit](https://aka.ms/iot-devkit) är en allt-i-ett-Arduino-kompatibelt kort med omfattande kring utrustning och sensorer. Du kan utveckla för IT-enheter med hjälp av [Azure IoT Device Workbench](https://aka.ms/iot-workbench) eller [Azure IoT tools](https://aka.ms/azure-iot-tools) Extension Pack i Visual Studio Code. [Projekt katalogen](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) innehåller exempel program som hjälper dig att prototypa IoT-lösningar.

## <a name="before-you-begin"></a>Innan du börjar

Utför stegen i den här självstudien genom att först utföra följande uppgifter:

* Förbered din DevKit genom att följa stegen i [ansluta IoT DevKit-AZ3166 till Azure IoT Hub i molnet](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="open-sample-project"></a>Öppna exempel projekt

Så här öppnar du exemplet för fjärrövervakning i VS Code:

1. Se till att din IoT-DevKit inte är till din dator. Starta VS Code först och Anslut sedan DevKit till datorn.

1. Klicka på `F1` för att öppna kommando-paletten, skriv och välj **Azure IoT Device Workbench: öppna exempel.** .. Välj sedan **IoT DevKit** som tavla.

1. Sök efter **fjärrövervakning** och klicka på **Öppna exempel**. Ett nytt VS Code-fönster öppnas med projektmappen:

   ![IoT Workbench, Välj exempel på fjärr styrning](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Konfigurera enheten

Så här konfigurerar du IoT Hub enhets anslutnings sträng på din DevKit-enhet:

1. Växla IoT-DevKit till **konfigurations läge**:

    * Håll ned knapp **A**.
    * Push och släpp knappen **Återställ** .

1. På skärmen visas DevKit-ID och `Configuration`.

    ![Konfigurations läge för IoT-DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Tryck på **F1** för att öppna kommando-paletten, skriv och välj **Azure IoT enhet Workbench: Konfigurera enhets inställningar... Anslutnings sträng för > config-enhet**.

1. Klistra in anslutnings strängen som du kopierade tidigare och tryck på **RETUR** för att konfigurera enheten.

## <a name="build-the-code"></a>Skapa koden

För att bygga och ladda upp enhets koden:

1. Tryck på `F1` för att öppna kommando-paletten, skriv och välj **Azure IoT enhet Workbench: Ladda upp enhets kod**:

1. VS Code kompilerar och laddar upp koden till din DevKit-enhet:

    ![IoT Workbench: enhets > uppladdat](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. DevKit-enheten startas om och kör den kod som du har laddat upp.

## <a name="test-the-sample"></a>Testa exemplet

Utför följande steg för att kontrol lera att det exempel program som du laddade upp till DevKit-enheten fungerar:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Visa telemetri som skickas till lösningen för fjärrövervakning

När exempel appen körs skickar DevKit-enheten telemetri från sensorer-data via Wi-Fi till Solution Accelerator. Så här visar du Telemetrin:

1. Gå till instrument panelen för lösningen och klicka på **Device Explorer**.

1. Klicka på enhets namnet för din DevKit-enhet. på den högra fliken kan du se Telemetrin från DevKit i real tid:

    ![Sensor data i Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Kontrol lera DevKit-enheten

Med lösningen för fjärrövervakning kan du kontrol lera din enhet via fjärr anslutning. Exempel koden implementerar tre metoder som du kan se i avsnittet **metod** när du väljer enheten på **Device Explorer** sidan:

![IoT DevKit-metoder](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Om du vill ändra färgen på en av DevKit-lysdioderna använder du metoden **LedColor** :

1. Välj enhets namnet från enhets listan och klicka på **jobben**:

    ![Skapa ett jobb](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Konfigurera jobben med följande värden och klicka på **Använd**:

   * Välj jobb: **Kör metod**
   * Metod namn: **LedColor**
   * Jobb namn: **ChangeLedColor**

     ![Jobbinställningar](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Efter några sekunder ändras färgen på RGB-INDIKATORn (under knappen A) på din DevKit:

    ![Indikator för IoT DevKit Red](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att gå vidare till självstudierna låter du acceleratorn för fjärrövervakningslösningen vara distribuerad.

Om du inte längre behöver Solution Accelerator tar du bort den från sidan etablerade lösningar genom att markera den och sedan klicka på ta bort lösning:

![Ta bort lösningen](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem kan du läsa [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss med följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit-enhet till din lösnings Accelerator för fjärr styrning är det några förslag på följande steg:

* [Översikt över Azure IoT Solution Accelerators](https://docs.microsoft.com/azure/iot-accelerators/)
* [Anpassa användargränssnittet](iot-accelerators-remote-monitoring-customize.md)
* [Ansluta IoT-DevKit till ditt Azure IoT Central-program](../iot-central/core/howto-connect-devkit.md)