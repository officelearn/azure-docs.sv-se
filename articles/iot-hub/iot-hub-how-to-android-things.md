---
title: 'Utveckla för Android saker plattform med hjälp av Azure IoT SDK: er | Microsoft Docs'
description: Developer Användarguide – Lär dig mer om hur du utvecklar på Android saker med Azure IoT Hub SDK.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 1/30/2019
ms.author: yizhon
ms.openlocfilehash: 0bfba7f923ca394aa29dd907db1b8b1284a605d8
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981680"
---
# <a name="develop-for-android-things-platform-using-azure-iot-sdks"></a>Utveckla för Android saker plattform med hjälp av Azure IoT SDK: er
[Azure IoT Hub SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) ger den första nivån support för populära plattformar som Windows, Linux, OSX, MBED och mobila plattformar, t.ex. Android och iOS.  Som en del av vårt åtagande att aktivera större valmöjligheter och flexibilitet i IoT-distributioner, Java SDK stöder också [Android saker](https://developer.android.com/things/) plattform.  Utvecklare kan utnyttja fördelarna med Android saker operativsystemet på enheten, när du använder [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) som den centrala meddelanden hub som kan skalas till miljontals samtidigt anslutna enheter. 

Den här självstudien beskriver steg för att skapa ett sida-program för enhet på Android saker med Azure IoT-Java-SDK.

## <a name="prerequisites"></a>Förutsättningar
* En Android saker stöds maskinvara med Android saker operativsystem som körs.  Du kan följa [Android saker dokumentation](https://developer.android.com/things/get-started/kits#flash-at) om hur du flash Android saker OS.  Kontrollera att enheten Android saker som är ansluten till internet med viktiga kringutrustning som tangentbord, bildskärm och mus.  Den här självstudien använder Raspberry Pi 3.
* Senaste versionen av [Android Studio](https://developer.android.com/studio/)
* Senaste versionen av [Git](https://git-scm.com/)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommandon i Azure Cloud Shell för att lägga till IoT Hub CLI-tillägget och skapa enhetens identitet. 

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyAndroidThingsDevice** : Det här är det namn du angav för den registrerade enheten. Använd MyAndroidThingsDevice som visas. Om du väljer ett annat namn för din enhet måste du även använda det namnet i hela artikeln, och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyAndroidThingsDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:  **YourIoTHubName** : Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyAndroidThingsDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidThingsDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

## <a name="building-an-android-things-application"></a>Skapar ett program för Android saker
1.  Det första steget att skapa ett program för Android saker ansluter till dina saker som Android-enheter.  Anslut enheten Android saker till en skärm och ansluta till internet.  Android saker ger [dokumentation](https://developer.android.com/things/get-started/kits) om hur du ansluter till trådlösa nätverk.  När du har anslutit till internet kan du anteckna den IP-adress som anges under nätverk.
2.  Använd den [GDB](https://developer.android.com/studio/command-line/adb) verktyg för att ansluta till enheten Android saker med IP-adressen som anges ovan.  Kontrollera anslutningen med hjälp av det här kommandot från terminalen.  Du bör se dina enheter listas som ”ansluten”
    ```
    adb devices
    ```
3.  Hämta vårt exempel för Android/Android saker från den här [databasen](https://github.com/Azure-Samples/azure-iot-samples-java) eller Använd Git.
    ```
    git clone https://github.com/Azure-Samples/azure-iot-samples-java.git
    ```
4.  Öppna Android-projekt i finns i ”\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample” i Android Studio.
5.  Öppna gradle.properties-filen och Ersätt ”Device_connection_string” med enhetens anslutningssträng antecknade tidigare.
    ![Skärmbild av huvuddelen av databasen](./media/how-to-android-things/connection-string.png)
6.  Klicka på Kör - felsöka och välj din enhet för att distribuera den här koden till dina saker som Android-enheter.
7.  Du kan se ett program som körs på enheten Android saker när programmet startades.  Det här exempelprogrammet skickar slumpmässigt genererat temperatur värdena.

## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

Du kan visa data via din IoT-hubb när de tas emot. CLI-tillägget för IoT Hub kan ansluta till **Events**-slutpunkten för tjänstsidan på din IoT Hub. Tillägget tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub-serverprogram körs normalt i molnet för att ta emot och bearbeta enhet-till-molnet-meddelanden.

Kör följande kommandon i Azure Cloud Shell, där du ersätter `YourIoTHubName` med namnet på din IoT-hubb:

```
azurecli-interactive
az iot hub monitor-events --device-id MyAndroidThingsDevice --hub-name YourIoTHubName
```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [hur du hanterar anslutningar och tillförlitlig meddelandehantering](iot-hub-reliability-features-in-sdks.md) med IoT Hub SDK.
* Läs om hur du [utveckla för mobila plattformar](iot-hub-how-to-develop-for-mobile-devices.md) som iOS och Android.
* [Support för Azure IoT SDK-plattform](iot-hub-device-sdk-platform-support.md)
