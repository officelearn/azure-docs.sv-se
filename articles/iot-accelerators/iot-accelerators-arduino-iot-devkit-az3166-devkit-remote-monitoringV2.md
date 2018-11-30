---
title: Ansluta IoT DevKit till lösningsacceleratorn för fjärrövervakning – Azure | Microsoft Docs
description: I den här guiden du lära dig hur du skickar telemetri från sensorer på IoT DevKit AZ3166 enheten den att lösningsacceleratorn för fjärrövervakning för övervakning och visualisering.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 7f67868f6220ab2940aa8ac4d4bf24f82191cc22
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620259"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Anslut en enhet med IoT DevKit till lösningsacceleratorn för fjärrövervakning

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Den här guiden visar hur du kör ett exempelprogram på enheten IoT DevKit. Exempelkoden skickar telemetri från sensorer på enheten DevKit till utvecklingsacceleratorn.

Den [IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino kompatibla tavla med omfattande kringutrustning och sensorer. Du kan utveckla för den med hjälp av [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) i Visual Studio Code. Den [projekt catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) innehåller exempelprogram som hjälper dig att skapa prototyper IoT-lösningar.

## <a name="prerequisites"></a>Förutsättningar

Följ den [IoT DevKet komma igång-guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) och gå igenom följande avsnitt:

* Förbered maskinvaran
* Konfigurera Wi-Fi
* Börja använda DevKit
* Förbereda utvecklingsmiljön

## <a name="open-the-sample"></a>Öppna exemplet

Öppna exemplet fjärrövervakning i VS Code:

1. Kontrollera att din IoT DevKit inte till din dator. Starta VS Code först och sedan ansluta DevKit till din dator.

1. Klicka på `F1` att öppna kommandopaletten, typ och väljer **IoT Workbench: exempel**. Välj sedan **IoT DevKit** som tavla.

1. Hitta **fjärrövervakning** och klicka på **öppna exemplet**. En ny VS Code-fönstret öppnas med projektmappen:

  ![IoT Workbench, Välj exempel för fjärrövervakning](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Konfigurera enheten

Konfigurera anslutningssträngen för IoT Hub-enhet på enheten DevKit:

1. Växla IoT DevKit till **konfigurationsläge**:

    * Håll ned knappen **A**.
    * Push- och släpp den **återställa** knappen.

1. Skärmen visar DevKit-ID och `Configuration`.

    ![IoT DevKit konfigurationsläge](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Tryck på **F1** att öppna kommandopaletten, typ och väljer **IoT Workbench: enhet > Config Enhetsinställningar**.

1. Klistra in anslutningssträngen som du kopierade tidigare och tryck på **RETUR** att konfigurera enheten.

## <a name="build-the-code"></a>Skapa koden

Att skapa och överföra kod för enheten:

1. Tryck på **F1**”** att öppna kommandopaletten, typ och väljer **IoT Workbench: enhet > enheten överför**:

    ![IoT Workbench: Enhet – > Överför](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-upload.png)

1. VS Code kompilerar och laddar upp koden till enheten DevKit:

    ![IoT Workbench: Enhet – > laddats upp](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. DevKit enheten startar om och kör den kod som du laddat upp.

## <a name="test-the-sample"></a>Testa exemplet

Om du vill kontrollera att det exempelprogram som du laddade upp till DevKit enheten fungerar, utför du följande steg:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Visa telemetri som skickas till lösningen för fjärrövervakning

När du kör exempelappen skickar DevKit enheten telemetri från sensorer data via Wi-Fi till utvecklingsacceleratorn. Visa telemetrin:

1. Gå till instrumentpanelen för lösningen och klicka **enheter**.

1. Klicka på namnet på enheten för DevKit enheten. Du kan se telemetrin från DevKit i realtid på fliken till höger:

    ![Sensordata i Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Styra DevKit enheten

Lösningsacceleratorn för fjärrövervakning kan du styra din enhet via fjärranslutning. Exempelkoden implementerar tre metoder som du ser i den **metoden** avsnittet när du väljer enheten på den **enheter** sidan:

![IoT DevKit metoder](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Du kan ändra färg på en DevKit-led: ar med den **LedColor** metod:

1. Välj namnet på enheten från listan över enheter och klicka på den **jobb**:

    ![Skapa ett jobb](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Konfigurera jobb med hjälp av följande värden och klicka på **tillämpa**:

    * Välj jobb: **metoden Kör**
    * Metodnamn: **LedColor**
    * Jobbnamnet: **ChangeLedColor**

    ![Jobbinställningar](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Efter några sekunder ändras färgen på RGB-LED (under knappen A) på din DevKit

    ![IoT DevKit red ledde](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att gå vidare till självstudierna låter du Fjärrövervakning-lösningsacceleratorn vara distribuerad.

Om du inte längre behöver lösningsaccelerator kan ta bort den från sidan etablerade lösningar genom att markera den och sedan klicka på Ta bort lösningen:

![Ta bort lösningen](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem, se [IoT DevKit vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss med hjälp av följande kanaler:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en enhet för DevKit till lösningsacceleratorn för fjärrövervakning, är här några nästa föreslagna steg:

* [Azure IoT acceleratorer lösningsöversikt](https://docs.microsoft.com/azure/iot-accelerators/)
* [Anpassa användargränssnittet](iot-accelerators-remote-monitoring-customize.md)
* [Ansluta IoT DevKit till programmet Azure IoT Central](../iot-central/howto-connect-devkit.md)