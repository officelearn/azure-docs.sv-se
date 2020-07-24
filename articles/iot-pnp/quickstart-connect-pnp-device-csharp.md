---
title: Ansluta IoT Plug and Play Preview-kod till IoT Hub-C# | Microsoft Docs
description: Använda C# (.NET), skapa och köra IoT Plug and Play enhets kod som ansluter till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d598bbb792c071db281ebdc562402ba0198305ab
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044193"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>Snabb start: ansluta ett exempel på IoT Plug and Play Preview enhets program till IoT Hub (C#)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Den här snabb starten visar hur du skapar ett exempel på IoT Plug and Play Device-program, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att visa den information som skickas till hubben. Exempel programmet skrivs i C# (med .NET) och tillhandahålls som en del av Azure IoT-exemplen för C#-samlingen (.NET). En lösnings utvecklare kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten måste du installera .NET Core 3,0 på din utvecklings dator. Du kan hämta den här versionen av .NET Core SDK för flera plattformar från [Hämta .net Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0).

Du kan kontrol lera vilken version av .NET som finns på utvecklings datorn genom att köra följande kommando i ett lokalt terminalfönster: 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>Installera Azure IoT Explorer

Hämta och installera den senaste versionen av **Azure IoT Explorer** från verktygets [databas](https://github.com/Azure/azure-iot-explorer/releases) sida genom att välja. msi-filen under "till gångar" för den senaste uppdateringen.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Kör följande kommando för att hämta _anslutnings strängen för IoT Hub_ för din hubb (Anmärkning för användning senare):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

I den här snabb starten förbereder du en utvecklings miljö som du kan använda för att klona och bygga Azure IoT-exempel för C# (.NET).

Öppna en kommando tolk i valfri katalog. Kör följande kommando för att klona [Azure IoT-exemplen för C# (.net)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-lagringsplatsen till den här platsen:

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

Den här åtgärden kan ta flera minuter att slutföra.

## <a name="run-the-device-sample"></a>Kör enhets exemplet

Du använder den klonade exempel koden för att bygga ett program som simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. I ett lokalt terminalfönster går du till mappen för den klonade lagrings platsen och navigerar till mappen **Azure-IoT-samples-csharp/digitaltwin/samples/Device/EnvironmentalSensorSample** . 

1. Konfigurera _enhets anslutnings strängen_:

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Kör ett exempel program för att simulera en IoT Plug and Play-enhet som skickar telemetri till IoT Hub. I samma terminalfönster, för att skapa de nödvändiga paketen och köra exempel programmet, använder du följande kommando:

    ```cmd\sh
    dotnet run --framework=netcoreapp3.0
    ```

Du ser meddelanden som säger att enheten har registrerats och väntar på uppdateringar från molnet. Detta anger att enheten nu är redo att ta emot kommandon och egenskaps uppdateringar och har börjat skicka telemetridata till hubben. Se till att exemplet körs när du slutför nästa steg.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Om du vill se till att verktyget kan läsa definitionerna för gränssnitts modellen från enheten väljer du **Inställningar**. På menyn Inställningar kan det hända att **den anslutna enheten** redan visas i Plug and Play konfigurationer. om den inte gör det väljer du **+ Lägg till modulens definitions källa** och sedan **på den anslutna enheten** för att lägga till den.

1. På sidan **enhets** Översikt hittar du enhets identiteten som du skapade tidigare. När enhets programmet fortfarande körs i kommando tolken kontrollerar du att enhetens **anslutnings status** i Azure IoT Explorer rapporterar som _ansluten_ (om inte klickar du på **Uppdatera** tills den är). Välj enheten om du vill visa mer information.

1. Expandera gränssnittet med ID **urn: csharp_sdk_sample: EnvironmentalSensor: 1** om du vill visa gränssnittet och IoT plug and Play-primitiver – egenskaper, kommandon och telemetri.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Anvisningar: ansluta till och interagera med en IoT Plug and Play för hands version](howto-develop-solution.md)
