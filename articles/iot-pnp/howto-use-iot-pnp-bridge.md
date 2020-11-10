---
title: Så här ansluter du ett IoT Plug and Play Bridge-exempel som körs på Linux eller Windows till en IoT-hubb | Microsoft Docs
description: Skapa och kör IoT Plug and Play Bridge på Linux eller Windows som ansluter till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: eedd19189d1e1ccedd3d505aecf407aca8fca831
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413368"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>Så här ansluter du ett IoT Plug and Play Bridge-exempel som körs på Linux eller Windows till IoT Hub

Den här instruktionen visar hur du skapar IoT Plug and Play Bridges exempel miljö kort, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att Visa telemetri som skickas. IoT Plug and Play-bryggan är skriven i C och innehåller Azure IoT-enhetens SDK för C. I slutet av den här själv studie kursen ska du kunna köra IoT Plug and Play-bryggan och se den rapportera telemetri i Azure IoT Explorer: :::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="en skärm bild som visar Azure IoT Explorer med en tabell med rapporterade telemetri (fuktighet, temperatur) från IoT plug and Play Bridge.":::

## <a name="prerequisites"></a>Förutsättningar

Du kan köra den här snabb starten på Linux eller Windows. Shell-kommandona i den här instruktions guiden följer Windows-konventionen för Sök vägs avgränsare `\` , om du följer med i Linux, se till att byta avgränsare för `/` .

Kraven varierar beroende på operativ system:

### <a name="linux"></a>Linux

I den här snabb starten förutsätter vi att du använder Ubuntu Linux. Stegen i den här snabb starten har testats med Ubuntu 18,04.

För att slutföra den här snabb starten på Linux installerar du följande program vara i din lokala Linux-miljö:

Installera **gcc** , **git** , **cmake** och alla nödvändiga beroenden med `apt-get` kommandot:

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

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) – se till att du inkluderar **Skriv bords utveckling med C++** -arbetsbelastning när du [installerar](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) Visual Studio.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Om du vill interagera med exempel enheten i den andra delen av den här snabb starten använder du **Azure IoT Explorer** -verktyget. [Hämta och installera den senaste versionen av Azure IoT Explorer](./howto-use-iot-explorer.md) för ditt operativ system.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _IoT Hub anslutnings sträng_ för hubben. Anteckna den här anslutnings strängen, du använder den senare i den här snabb starten:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Du kan också använda Azure IoT Explorer-verktyget för att hitta anslutnings strängen för IoT Hub.

Kör följande kommando för att hämta _enhets anslutnings strängen_ för den enhet som du har lagt till i hubben. Anteckna den här anslutnings strängen, du använder den senare i den här snabb starten:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="view-the-model"></a>Visa modellen

Du kommer att använda Azure IoT Explorer i senare steg för att visa enheten när den ansluter till din IoT-hubb. Azure IoT Explorer behöver en lokal kopia av modell filen som matchar det **modell-ID** som din enhet skickar. Med modell filen kan IoT Explorer Visa telemetri, egenskaper och kommandon som enheten implementerar.

När du hämtar koden i steget nedan innehåller exempel modellens filer under `pnpbridge/docs/schema` mappen. Förbered Azure IoT Explorer:

1. Skapa en mapp med namnet *modeller* på den lokala datorn.
1. Visa [EnvironmentalSensor.jspå](https://aka.ms/iot-pnp-bridge-env-model) och spara JSON-filen till mappen *modeller*
1. Visa [RootBridgeSampleDevice.jspå](https://aka.ms/iot-pnp-bridge-root-model) och spara JSON-filen i mappen *modeller* .

## <a name="download-the-code"></a>Ladda ned koden

Öppna en kommando tolk i valfri katalog. Kör följande kommando för att klona [IoT plug and Play Bridge](https://aka.ms/iotplugandplaybridge) GitHub-lagringsplatsen till den här platsen:

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

När du har klonat IoT Plug and Play Bridge-lagrings platsen till datorn öppnar du en administrativ kommando tolk och går till katalogen för den klonade lagrings platsen:

```cmd
cd pnpbridge
git submodule update --init --recursive
```

Det kan ta flera minuter att slutföra den här åtgärden.

>[!NOTE]
> Om du stöter på problem med uppdatering av Git-kloningen, är detta ett känt problem med Windows fil Sök vägar och git se: https://github.com/msysgit/git/pull/110 . Du kan köra följande kommando för att lösa problemet: `git config --system core.longpaths true`

## <a name="setting-up-the-configuration-json"></a>Konfigurera konfigurations-JSON

När du har klonat IoT Plug and Play Bridge-lagrings platsen till din dator, navigerar du till `pnpbridge/docs/schema` katalogen i den klonade lagrings platsen där du hittar [konfigurations-JSON](https://aka.ms/iot-pnp-bridge-env-config) eller `config.json` för miljö sensor exemplet i bryggan. Du kan lära dig mer om config-filer i [IoT plug and Play Bridge Concepts-dokument](concepts-iot-pnp-bridge.md).

För `root-_interface_model_id` fältet måste du kopiera IoT-plug and Play modell-ID som identifierar modellen för enheten. I det här exemplet är det `dtmi:com:example:SampleDevice;1`. Ändra följande parametrar under **pnp_bridge_parameters** nod i `config.json` filen:

* connection_string 
* symmetric_key 

>[!NOTE]
> Symmetric_key måste matcha SAS-nyckeln i anslutnings strängen.

  ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "[To fill in]",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
  ```

 När den har fyllts i `config.json` bör filen likna följande:

   ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "dtmi:com:example:SampleDevice;1",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
```

 När du har skapat bryggan måste du placera detta `config.json` i samma katalog som bryggan eller ange dess sökväg när den körs.

## <a name="build-the-iot-plug-and-play-bridge"></a>Skapa IoT Plug and Play-bryggan

Navigera till mappen *pnpbridge* i katalogen för lagrings platsen.

För Windows kör du följande i en [kommando tolk för utvecklare för Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs):

```cmd
cd scripts\windows
build.cmd
```

På samma sätt för Linux kör du följande:

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>I Windows kan du öppna lösningen som genererats av cmake-kommandot i Visual Studio 2019. Öppna projekt filen *azure_iot_pnp_bridge. SLN* i katalogen cmake och ange *pnpbridge_bin* projektet som start projekt i lösningen. Nu kan du skapa exemplet i Visual Studio och köra det i fel söknings läge.

## <a name="start-the-iot-plug-and-play-bridge"></a>Starta IoT Plug and Play-bryggan

 Starta exemplet IoT Plug and Play Bridge för miljö sensorer genom att navigera till mappen *pnpbridge* och köra följande i en kommando tolk:

```bash
 cd cmake/pnpbridge_linux/src/adapters/samples/environmental_sensor/
./pnpbridge_environmentalsensor

```

```cmd
REM Windows
cd cmake\pnpbridge_x86\src\adapters\samples\environmental_sensor
Debug\pnpbridge_environmentalsensor.exe
```

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

När enhets klient exemplet startar använder du verktyget Azure IoT Explorer för att kontrol lera att det fungerar.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

* [Vad är IoT Plug and Play Bridge](./concepts-iot-pnp-bridge.md)
* [Se GitHub Developer Reference för IoT Plug and Play Bridge](https://aka.ms/iot-pnp-bridge-dev-doc)
* [IoT Plug and Play-brygga på GitHub](https://aka.ms/iotplugandplaybridge)
