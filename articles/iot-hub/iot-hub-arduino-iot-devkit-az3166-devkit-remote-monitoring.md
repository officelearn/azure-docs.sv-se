---
title: 'IoT DevKit till molnet: ansluta IoT MXChip DevKit till Azure IoT Hub | Microsoft Docs'
description: I den här kursen lär du dig hur du skickar status för sensorer på IoT DevKit AZ3166 till Azure IoT-Fjärrövervaknings solution accelerator.
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
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: d59a739e2222aca27993f6ae944a1d3bda567041
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Ansluta MXChip IoT DevKit till Azure IoT-Fjärrövervaknings solution accelerator

Lär dig hur du kör en exempelapp på din DevKit sensordata ska skickas till din Azure IoT-Fjärrövervaknings lösningsaccelerator i den här självstudiekursen.

Den [MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino kompatibel skiva med sensorer och omfattande kringutrustning. Du kan utveckla för den med hjälp av [Visual Studio Code-tillägget för Arduino](https://aka.ms/arduino). Och det ingår en växande [projekt katalogen](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) att hjälpa dig prototyp Sakernas Internet (IoT) lösningar som utnyttjar Microsoft Azure-tjänster.

## <a name="what-you-need"></a>Vad du behöver

Slutför den [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) till:

* Har din DevKit ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

En aktiv Azure-prenumeration. Om du inte har någon, kan du registrera via någon av följande två metoder:

* Aktivera en [kostnadsfria 30-dagars utvärderingsversion av Microsoft Azure-konto](https://azure.microsoft.com/free/)
* Anspråk din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du prenumererar på MSDN eller Visual Studio

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Skapa en Azure IoT-Fjärrövervaknings solution accelerator

1. Gå till [Azure IoT-lösningen acclerators plats](https://www.azureiotsuite.com/) och på **skapa en ny lösning**.
  ![Välj typ av Azure IoT solution accelerator](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)
  > [!WARNING]
  > Det här exemplet skapar en IoT-hubb S2 när den skapar en IoT Fjärrövervaknings solution accelerator som standard. Om den här IoT-hubb inte används med massiv antalet enheter, rekommenderar vi du nedgradera den från S2 till S1 och ta bort IoT Fjärrövervaknings solution accelerator så att relaterade IoT-hubb kan också tas bort, när du inte längre behöver. 

2. Välj **fjärrövervaknings**.

3. Ange ett lösningsnamn på, Välj en prenumeration och en region och klicka sedan på **skapa lösning**. Lösningen kan ta en stund att etableras.
  ![Skapa lösningen](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. När tillhandahållande är klar klickar du på **starta**. Vissa simulerade enheter skapas under processen etablera för lösningen. Klicka på **enheter** att checka ut dem. ![Instrumentpanelen](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  ![konsolen](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Klicka på **lägger till en enhet**.

6. Klicka på **lägga till nya** för **anpassade**.
  ![Lägg till ny enhet](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Klicka på **Låt mig ange mitt eget enhets-ID**, ange `AZ3166`, och klicka sedan på **skapa**.
  ![Skapa enhet med ID](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Anteckna **IoT-hubb Hostname**, och klicka på **klar**.

## <a name="open-the-remotemonitoring-sample"></a>Öppna RemoteMonitoring-exempel

1. Koppla från DevKit från datorn, om den är ansluten.

2. Starta VS-kod.

3. Anslut DevKit till datorn. VS kod identifierar din DevKit automatiskt och öppnar följande sidor:
  * DevKit startsidan.
  * Arduino exempel: Praktiska exempel för att komma igång med DevKit.

4. Expandera vänster **ARDUINO exempel** bläddrar du till **exempel MXCHIP AZ3166 > AzureIoT**, och välj **RemoteMonitoring**. Ett nytt fönster för VS kod öppnas med en projektmapp i den.
  > [!NOTE]
  > Om du råkar stänga fönstret kan du öppna det igen. Använd `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) för att öppna paletten kommando skriver **Arduino**, och sedan söka efter och välj **Arduino: exempel**.

## <a name="provision-required-azure-services"></a>Etablera krävs för Azure-tjänster

I fönstret lösning kör uppgiften `Ctrl+P` (macOS: `Cmd+P`) genom att ange `task cloud-provision` i textrutan:

I terminalen VS kod hjälper dig att etablera nödvändiga Azure-tjänster i en interaktiv kommandorad:

![Etablera Azure-resurser](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Skapa och skicka kod för enheten

1. Använd `Ctrl+P` (macOS: `Cmd + P`) och skriv **uppgift config enhetsanslutning**.

2. Terminalen frågar om du vill använda anslutningssträngen som hämtas från `task cloud-provision` steg. Du kan också ange anslutningssträngen din egen enhet genom att klicka på ”Skapa nytt...”

3. Terminalen uppmanas du att ange konfigurationsläge. Om du vill göra det, hålla ned A, och sedan push och släpp återställningsknappen. Skärmen visar DevKit ID och ”Configuration”.
  ![Inkommande anslutningssträngen](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Efter `task config-device-connection` färdig, klicka på `F1` att läsa in VS kodkommandon och välj `Arduino: Upload`, VS kod startas verifierar och laddar upp Arduino skissa: ![kontroll och överföring av Arduino ritningarna](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

DevKit startar om och börjar köras koden.

## <a name="test-the-project"></a>Testa projektet

När du kör exempelappen skickar DevKit sensordata via Wi-Fi till dina Azure IoT-Fjärrövervaknings lösningsaccelerator. Följ dessa steg om du vill visa resultatet:

1. Gå till din Azure IoT-Fjärrövervaknings lösningsaccelerator och på **INSTRUMENTPANELEN**.

2. På konsolen Fjärrövervaknings lösningen kommer att se din DevKit sensor status.

![Sensordata i Azure IoT-Fjärrövervaknings solution accelerator](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Ändra enhets-ID

Du kan ändra enhets-ID i IoT-hubb genom att följa [handboken](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/). Och om du vill ändra hårdkodad **AZ3166** till anpassade enhets-ID i koden. [Här](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23) kodrad som du kan ändra.

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem, läser du [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller nå oss från följande kanaler:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit enhet till din Azure IoT-Fjärrövervaknings lösningsaccelerator och visualisera sensordata, är här de föreslagna nästa steg:

* [Azure IoT-lösningen acceleratorer översikt](https://docs.microsoft.com/azure/iot-suite/)
* [Anslut en MXChip IoT DevKit enhet till programmet Microsoft IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
