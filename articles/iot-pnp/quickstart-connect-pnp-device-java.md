---
title: Anslut IoT Plug and Play Preview-exempelenhetskod till IoT Hub | Microsoft-dokument
description: Med Java kan du skapa och köra exempelenhetskod för IoT Plug and Play Preview som ansluter till en IoT-hubb. Använd Azure IoT explorer-verktyget för att visa informationen som skickas av enheten till navet.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: aa676dd374eccf2a4b5c4622689ed402c8679e5a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964843"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-java"></a>Snabbstart: Ansluta ett exempel på IoT Plug and Play Preview-enhetsprogram till IoT Hub (Java)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Den här snabbstarten visar hur du skapar ett exempel på IoT Plug and Play-enhetsprogram, ansluter det till din IoT-hubb och använder Azure IoT explorer-verktyget för att visa informationen som skickas till hubben. Exempelprogrammet är skrivet i Java och tillhandahålls som en del av Azure IoT-samplingarna för Java-samlingen. En lösningsutvecklare kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhetskod.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav

För att slutföra denna snabbstart behöver du Java SE 8 på din utvecklingsmaskin. Du måste också installera Maven 3.

Mer information om hur du konfigurerar med dessa finns i [Förbereda din utvecklingsmiljö](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) i Microsoft Azure IoT-enheten SDK för Java.

### <a name="install-the-azure-iot-explorer"></a>Installera Azure IoT-utforskaren

Hämta och installera den senaste versionen av **Azure IoT Explorer** från verktygets [databassida](https://github.com/Azure/azure-iot-explorer/releases) genom att välja MSI-filen under "Tillgångar" för den senaste uppdateringen.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutningssträngen för IoT-hubben_ för navet (observera för användning senare):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här snabbstarten förbereder du en utvecklingsmiljö som du kan använda för att klona och skapa Azure IoT-exempel för Java.

Öppna ett terminalfönster i valfri katalog. Kör följande kommando för att klona [Azure IoT-exempel för Java](https://github.com/Azure-Samples/azure-iot-samples-java) GitHub-databasen till den här platsen:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-java
```

Den här åtgärden kan ta flera minuter att slutföra.

## <a name="build-the-code"></a>Skapa koden

Du använder den klonade exempelkoden för att skapa ett program som simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. I ett lokalt terminalfönster går du till mappen i den klonade databasen och navigerar till mappen **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample.** Kör sedan följande kommando för att installera de bibliotek som krävs och skapa det simulerade enhetsprogrammet:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Konfigurera _enhetens anslutningssträng:_

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-device-sample"></a>Kör enhetsprovet

Kör ett exempelprogram för att simulera en IoT Plug and Play-enhet som skickar telemetri till din IoT-hubb. Så här kör du exempelprogrammet:

```cmd\sh
java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
```

Du ser meddelanden som säger att enheten är ansluten, utför olika installationssteg och väntar på tjänstuppdateringar, följt av telemetriloggar. Detta indikerar att enheten nu är redo att ta emot kommandon och egenskapsuppdateringar och har börjat skicka telemetridata till navet. Håll exemplet igång när du slutför nästa steg.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Använda Azure IoT explorer för att validera koden

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Om du vill vara säkra på att verktyget kan läsa definitionerna för gränssnittsmodell från enheten väljer du **Inställningar**. På menyn Inställningar kan det hända att **den anslutna enheten** redan visas i Plug and Play-konfigurationerna. Om den inte gör det väljer du **+ Lägg till moduldefinitionskälla** och sedan på den anslutna enheten för att lägga till **den.**

1. Leta reda på den enhetsidentitet som du skapade tidigare på sidan **Enhetsöversikt.** När enhetsprogrammet fortfarande körs i kommandotolken kontrollerar du att enhetens **anslutningstillstånd** i Azure IoT Explorer rapporterar som _Ansluten_ (om inte, tryck **på Uppdatera** tills den är). Välj den enhet som ska visas för mer information.

1. Expandera gränssnittet med ID **urn:java_sdk_sample:EnvironmentalSensor:1** för att visa gränssnittet och IoT Plug and Play-primitiverna – egenskaper, kommandon och telemetri.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med dina IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Så här gör du: Ansluta till och interagera med en IoT Plug and Play Preview-enhet](howto-develop-solution.md)
