---
title: Utveckla för Android Things-plattformen med Azure IoT SDK:er | Microsoft-dokument
description: Utvecklarguide – Lär dig mer om hur du utvecklar på Android Things med Azure IoT Hub SDK:er.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: robinsh
ms.openlocfilehash: a06583e9aab4b082517d47c1022f7bec5184b9bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673387"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Utveckla för Android Things-plattformen med Azure IoT SDK:er

[Azure IoT Hub SDK:er](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) ger stöd på den första nivån för populära plattformar som Windows, Linux, OSX, MBED och mobila plattformar som Android och iOS.  Som en del av vårt åtagande att möjliggöra större valfrihet och flexibilitet i IoT-distributioner stöder Java SDK också [Android Things-plattformen.](https://developer.android.com/things/)  Utvecklare kan utnyttja fördelarna med Android Things operativsystem på enhetssidan, samtidigt som de använder [Azure IoT Hub](about-iot-hub.md) som den centrala meddelandehubben som skalas till miljontals enheter som är anslutna samtidigt.

Den här självstudien beskriver stegen för att skapa ett program på enhetssidan på Android Things med Azure IoT Java SDK.

## <a name="prerequisites"></a>Krav

* En Android Things stöds hårdvara med Android Things OS körs.  Du kan följa [Android Things dokumentation](https://developer.android.com/things/get-started/kits#flash-at) om hur man blinkar Android Things OS.  Kontrollera att din Android Things-enhet är ansluten till internet med viktig kringutrustning som tangentbord, bildskärm och mus.  Den här självstudien använder Raspberry Pi 3.

* Senaste versionen av [Android Studio](https://developer.android.com/studio/)

* Senaste versionen av [Git](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommandon i Azure Cloud Shell för att lägga till IoT Hub CLI-tillägget och skapa enhetens identitet.

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

   **MyAndroidThingsDevice** : Detta är namnet på den registrerade enheten. Använd MyAndroidThingsDevice som visas. Om du väljer ett annat namn för din enhet måste du även använda det namnet i hela artikeln samt uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az extension add --name azure-iot
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta *enhetsanslutningssträngen* för den enhet du just registrerade. Ersätt `YourIoTHubName` nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

## <a name="building-an-android-things-application"></a>Skapa ett Android Things-program

1. Det första steget för att skapa ett Android Things-program är att ansluta till dina Android Things-enheter. Anslut din Android Things-enhet till en bildskärm och anslut den till internet. Android Saker ger [dokumentation](https://developer.android.com/things/get-started/kits) om hur du ansluter till WiFi. När du har anslutit till Internet, ta en anteckning om IP-adressen som anges under Nätverk.

2. Använd [adb-verktyget](https://developer.android.com/studio/command-line/adb) för att ansluta till din Android Things-enhet med IP-adressen som anges ovan. Dubbelkolla anslutningen med det här kommandot från terminalen. Du bör se dina enheter listade som "anslutna".

   ```
   adb devices
   ```

3. Ladda ner vårt exempel för Android / Android Saker från denna [databas](https://github.com/Azure-Samples/azure-iot-samples-java) eller använda Git.

   ```
   git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
   ```

4. Öppna Android-projektet i Android Studio i "\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample".

5. Öppna filen gradle.properties och ersätt "Device_connection_string" med enhetsanslutningssträngen som noterats tidigare.
 
6. Klicka på Kör - Felsökning och välj din enhet för att distribuera den här koden till dina Android Things-enheter.

7. När programmet startas kan du se ett program som körs på din Android Things-enhet. Det här exempelprogrammet skickar slumpmässigt genererade temperaturavläsningar.

## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

Du kan visa data via din IoT-hubb när den tas emot. CLI-tillägget för IoT Hub kan ansluta till **Events**-slutpunkten för tjänstsidan på din IoT Hub. Tillägget tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub-serverprogram körs normalt i molnet för att ta emot och bearbeta enhet-till-molnet-meddelanden.

Kör följande kommandon i Azure Cloud Shell, där du ersätter `YourIoTHubName` med namnet på din IoT-hubb:

```azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [hur du hanterar anslutning och tillförlitliga meddelanden](iot-hub-reliability-features-in-sdks.md) med hjälp av IoT Hub SDK:er.
* Läs mer om hur du [utvecklar för mobila plattformar](iot-hub-how-to-develop-for-mobile-devices.md) som iOS och Android.
* [Support för Azure IoT SDK-plattformen](iot-hub-device-sdk-platform-support.md)
