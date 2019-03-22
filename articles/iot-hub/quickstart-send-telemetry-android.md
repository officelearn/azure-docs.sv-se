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
ms.date: 11/05/2018
ms.author: wesmc
ms.openlocfilehash: 8f30918a73e1e7012ad3be151e778eb073627b84
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726693"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>Snabbstart: Skicka IoT-telemetri från en Android-enhet

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabbstarten skickar du telemetri till en IoT-hubb från ett Android-program som körs på en fysisk eller simulerad enhet.

Snabbstarten använder ett färdigt Android-program för att skicka telemetrin. Telemetrin läses från IoT-hubben med hjälp av Azure Cloud Shell. Innan du kör programmet skapar du en IoT-hubb och registrera ren enhet med hubben.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Android Studio från https://developer.android.com/studio/. Mer information om Android Studio-installationen finns i [android-installation](https://developer.android.com/studio/install). 

* Android SDK-27 används av exemplet i den här artikeln. 

* Det [Android-exempelprogram](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) som du kör i den här snabbstarten är en del av lagringsplatsen azure-iot-samples-java på GitHub. Ladda ned eller klona lagringsplatsen [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).



## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommandon i Azure Cloud Shell för att lägga till IoT Hub CLI-tillägget och skapa enhetens identitet. 

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyAndroidDevice**: MyAndroidDevice är det namn som du angav för den registrerade enheten. Använd MyAndroidDevice enligt bilden. Om du väljer ett annat namn för din enhet måste du även använda det namnet i hela artikeln, och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

    **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Du använder det här värdet senare i den här snabbstarten för att skicka telemetri.

## <a name="send-telemetry"></a>Skicka telemetri

1. Öppna Android-exempelprojektet från GitHub i Android Studio. Projektet finns i följande katalog för din klonade eller nedladdade kopia av lagringsplatsen [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. I Android Studio öppnar du *gradle.properties* för exempelprojektet och ersätter platshållaren **Device_Connection_String** med enhetens anslutningssträng som du antecknade tidigare.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. I Android Studio klickar du på **Arkiv** > **Synkronisera projekt med Gradle-filer**. Kontrollera att bygget slutförs.

   > [!NOTE]
   > Om projektet synkroniseringen misslyckas, kan det bero på något av följande orsaker:
   >
   > * Versioner av Android Gradle-plugin-programmet och Gradle som refereras till i projektet har upphört att gälla för din version av Android Studio. Följ [instruktionerna](https://developer.android.com/studio/releases/gradle-plugin) att referera till och installera rätt version av plugin-programmet och Gradle för installationen.
   > * Licensavtalet för Android SDK har inte signerats. Följ instruktionerna i resultatet från att logga i detta avtal och ladda ned SDK.

4. När bygget har slutförts klickar du på **Kör** > **Kör app**. Konfigurera appen att köras på en fysisk Android-enhet eller en Android-emulator. Mer information om att köra en Android-app på en fysisk enhet eller emulator finns i [Köra appen](https://developer.android.com/training/basics/firstapp/running-app).

5. När appen har lästs in klickar du på knappen **Starta** för att börja skicka telemetri till din IoT-hubb:

    ![Program](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

I det här avsnittet använder du Azure Cloud Shell med [IoT-tillägget](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) för att övervaka de enhetsmeddelanden som skickas av Android-enheten.

1. Med Azure Cloud Shell kör du följande kommando för att ansluta och läsa meddelanden från IoT-hubben:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```
    Följande skärmbild visar utdata när IoT-hubben tar emot telemetri som skickas av Android-enheten:

      ![Läsa enhetsmeddelanden med hjälp av Azure CLI](media/quickstart-send-telemetry-android/read-data.png)


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du konfigurerat en IoT-hubb, registrerat en enhet, skickat simulerad telemetri till hubben med hjälp av ett Android-program och läst telemetrin från hubben med hjälp av Azure Cloud Shell.

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT Hub](quickstart-control-device-android.md)

