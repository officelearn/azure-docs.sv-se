---
title: Snabbstart för att skicka telemetri till Azure IoT Hub (Android) | Microsoft Docs
description: I den här snabbstarten kör du ett Android-exempelprogram som skickar simulerad telemetri till en IoT-hubb och läser telemetrin från IoT-hubben för bearbetning i molnet.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: e239f8c6a7738500176b84589f2032636eeb71e5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675431"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Snabbstart: Skicka IoT-telemetri från en Android-enhet

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

I den här snabbstarten skickar du telemetri till en Azure IoT Hub från ett Android-program som körs på en fysisk eller simulerad enhet. IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. Den här snabbstarten använder ett förskrivet Android-program för att skicka telemetrin. Telemetrin läses från IoT-hubben med hjälp av Azure Cloud Shell. Innan du kör programmet skapar du en IoT-hubb och registrera ren enhet med hubben.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android Studio med Android SDK 27](https://developer.android.com/studio/). Mer information finns i [android-installation](https://developer.android.com/studio/install). Android SDK-27 används av exemplet i den här artikeln.

* [Ett exempel På Android-programmet](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample). Det här exemplet är en del av [azure-iot-samples-java-databasen.](https://github.com/Azure-Samples/azure-iot-samples-java)

* Port 8883 öppen i brandväggen. Enhetsprovet i den här snabbstarten använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Lägga till Azure IoT-tillägg

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI i din Cloud Shell-instans. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning Service (DPS) till Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetsidentiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyAndroidDevice:** Detta är namnet på den enhet du registrerar. Det rekommenderas att använda **MyAndroidDevice** som visas. Om du väljer ett annat namn för enheten måste du också använda det namnet i hela den här artikeln och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Kör följande kommando i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för den enhet du just registrerade:

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Du ska använda det här värdet senare i den här snabbstarten för att skicka telemetri.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

1. Öppna Android-exempelprojektet från GitHub i Android Studio. Projektet finns i följande katalog för din klonade eller nedladdade kopia av lagringsplatsen [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. I Android Studio öppnar *du gradle.properties* för exempelprojektet och ersätter **Device_Connection_String** platshållaren med enhetsanslutningssträngen som du har noterat tidigare.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. I Android Studio klickar du på > **Filsynkroniseringsprojekt med Gradle-filer**. **File** Kontrollera att bygget slutförs.

   > [!NOTE]
   > Om synkroniseringen av projektet misslyckas kan det bero på något av följande:
   >
   > * Versionerna av Plugin-programmet Android Gradle och Gradle som refereras i projektet är inaktuella för din version av Android Studio. Följ [dessa instruktioner](https://developer.android.com/studio/releases/gradle-plugin) för att referera till och installera rätt versioner av plugin och Gradle för din installation.
   > * Licensavtalet för Android SDK har inte undertecknats. Följ instruktionerna i Build-utdata för att signera licensavtalet och hämta SDK.

4. När bygget är klart klickar du på **Kör** > **kör "app"**. Konfigurera appen att köras på en fysisk Android-enhet eller en Android-emulator. Mer information om att köra en Android-app på en fysisk enhet eller emulator finns i [Köra appen](https://developer.android.com/training/basics/firstapp/running-app).

5. När appen har lästs in klickar du på knappen **Starta** för att börja skicka telemetri till din IoT-hubb:

    ![Program](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

I det här avsnittet använder du Azure Cloud Shell med [IoT-tillägget](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) för att övervaka de enhetsmeddelanden som skickas av Android-enheten.

1. Med Azure Cloud Shell kör du följande kommando för att ansluta och läsa meddelanden från IoT-hubben:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Följande skärmbild visar utdata när IoT-hubben tar emot telemetri som skickas av Android-enheten:

      ![Läsa enhetsmeddelanden med hjälp av Azure CLI](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten konfigurerar du en IoT-hubb, registrerade en enhet, skickade simulerad telemetri till navet med hjälp av ett Android-program och läser telemetrin från navet med Azure Cloud Shell.

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb](quickstart-control-device-android.md)