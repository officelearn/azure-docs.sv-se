---
title: 'IoT DevKit till molnet: ansluta IoT MXChip DevKit till Azure IoT Hub | Microsoft Docs'
description: I den här kursen lär du dig hur du skicka status för sensorer på IoT DevKit AZ3166 till Azure IoT Suite för övervakning och visualisering.
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
ms.openlocfilehash: 92efd0970bcf516c4210f831a0c2f23b3ee7b5d8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-suite-for-remote-monitoring"></a>Ansluta MXChip IoT DevKit till Azure IoT Suite för övervakning av fjärråtkomst

I kursen får du lära dig hur du kör en exempelapp på din DevKit sensordata ska skickas till din Azure IoT Suite.

Den [MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino kompatibel skiva med sensorer och omfattande kringutrustning. Du kan utveckla för den med hjälp av [Visual Studio Code-tillägget för Arduino](https://aka.ms/arduino). Och det ingår en växande [projekt katalogen](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) att hjälpa dig prototyp Sakernas Internet (IoT) lösningar som utnyttjar Microsoft Azure-tjänster.

## <a name="what-you-need"></a>Vad du behöver

Slutför den [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) till:

* Har din DevKit ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

En aktiv Azure-prenumeration. Om du inte har någon, kan du registrera via någon av följande två metoder:

* Aktivera en [kostnadsfria 30-dagars utvärderingsversion av Microsoft Azure-konto](https://azureinfo.microsoft.com/us-freetrial.html)
* Anspråk din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du prenumererar på MSDN eller Visual Studio

## <a name="create-an-azure-iot-suite"></a>Skapa ett Azure IoT Suite

1. Gå till [Azure IoT Suite plats](https://www.azureiotsuite.com/) och på **skapa en ny lösning**.
  ![Välj Azure IoT Suite-typ](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)
  > [!WARNING]
  > Det här exemplet skapar en IoT-hubb S2 när den skapar en IoT Suite som standard. Om den här IoT-hubb inte används med massiv antalet enheter, rekommenderar vi du nedgradera den från S2 till S1 och ta bort IoT Suite så att relaterade IoT-hubb kan också tas bort, när du inte längre behöver. 

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

När du kör exempelappen skickar DevKit sensordata via Wi-Fi till dina Azure IoT Suite. Följ dessa steg om du vill visa resultatet:

1. Gå till din Azure IoT Suite och klicka **INSTRUMENTPANELEN**.

2. På konsolen Azure IoT Suite-lösningen kommer att se din DevKit sensor status.

![Sensordata i Azure IoT Suite](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Ändra enhets-ID

Du kan ändra enhets-ID i IoT-hubb genom att följa [handboken](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/). Och om du vill ändra hårdkodad **AZ3166** till anpassade enhets-ID i koden. [Här](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23) kodrad som du kan ändra.

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem, läser du [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller nå oss från följande kanaler:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit enhet till din Azure IoT Suite och visualisera sensordata, är här de föreslagna nästa steg:

* [Översikt över Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)
* [Anslut en MXChip IoT DevKit enhet till programmet Microsoft IoT Central](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit)
