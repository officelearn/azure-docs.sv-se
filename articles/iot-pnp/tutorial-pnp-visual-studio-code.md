---
title: Skapa och testa en IoT Plug and Play Preview-enhet | Microsoft-dokument
description: Som enhetsutvecklare kan du läsa om hur du använder VS-kod för att skapa och testa en ny enhetskapacitetsmodell för en IoT Plug and Play Preview-enhet.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 720b3e56e1dd45bd2940b337adefa6ebdaa2e5a1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76719729"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Självstudiekurs: Skapa och testa en enhetskapacitetsmodell med Visual Studio-kod

Den här självstudien visar hur du som enhetsutvecklare använder Visual Studio Code för att skapa en _enhetskapacitetsmodell_. Du kan använda modellen för att generera skelettkod för att köras på en enhet som ansluter till en Azure IoT Hub-instans i molnet.

Avsnittet i den här självstudien som beskriver hur du skapar den genererade skelettkoden förutsätter att du använder Windows.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en enhetskapacitetsmodell
> * Generera standardkod för skelettenheter från modellen
> * Implementera stubs i den genererade koden
> * Kör koden för att testa interaktionerna med en IoT-hubb

## <a name="prerequisites"></a>Krav

Om du vill arbeta med enhetskapacitetsmodellen i den här självstudien behöver du:

