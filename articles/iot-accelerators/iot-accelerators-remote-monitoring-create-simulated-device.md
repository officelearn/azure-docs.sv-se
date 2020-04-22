---
title: Enhetssimulering med IoT-fjärrövervakning – Azure | Microsoft-dokument
description: Den här programguiden visar hur du använder enhetssimulatorn med snabblösningsacceleratorn för fjärrövervakning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: mqtt
ms.openlocfilehash: d31e520dac1c7e2a13fbd9e24a0cd3167f69e904
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682010"
---
# <a name="create-and-test-a-new-simulated-device"></a>Skapa och testa en ny simulerad enhet

Med lösningsacceleratorn för fjärrövervakning kan du definiera dina egna simulerade enheter. Den här artikeln visar hur du definierar en ny simulerad glödlampa enhet och sedan testa den lokalt. Lösningsacceleratorn innehåller simulerade enheter som kylmaskiner och lastbilar. Du kan dock definiera dina egna simulerade enheter för att testa dina IoT-lösningar innan du distribuerar riktiga enheter.

> [!NOTE]
> I den här artikeln beskrivs hur du använder simulerade enheter som finns i enhetssimuleringstjänsten. Om du vill skapa en riktig enhet läser du [Anslut enheten till lösningsacceleratorn för fjärrövervakning](iot-accelerators-connecting-devices.md).

Den här guiden visar hur du anpassar mikrotjänsten för enhetssimulering. Den här mikrotjänsten är en del av lösningsacceleratorn för fjärrövervakning. Om du vill visa enhetssimuleringsfunktionerna använder den här programguiden två scenarier i Contoso IoT-programmet:

I det första scenariot lägger du till en ny **Chiller** telemetrityp i Contosos befintliga chiller-enhetstyp.

I det andra scenariot vill Contoso testa en ny smart glödlampa enhet. Om du vill köra testerna skapar du en ny simulerad enhet med följande egenskaper:

*Egenskaper*

| Namn                     | Värden                      |
| ------------------------ | --------------------------- |
| Color                    | Vit, Röd, Blå            |
| Ljusstyrka               | 0 till 100                    |
| Beräknad återstående livslängd | Nedräkning från 10 000 timmar |

*Telemetri*

I följande tabell visas de data som glödlampan rapporterar till molnet som en dataström:

| Namn   | Värden      |
| ------ | ----------- |
| Status | "på", "av" |
| Temperatur | Grader F |
| online | SANT, FALSKT |

> [!NOTE]
> Värdet **för onlinetelemetri** är obligatoriskt för alla simulerade typer.

*Metoder*

I följande tabell visas de åtgärder som den nya enheten stöder:

| Namn        |
| ----------- |
| Slå på   |
| Stänga  |

*Ursprungligt tillstånd*

I följande tabell visas enhetens ursprungliga status:

| Namn                     | Värden |
| ------------------------ | -------|
| Inledande färg            | Vit  |
| Initial ljusstyrka       | 75     |
| Inledande återstående livslängd   | 10 000 |
| Ursprunglig telemetristatus | "på"   |
| Initial telemetritemperatur | 200   |

För att kunna slutföra stegen i den här programguiden behöver du en aktiv Azure-prenumeration.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Krav

För att följa den här guiden behöver du:

