---
title: Ansluta IoT Plug and Play Preview exempel kod för Java-komponent enhet till IoT Hub | Microsoft Docs
description: Skapa och köra IoT Plug and Play Preview-exempel Java-enhets kod som använder flera komponenter och ansluter till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1d16d8c54939c4f659b6a1530e2d360b957a09ad
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352849"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-java"></a>Självstudie: ansluta en exempel-IoT Plug and Play förhandsgranska flera komponent enhets program till IoT Hub (Java)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

I den här självstudien får du lära dig hur du skapar ett Plug and Play enhets program för flera komponenter, ansluter det till din IoT-hubb och använder Azure CLI för att Visa telemetri som skickas. Exempel programmet är skrivet i Java och ingår i Azure IoT-enhetens SDK för Java. Ett Solution Builder kan använda Azure CLI för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

I den här självstudien får du lära dig hur du skapar ett exempel på IoT Plug and Play enhets program med komponenter och rot gränssnitt, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att visa den information som skickas till hubben. Exempel programmet är skrivet i Java och ingår i Azure IoT-enhetens SDK för Java. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill slutföra den här självstudien i Windows installerar du följande program vara i din lokala Windows-miljö:

* Java SE Development Kit 8. I [Java långsiktigt stöd för Azure och Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable), under **långsiktig support**, väljer du **Java 8**.
* [Apache maven 3](https://maven.apache.org/download.cgi).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Om du vill interagera med exempel enheten i den andra delen av den här snabb starten använder du **Azure IoT Explorer** -verktyget. [Hämta och installera den senaste versionen av Azure IoT Explorer](./howto-use-iot-explorer.md) för ditt operativ system.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutnings strängen för IoT Hub_ för hubben. Anteckna den här anslutnings strängen, du använder den senare i den här snabb starten:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Du kan också använda Azure IoT Explorer-verktyget för att hitta anslutnings strängen för IoT Hub.

Kör följande kommando för att hämta _enhets anslutnings strängen_ för den enhet som du har lagt till i hubben. Anteckna den här anslutnings strängen, du använder den senare i den här snabb starten:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Ladda ned koden

I den här självstudien förbereder du en utvecklings miljö som du kan använda för att klona och bygga Azure IoT Hub Device Java SDK.

Öppna en kommando tolk i valfri katalog. Kör följande kommando för att klona [Azure IoT Java-SDK: er och bibliotek](https://github.com/Azure/azure-iot-sdk-java) GitHub-lagringsplatsen till den här platsen:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

Det kan ta flera minuter att slutföra den här åtgärden.

## <a name="build-the-code"></a>Skapa koden

I Windows navigerar du till rotmappen för den klonade Java SDK-lagringsplatsen. Navigera sedan till mappen *\device\iot-Device-samples\pnp-Device-sample\temerature-Controller-Device-Sample*

Kör följande kommando för att skapa exempel programmet:

```java
mvn clean package
```

## <a name="run-the-device-sample"></a>Kör enhets exemplet

Skapa en miljö variabel med namnet **IOTHUB_DEVICE_CONNECTION_STRING** för att lagra enhets anslutnings strängen som du antecknade tidigare.

Kör exempel programmet genom att köra följande kommando:

```java
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

Enheten är nu redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exemplet körs när du slutför nästa steg.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Granska koden

Det här exemplet implementerar en IoT Plug and Play temperatur styrenhets enhet. Modellen som det här exemplet implementerar använder [flera komponenter](concepts-components.md). [DTDL-modell filen (Digital Definition Language) för temperatur enheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definierar telemetri, egenskaper och kommandon som enheten implementerar.

Enhets koden använder standard- `DeviceClient` klassen för att ansluta till din IoT-hubb. Enheten skickar modell-ID: t för DTDL-modellen som den implementerar i anslutningsbegäran. En enhet som skickar ett modell-ID är en IoT Plug and Play-enhet:

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

Modell-ID: t lagras i koden som visas i följande kodfragment:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

När enheten ansluter till din IoT-hubb registreras kommando hanterare i koden.

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

Det finns separata hanterare för önskade egenskaps uppdateringar för de två termostat-komponenterna:

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

Exempel koden skickar telemetri från varje termostat-komponent:

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

`sendTemperatureReading`Metoden använder `PnpHhelper` klassen för att skapa meddelanden för varje komponent:

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

`PnpHelper`Klassen innehåller andra exempel metoder som du kan använda med en modell för flera komponenter.

Använd Azure IoT Explorer-verktyget för att Visa telemetri och egenskaper från de två termostat-komponenterna:

:::image type="content" source="media/tutorial-multiple-components-java/multiple-component.png" alt-text="Enhet för flera komponenter i Azure IoT Explorer":::

Du kan också använda Azure IoT Explorer-verktyget för att anropa kommandon i någon av de två termostat-komponenterna eller i rot gränssnittet.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du ansluter en IoT Plug and Play-enhet med komponenter till en IoT-hubb. Mer information om IoT Plug and Play enhets modeller finns i:

> [!div class="nextstepaction"]
> [IoT Plug and Play Preview Modeling Developer Guide](concepts-developer-guide.md)