* [Visual Studio-kod:](https://code.visualstudio.com/download)VS-kod är tillgänglig för flera plattformar
* [Azure IoT Tools för VS-kodtilläggspaket.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Gör så här för att installera tilläggspaketet i VS-kod:

    1. Välj fliken **Tillägg** i VS-kod.
    1. Sök efter **Azure IoT Tools**.
    1. Välj **Installera**.

Om du vill skapa den genererade C-koden i Windows i den här självstudien behöver du:

* [Byggverktyg för Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) med **C++ byggverktyg** och **NuGet-komponentarbetsbelastningar för pakethanteraren.** Eller om du redan har [Visual Studio (Community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 eller 2015 med samma arbetsbelastningar installerade.
* [Git](https://git-scm.com/download)
* [Cmake](https://cmake.org/download/)

Om du vill testa enhetskoden i den här självstudien behöver du:

* [Azure IoT explorer](https://github.com/Azure/azure-iot-explorer/releases).
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Modellera enheten

Du använder det _digitala dubbla definitionsspråket_ för att skapa en enhetskapacitetsmodell. En modell består _interface_ vanligtvis av flera gränssnittsdefinitionsfiler och en enda modellfil. **Azure IoT Tools for VS Code** innehåller verktyg som hjälper dig att skapa och redigera dessa JSON-filer.

### <a name="create-the-interface-file"></a>Skapa gränssnittsfilen

Så här skapar du en gränssnittsfil som definierar funktionerna för din IoT-enhet i VS-kod:

1. Skapa en mapp som heter **devicemodel**.

1. Starta VS-kod och använd **Ctrl+Skift+P** för att öppna kommandopaletten.

1. Ange **Plug and Play** och välj sedan kommandot **IoT Plug & Play: Create Interface.**

1. Bläddra till och välj den **enhetsmodellmapp** som du skapade.

1. Ange sedan **EnvironmentalSensor** som namnet på gränssnittet och tryck på **Retur**. VS-kod skapar en exempelgränssnittsfil som heter **EnvironmentalSensor.interface.json**.

1. Ersätt innehållet i den här filen med `{your name}` följande `@id` JSON och ersätt i fältet med ett unikt värde. Använd bara tecknen a-z, A-Ö, 0-9 och understreck. Mer information finns i [Digital Twin-identifierarformat](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format). Gränssnitts-ID:n måste vara unikt för att kunna spara gränssnittet i databasen:

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Det här gränssnittet definierar enhetsegenskaper som **Kundnamn,** telemetrityper som **Temperatur**och kommandon som **turnon**.

1. Lägg till en kommandofunktion som kallas **blink i** slutet av den här gränssnittsfilen. Se till att lägga till ett kommatecken innan du lägger till kommandot. Prova att skriva in definitionen för att se hur intellisense, komplettera automatiskt och validering kan hjälpa dig att redigera en gränssnittsdefinition:

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. Spara filen.

### <a name="create-the-model-file"></a>Skapa modellfilen

Modellfilen anger de gränssnitt som IoT Plug and Play-enheten implementerar. Det finns vanligtvis minst två gränssnitt i en modell - ett eller flera som definierar enhetens specifika funktioner och ett standardgränssnitt som alla IoT Plug and Play-enheter måste implementera.

Så här skapar du en modellfil som anger de gränssnitt som IoT Plug and Play-enheten implementerar i VS-kod:

1. Öppna kommandopaletten genom att använda **Ctrl+Skift+P.**

1. Ange **Plug and Play** och välj sedan kommandot **IoT Plug & Play: Create Capability Model.** Ange sedan **SensorboxModel** som namnet på modellen. VS-kod skapar en exempelgränssnittsfil som heter **SensorboxModel.capabilitymodel.json**.

1. Ersätt innehållet i den här filen med `{your name}` följande `@id` JSON `EnvironmentalSensor` och ersätt i fältet och i gränssnittet med samma värde som du använde i **filen EnvironmentalSensor.interface.json.** Gränssnitts-ID:n måste vara unikt för att kunna spara gränssnittet i databasen:

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Modellen definierar en enhet som implementerar ditt **EnvironmentalSensor-gränssnitt** och det vanliga **DeviceInformation-gränssnittet.**

1. Spara filen.

### <a name="download-the-deviceinformation-interface"></a>Ladda ned DeviceInformation-gränssnittet

Innan du kan generera skelettkod från modellen måste du skapa en lokal kopia av **DeviceInformation** från den *offentliga modelldatabasen*. Den offentliga modelldatabasen innehåller redan **DeviceInformation-gränssnittet.**

Så här hämtar du **DeviceInformation-gränssnittet** från den offentliga modelldatabasen med VS-kod:

1. Öppna kommandopaletten genom att använda **Ctrl+Skift+P.**

1. Ange **Plug and Play**, välj kommandot Öppna **modelldatabas** och välj sedan Öppna offentlig **modelldatabas**.

1. Välj Gränssnitt och välj sedan enhetsinformationsgränssnittet med ID **Interfaces** `urn:azureiot:DeviceManagement:DeviceInformation:1`och välj sedan **Hämta**.

Du har nu de tre filer som utgör din enhetskapacitetsmodell:

* urn_azureiot_DeviceManagement_DeviceInformation_1.interface.json
* EnvironmentalSensor.interface.json
* SensorboxModel.capabilitymodel.json

## <a name="publish-the-model"></a>Publicera modellen

För att Azure IoT explorer-verktyget ska kunna läsa din enhetskapacitetsmodell måste du publicera den i företagets databas. Om du vill publicera från VS-kod behöver du anslutningssträngen för företagsdatabasen:

1. Navigera till [Azure Certified for IoT-portalen](https://aka.ms/ACFI).

1. Använd ditt _Microsoft-arbetskonto_ för att logga in på portalen.

1. Välj **Företagsdatabas** och sedan **Anslutningssträngar**.

1. Kopiera anslutningssträngen.

Så här öppnar du företagsarkivet i VS-kod:

1. Öppna kommandopaletten genom att använda **Ctrl+Skift+P.**

1. Ange **Plug and Play** och välj sedan kommandot **IoT Plug & Play: Open Model Repository.**

1. Välj **Öppna organisationsmodelldatabas och** klistra in i anslutningssträngen.

1. Öppna företagsdatabasen genom att trycka **på Retur.**

Så här publicerar du enhetens kapacitetsmodell och gränssnitt i företagsdatabasen:

1. Öppna kommandopaletten genom att använda **Ctrl+Skift+P.**

1. Ange **Plug and Play** och välj sedan kommandot **IoT Plug & Play: Skicka filer till** kommandot Model Repository.

1. Välj **filerna EnvironmentalSensor.interface.json** och **SensorboxModel.capabilitymodel.json** och välj **OK**.

Filerna lagras nu i företagets databas.

## <a name="generate-code"></a>Generera kod

Du kan använda **Azure IoT Tools for VS-kod för** att generera skelett C-kod från din modell. Så här genererar du skelettkoden i VS-kod:

1. Öppna kommandopaletten genom att använda **Ctrl+Skift+P.**

1. Ange **Plug and Play** och välj sedan kommandot **IoT Plug & Play: Generera kommandot Enhetskod.**

1. Välj modellfilen **för SensorboxModel.capabilitymodel.json-funktioner.**

1. Ange **sensorbox_app** som projektnamn.

1. Välj **ANSI C** som språk.

1. Välj **Anslutningssträngen för Via IoT Hub-enheten** som ett sätt att ansluta.

1. Välj **CMake Project i Windows** som projektmall.

1. Välj **Via Vcpkg** som sätt att inkludera enheten SDK.

VS-kod genererar skelett C-koden och sparar filerna i **mappen sensorbox_app** i **modellkodmappen.** VS-kod öppnar ett nytt fönster som innehåller de genererade kodfilerna.

## <a name="update-the-generated-code"></a>Uppdatera den genererade koden

Innan du kan skapa och köra koden måste du implementera de stubbade egenskaperna, telemetrin och kommandona.

Så här tillhandahåller du implementeringar för den stubbade koden i VS-kod:

1. Öppna **filen SensorboxModel_impl.c.**

1. Lägg till kod för att implementera de stubbade funktionerna.

1. Spara ändringarna.

## <a name="build-the-code"></a>Skapa koden

Innan du kör koden för att testa din IoT Plug and Play-enhet med en Azure IoT-hubb måste du kompilera koden.

Följ instruktionerna i **Readme.md** filen i **mappen sensorbox_app** för att skapa och köra koden i Windows. I följande avsnitt finns instruktioner för hur du hämtar en enhetsanslutningssträng som ska användas när du kör enhetskoden.

## <a name="test-the-code"></a>Testa koden

När du kör koden ansluter den till IoT Hub och börjar skicka exempeltelemetri- och egenskapsvärden. Enheten svarar också på kommandon som skickas från IoT Hub. Så här verifierar du detta:

1. Så här skapar du en IoT-hubb:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Lägg till en enhet i IoT-hubben och hämta anslutningssträngen:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Anteckna anslutningssträngen.

1. I en kommandotolk navigerar du till mappen **azure-iot-sdk-c** där du har skapat SDK och exempel. Navigera sedan till **mappen\\cmake\\sensorbox_app Release.**

1. Kör följande kommando:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Använd Azure IoT explorer-verktyget för att interagera med IoT Plug and Play-enheten som är ansluten till din IoT-hubb. Mer information finns i [Installera och använda Azure IoT explorer](./howto-install-iot-explorer.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har byggt en IoT Plug and Play redo för certifiering kan du läsa om hur du:

> [!div class="nextstepaction"]
> [Skapa en enhet som är redo för certifiering](tutorial-build-device-certification.md)
