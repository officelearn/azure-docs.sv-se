---
title: Ansluta IoT Plug and Play Preview exempel C enhets kod till IoT Hub | Microsoft Docs
description: Skapa och kör IoT Plug and Play för hands versions exempel enhets kod på Linux eller Windows som ansluter till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 65b35c30d796f733e91c71e569634b61f9f7c52b
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88959297"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-or-windows-to-iot-hub-c"></a>Snabb start: ansluta ett exempel på IoT Plug and Play Preview-enhets program som körs på Linux eller Windows till IoT Hub (C)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Den här snabb starten visar hur du skapar ett exempel på IoT Plug and Play Device-program, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att Visa telemetri som skickas. Exempel programmet skrivs i C och ingår i Azure IoT-enhetens SDK för C. Ett Solution Builder kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Förutsättningar

Du kan köra den här snabb starten på Linux eller Windows. Shell-kommandona i den här snabb starten följer Linux-konventionen för Sök vägs avgränsare ' `/` ', om du följer med i Windows måste du byta avgränsare för ' `\` '.

Kraven varierar beroende på operativ system:

### <a name="linux"></a>Linux

I den här snabb starten förutsätter vi att du använder Ubuntu Linux. Stegen i den här snabb starten har testats med Ubuntu 18,04.

För att slutföra den här snabb starten på Linux installerar du följande program vara i din lokala Linux-miljö:

Installera **gcc**, **git**, **cmake**och alla nödvändiga beroenden med `apt-get` kommandot:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Kontrol lera att versionen av `cmake` är över **2.8.12** och att versionen av **gcc** är över **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

För att slutföra den här snabb starten på Windows installerar du följande program vara i din lokala Windows-miljö:

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) – se till att du inkluderar **Skriv bords utveckling med C++** -arbetsbelastning när du [installerar](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) Visual Studio.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

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

I den här snabb starten förbereder du en utvecklings miljö som du kan använda för att klona och bygga Azure IoT Hub Device C SDK.

Öppna en kommando tolk i valfri katalog. Kör följande kommando för att klona [Azure IoT C SDK: er och bibliotek](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen på den här platsen:

```cmd\bash
git clone --depth 1 --recurse-submodules https://github.com/Azure/azure-iot-sdk-c.git
```

Det kan ta flera minuter att slutföra den här åtgärden.

## <a name="build-the-code"></a>Skapa koden

Du kan använda enhets-SDK: n för att bygga den inkluderade exempel koden:

1. Skapa en _cmake_ under katalog i rotmappen för enhets-SDK: n och navigera till mappen:

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att skapa SDK och exempel:

    ```cmd\bash
    cmake ..
    cmake --build .
    ```

> [!TIP]
> I Windows kan du öppna lösningen som genereras av `cmake` kommandot i Visual Studio 2019. Öppna projekt filen *azure_iot_sdks. SLN* i katalogen _cmake_ och ange **pnp_simple_thermostat** projektet som start projekt i lösningen. Nu kan du skapa exemplet i Visual Studio och köra det i fel söknings läge.

## <a name="run-the-device-sample"></a>Kör enhets exemplet

Köra exempel programmet i SDK som simulerar en IoT Plug and Play-enhet som skickar telemetri till din IoT-hubb:

Skapa två miljövariabler för att konfigurera exemplet för att använda en anslutnings sträng för att ansluta till din IoT-hubb:

- **IOTHUB_DEVICE_SECURITY_TYPE** med värdet `"connectionString"`
- **IOTHUB_DEVICE_CONNECTION_STRING** att lagra enhets anslutnings strängen som du antecknade tidigare.

Från mappen _cmake_ navigerar du till mappen som innehåller den körbara filen och kör den:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd  iothub_client\samples\pnp\pnp_simple_thermostat\Debug\pnp_simple_thermostat.exe
```

> [!TIP]
> Om du vill spåra kod körningen i Visual Studio på Windows lägger du till en Bryt punkt i `main` funktionen i filen _pnp_simple_thermostat. c_ .

Enheten är nu redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exemplet körs när du slutför nästa steg.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Granska koden

I det här exemplet implementeras en enkel IoT Plug and Play termostat-enhet. Modellen som det här exemplet implementerar använder inte IoT Plug and Play- [komponenter](concepts-components.md). [DTDL-modell filen för termostat-enheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definierar telemetri, egenskaper och kommandon som enheten implementerar.

Enhets koden använder standard-funktionen för att ansluta till din IoT-hubb:

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

Enheten skickar modell-ID: t för DTDL-modellen som den implementerar i anslutningsbegäran. En enhet som skickar ett modell-ID är en IoT Plug and Play-enhet:

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

Den kod som uppdaterar egenskaper, hanterar kommandon och skickar telemetri är identisk med koden för en enhet som inte använder IoT Plug and Play konventioner.

I koden används Parson-biblioteket för att parsa JSON-objekt i de nytto laster som skickas från din IoT Hub:

```c
// JSON parser
#include "parson.h"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Anvisningar: ansluta till och interagera med en enhet](howto-develop-solution.md)
