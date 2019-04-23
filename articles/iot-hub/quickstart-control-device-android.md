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
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: 4f9f4ccb53f9530122f0a2463f8f45b596856282
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149690"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Snabbstart: Kontrollera en enhet ansluten till en IoT Hub (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet och hantera dina enheter från molnet. I den här snabbstarten använder du en *direktmetod* för att styra en simulerad enhet som är ansluten till IoT Hub. Du kan använda direktmetoder för att fjärrändra beteendet hos en enhet ansluten till IoT Hub.

Snabbstarten använder två färdiga Java-program:

* Ett simulerat enhetsprogram som svarar på direkta metoder anropas från ett tjänstprogram på serverdelen. För att kunna ta emot direkta metodanrop ansluter det här programmet till en enhetsspecifik slutpunkt på din IoT-hubb.

* Ett tjänstprogram som anropar direktmetoden på Android-enheten. För att kunna anropa en direkt metod på en enhet ansluter det här programmet till en serverdelsslutpunkt på din IoT-hubb.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Android Studio från https://developer.android.com/studio/. Mer information om Android Studio-installationen finns i [android-installation](https://developer.android.com/studio/install).

* Android SDK-27 används av exemplet i den här artikeln.

* Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI i Cloud Shell-instans. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning-tjänsten (DPS) för vissa kommandon i Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* Två exempelprogram krävs av den här snabbstarten: [Android-exempelprogrammet för enhets-SDK](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) och [Android-exempelprogrammet för tjänst-SDK](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample). Båda dessa exempel är en del av lagringsplatsen azure-iot-samples-java på GitHub. Ladda ned eller klona lagringsplatsen [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-android.md) kan du hoppa över det här steget och använda den IoT-hubb som du redan har skapat.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

Om du har slutfört föregående [Snabbstart: Skicka telemetri från en enhet till en IoT-hubb](quickstart-send-telemetry-android.md) kan du hoppa över det här steget och använda samma enhet som registrerades i föregående snabbstart.

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetens identitet.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb.

   **MyAndroidDevice**: Det här värdet är det namn som du angav för den registrerade enheten. Använd MyAndroidDevice enligt bilden. Om du väljer ett annat namn för din enhet kan det hända att du även behöver använda det namnet i hela artikeln och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyAndroidDevice \
      --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

## <a name="retrieve-the-service-connection-string"></a>Hämta anslutningssträngen för tjänsten

Du behöver även ha en _tjänstanslutningssträng_ för att kunna aktivera tjänstprogrammet för serverdelen och ansluta till din IoT-hubb för att köra metoder och hämta meddelanden. Följande kommando hämtar tjänstanslutningssträngen för din IoT-hubb:

**YourIoTHubName**: Ersätt platshållaren nedan med det namn som du har valt för din IoT-hubb.

```azurecli-interactive
az iot hub show-connection-string --name YourIoTHubName --output table
```

Anteckna tjänstanslutningssträngen. Den ser ut ungefär som:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

Du kommer att använda det här värdet senare i snabbstarten. Tjänstanslutningssträngen skiljer sig från enhetsanslutningssträngen.

## <a name="listen-for-direct-method-calls"></a>Lyssna efter direkta metodanrop

Exempelprogrammet för enhets-SDK kan köras på en fysisk Android-enhet eller en Android-emulator. Exemplet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb, skickar simulerad telemetri och lyssnar efter direkta metodanrop från din hubb. I den här snabbstarten uppmanar det direkta metodanropet från hubben enheten att ändra det intervall med vilket den skickar telemetri. Den simulerade enheten skickar tillbaka en bekräftelse till din hubb när den har kört den direkta metoden.

