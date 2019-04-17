---
title: ta med fil
description: ta med fil
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 09/28/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: cda08d44cba9e59af853b1705f538ec199ec4d3a
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59630576"
---
I det första scenariot lägger du till en ny typ av telemetri till Contoso befintliga **kylaggregat** enhetstyp.

I det andra scenariot vill Contoso testa en ny smart glödlampan-enhet. Om du vill köra testerna skapar du en ny simulerad enhet med följande egenskaper:

*Egenskaper*

| Namn                     | Värden                      |
| ------------------------ | --------------------------- |
| Färg                    | White, Red, Blue            |
| Ljusstyrka               | 0 till 100                    |
| Beräknad återstående livslängd | Nedräkning från 10 000 timmar |

*Telemetri*

I följande tabell visas data på glödlampan rapporterar till molnet som en dataström:

| Namn   | Värden      |
| ------ | ----------- |
| Status | "on", "off" |
| Temperatur | Grader F |
| online | SANT, FALSKT |

> [!NOTE]
> Den **online** telemetrivärde är obligatoriskt för alla simulerade typer.

*Metoder*

I följande tabell visas de åtgärder som har stöd för den nya enheten:

| Namn        |
| ----------- |
| Aktivera   |
| Stänga av  |

*Starttillstånd*

I följande tabell visas den första statusen för enheten:

| Namn                     | Värden |
| ------------------------ | -------|
| Inledande färg            | Vit  |
| Inledande ljusstyrka       | 75     |
| Inledande återstående livslängd   | 10 000 |
| Inledande telemetri status | ”on”   |
| Inledande telemetri temperatur | 200   |

För att slutföra stegen i den här guiden behöver du en aktiv Azure-prenumeration.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill följa den här guiden behöver du:

* Visual Studio Code. Du kan [ladda ned Visual Studio Code för Mac, Linux och Windows](https://code.visualstudio.com/download).
* .NET Core. Du kan ladda ned [.NET Core för Mac, Linux och Windows](https://www.microsoft.com/net/download).
* [C# för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* Postman. Du kan ladda ned [Postman för Mac, Windows eller Linux](https://www.getpostman.com/apps).
* En [IoT hub som är distribuerad på Azure-prenumerationen](../articles/iot-hub/iot-hub-create-through-portal.md). Du behöver anslutningssträngen för IoT-hubben att slutföra stegen i den här guiden. Du kan hämta anslutningssträngen från Azure-portalen.
* En Cosmos DB-databas som använder SQL-API och som är konfigurerad för [stark konsekvens](../articles/cosmos-db/manage-account.md). Du behöver anslutningssträngen för Cosmos DB-databasen för att slutföra stegen i den här guiden. Du kan hämta anslutningssträngen från Azure-portalen.

## <a name="prepare-your-development-environment"></a>Förbereda utvecklingsmiljön

Utför följande uppgifter för att förbereda utvecklingsmiljön:

* Hämta källan för enheten simulering mikrotjänst.
* Hämta källan för storage nätverkskort mikrotjänst.
* Köra storage nätverkskort mikrotjänster lokalt.

Anvisningarna i den här artikeln förutsätter att du använder Windows. Om du använder ett annat operativsystem kan du behöva justera några av de sökvägar och kommandon för att de passar din miljö.

### <a name="download-the-microservices"></a>Ladda ned mikrotjänster

Hämta och packa upp den [mikrotjänster för fjärrövervakning](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) från GitHub till en lämplig plats på den lokala datorn. Artikeln förutsätter vi att namnet på den här mappen är **remote-monitoring-services-dotnet-master**.

Hämta och packa upp den [enheten simulering mikrotjänst](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) från GitHub till en lämplig plats på den lokala datorn. Artikeln förutsätter vi att namnet på den här mappen är **enhet-simulering-dotnet-master**.

### <a name="run-the-storage-adapter-microservice"></a>Kör storage nätverkskort mikrotjänst

Öppna den **remote-monitoring-services-dotnet-master\storage-adapter** mappen i Visual Studio Code. Klicka på någon **återställa** knappar för att åtgärda eventuella olöst beroenden.

Öppna den **storage-adapter/WebService/appsettings.ini** fil och tilldela din Cosmos DB-anslutningssträngen till den **documentDBConnectionString** variabeln.

Om du vill köra mikrotjänst lokalt, klickar du på **Felsök > Starta felsökning**.

Den **Terminal** fönstret i Visual Studio Code visas utdata från körs mikrotjänst, inklusive en URL för web service-hälsokontroll: [ http://127.0.0.1:9022/v1/status ](http://127.0.0.1:9022/v1/status). När du navigerar till den här adressen statusen ska vara ”OK: Alive och väl ”.

Lämna storage adapter-mikrotjänster som körs i den här instansen av Visual Studio Code när du slutför nästa steg.

## <a name="modify-the-chiller"></a>Ändra kylaggregat

I det här avsnittet ska du lägga till en ny **intern temperatur** typ av telemetri i den befintliga **kylaggregat** enhetstyp:

1. Skapa en ny mapp **C:\temp\devicemodels** på den lokala datorn.

1. Kopiera följande filer till den nya mappen från hämtade kopian av enheten simulering mikrotjänst:

    | Källa | Mål |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-Method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-Method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Öppna den **C:\temp\devicemodels\chiller-01.json** fil.

1. I den **InitialState** lägger du till följande två definitioner:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. I den **telemetri** kan lägga till följande definition:

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

1. Spara den **C:\temp\devicemodels\chiller-01.json** fil.

1. Öppna den **C:\temp\devicemodels\scripts\chiller-01-state.js** fil.

1. Lägg till följande fält i den **tillstånd** variabeln:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Uppdatera den **huvudsakliga** fungerar på följande sätt:

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

1. Spara den **C:\temp\devicemodels\scripts\chiller-01-state.js** fil.

## <a name="create-the-lightbulb"></a>Skapa glödlampan

I det här avsnittet definierar du en ny **glödlampan** enhetstyp:

1. Skapa en fil **C:\temp\devicemodels\lightbulb-01.json** och Lägg till följande innehåll:

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

    Spara ändringarna i **C:\temp\devicemodels\lightbulb-01.json**.

1. Skapa en fil **C:\temp\devicemodels\scripts\lightbulb-01-state.js** och Lägg till följande innehåll:

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

    Spara ändringarna i **C:\temp\devicemodels\scripts\lightbulb-01-state.js**.

1. Skapa en fil **C:\temp\devicemodels\scripts\SwitchOn-method.js** och Lägg till följande innehåll:

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

    Spara ändringarna i **C:\temp\devicemodels\scripts\SwitchOn-method.js**.

1. Skapa en fil **C:\temp\devicemodels\scripts\SwitchOff-method.js** och Lägg till följande innehåll:

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

    Spara ändringarna i **C:\temp\devicemodels\scripts\SwitchOff-method.js**.

Nu har du skapat en anpassad version av den **kylaggregat** enhetstyp och skapat en ny **glödlampan** enhetstyp.

## <a name="test-the-devices"></a>Testa enheterna

I det här avsnittet ska testa du de enhetstyper som du skapade i föregående avsnitt lokalt.

### <a name="run-the-device-simulation-microservice"></a>Kör enheten simulering mikrotjänst

Öppna den **enhet-simulering-dotnet-master** mapp som du laddade ned från GitHub i en ny instans av Visual Studio Code. Klicka på någon **återställa** knappar för att åtgärda eventuella olöst beroenden.

Öppna den **WebService/appsettings.ini** fil och tilldela din Cosmos DB-anslutningssträngen till den **documentdb_connstring** variabeln och även ändra följande inställningar:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Om du vill köra mikrotjänst lokalt, klickar du på **Felsök > Starta felsökning**.

Den **Terminal** utdata från körs mikrotjänst visas i fönstret i Visual Studio Code.

Lämna enheten simulering mikrotjänster som körs i den här instansen av Visual Studio Code när du slutför nästa steg.

### <a name="set-up-a-monitor-for-device-events"></a>Konfigurera en Övervakare för enhetshändelser

I det här avsnittet använder du Azure CLI för att ställa in en Händelseövervakare visar den telemetri som skickas från enheter som är anslutna till din IoT hub.

Skriptet nedan förutsätter att namnet på din IoT-hubb **enhet-simulering-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Lämna Händelseövervakare som körs medan du testa de simulerade enheterna.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Skapa en simulering med den uppdaterade kylaggregat typ av enhet

I det här avsnittet ska använda du verktyget Postman för att begära enheten simulering mikrotjänst att köra en simulering med uppdaterade kylaggregat enhetstyp. Postman är ett verktyg som låter dig skicka REST-begäranden till en webbtjänst. Postman configuration-filer som du behöver finns i den lokala kopian av den **enhet-simulering-dotnet** lagringsplats.

Konfigurera Postman:

1. Öppna Postman på den lokala datorn.

1. Klicka på **fil > Import**. Klicka sedan på **Välj filer**.

1. Navigera till den **enhet-simulering-dotnet-master/docs/postman** mapp. Välj **Enhetssimulering för Azure IoT-lösning accelerator.postman_collection** och **Enhetssimulering för Azure IoT-lösning accelerator.postman_environment** och klicka på **öppna**.

1. Expandera den **Azure IoT-Enhetssimulering lösningsaccelerator** begäranden som du kan skicka.

1. Klicka på **nr miljö** och välj **Azure IoT-Enhetssimulering lösningsaccelerator**.

Nu har du en samling och läses in i din Postman-arbetsyta som du kan använda för att interagera med enheten simulering mikrotjänst-miljö.

Konfigurera och köra simuleringen:

1. Välj i Postman-samlingen **skapa ändras kylaggregat simulering** och klicka på **skicka**. Den här begäran skapar fyra instanser av den simulerade kylaggregat enhetstypen.

1. Händelsen övervaka utdata i Azure CLI-fönstret visas telemetri från simulerade enheter, inklusive den nya **internal_temperature** värden.

Om du vill stoppa simuleringen, Välj den **stoppa simulering** begäran i Postman och klicka på **skicka**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Skapa en simulering med glödlampan enhetstyp

I det här avsnittet ska använda du verktyget Postman för att begära enheten simulering mikrotjänst att köra en simulering med glödlampan enhetstyp. Postman är ett verktyg som låter dig skicka REST-begäranden till en webbtjänst.

Konfigurera och köra simuleringen:

1. Välj i Postman-samlingen **glödlampan simulera** och klicka på **skicka**. Den här begäran skapar två instanser av den simulerade glödlampa enhetstypen.

1. Händelsen övervaka utdata i Azure CLI-fönstret visas telemetri från simulerade lightbulbs.

Om du vill stoppa simuleringen, Välj den **stoppa simulering** begäran i Postman och klicka på **skicka**.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan stoppa två som körs lokalt mikrotjänster i sina Visual Studio Code-instanser (**Felsök > Stoppa felsökning**).

Om du behöver inte längre IoT Hub och Cosmos DB-instanser kan du ta bort dem från Azure-prenumerationen för att undvika onödiga kostnader.