---
title: Skapa och testa en IoT Plug and Play förhands gransknings enhet | Microsoft Docs
description: Som enhets utvecklare kan du läsa om hur du använder VS Code för att skapa och testa en ny enhets kapacitets modell för en IoT Plug and Play för hands versions enhet.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 720b3e56e1dd45bd2940b337adefa6ebdaa2e5a1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719729"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Självstudie: skapa och testa en enhets kapacitets modell med Visual Studio Code

Den här självstudien visar hur du, som enhets utvecklare, använder Visual Studio Code för att skapa en _enhets kapacitets modell_. Du kan använda modellen för att generera Skeleton-kod som ska köras på en enhet som ansluter till en Azure IoT Hub-instans i molnet.

I avsnittet i den här självstudien som beskriver hur du skapar den genererade Skeleton-koden förutsätter vi att du använder Windows.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en enhets kapacitets modell
> * Generera Skeleton enhets kod från modellen
> * Implementera stub-funktioner i den genererade koden
> * Kör koden för att testa interaktionerna med en IoT-hubb

## <a name="prerequisites"></a>Krav

Om du vill arbeta med enhets kapacitets modellen i den här självstudien behöver du:

* [Visual Studio Code](https://code.visualstudio.com/download): vs Code är tillgängligt för flera plattformar
* [Azure IoT Tools för vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack. Använd följande steg för att installera tilläggs paketet i VS Code:

    1. I VS Code väljer du fliken **tillägg** .
    1. Sök efter **Azure IoT-verktyg**.
    1. Välj **Installera**.

Om du vill skapa den genererade C-koden i Windows i den här självstudien behöver du:

* [Bygg verktyg för Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) med  **C++ build-verktyg** och **NuGet Package Manager-komponentens** arbets belastningar. Eller om du redan har [Visual Studio (community, Professional eller Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 eller 2015 med samma arbets belastningar installerade.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)

Om du vill testa enhets koden i den här självstudien behöver du:

* [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases).
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Modellera din enhet

Du använder det _digitala, dubbla definitions språket_ för att skapa en enhets kapacitets modell. En modell består vanligt vis av flera _gränssnitts_ definitions filer och en enda modell fil. **Azure IoT Tools för vs Code** innehåller verktyg som hjälper dig att skapa och redigera dessa JSON-filer.

### <a name="create-the-interface-file"></a>Skapa gränssnitts filen

Så här skapar du en gränssnitts fil som definierar funktionerna i din IoT-enhet i VS Code:

1. Skapa en mapp med namnet **devicemodel**.

1. Starta VS Code och Använd **Ctrl + Shift + P** för att öppna kommando paletten.

1. Ange **Plug and Play** och välj sedan kommandot **IoT plugg & Play: Create Interface** .

1. Bläddra till och välj mappen **devicemodel** som du skapade.

1. Ange sedan **EnvironmentalSensor** som namn på gränssnittet och tryck på **RETUR**. VS Code skapar en exempel gränssnitts fil med namnet **EnvironmentalSensor. Interface. JSON**.

1. Ersätt innehållet i den här filen med följande JSON och ersätt `{your name}` i fältet `@id` med ett unikt värde. Använd bara tecknen a-z, A-Z, 0-9 och under streck. Mer information finns i [digitalt format för dubbla identifierare](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format). Gränssnitts-ID: t måste vara unikt för att spara gränssnittet i databasen:

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

    Det här gränssnittet definierar enhets egenskaper som **kundens namn**, typer av telemetri, till exempel **temperatur**och kommandon som **TurnON**.

1. Lägg till en kommando funktion som kallas **Blink** i slutet av den här gränssnitts filen. Se till att lägga till ett kommatecken innan du lägger till kommandot. Försök att skriva in definitionen för att se hur IntelliSense, Autoavsluta och validering kan hjälpa dig att redigera en gränssnitts definition:

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

### <a name="create-the-model-file"></a>Skapa modell filen

Modell filen anger de gränssnitt som din IoT Plug and Play-enhet implementerar. Det finns vanligt vis minst två gränssnitt i en modell – en eller flera som definierar de olika funktionerna i enheten och ett standard gränssnitt som alla IoT Plug and Play-enheter måste implementera.

För att skapa en modell fil som anger de gränssnitt som din IoT Plug and Play-enhet implementerar i VS Code:

1. Använd **CTRL + SKIFT + P** för att öppna kommando paletten.

1. Ange **Plug and Play** och välj sedan kommandot **IoT plugg & Play: Create Capability Model** . Ange sedan **SensorboxModel** som namn på modellen. VS Code skapar en exempel gränssnitts fil med namnet **SensorboxModel. capabilitymodel. JSON**.

1. Ersätt innehållet i den här filen med följande JSON och ersätt `{your name}` i fältet `@id` och i `EnvironmentalSensor` gränssnittet med samma värde som du använde i filen **EnvironmentalSensor. Interface. JSON** . Gränssnitts-ID: t måste vara unikt för att spara gränssnittet i databasen:

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

    Modellen definierar en enhet som implementerar **EnvironmentalSensor** -gränssnittet och standard gränssnittet för **DeviceInformation** .

1. Spara filen.

### <a name="download-the-deviceinformation-interface"></a>Ladda ned DeviceInformation-gränssnittet

Innan du kan generera Skeleton-kod från modellen måste du skapa en lokal kopia av **DeviceInformation** från den *offentliga modellens lagrings plats*. Den offentliga modellens lagrings plats innehåller redan **DeviceInformation** -gränssnittet.

Hämta **DeviceInformation** -gränssnittet från den offentliga modellens lagrings plats med vs Code:

1. Använd **CTRL + SKIFT + P** för att öppna kommando paletten.

1. Ange **Plug and Play**, Välj kommandot **Öppna modell databas** och välj sedan **Öppna offentlig modell databas**.

1. Välj **gränssnitt**, välj sedan gränssnittet för enhets information med ID `urn:azureiot:DeviceManagement:DeviceInformation:1`och välj sedan **Ladda ned**.

Nu har du tre filer som utgör enhetens kapacitets modell:

* urn_azureiot_DeviceManagement_DeviceInformation_1. Interface. JSON
* EnvironmentalSensor. Interface. JSON
* SensorboxModel. capabilitymodel. JSON

## <a name="publish-the-model"></a>Publicera modellen

För att kunna läsa din enhets kapacitets modell i Azure IoT Explorer-verktyget måste du publicera den på din företags lagrings plats. Om du vill publicera från VS Code behöver du anslutnings strängen för företags databasen:

1. Navigera till [Azure-certifierad för IoT-portalen](https://aka.ms/ACFI).

1. Använd ditt Microsoft _Work-konto_ för att logga in på portalen.

1. Välj **företags databas** och sedan **anslutnings strängar**.

1. Kopiera anslutnings strängen.

Så här öppnar du företags databasen i VS Code:

1. Använd **CTRL + SKIFT + P** för att öppna kommando paletten.

1. Ange **Plug and Play** och välj **IoT-plugin-& spela upp: öppna modell databas** kommandot.

1. Välj **Öppna organisations modellens lagrings plats** och klistra in anslutnings strängen.

1. Öppna företags databasen genom att trycka på **RETUR** .

Publicera enhetens kapacitets modell och gränssnitt till företags databasen:

1. Använd **CTRL + SKIFT + P** för att öppna kommando paletten.

1. Ange **Plug and Play** och välj **IoT plugg & Play: skicka filer till modell lagrings** kommandot.

1. Välj filerna **EnvironmentalSensor. Interface. JSON** och **SensorboxModel. capabilitymodel. JSON** och välj **OK**.

Filerna lagras nu på din företags lagrings plats.

## <a name="generate-code"></a>Generera kod

Du kan använda **Azure IoT Tools för vs Code** för att skapa Skeleton C-kod från din modell. Så här genererar du Skeleton-koden i VS Code:

1. Använd **CTRL + SKIFT + P** för att öppna kommando paletten.

1. Ange **Plug and Play** och välj **IoT-plugin-& spela upp: generera enhets kod stub-** kommandot.

1. Välj din **SensorboxModel. capabilitymodel. JSON** -kapacitets modell fil.

1. Ange **sensorbox_app** som projekt namn.

1. Välj **ANSI C** som språk.

1. Välj **Via IoT Hub enhets anslutnings sträng** som ett sätt att ansluta.

1. Välj **cmake-projekt i Windows** som projekt mal len.

1. Välj **via Vcpkg** som ett sätt att inkludera enhets-SDK: n.

VS Code genererar Skeleton C-koden och sparar filerna i mappen **sensorbox_app** i mappen **modelcode** . VS Code öppnar ett nytt fönster som innehåller de genererade filerna.

## <a name="update-the-generated-code"></a>Uppdatera den genererade koden

Innan du kan skapa och köra koden måste du implementera stubbed-egenskaper, telemetri och kommandon.

För att tillhandahålla implementeringar för stubbed-koden i VS Code:

1. Öppna filen **SensorboxModel_impl. c** .

1. Lägg till kod för att implementera stubbed-funktionerna.

1. Spara ändringarna.

## <a name="build-the-code"></a>Skapa koden

Innan du kör koden för att testa din IoT Plug and Play-enhet med en Azure IoT-hubb måste du kompilera koden.

Följ anvisningarna i **Readme.MD** -filen i mappen **sensorbox_app** för att skapa och köra koden i Windows. Följande avsnitt innehåller instruktioner för att hämta en enhets anslutnings sträng som ska användas när du kör enhets koden.

## <a name="test-the-code"></a>Testa koden

När du kör koden ansluter den till IoT Hub och börjar skicka exempel telemetri och egenskaps värden. Enheten svarar också på kommandon som skickas från IoT Hub. Så här kontrollerar du problemet:

1. Så här skapar du en IoT-hubb:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Lägg till en enhet i IoT-hubben och hämta dess anslutnings sträng:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Anteckna anslutnings strängen.

1. Gå till mappen **Azure-IoT-SDK-c** i en kommando tolk där du skapade SDK och exempel. Gå sedan till mappen **cmake\\sensorbox_app\\release** .

1. Kör följande kommando:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Använd Azure IoT Explorer-verktyget för att interagera med IoT Plug and Play-enheten som är ansluten till din IoT-hubb. Mer information finns i [Installera och använda Azure IoT Explorer](./howto-install-iot-explorer.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en IoT-Plug and Play redo för certifiering, lär du dig att:

> [!div class="nextstepaction"]
> [Bygg en enhet som är redo för certifiering](tutorial-build-device-certification.md)