* Visual Studio Code. Du kan [ladda ner Visual Studio Code för Mac, Linux och Windows](https://code.visualstudio.com/download).
* .NET-kärna. Du kan hämta [.NET Core för Mac, Linux och Windows](https://www.microsoft.com/net/download).
* [C# för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* Brevbäraren. Du kan ladda ner [Postman för Mac, Windows eller Linux](https://www.getpostman.com/apps).
* En [IoT-hubb som distribueras till din Azure-prenumeration](../../articles/iot-hub/iot-hub-create-through-portal.md). Du behöver IoT-hubbens anslutningssträng för att kunna slutföra stegen i den här guiden. Du kan hämta anslutningssträngen från Azure-portalen.
* En Cosmos DB-databas som använder SQL API och som är konfigurerad för [stark konsekvens](../../articles/cosmos-db/how-to-manage-database-account.md). Du behöver Cosmos DB-databasens anslutningssträng för att slutföra stegen i den här guiden. Du kan hämta anslutningssträngen från Azure-portalen.

## <a name="prepare-your-development-environment"></a>Förbereda utvecklingsmiljön

Slutför följande uppgifter för att förbereda utvecklingsmiljön:

* Hämta källan för mikrotjänsten för enhetssimulering.
* Hämta källan för lagringskortets mikrotjänst.
* Kör mikrotjänsten för lagringsadapter lokalt.

Instruktionerna i den här artikeln förutsätter att du använder Windows. Om du använder ett annat operativsystem kan du behöva justera några av filsökvägarna och kommandona så att de passar din miljö.

### <a name="download-the-microservices"></a>Ladda ner mikrotjänsterna

Ladda ned och packa upp [fjärrövervakningsmikrotjänsterna](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) från GitHub till en lämplig plats på din lokala dator. Artikeln förutsätter att namnet på den här mappen är **fjärrövervakning-tjänster-dotnet-master**.

Ladda ned och packa upp [enhetssimuleringsmikrotjänsten](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) från GitHub till en lämplig plats på din lokala dator. Artikeln förutsätter att namnet på den här mappen är **device-simulation-dotnet-master**.

### <a name="run-the-storage-adapter-microservice"></a>Kör mikrotjänsten för lagringsadapter

Öppna mappen **remote-monitoring-services-dotnet-master\storage-adapter** i Visual Studio Code. Klicka på **valfria återställningsknappar** om du vill åtgärda eventuella olösta beroenden.

Öppna **filen storage-adapter/WebService/appsettings.ini** och tilldela cosmos DB-anslutningssträngen till **variabeln documentDBConnectionString.**

Om du vill köra mikrotjänsten lokalt klickar du på **Felsök > Starta felsökning**.

**Terminalfönstret** i Visual Studio-koden visar utdata från den mikrotjänst som [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status)körs, inklusive en URL för hälsokontrollen av webbtjänsten: . När du navigerar till den här adressen ska statusen vara "OK: Alive and well".

Låt lagringskortets mikrotjänst köras i den här instansen av Visual Studio-kod medan du slutför nästa steg.

## <a name="modify-the-chiller"></a>Ändra kylaggregatet

I det här avsnittet lägger du till en ny telemetrityp **för intern temperatur** i den befintliga **kylarenhetstypen:**

1. Skapa en ny mapp **C:\temp\devicemodels** på den lokala datorn.

1. Kopiera följande filer till den nya mappen från den nedladdade kopian av mikrotjänsten för enhetssimulering:

    | Källa | Mål |
    | ------ | ----------- |
    | Tjänster\data\enhetsmodeller\kylaggregat-01.json | C:\temp\devicemodels\chiller-01.json |
    | Tjänster\data\enhetsmodeller\skript\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Tjänster\data\enhetsmodeller\skript\Omstart-metod.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Tjänster\data\enhetsmodeller\skript\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Tjänster\data\enhetsmodeller\skript\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Tjänster\data\enhetsmodeller\skript\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Öppna filen **C:\temp\devicemodels\chiller-01.json.**

1. Lägg till följande två definitioner i avsnittet **InitialState:**

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Lägg till följande definition i matrisen **Telemetri:**

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

1. Spara filen **C:\temp\devicemodels\chiller-01.json.**

1. Öppna filen **C:\temp\devicemodels\scripts\chiller-01-state.js.**

1. Lägg till följande **state** fält i tillståndsvariabeln:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Uppdatera **huvudfunktionen** enligt följande:

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

1. Spara filen **C:\temp\devicemodels\scripts\chiller-01-state.js.**

## <a name="create-the-lightbulb"></a>Skapa glödlampan

I det här avsnittet definierar du en ny **glödlampa** enhet typ:

1. Skapa en fil **C:\temp\devicemodels\lightbulb-01.json** och lägg till följande innehåll:

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

    Spara ändringarna på **C:\temp\devicemodels\lightbulb-01.json**.

1. Skapa en fil **C:\temp\devicemodels\scripts\lightbulb-01-state.js** och lägg till följande innehåll:

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

1. Skapa en fil **C:\temp\devicemodels\scripts\SwitchOn-method.js** och lägg till följande innehåll:

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

1. Skapa en fil **C:\temp\devicemodels\scripts\SwitchOff-method.js** och lägg till följande innehåll:

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

Du har nu skapat en anpassad version av **chiller-enhetstypen** och skapat en ny **glödlampa** enhetstyp.

## <a name="test-the-devices"></a>Testa enheterna

I det här avsnittet testar du de enhetstyper som du skapade i föregående avsnitt lokalt.

### <a name="run-the-device-simulation-microservice"></a>Kör mikrotjänsten för enhetssimulering

Öppna **mappen device-simulation-dotnet-master** som du hämtade från GitHub i en ny instans av Visual Studio Code. Klicka på **valfria återställningsknappar** om du vill åtgärda eventuella olösta beroenden.

Öppna **filen WebService/appsettings.ini** och tilldela cosmos DB-anslutningssträngen till **variabeln documentdb_connstring** och ändra även inställningarna enligt följande:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Om du vill köra mikrotjänsten lokalt klickar du på **Felsök > Starta felsökning**.

**Terminalfönstret** i Visual Studio-koden visar utdata från den mikrotjänst som körs.

Låt mikrotjänsten för enhetssimulering köras i den här instansen av Visual Studio-kod medan du slutför nästa steg.

### <a name="set-up-a-monitor-for-device-events"></a>Konfigurera en bildskärm för enhetshändelser

I det här avsnittet använder du Azure CLI för att konfigurera en händelseövervakare för att visa telemetrin som skickas från de enheter som är anslutna till din IoT-hubb.

Följande skript förutsätter att namnet på IoT-hubben är **enhetssimuleringstest**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Låt händelseövervakaren vara igång medan du testar de simulerade enheterna.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Skapa en simulering med den uppdaterade kylarenhetstypen

I det här avsnittet använder du Postman-verktyget för att begära att mikrotjänsten för enhetssimulering ska köra en simulering med hjälp av den uppdaterade kylarenhetstypen. Brevbäraren är ett verktyg som låter dig skicka REST-begäranden till en webbtjänst. De Postman-konfigurationsfiler du behöver finns i din lokala kopia av **databasen för enhetssimulering-dotnet.**

Så här ställer du in Postman:

1. Öppna Brevbäraren på din lokala dator.

1. Klicka på **Importera >** fil. Klicka sedan på **Välj filer**.

1. Navigera till mappen **device-simulation-dotnet-master/docs/postman.** Välj **Azure IoT Device Simulation solution accelerator.postman_collection** och **Azure IoT Device Simulation solution accelerator.postman_environment** och klicka på **Öppna**.

1. Expandera **Azure IoT Device Simulation-lösningsacceleratorn** till de begäranden du kan skicka.

1. Klicka på **Ingen miljö** och välj **Azure IoT Device Simulation solution accelerator**.

Du har nu en samling och miljö inläst i postmanarbetsytan som du kan använda för att interagera med mikrotjänsten för enhetssimulering.

Så här konfigurerar och kör du simuleringen:

1. I postmansamlingen väljer du **Skapa modifierad kylmaskinssimulering** och klickar på **Skicka**. Den här begäran skapar fyra instanser av den simulerade kylmaskinsenhetens typ.

1. Händelseövervakarutdata i Azure CLI-fönstret visar telemetrin från de simulerade enheterna, inklusive de nya **internal_temperature** värdena.

Om du vill stoppa simuleringen väljer du **stop simulation-begäran** i Postman och klickar på **Skicka**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Skapa en simulering med filbulbenhetstypen

I det här avsnittet använder du Postman-verktyget för att begära att mikrotjänsten för enhetssimulering ska köra en simulering med hjälp av filbulbenhetstypen. Brevbäraren är ett verktyg som låter dig skicka REST-begäranden till en webbtjänst.

Så här konfigurerar och kör du simuleringen:

1. I Postman-samlingen väljer du **Skapa simulering** av glödlampa och klickar på **Skicka**. Den här begäran skapar två instanser av den simulerade glödlampans enhetstyp.

1. Händelseövervakarutdata i Azure CLI-fönstret visar telemetrin från de simulerade glödlamporna.

Om du vill stoppa simuleringen väljer du **stop simulation-begäran** i Postman och klickar på **Skicka**.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan stoppa de två lokalt körande mikrotjänsterna i deras Visual Studio-kodinstanser (**Debug > Stop Debugging**).

Om du inte längre behöver IoT Hub- och Cosmos DB-instanserna tar du bort dem från din Azure-prenumeration för att undvika onödiga debiteringar.

## <a name="next-steps"></a>Nästa steg

Den här guiden visar hur du skapar en anpassad simulerad enhetstyper och testar dem genom att köra mikrotjänsten för enhetssimulering lokalt.

Det föreslagna nästa steget är att lära sig hur du distribuerar dina anpassade simulerade enhetstyper till [lösningsacceleratorn för fjärrövervakning](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
