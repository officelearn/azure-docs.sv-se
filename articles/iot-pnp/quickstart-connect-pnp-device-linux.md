---
title: Ansluta IoT Plug and Play förhandsgranska exempel enhets kod till IoT Hub (Linux) | Microsoft Docs
description: Skapa och kör IoT Plug and Play för hands versions exempel enhets kod på Linux som ansluter till en IoT-hubb. Använd Azure CLI för att visa informationen som skickas av enheten till hubben.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 802578c79fa086c74a56db8d47f83ae96d6b0194
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152133"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Snabb start: ansluta ett exempel på IoT Plug and Play för hands versions program som körs i Linux för att IoT Hub (C Linux)

Den här snabb starten visar hur du skapar ett exempel på IoT Plug and Play Device-program i Linux, ansluter det till IoT-BUB och använder Azure CLI för att visa den information som skickas till hubben. Exempel programmet skrivs i C och ingår i Azure IoT-enhetens SDK för C. En lösnings utvecklare kan använda Azure CLI för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav

I den här snabb starten förutsätter vi att du använder Ubuntu Linux. Stegen i den här självstudien har testats med Ubuntu 18,04.

För att slutföra den här snabb starten måste du installera följande program vara på den lokala Linux-datorn:

Installera **gcc**, **git**, **cmake**och alla beroenden med kommandot `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Kontrol lera att versionen av `cmake` är över **2.8.12** och att versionen av **gcc** är över **4.4.7**.

```sh
cmake --version
gcc --version
```

## <a name="prepare-an-iot-hub"></a>Förbered en IoT-hubb

Du behöver också en Azure IoT-hubb i din Azure-prenumeration för att slutföra den här snabb starten. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. Om du inte har en IoT-hubb följer du [de här anvisningarna för att skapa en](../iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> Under den offentliga för hands versionen är IoT Plug and Play-funktioner bara tillgängliga på IoT-hubbar som skapats i regionerna **Central USA**, **Nord Europa**och **Östra Japan** .

Om du använder Azure CLI lokalt bör `az`-versionen vara **2.0.73** eller senare. Azure Cloud Shell använder den senaste versionen. Använd kommandot `az --version` för att kontrol lera vilken version som är installerad på datorn.

Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI till Cloud Shell-instansen:
```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Stegen i den här snabb starten kräver version **0.8.5** eller senare av tillägget. Använd kommandot `az extension list` för att kontrol lera vilken version du har installerat och `az extension update` kommandot för att uppdatera vid behov.

Om du använder CLI lokalt loggar du in på Azure-prenumerationen med följande kommando:

```bash
az login
```

Om du använder Azure Cloud Shell är du redan inloggad automatiskt.

Kör följande kommando för att skapa enhets identiteten i din IoT-hubb. Ersätt plats hållarna **YourIoTHubName** och **YourDeviceID** med ditt eget _IoT Hub namn_ och ett valfritt _enhets-ID_ .

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Kör följande kommando för att hämta _enhets anslutnings strängen_ för enheten som du nyss registrerade (Anmärkning för användning senare):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här snabb starten förbereder du en utvecklings miljö som du kan använda för att klona och bygga Azure IoT Hub Device C SDK.

Öppna en kommando tolk i valfri katalog. Kör följande kommando för att klona [Azure IoT C SDK: er och bibliotek](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen på den här platsen:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Den här åtgärden kan förväntas ta flera minuter att slutföra.

## <a name="build-the-code"></a>Skapa koden

Du kan använda enhets-SDK: n för att bygga den inkluderade exempel koden. Det program som du skapar simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. Skapa en `cmake` under katalog i rotmappen för enhetens SDK och navigera till mappen:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att skapa enhets-SDK och den genererade koden stub:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Uppdatera din modell databas

Innan du kör exemplet lägger du till enhetens kapacitets modell och gränssnitts definitioner i företagets modell lagrings plats:

1. Logga in på [Azure-certifierad för IoT Portal](https://preview.catalog.azureiotsolutions.com) -portalen med ditt arbets-eller skol konto i Microsoft, eller ditt Microsoft partner-ID om du har ett.

1. Välj **företags databas** och sedan **kapacitets modeller**.

1. Välj **ny** och sedan **Ladda upp**.

1. Markera filen `SampleDevice.capabilitymodel.json` i mappen `digitaltwin_client/samples` i rotmappen för enhetens SDK. Välj **Öppna** och sedan **Spara** för att ladda upp modell filen till din lagrings plats.

1. Välj **företags databas** och sedan **gränssnitt**.

1. Välj **ny** och sedan **Ladda upp**.

1. Markera filen `EnvironmentalSensor.interface.json` i mappen `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` i rotmappen för enhetens SDK. Välj **Öppna** och sedan **Spara** för att ladda upp gränssnitts filen till din lagrings plats.

1. Välj **företags databas** och sedan **anslutnings strängar**. Anteckna den första _företags modell databasens anslutnings sträng_, när du använder den senare i den här snabb starten.

## <a name="run-the-device-sample"></a>Kör enhets exemplet

Kör ett exempel program i SDK för att simulera en IoT Plug and Play-enhet som skickar telemetri till IoT Hub. Köra exempel programmet:

1. Från mappen `cmake` navigerar du till mappen som innehåller den körbara filen:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Kör den körbara filen:

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

Enheten är nu redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exemplet körs när du slutför nästa steg.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Använd Azure IoT CLI för att validera koden

När enhets klient exemplet startar kontrollerar du att det fungerar med Azure CLI.

Använd följande kommando för att Visa telemetri som exempel enheten skickar. Du kan behöva vänta en minut eller två innan du ser en telemetri i utdata:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Använd följande kommando för att visa de egenskaper som har skickats av enheten:

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Anvisningar: ansluta till och interagera med en enhet](howto-develop-solution.md)
