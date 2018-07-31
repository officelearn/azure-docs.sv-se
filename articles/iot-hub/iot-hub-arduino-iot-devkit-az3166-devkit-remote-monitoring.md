---
title: IoT DevKit till molnet – ansluta IoT MXChip DevKit till Azure IoT Hub | Microsoft Docs
description: I de här självstudierna lär du dig hur du skickar status för sensorer på IoT DevKit AZ3166 till Azure IoT Remote Monitoring solution accelerator.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 806ae38f614c44ce25b8fcc159b74f1bda3f00f3
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343125"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Ansluta MXChip IoT DevKit till Azure IoT lösningsacceleratorn för fjärrövervakning

I de här självstudierna lär du dig att köra en exempelapp på din DevKit att skicka sensordata till din Azure IoT lösningsacceleratorn för fjärrövervakning.

Den [MXChip IoT DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino kompatibla tavla med omfattande kringutrustning och sensorer. Du kan utveckla för den med hjälp av [Visual Studio Code-tillägg för Arduino](https://aka.ms/arduino). Och det levereras med en växande [projekt catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) att guida dig prototyp Internet of Things (IoT) lösningar som utnyttjar Microsoft Azure-tjänster.

## <a name="what-you-need"></a>Vad du behöver

Slutför den [Kom igång med](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) till:

* Har din DevKit som är ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

En aktiv Azure-prenumeration. Om du inte har någon kan registrera du via någon av följande två metoder:

* Aktivera en [kostnadsfria 30-dagars utvärderingsversion av Microsoft Azure-konto](https://azure.microsoft.com/free/)

* Anspråk din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du är MSDN eller Visual Studio-prenumerant

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Skapa en Azure IoT lösningsacceleratorn för fjärrövervakning

1. Gå till [Azure IoT-Lösningsacceleratorer plats](https://www.azureiotsolutions.com/) och klicka på **skapa en ny lösning**.

   ![Välj typ av Azure IoT solution accelerator](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Det här exemplet skapar en IoT Hub S2 när den skapar en IoT-Remote Monitoring solution accelerator som standard. Om denna IoT hub inte används med stort antal enheter, vi rekommenderar starkt att du nedgradera den från S2 till S1 och ta bort IoT-Remote Monitoring solution accelerator så att relaterade IoT-hubben kan också tas bort, när du inte längre behöver den. 

2. Välj **fjärrövervakning**.

3. Ange ett lösningsnamn på, Välj en prenumeration och en region och klicka sedan på **skapa lösning**. Lösningen kan ta en stund att etableras.
  
   ![Skapa lösning](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. När etableringen har slutförts klickar du på **starta**. Vissa simulerade enheter har skapats för lösningen under etableringen. Klicka på **enheter** att Kolla in dem.

   ![Instrumentpanel](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Konsolen](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Klicka på **lägga till en enhet**.

6. Klicka på **Lägg till ny** för **anpassad enhet**.
  
   ![Lägg till en ny enhet](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Klicka på **Låt mig ange mitt eget enhets-ID**, ange `AZ3166`, och klicka sedan på **skapa**.
  
   ![Skapa enhet med ID:](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Anteckna **IoT-Hubbvärdnamnet**, och klicka på **klar**.

## <a name="open-the-remotemonitoring-sample"></a>Öppna exemplet RemoteMonitoring

1. Koppla från DevKit från datorn, om den är ansluten.

2. Starta VS Code.

3. Anslut DevKit till din dator. VS Code automatiskt identifierar din DevKit och öppnas på följande sidor:

  * DevKit startsidan.
  * Arduino-exempel: Praktiska exempel för att komma igång med DevKit.

4. Expandera vänster **ARDUINO exempel** bläddrar du till **exempel för MXCHIP AZ3166 > AzureIoT**, och välj **RemoteMonitoring**. Ett nytt VS Code-fönster öppnas med en projektmapp, till exempel i den.

   > [!NOTE]
   > Om du råkar stänga fönstret kan öppna du den. Använd `Ctrl+Shift+P` (Mac OS: `Cmd+Shift+P`) Skriv för att öppna kommandopaletten **Arduino**, och leta upp och välj **Arduino: exempel**.

## <a name="provision-required-azure-services"></a>Etablera nödvändiga Azure-tjänster

Kör uppgiften i Lösningsfönstret `Ctrl+P` (Mac OS: `Cmd+P`) genom att ange `task cloud-provision` i textrutan.

I VS Code-terminalen hjälper en interaktiv kommandorad dig att etablera nödvändiga Azure-tjänster.

![Etablera Azure-resurser](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Skapa och överföra kod för enhet

1. Använd `Ctrl+P` (Mac OS: `Cmd + P`) och skriv **uppgift config enhetsanslutning**.

2. Terminalen frågar om du vill använda en anslutningssträng som hämtas från den `task cloud-provision` steg. Du kan också ange egna enhetens anslutningssträng genom att klicka på ”Skapa nytt...”

3. Terminalen uppmanas du att ange konfigurationsläge. För att göra det, håll ned knappen A, och sedan push- och släpp återställningsknappen. På skärmen visas DevKit ID och ”Configuration”.

   ![Indata-anslutningssträng](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Efter `task config-device-connection` har slutförts klickar du på `F1` att läsa in VS Code-kommandon och välj `Arduino: Upload`. VS Code startar verifierar och laddar upp Arduino skissen.
  
   ![Verifiering och överföring av Arduino-skiss](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

DevKit startar om och börjat köra koden.

## <a name="test-the-project"></a>Testa projektet

När exempelappen körs, skickar DevKit sensordata via Wi-Fi till din Azure IoT lösningsacceleratorn för fjärrövervakning. Följ dessa steg om du vill visa resultatet genom:

1. Gå till din Azure IoT lösningsacceleratorn för fjärrövervakning och klicka **INSTRUMENTPANELEN**.

2. I lösningskonsolen för fjärrövervakning visas din DevKit sensor status.

   ![Sensordata i Azure IoT lösningsacceleratorn för fjärrövervakning](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Ändra enhets-ID

Du kan ändra enhets-ID i IoT Hub genom att följa den [anpassa enhets-ID för](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/). Om du vill ändra hårdkodat **AZ3166** till en anpassad enhets-ID i koden, ändrar du raden i koden som visas oi den [fjärransluten övervakning exempel](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23).

## <a name="problems-and-feedback"></a>Problem och feedback

Om du får problem kan se [IoT developer kit vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss med hjälp av följande kanaler:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit enhet till din Azure IoT lösningsacceleratorn för fjärrövervakning och visualisera sensordata, är här nästa föreslagna steg:

* [Azure IoT acceleratorer lösningsöversikt](https://docs.microsoft.com/azure/iot-suite/)

* [Anslut en enhet för MXChip IoT DevKit till programmet Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)