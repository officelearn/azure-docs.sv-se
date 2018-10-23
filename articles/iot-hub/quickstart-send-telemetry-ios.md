---
title: Snabbstart – Skicka telemetri till Azure IoT Hub | Microsoft Docs
description: I den här snabbstarten kör du ett iOS-exempelprogram som skickar simulerad telemetri till en IoT-hubb, läser telemetrin från IoT-hubben och bearbetar den i molnet.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/20/2018
ms.author: kgremban
ms.openlocfilehash: 96989f8c53508dd1520a38c0df408057ad673d53
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365381"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>Snabbstart: Skicka telemetri från en enhet till en IoT-hubb (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här artikeln skickar du telemetri från ett simulerat program till IoT Hub. Sedan kan du visa data från ett program på serversidan. 

I den här artikeln används ett färdigt Swift-program till att skicka telemetrin och ett CLI-verktyg till att läsa telemetrin från IoT Hub. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Ladda ned kodexemplet från [Azure-exemplen](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip) 
- Den senaste versionen av [XCode](https://developer.apple.com/xcode/) med den senaste versionen av iOS SDK. Denna snabbstart har testats med XCode 9.3 och iOS 11.3.
- Den senaste versionen av [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommandon i Azure Cloud Shell för att lägga till IoT Hub CLI-tillägget och skapa enhetens identitet. 

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

   **myiOSdevice**: Det här är det namn du angav för den registrerade enheten. Använd myiOSdevice som det visas. Om du väljer ett annat namn för din enhet måste du även använda det namnet i hela artikeln, och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   az iot hub device-identity create --hub-name YourIoTHubName --device-id myiOSdevice
   ```

1. Kör följande kommando för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id myiOSdevice --output table
   ```

   Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Exempelprogrammet körs på en iOS-enhet som ansluter till en enhetsspecifik slutpunkt på din IoT-hubb, och skickar simulerad telemetri om temperatur och luftfuktighet. 

### <a name="install-cocoapods"></a>Installera CocoaPods

CocoaPods hanterar beroenden för iOS-projekt där du använder bibliotek från tredje part.

Öppna ett lokalt terminalfönster och navigera till mappen Azure-IoT-Samples-iOS som du laddade ned i förberedelserna. Navigera sedan till exempelprojektet:

```sh
cd quickstart/sample-device
```

Kontrollera att XCode är stängt och kör följande kommando för att installera CocoaPods som deklareras i filen **podfile**:

```sh
pod install
```

Förutom att installera de poddar som krävs för projektet så skapar även installationskommandot en XCode-arbetsytefil som redan är konfigurerad att använda poddarna för beroenden. 

### <a name="run-the-sample-application"></a>Köra exempelprogrammet 

1. Öppna exempelarbetsytan i XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Expandera projektet **MQTT Client Sample** och expandera sedan mappen med samma namn.  
3. Öppna **ViewController.swift** för redigering i XCode. 
4. Sök efter variabeln **connectionString** och uppdatera värdet med enhetsanslutningssträngen som du antecknade tidigare.
5. Spara ändringarna. 
6. Kör projektet i enhetsemulatorn med knappen **Build and run** (Skapa och kör) eller tangentkombinationen **command + r**. 

   ![Kör projektet](media/quickstart-send-telemetry-ios/run-sample.png)

7. När emulatorn öppnas väljer du **Start** i exempelappen.

Följande skärmbild visar några exempelutdata från när programmet skickar simulerad telemetri till din IoT-hubb:

   ![Kör den simulerade enheten](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

Exempelappen du körde i XCode-emulatorn visar data om meddelanden som skickas från enheten. Du kan också visa data via din IoT-hubb när de tas emot. CLI-tillägget för IoT Hub kan ansluta till **Events**-slutpunkten för tjänstsidan på din IoT Hub. Tillägget tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub-serverprogram körs normalt i molnet för att ta emot och bearbeta enhet-till-molnet-meddelanden.

Kör följande kommandon i Azure Cloud Shell, där du ersätter `YourIoTHubName` med namnet på din IoT-hubb:

```azurecli-interactive
az iot hub monitor-events --device-id myiOSdevice --hub-name YourIoTHubName
```

Följande skärmbild visar utdata när tillägget tar emot telemetridata som skickats från den simulerade enheten till hubben:

Följande skärmbild visar vilken typ av telemetri du ser i det lokala terminalfönstret:

![Visa telemetrin](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här artikeln konfigurerade du en IoT-hubb, registrerade en enhet, skickade simulerad telemetri till hubben från en iOS-enhet och läste telemetrin från hubben. 

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb](quickstart-control-device-node.md)

<!-- Links -->
[lnk-process-d2c-tutorial]: tutorial-routing.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
