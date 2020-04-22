---
title: Anslut IoT Plug and Play Preview-exempelenhetskod till IoT Hub (Windows) | Microsoft-dokument
description: Skapa och kör IoT Plug and Play Preview-exempelenhetskod i Windows som ansluter till en IoT-hubb. Använd Azure IoT explorer-verktyget för att visa informationen som skickas av enheten till navet.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 080820024db1302ff5a841761428442396b90040
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769852"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>Snabbstart: Ansluta ett exempel på IoT Plug and Play Preview-enhetsprogram som körs på Windows till IoT Hub (C Windows)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Den här snabbstarten visar hur du skapar ett exempel på IoT Plug and Play-enhetsprogram, ansluter det till din IoT-hubb och använder Azure IoT explorer-verktyget för att visa informationen som skickas till hubben. Exempelprogrammet är skrivet i C och ingår i Azure IoT Hub Device C SDK. En lösningsutvecklare kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhetskod.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten måste du installera följande programvara på din lokala dator:

* [Visual Studio (Community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) – se till att du inkluderar **Komponenten NuGet-pakethanteraren** och **skrivbordsutvecklingen med C++-arbetsbelastning** när du installerar Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Installera Azure IoT-utforskaren

Hämta och installera den senaste versionen av **Azure IoT Explorer** från verktygets [databassida](https://github.com/Azure/azure-iot-explorer/releases) genom att välja MSI-filen under "Tillgångar" för den senaste uppdateringen.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutningssträngen för IoT-hubben_ för navet (observera för användning senare):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här snabbstarten förbereder du en utvecklingsmiljö som du kan använda för att klona och skapa Azure IoT Hub Device C SDK.

Öppna en kommandotolk i valfri katalog. Kör följande kommando för att klona [Azure IoT C SDK:er och bibliotek](https://github.com/Azure/azure-iot-sdk-c) GitHub-databasen till den här platsen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Den här åtgärden kan förväntas ta flera minuter att slutföra.

## <a name="build-the-code"></a>Skapa koden

Du använder enheten SDK för att skapa den medföljande exempelkoden. Programmet du skapar simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. Skapa `cmake` en underkatalog i enhetens SDK-rotmapp och navigera till den mappen:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att skapa enhetenSDK och den genererade koden stub:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Om cmake inte kan hitta din C++-kompilator får du byggfel när du kör föregående kommando. Om det händer kan du prova att köra det här kommandot i [kommandotolken i Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Kör enhetsprovet

Kör ett exempelprogram i SDK för att simulera en IoT Plug and Play-enhet som skickar telemetri till din IoT-hubb. Om du vill köra exempelprogrammet använder du dessa kommandon och skickar _enhetsanslutningssträngen_ som en parameter.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

Enheten är nu redo att ta emot kommandon och egenskapsuppdateringar och har börjat skicka telemetridata till navet. Håll exemplet igång när du slutför nästa steg.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Använda Azure IoT explorer för att validera koden

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Om du vill vara säkra på att verktyget kan läsa definitionerna för gränssnittsmodell från enheten väljer du **Inställningar**. På menyn Inställningar kan det hända att **den anslutna enheten** redan visas i Plug and Play-konfigurationerna. Om den inte gör det väljer du **+ Lägg till moduldefinitionskälla** och sedan på den anslutna enheten för att lägga till **den.**

1. Leta reda på den enhetsidentitet som du skapade tidigare på sidan **Enhetsöversikt.** När enhetsprogrammet fortfarande körs i kommandotolken kontrollerar du att enhetens **anslutningstillstånd** i Azure IoT Explorer rapporterar som _Ansluten_ (om inte, tryck **på Uppdatera** tills den är). Välj den enhet som ska visas för mer information.

1. Expandera gränssnittet med ID **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** för att visa gränssnittet och IoT Plug and Play-primitiverna – egenskaper, kommandon och telemetri.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med dina IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Så här gör du: Ansluta till och interagera med en IoT Plug and Play Preview-enhet](howto-develop-solution.md)
