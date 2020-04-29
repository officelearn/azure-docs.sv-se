---
title: Enhets simulering med IoT-fjärrövervakning – Azure | Microsoft Docs
description: Den här instruktions guiden visar hur du använder enhets simulatorn med lösnings acceleratorn för fjärrövervakning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: mqtt
ms.openlocfilehash: d31e520dac1c7e2a13fbd9e24a0cd3167f69e904
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682010"
---
# <a name="create-and-test-a-new-simulated-device"></a>Skapa och testa en ny simulerad enhet

Med hjälp av lösningen för fjärr styrning kan du definiera dina egna simulerade enheter. Den här artikeln visar hur du definierar en ny simulerad glöd lampan-enhet och sedan testar den lokalt. Solution Accelerator innehåller simulerade enheter som kyl-och Last bilar. Du kan dock definiera egna simulerade enheter för att testa dina IoT-lösningar innan du distribuerar riktiga enheter.

> [!NOTE]
> Den här artikeln beskriver hur du använder simulerade enheter som finns i enhets simulerings tjänsten. Om du vill skapa en riktig enhet kan du läsa [Anslut din enhet till-acceleratorn för fjärr styrning](iot-accelerators-connecting-devices.md).

Den här instruktions guiden visar hur du anpassar enhets simuleringens mikrotjänst. Den här mikrotjänsten är en del av lösnings acceleratorn för fjärrövervakning. För att visa funktioner för enhets simulering använder den här instruktions guiden två scenarier i Contoso IoT-program:

I det första scenariot lägger du till en ny typ av telemetri till Contosos befintliga **kyl** enhets typ.

I det andra scenariot vill contoso testa en ny smart glöd lampan-enhet. För att köra testerna skapar du en ny simulerad enhet med följande egenskaper:

*Egenskaper*

| Name                     | Värden                      |
| ------------------------ | --------------------------- |
| Färg                    | Vit, röd, blå            |
| Ljusstyrka               | 0 till 100                    |
| Uppskattad återstående livs längd | Nedräkning från 10 000 timmar |

*Telemetri*

I följande tabell visas de data som glöd lampan rapporterar till molnet som en data ström:

| Name   | Värden      |
| ------ | ----------- |
| Status | "på", "av" |
| Temperatur | Grader F |
| online | SANT, FALSKT |

> [!NOTE]
> Värdet **online** för telemetri är obligatoriskt för alla simulerade typer.

*Metoder*

Följande tabell visar de åtgärder som den nya enheten stöder:

| Name        |
| ----------- |
| Växla på   |
| Stäng av  |

*Initialt tillstånd*

I följande tabell visas enhetens initiala status:

| Name                     | Värden |
| ------------------------ | -------|
| Ursprunglig färg            | Vit  |
| Inledande ljus styrka       | 75     |
| Ursprunglig återstående livs längd   | 10 000 |
| Status för inledande telemetri | för   |
| Inledande telemetri temperatur | 200   |

För att slutföra stegen i den här instruktions guiden behöver du en aktiv Azure-prenumeration.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav

Om du vill följa den här instruktions guiden behöver du:

* Visual Studio Code. Du kan [Hämta Visual Studio Code för Mac, Linux och Windows](https://code.visualstudio.com/download).
* .NET Core. Du kan ladda ned [.net Core för Mac, Linux och Windows](https://www.microsoft.com/net/download).
* [C# för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* Postman. Du kan hämta [Postman för Mac, Windows eller Linux](https://www.getpostman.com/apps).
* En [IoT-hubb som distribueras till din Azure-prenumeration](../../articles/iot-hub/iot-hub-create-through-portal.md). Du behöver IoT-hubbens anslutnings sträng för att slutföra stegen i den här hand boken. Du kan hämta anslutnings strängen från Azure Portal.
* En Cosmos DB databas som använder SQL API och som har kon figurer ATS för [stark konsekvens](../../articles/cosmos-db/how-to-manage-database-account.md). Du behöver Cosmos DB databasens anslutnings sträng för att slutföra stegen i den här hand boken. Du kan hämta anslutnings strängen från Azure Portal.

## <a name="prepare-your-development-environment"></a>Förbereda utvecklingsmiljön

Utför följande uppgifter för att förbereda utvecklings miljön:

* Hämta källan för mikrotjänsten Device simulering.
* Ladda ned källan för Storage adapter-mikrotjänsten.
* Kör Storage adapter-mikrotjänsten lokalt.

I instruktionerna i den här artikeln förutsätter vi att du använder Windows. Om du använder ett annat operativ system kan du behöva justera några av fil Sök vägarna och kommandona för att passa din miljö.

### <a name="download-the-microservices"></a>Ladda ned mikrotjänster

Ladda ned och zippa upp [mikrotjänsterna för fjärrövervakning](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) från GitHub till en lämplig plats på den lokala datorn. Artikeln förutsätter att namnet på den här mappen är **fjärrövervakning-tjänster-dotNet-Master**.

Ladda ned och zippa upp [mikrotjänsten för enhets simulering](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) från GitHub till en lämplig plats på den lokala datorn. Artikeln förutsätter att namnet på den här mappen är **Device-simulering-dotNet-Master**.

### <a name="run-the-storage-adapter-microservice"></a>Köra mikrotjänsten Storage adapter

Öppna mappen **Remote-Monitoring-Services-dotNet-master\storage-adapter** i Visual Studio Code. Klicka på alla **återställnings** knappar för att åtgärda eventuella olösta beroenden.

Öppna filen **Storage-adapter/WebService/appSettings. ini** och tilldela Cosmos DB anslutnings strängen till **documentDBConnectionString** -variabeln.

Kör mikrotjänsten lokalt genom att klicka på **felsök > starta fel sökning**.

**Terminalfönstret** i Visual Studio Code visar utdata från den aktiva mikrotjänsten inklusive en URL för webb tjänstens hälso kontroll: [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status). När du navigerar till den här adressen ska statusen vara "OK: Alive".

Lämna den mikrotjänsten Storage adapter som körs i den här instansen av Visual Studio Code medan du slutför nästa steg.

## <a name="modify-the-chiller"></a>Ändra kylning

I det här avsnittet lägger du till en ny typ av telemetri för **intern temperatur** till den befintliga typen av **kylnings** enhet:

1. Skapa en ny mapp **C:\temp\devicemodels** på din lokala dator.

1. Kopiera följande filer till den nya mappen från den hämtade kopian av mikrotjänsten Device simulering:

    | Källa | Mål |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Öppna filen **C:\temp\devicemodels\chiller-01.JSON** .

1. I avsnittet **InitialState** lägger du till följande två definitioner:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Lägg till följande definition i **telemetri** -matrisen:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Spara filen **C:\temp\devicemodels\chiller-01.JSON** .

1. Öppna filen **C:\temp\devicemodels\scripts\chiller-01-State.js** .

1. Lägg till följande fält i **delstaten** -variabeln:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Uppdatera **huvud** funktionen enligt följande:

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global state before generating the new telemetry, so that
        // the telemetry can apply changes using the previous function state.
        restoreSimulation(previousState, previousProperties);

        // 75F +/- 5%,  Min 25F, Max 100F
        state.temperature = vary(75, 5, 25, 100);

        // 70% +/- 5%,  Min 2%, Max 99%
        state.humidity = vary(70, 5, 2, 99);

        // 65F +/- 2%,  Min 15F, Max 125F
        state.internal_temperature = vary(65, 2, 15, 125);

        log("Simulation state: " + state.simulation_state);
        if (state.simulation_state === "high_pressure") {
            // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
            state.pressure = vary(250, 25, 50, 300);
        } else {
            // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
            state.pressure = vary(150, 10, 50, 300);
        }

        updateState(state);
        return state;
    }
    ```

1. Spara filen **C:\temp\devicemodels\scripts\chiller-01-State.js** .

## <a name="create-the-lightbulb"></a>Skapa glöd lampan

I det här avsnittet definierar du en ny **glöd lampan** enhets typ:

1. Skapa en fil **C:\temp\devicemodels\lightbulb-01.JSON** och Lägg till följande innehåll:

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "lightbulb-01",
      "Version": "0.0.1",
      "Name": "Lightbulb",
      "Description": "Smart lightbulb device.",
      "Protocol": "MQTT",
      "Simulation": {
        "InitialState": {
          "online": true,
          "temperature": 200.0,
          "temperature_unit": "F",
          "status": "on"
        },
        "Interval": "00:00:20",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "lightbulb-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "Lightbulb",
        "Color": "White",
        "Brightness": 75,
        "EstimatedRemainingLife": 10000
      },
      "Tags": {
        "Location": "Building 2",
        "Floor": "2",
        "Campus": "Redmond"
      },
      "Telemetry": [
        {
          "Interval": "00:00:20",
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
          "MessageSchema": {
            "Name": "lightbulb-status;v1",
            "Format": "JSON",
            "Fields": {
              "temperature": "double",
              "temperature_unit": "text",
              "status": "text"
            }
          }
        }
      ],
      "CloudToDeviceMethods": {
        "SwitchOn": {
          "Type": "javascript",
          "Path": "SwitchOn-method.js"
        },
        "SwitchOff": {
          "Type": "javascript",
          "Path": "SwitchOff-method.js"
        }
      }
    }
    ```

    Spara ändringarna i **C:\temp\devicemodels\lightbulb-01.JSON**.

1. Skapa en fil **C:\temp\devicemodels\scripts\lightbulb-01-State.js** och Lägg till följande innehåll:

    ```javascript
    "use strict";

    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };

    // Default device properties
    var properties = {};

    /**
     * Restore the global state using data from the previous iteration.
     *
     * @param previousState device state from the previous iteration
     * @param previousProperties device properties from the previous iteration
     */
    function restoreSimulation(previousState, previousProperties) {
      // If the previous state is null, force a default state
      if (previousState) {
        state = previousState;
      } else {
        log("Using default state");
      }

      if (previousProperties) {
        properties = previousProperties;
      } else {
        log("Using default properties");
      }
    }

    /**
     * Simple formula generating a random value around the average
     * in between min and max
     *
     * @returns random value with given parameters
     */
    function vary(avg, percentage, min, max) {
      var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
      value = Math.max(value, min);
      value = Math.min(value, max);
      return value;
    }

    /**
     * Simple formula that sometimes flips the status of the lightbulb
     */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }

    /**
     * Entry point function called by the simulation engine.
     * Returns updated simulation state.
     * Device property updates must call updateProperties() to persist.
     *
     * @param context             The context contains current time, device model and id
     * @param previousState       The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState, previousProperties) {

      // Restore the global device properties and the global state before
      // generating the new telemetry, so that the telemetry can apply changes
      // using the previous function state.
      restoreSimulation(previousState, previousProperties);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      updateState(state);

      return state;
    }
    ```

    Spara ändringarna i **C:\temp\devicemodels\scripts\lightbulb-01-State.js**.

1. Skapa en fil **C:\temp\devicemodels\scripts\SwitchOn-Method.js** och Lägg till följande innehåll:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch On method.");
      state.status = "on";
      updateState(state);
    }
    ```

    Spara ändringarna i **C:\temp\devicemodels\scripts\SwitchOn-Method.js**.

1. Skapa en fil **C:\temp\devicemodels\scripts\SwitchOff-Method.js** och Lägg till följande innehåll:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch Off method.");
      state.status = "off";
      updateState(state);
    }
    ```

    Spara ändringarna i **C:\temp\devicemodels\scripts\SwitchOff-Method.js**.

Nu har du skapat en anpassad version av **kyl** enhets typen och skapat en ny **glöd lampan** -enhets typ.

## <a name="test-the-devices"></a>Testa enheterna

I det här avsnittet testar du de enhets typer som du skapade i föregående avsnitt lokalt.

### <a name="run-the-device-simulation-microservice"></a>Kör mikrotjänsten enhets simulering

Öppna mappen **Device-simulering-dotNet-Master** som du laddade ned från GitHub i en ny instans av Visual Studio Code. Klicka på alla **återställnings** knappar för att åtgärda eventuella olösta beroenden.

Öppna filen **WebService/appSettings. ini** och tilldela Cosmos DB anslutnings sträng till **documentdb_connstring** -variabeln och ändra inställningarna enligt följande:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Kör mikrotjänsten lokalt genom att klicka på **felsök > starta fel sökning**.

**Terminalfönstret** i Visual Studio Code visar utdata från den aktiva mikrotjänsten.

Lämna mikrotjänsten för enhets simulering som körs i den här instansen av Visual Studio Code medan du slutför nästa steg.

### <a name="set-up-a-monitor-for-device-events"></a>Konfigurera en Övervakare för enhets händelser

I det här avsnittet använder du Azure CLI för att konfigurera en händelse Övervakare för att Visa telemetri som skickas från enheter som är anslutna till din IoT-hubb.

Följande skript förutsätter att namnet på din IoT Hub är **Device-simulering-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Lämna händelse övervakaren igång medan du testar de simulerade enheterna.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Skapa en simulering med den uppdaterade typen av kylnings enhet

I det här avsnittet ska du använda Postman-verktyget för att begära mikrotjänsten för enhets simulering för att köra en simulering med hjälp av den uppdaterade typen av kylnings enhet. Postman är ett verktyg som du kan använda för att skicka REST-begäranden till en webb tjänst. De Postman-konfigurationsfiler du behöver finns i din lokala kopia av **enhets simulering-dotNet-** lagringsplatsen.

Så här konfigurerar du Postman:

1. Öppna Postman på den lokala datorn.

1. Klicka på **fil > importera**. Klicka sedan på **Välj filer**.

1. Navigera till mappen **Device-simulering-dotNet-Master/dokument/Postman** . Välj **Azure IoT Device simulering Solution Accelerator. postman_collection** och **Azure IoT Device simulering solution Accelerator. postman_environment** och klicka på **Öppna**.

1. Expandera **Solution Accelerator för Azure IoT Device simulering** till de begär Anden som du kan skicka.

1. Klicka på **ingen miljö** och välj **Azure IoT Device simulering Solution Accelerator**.

Nu har du en samling och en miljö som har lästs in i Postman-arbetsytan som du kan använda för att interagera med mikrotjänsten Device simulering.

Konfigurera och köra simuleringen:

1. I Postman-samlingen väljer du **skapa ändrad kylnings simulering** och klickar på **Skicka**. Den här begäran skapar fyra instanser av den simulerade kyl enhets typen.

1. Händelse övervakarens utdata i fönstret Azure CLI visar Telemetrin från de simulerade enheterna, inklusive de nya **internal_temperature** värdena.

Stoppa simuleringen genom att välja begäran **stoppa simulering** i Postman och klicka på **Skicka**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Skapa en simulering med enhets typen glöd lampan

I det här avsnittet ska du använda Postman-verktyget för att begära mikrotjänsten för enhets simulering för att köra en simulering med enhets typen glöd lampan. Postman är ett verktyg som du kan använda för att skicka REST-begäranden till en webb tjänst.

Konfigurera och köra simuleringen:

1. I Postman-samlingen väljer du **skapa glöd lampan-simulering** och klickar på **Skicka**. Den här begäran skapar två instanser av enhets typen simulerad glöd lampan.

1. Händelse övervakarens utdata i fönstret Azure CLI visar Telemetrin från den simulerade lightbulbs.

Stoppa simuleringen genom att välja begäran **stoppa simulering** i Postman och klicka på **Skicka**.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan stoppa de två lokalt använda mikrotjänster i sina Visual Studio Code-instanser (**felsök > stoppa fel sökning**).

Om du inte längre behöver IoT Hub och Cosmos DB instanser tar du bort dem från din Azure-prenumeration för att undvika onödiga kostnader.

## <a name="next-steps"></a>Nästa steg

Den här guiden visar hur du skapar en anpassad simulerad enhets typ och testar dem genom att köra mikrotjänsten Device simulering lokalt.

Det föreslagna nästa steg är att lära dig hur du distribuerar dina anpassade simulerade enhets typer till en [lösnings Accelerator för fjärr styrning](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