1. Öppna Android-exempelprojektet från GitHub i Android Studio. Projektet finns i följande katalog för din klonade eller nedladdade kopia av lagringsplatsen [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. I Android Studio öppnar du *gradle.properties* för exempelprojektet och ersätter platshållaren **Device_Connection_String** med enhetens anslutningssträng som du antecknade tidigare.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. I Android Studio klickar du på **Arkiv** > **Synkronisera projekt med Gradle-filer**. Kontrollera att bygget slutförs.

   > [!NOTE]
   > Om projektet synkroniseringen misslyckas, kan det bero på något av följande orsaker:
   >
   > * Versioner av Android Gradle-plugin-programmet och Gradle som refereras till i projektet har upphört att gälla för din version av Android Studio. Följ [instruktionerna](https://developer.android.com/studio/releases/gradle-plugin) att referera till och installera rätt version av plugin-programmet och Gradle för installationen.
   > * Licensavtalet för Android SDK har inte signerats. Följ instruktionerna i resultatet från att logga i detta avtal och ladda ned SDK.

4. När bygget har slutförts klickar du på **Kör** > **Kör app**. Konfigurera appen att köras på en fysisk Android-enhet eller en Android-emulator. Mer information om att köra en Android-app på en fysisk enhet eller emulator finns i [Köra appen](https://developer.android.com/training/basics/firstapp/running-app).

5. När appen har lästs in klickar du på knappen **Starta** för att börja skicka telemetri till din IoT-hubb:

    ![Exempel Skärmbild av enheten android-klientappen](media/quickstart-control-device-android/sample-screenshot.png)

Den här appen måste fortsätta köras körs på en fysisk enhet eller emulator medan du kör exemplet för tjänst-SDK för att uppdatera telemetriintervallet under körningen.

## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

I det här avsnittet använder du Azure Cloud Shell med [IoT-tillägget](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) för att övervaka de enhetsmeddelanden som skickas av Android-enheten.

1. Med Azure Cloud Shell kör du följande kommando för att ansluta och läsa meddelanden från IoT-hubben:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

    Följande skärmbild visar utdata när IoT-hubben tar emot telemetri som skickas av Android-enheten:

      ![Läsa enhetsmeddelanden med hjälp av Azure CLI](media/quickstart-control-device-android/read-data.png)

Som standard skickar telemetriappen telemetri från Android-enheten var 5:e sekund. I nästa avsnitt använder du ett direktmetodsanrop för att uppdatera telemetriintervallet för Android IoT-enheten.

## <a name="call-the-direct-method"></a>Anropa den direkta metoden

Tjänstprogrammet ansluter till en slutpunkt på tjänstsidan på din IoT-hubb. Programmet gör direkta metodanrop till en enhet via din IoT-hubb och lyssnar efter bekräftelser.

Kör den här appen på en separat fysisk Android-enhet eller en Android-emulator.

Ett IoT-hubbtjänstprogram för serverdel körs vanligtvis i molnet, där det är enklare att minska riskerna med den känsliga anslutningssträng som styr alla enheter på en IoT-hubb. I det här exemplet kör vi det som en Android-app endast i demonstrationssyfte. De andra språkversionerna av den här snabbstarten innehåller andra exempel som bättre överensstämmer med ett tjänstprogram för serverdel.

1. Öppna Android-tjänstexempelprojektet från GitHub i Android Studio. Projektet finns i följande katalog för din klonade eller nedladdade kopia av lagringsplatsen [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. I Android Studio öppnar du *gradle.properties* för exempelprojektet och uppdaterar värdet för egenskaperna **ConnectionString** och **DeviceId** med den tjänstanslutningssträng som du antecknade tidigare och det Android-enhets-ID som du har registrerat.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. I Android Studio klickar du på **Arkiv** > **Synkronisera projekt med Gradle-filer**. Kontrollera att bygget slutförs.

   > [!NOTE]
   > Om projektet synkroniseringen misslyckas, kan det bero på något av följande orsaker:
   >
   > * Versioner av Android Gradle-plugin-programmet och Gradle som refereras till i projektet har upphört att gälla för din version av Android Studio. Följ [instruktionerna](https://developer.android.com/studio/releases/gradle-plugin) att referera till och installera rätt version av plugin-programmet och Gradle för installationen.
   > * Licensavtalet för Android SDK har inte signerats. Följ instruktionerna i resultatet från att logga i detta avtal och ladda ned SDK.

4. När bygget har slutförts klickar du på **Kör** > **Kör app**. Konfigurera appen att köras på en separat fysisk Android-enhet eller en Android-emulator. Mer information om att köra en Android-app på en fysisk enhet eller emulator finns i [Köra appen](https://developer.android.com/training/basics/firstapp/running-app).

5. När appen har lästs in uppdaterar du värdet **Set Messaging Interval** (Ange meddelandeintervall) till **1000** och klickar på **Invoke** (Anropa).

    Meddelandeintervallet för telemetri anges i millisekunder. Standardintervallet för telemetri för enhetsexemplet är inställt på 5 sekunder. Den här ändringen uppdaterar Android IoT-enheten så att telemetri skickas varje sekund.

    ![Ange telemetriintervall](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. Appen får en bekräftelse som anger huruvida metoden kördes korrekt.

    ![Bekräftelse för direktmetod](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du anropat en direktmetod på en enhet från ett serverdelsprogram och svarat på det direkta metodanropet i ett simulerat enhetsprogram.

Om du vill lära dig mer om hur man skickar enhet-till-molnet-meddelanden till olika mål i molnet går du vidare till nästa självstudier.

> [!div class="nextstepaction"]
> [Självstudie: Skicka telemetri till olika slutpunkter för bearbetning](tutorial-routing.md)
