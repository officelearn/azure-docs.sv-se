---
title: Kontrollera en enhet från snabbstart för Azure IoT Hub (Android) | Microsoft Docs
description: I den här snabbstarten kör du två Java-exempelprogram. Det enda programmet är ett tjänstprogram som kan fjärrstyra enheter som är anslutna till hubben. Det andra programmet körs på en fysisk eller simulerad enhet som är ansluten till hubben och kan fjärrstyras.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: 5a912199869cb2cce690a3502345ec6f9ca43d22
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675675"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Snabbstart: Kontrollera en enhet ansluten till en IoT Hub (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

I den här snabbstarten använder du en direkt metod för att styra en simulerad enhet som är ansluten till Azure IoT Hub. IoT Hub är en Azure-tjänst som gör att du kan hantera dina IoT-enheter från molnet och inta stora volymer av enhetstelemetri till molnet för lagring eller bearbetning. Du kan använda direktmetoder för att fjärrändra beteendet hos en enhet ansluten till IoT Hub. Den här snabbstarten använder två program: ett simulerat enhetsprogram som svarar på direkta metoder som anropas från ett backend-tjänstprogram och ett tjänstprogram som anropar den direkta metoden på Android-enheten.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android Studio med Android SDK 27](https://developer.android.com/studio/). Mer information finns i [Installera Android Studio](https://developer.android.com/studio/install).

* [Git](https://git-scm.com/download/).

* [Exempel på Android-program för enhet SDK](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample), som ingår i [Java(Azure IoT Samples).](https://github.com/Azure-Samples/azure-iot-samples-java)

* [Exempel på Android-program för Tjänst SDK](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample), som ingår i Azure IoT-exempel (Java).

* Port 8883 öppen i brandväggen. Enhetsprovet i den här snabbstarten använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Lägga till Azure IoT-tillägg

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI i din Cloud Shell-instans. IoT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning Service (DPS) till Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-android.md) kan du hoppa över det här steget och använda den IoT-hubb som du redan har skapat.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-android.md) kan du hoppa över det här steget och använda samma enhet som registrerats i föregående snabbstart.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetsidentiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyAndroidDevice:** Detta är namnet på den enhet du registrerar. Det rekommenderas att använda **MyAndroidDevice** som visas. Om du väljer ett annat namn för enheten måste du också använda det namnet i hela den här artikeln och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

## <a name="retrieve-the-service-connection-string"></a>Hämta anslutningssträngen för tjänsten

Du behöver även ha en _tjänstanslutningssträng_ för att kunna aktivera tjänstprogrammet för serverdelen och ansluta till din IoT-hubb för att köra metoder och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

**YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Anteckna tjänstanslutningssträngen. Den ser ut ungefär som:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Du kommer att använda det här värdet senare i snabbstarten. Den här tjänstanslutningssträngen skiljer sig från den enhetsanslutningssträng som du noterade i föregående steg.

## <a name="listen-for-direct-method-calls"></a>Lyssna efter direkta metodanrop

Båda exemplen för den här snabbstarten är en del av azure-iot-samples-java-arkivet på GitHub. Ladda ned eller klona lagringsplatsen [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).

Exempelprogrammet för enhets-SDK kan köras på en fysisk Android-enhet eller en Android-emulator. Exemplet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb, skickar simulerad telemetri och lyssnar efter direkta metodanrop från din hubb. I den här snabbstarten uppmanar det direkta metodanropet från hubben enheten att ändra det intervall med vilket den skickar telemetri. Den simulerade enheten skickar en bekräftelse tillbaka till din hubb när den har kör den direkta metoden.

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

    ![Exempel på skärmbild av android-appen för klientenheter](media/quickstart-control-device-android/sample-screenshot.png)

Den här appen måste lämnas igång på en fysisk enhet eller emulator medan du kör service-SDK-exemplet för att uppdatera telemetriintervallet under körning.

## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

I det här avsnittet använder du Azure Cloud Shell med [IoT-tillägget](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) för att övervaka de meddelanden som skickas av Android-enheten.

1. Med Azure Cloud Shell kör du följande kommando för att ansluta och läsa meddelanden från IoT-hubben:

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    Följande skärmbild visar utdata när IoT-hubben tar emot telemetri som skickas av Android-enheten:

      ![Läsa enhetsmeddelanden med hjälp av Azure CLI](media/quickstart-control-device-android/read-data.png)

Som standard skickar telemetriappen telemetri från Android-enheten var femte sekund. I nästa avsnitt använder du ett direktmetodsanrop för att uppdatera telemetriintervallet för Android IoT-enheten.

## <a name="call-the-direct-method"></a>Anropa den direkta metoden

Tjänstprogrammet ansluter till en slutpunkt på tjänstsidan på din IoT-hubb. Programmet gör direkta metodanrop till en enhet via din IoT-hubb och lyssnar efter bekräftelser.

Kör den här appen på en separat fysisk Android-enhet eller en Android-emulator.

Ett IoT Hub-backend-tjänstprogram körs vanligtvis i molnet, där det är enklare att minska riskerna med den känsliga anslutningssträngen som styr alla enheter på en IoT Hub. I det här exemplet kör vi det som en Android-app endast i demonstrationssyfte. De andra språkversionerna av den här snabbstarten ger exempel som ligger närmare varandra med ett vanligt backend-tjänstprogram.

1. Öppna Android-tjänstexempelprojektet från GitHub i Android Studio. Projektet finns i följande katalog för din klonade eller nedladdade kopia av lagringsplatsen [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. Öppna *gradle.properties* för exempelprojektet i Android Studio. Uppdatera värdena för **egenskaperna ConnectionString** och **DeviceId** med den tjänstanslutningssträng som du noterade tidigare och det Android-enhets-ID som du registrerade.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. I Android Studio klickar du på > **Filsynkroniseringsprojekt med Gradle-filer**. **File** Kontrollera att bygget slutförs.

   > [!NOTE]
   > Om synkroniseringen av projektet misslyckas kan det bero på något av följande:
   >
   > * Versionerna av Plugin-programmet Android Gradle och Gradle som refereras i projektet är inaktuella för din version av Android Studio. Följ [dessa instruktioner](https://developer.android.com/studio/releases/gradle-plugin) för att referera till och installera rätt versioner av plugin och Gradle för din installation.
   > * Licensavtalet för Android SDK har inte undertecknats. Följ instruktionerna i Build-utdata för att signera licensavtalet och hämta SDK.

4. När bygget är klart klickar du på **Kör** > **kör "app"**. Konfigurera appen att köras på en separat fysisk Android-enhet eller en Android-emulator. Mer information om att köra en Android-app på en fysisk enhet eller emulator finns i [Köra appen](https://developer.android.com/training/basics/firstapp/running-app).

5. När appen har lästs in uppdaterar du värdet **Set Messaging Interval** (Ange meddelandeintervall) till **1000** och klickar på **Invoke** (Anropa).

    Meddelandeintervallet för telemetri anges i millisekunder. Standardintervallet för telemetri för enhetsexemplet är inställt på 5 sekunder. Den här ändringen uppdaterar Android IoT-enheten så att telemetri skickas varje sekund.

    ![Ange telemetriintervall](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Appen får en bekräftelse som anger om metoden har körts eller inte.

    ![Direkt metodbekräftelse](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du anropat en direktmetod på en enhet från ett serverdelsprogram och svarat på det direkta metodanropet i ett simulerat enhetsprogram.

Om du vill lära dig mer om hur man skickar enhet-till-molnet-meddelanden till olika mål i molnet går du vidare till nästa självstudier.

> [!div class="nextstepaction"]
> [Självstudier: Skicka telemetri till olika slutpunkter för bearbetning](tutorial-routing.md)
