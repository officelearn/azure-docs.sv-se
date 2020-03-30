---
title: Anslut IoT Plug and Play Preview-exempelenhetskod till IoT Hub (Linux) | Microsoft-dokument
description: Skapa och kör IoT Plug and Play Preview-exempelenhetskod på Linux som ansluter till en IoT-hubb. Använd Azure CLI för att visa informationen som skickas av enheten till navet.
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75531278"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Snabbstart: Anslut ett exempel på IoT Plug and Play Preview-enhetsprogram som körs på Linux till IoT Hub (C Linux)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Den här snabbstarten visar hur du skapar ett exempel på ett IoT Plug and Play-enhetsprogram på Linux, ansluter det till din IoT bub och använder Azure CLI för att visa informationen som skickas till hubben. Exempelprogrammet skrivs i C och ingår i Azure IoT-enheten SDK för C. En lösningsutvecklare kan använda Azure CLI för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhetskod.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav

Denna snabbstart förutsätter att du använder Ubuntu Linux. Stegen i den här självstudien testades med Ubuntu 18.04.

För att slutföra den här snabbstarten måste du installera följande programvara på din lokala Linux-dator:

Installera **GCC,** **Git**, **cmake**och `apt-get` alla beroenden med kommandot:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Kontrollera att `cmake` versionen av är över **2.8.12** och versionen av **GCC** är över **4.4.7**.

```sh
cmake --version
gcc --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här snabbstarten förbereder du en utvecklingsmiljö som du kan använda för att klona och skapa Azure IoT Hub Device C SDK.

Öppna en kommandotolk i valfri katalog. Kör följande kommando för att klona [Azure IoT C SDK:er och bibliotek](https://github.com/Azure/azure-iot-sdk-c) GitHub-databasen till den här platsen:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Den här åtgärden kan förväntas ta flera minuter att slutföra.

## <a name="build-the-code"></a>Skapa koden

Du använder enheten SDK för att skapa den medföljande exempelkoden. Programmet du skapar simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. Skapa `cmake` en underkatalog i enhetens SDK-rotmapp och navigera till den mappen:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att skapa enhetenSDK och den genererade koden stub:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Uppdatera modelldatabasen

Innan du kör exemplet lägger du till enhetskapacitetsmodellen och gränssnittsdefinitionerna i företagsmodelldatabasen:

1. Logga in på [Azure Certified for IoT-portalen](https://preview.catalog.azureiotsolutions.com) med ditt Microsoft-arbets- eller skolkonto eller ditt Microsoft Partner-ID om du har ett.

1. Välj **Företagsdatabas** och sedan **Capability-modeller**.

1. Välj **Ny** och ladda sedan **upp**.

1. Markera filen `SampleDevice.capabilitymodel.json` i `digitaltwin_client/samples` mappen i enhetens SDK-rotmapp. Välj **Öppna** och sedan **Spara** om du vill överföra modellfilen till databasen.

1. Välj **Företagsdatabas** och sedan **Gränssnitt**.

1. Välj **Ny** och ladda sedan **upp**.

1. Markera filen `EnvironmentalSensor.interface.json` i `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` mappen i enhetens SDK-rotmapp. Välj **Öppna** och sedan **Spara** om du vill överföra gränssnittsfilen till databasen.

1. Välj **Företagsdatabas** och sedan **Anslutningssträngar**. Anteckna den första _anslutningssträngen för företagsmodelllagringsplats_, som du använder den senare i den här snabbstarten.

## <a name="run-the-device-sample"></a>Kör enhetsprovet

Kör ett exempelprogram i SDK för att simulera en IoT Plug and Play-enhet som skickar telemetri till din IoT-hubb. Så här kör du exempelprogrammet:

1. Gå `cmake` till mappen som innehåller den körbara filen i mappen:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Kör den körbara filen:

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

Enheten är nu redo att ta emot kommandon och egenskapsuppdateringar och har börjat skicka telemetridata till navet. Håll exemplet igång när du slutför nästa steg.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Använda Azure IoT CLI för att validera koden

När enhetsklientexemplet har startat kontrollerar du att det fungerar med Azure CLI.

Använd följande kommando för att visa den telemetri som exempelenheten skickar. Du kan behöva vänta en minut eller två innan du ser någon telemetri i utdata:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Använd följande kommando för att visa de egenskaper som enheten har skickat:

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med dina IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Så här: Ansluta till och interagera med en enhet](howto-develop-solution.md)
