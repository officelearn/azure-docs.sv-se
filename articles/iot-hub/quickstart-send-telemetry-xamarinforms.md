---
title: Snabbstart – Skicka telemetri till Azure IoT Hub | Microsoft Docs
description: I den här snabb starten kör du ett exempel på Xamarin Forms-program för att skicka simulerad telemetri till en IoT-hubb och läsa telemetri från IoT Hub för bearbetning i molnet.
author: cmaneu
manager: philmea
ms.author: chmaneu
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
ms.date: 05/20/2020
ms.openlocfilehash: d2292a65ef25f6450d6810b50366daa622b92bc8
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802743"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-xamarin-forms"></a>Snabb start: skicka telemetri från en enhet till en IoT Hub (Xamarin-formulär)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här artikeln skickar du telemetri från ett simulerat program till IoT Hub. Sedan kan du visa data från ett program på serversidan.

I den här artikeln används ett fördefinierat Xamarin formulär program för att skicka Telemetrin och ett CLI-verktyg för att läsa Telemetrin från IoT Hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="prerequisites"></a>Krav

- Ladda ned kodexemplet från [Azure-exemplen](https://github.com/Azure-Samples/azure-iot-samples-xamarin/archive/master.zip)

- Den senaste versionen av [Visual Studio 2019](https://visualstudio.microsoft.com/) eller [Visual Studio för Mac](https://visualstudio.microsoft.com/) med verktyg för Xamarin Forms installerad. Den här snabb starten har testats med Visual Studio 16.6.0.

- Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här snabb starten använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

- Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI till Cloud Shell-instansen. IoT-tillägget lägger till IoT Hub-, IoT Edge-och IoT Device Provisioning-tjänst (DPS)-kommandon i Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhets identiteten.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **myXamarinDevice**: det här är namnet på enheten som du registrerar. Vi rekommenderar att du använder **myXamarinDevice** som det visas. Om du väljer ett annat namn på din enhet måste du också använda det namnet i den här artikeln och uppdatera enhets namnet i exempel programmen innan du kör dem.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myXamarinDevice
   ```

1. Kör följande kommando i Azure Cloud Shell för att hämta _enhets anslutnings strängen_ för enheten som du nyss registrerade:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id myXamarinDevice --output table
   ```

   Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=myXamarinDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabb starten.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Exempel programmet körs på Windows – via en UWP-app – en iOS-enhet eller Simulator och på en Android-enhet eller simulator, som ansluter till en enhetsspecifika slut punkt på din IoT Hub och skickar simulerad temperatur och fuktighets-telemetri. 

1. Öppna exempel arbets ytan i Visual Studio eller Visual Studio för Mac.
2. Expandera **SimulatedDevice** -projektet.  
3. Öppna **IoTHubService.cs** för redigering i Visual Studio. 
4. Sök efter variabeln **_iotHubConnectionString** och uppdatera värdet med enhets anslutnings strängen som du gjorde en anteckning om tidigare.
5. Spara ändringarna. 
6. Kör projektet i enhetens emulator eller en riktig enhet med hjälp av knappen **skapa och kör** eller kortkommandot **F5** på Windows eller **kommando + r** på Mac. 

   ![Kör projektet](media/quickstart-send-telemetry-xamarinforms/run-sample.png)

7. När emulatorn öppnas väljer du **Start** i exempelappen.

Följande skärm bild visar ett exempel på utdata när programmet skickar simulerad telemetri till IoT Hub: ![ kör den simulerade enheten](media/quickstart-send-telemetry-xamarinforms/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb

Exempelappen du körde i XCode-emulatorn visar data om meddelanden som skickas från enheten. Du kan också visa data via din IoT-hubb när de tas emot. CLI-tillägget för IoT Hub kan ansluta till **Events**-slutpunkten för tjänstsidan på din IoT Hub. Tillägget tar emot enhet-till-moln-meddelanden som skickats från din simulerade enhet. Ett IoT Hub-serverprogram körs normalt i molnet för att ta emot och bearbeta enhet-till-molnet-meddelanden.

Kör följande kommandon i Azure Cloud Shell, där du ersätter `YourIoTHubName` med namnet på din IoT-hubb:

```azurecli-interactive
az iot hub monitor-events --device-id myXamarinDevice --hub-name {YourIoTHubName}
```

Följande skärmbild visar utdata när tillägget tar emot telemetridata som skickats från den simulerade enheten till hubben:

Följande skärm bild visar vilken typ av telemetri som visas i det lokala terminalfönstret: ![ Visa telemetri](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapar du en IoT-hubb, registrerat en enhet, skickade simulerad telemetri till hubben från ett Xamarin Forms-program och läser Telemetrin från hubben. 

Om du vill veta hur du kan styra den simulerade enheten från ett serverdelsprogram fortsätter du till nästa snabbstart.

> [!div class="nextstepaction"]
> [Snabbstart: Kontrollera en enhet ansluten till en IoT-hubb](quickstart-control-device-node.md)
