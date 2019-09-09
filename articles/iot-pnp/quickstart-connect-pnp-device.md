---
title: Ansluta IoT Plug and Play förhandsgranska exempel enhets kod till IoT Hub | Microsoft Docs
description: Skapa och kör IoT Plug and Play exempel enhets kod som ansluter till en IoT-hubb. Använd Azure IoT Explorer-verktyget för att visa informationen som skickas av enheten till hubben.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 4802c120f4722796fcad4ea99f25adad368451fd
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806537"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub"></a>Snabbstart: Anslut ett exempel på IoT Plug and Play för hands version av enhet till IoT Hub

Den här snabb starten visar hur du skapar ett exempel på IoT Plug and Play Device-program, ansluter det till din IoT-hubb och använder Azure IoT Explorer-verktyget för att visa den information som skickas till hubben. Exempel programmet skrivs i C och ingår i Azure IoT-enhetens SDK för C. En lösnings utvecklare kan använda Azure IoT Explorer-verktyget för att förstå funktionerna i en IoT Plug and Play-enhet utan att behöva visa någon enhets kod.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten måste du installera följande program vara på den lokala datorn:

* [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) – se till att du inkluderar **NuGet Package Manager** -komponenten och **Skriv bords utveckling med C++**  arbets belastning när du installerar Visual Studio.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Installera Azure IoT Explorer

Hämta och installera verktyget Azure IoT Explorer från den [senaste versions](https://github.com/Azure/azure-iot-explorer/releases) sidan.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Förbered en IoT-hubb

Du behöver också en Azure IoT-hubb i din Azure-prenumeration för att slutföra den här snabb starten. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!NOTE]
> Under den offentliga för hands versionen är IoT Plug and Play-funktioner bara tillgängliga på IoT-hubbar som skapats i regionerna **Central USA**, **Nord Europa**och **Östra Japan** .

Lägg till Microsoft Azure IoT-tillägg för Azure CLI:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Kör följande kommando för att skapa enhets identiteten i din IoT-hubb. Ersätt plats hållarna **YourIoTHubName** och **YourDevice** med dina faktiska namn. Om du inte har något IoT Hub, följer du [dessa anvisningar för att skapa ett](../iot-hub/iot-hub-create-using-cli.md):

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Kör följande kommandon för att hämta _enhets anslutnings strängen_ för enheten som du nyss registrerade:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Kör följande kommandon för att hämta _anslutnings strängen för IoT Hub_ för hubben:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

### <a name="get-azure-iot-device-sdk-for-c"></a>Hämta Azure IoT-enhetens SDK för C

I den här snabb starten förbereder du en utvecklings miljö som du kan använda för att klona och skapa Azure IoT C-enhetens SDK.

Öppna en kommandotolk. Kör följande kommando för att klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Den här åtgärden kan förväntas ta flera minuter att slutföra.

## <a name="build-the-code"></a>Skapa koden

Det program som du skapar simulerar en enhet som ansluter till en IoT-hubb. Programmet skickar telemetri och egenskaper och tar emot kommandon.

1. Skapa en `cmake` under katalog i rotmappen för enhetens SDK och navigera till mappen:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Kör följande kommandon för att skapa enhets-SDK och den genererade koden stub:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Om cmake inte kan hitta C++ din kompilator får du build-fel när du kör föregående kommando. Om det händer kan du prova att köra det här kommandot i [Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)-Kommandotolken.

## <a name="run-the-device-sample"></a>Kör enhets exemplet

Kör programmet genom att skicka IoT Hub-enhetens anslutnings sträng som parameter.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "[IoT Hub device connection string]"
```

Enhets programmet börjar skicka data till IoT Hub.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Använd Azure IoT Explorer för att verifiera koden

1. Öppna Azure IoT Explorer, du ser sidan **AppData** .

1. Ange din IoT Hub anslutnings sträng och klicka på **Anslut**.

1. När du har anslutit visas sidan enhets översikt.

1. Om du vill lägga till företags databasen väljer du **Inställningar**, sedan **+ ny**och sedan **på den anslutna enheten**.

1. På sidan enhets översikt letar du reda på enhets identiteten som du skapade tidigare och väljer den för att visa mer information.

1. Expandera gränssnittet med ID **urn: YOUR_COMPANY_NAME_HERE: EnvironmentalSensor: 1** om du vill se IoT plug and Play primitiver – egenskaper, kommandon och telemetri.

1. Välj sidan **telemetri** för att visa de telemetridata som enheten skickar.

1. Välj sidan **egenskaper (inte skrivbar)** om du vill visa de icke skrivbara egenskaper som rapporteras av enheten.

1. Välj sidan **egenskaper (skrivbar)** om du vill visa de skrivbara egenskaperna som du kan uppdatera.

1. Expandera egenskaps **namn**, uppdatera med ett nytt namn och välj **Uppdatera skrivbar egenskap**. 

1. Om du vill se det nya namnet visas i kolumnen **rapporterad egenskap** klickar du på knappen **Uppdatera** överst på sidan.

1. Välj **kommando** sidan för att visa alla kommandon som enheten stöder.

1. Expandera kommandot **blinkar** och ange ett nytt intervall för blinknings tid. Välj **kommandot Skicka** för att anropa kommandot på enheten.

1. Gå till den simulerade enheten för att kontrol lera att kommandot körs som förväntat.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du ansluter en IoT Plug and Play-enhet till en IoT-hubb. Mer information om hur du skapar en lösning som interagerar med din IoT Plug and Play-enheter finns i:

> [!div class="nextstepaction"]
> [Instruktioner: Ansluta till och interagera med en IoT Plug and Play förhands gransknings enhet](howto-develop-solution.md)
