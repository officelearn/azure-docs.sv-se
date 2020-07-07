---
title: Simulerad enhet i fjärr styrnings lösning – Azure | Microsoft Docs
description: Den här artikeln beskriver hur du använder Java Script för att definiera beteendet för en simulerad enhet i lösningen för fjärrövervakning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c39ca0a018bd22844cf7e5350e6d3586319aac16
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "73890851"
---
# <a name="implement-the-device-model-behavior"></a>Implementera beteendet för enhets modellen

I artikeln [förstås enhets modell schemat](iot-accelerators-remote-monitoring-device-schema.md) som beskrivits det schema som definierar en simulerad enhets modell. Den artikeln hänvisade till två typer av JavaScript-filer som implementerar beteendet för en simulerad enhet:

- **Tillstånd** JavaScript-filer som körs med fasta intervall för att uppdatera enhetens interna status.
- **Metod** JavaScript-filer som körs när lösningen anropar en metod på enheten.

> [!NOTE]
> Enhets modell beteenden är bara för simulerade enheter som finns i enhets simulerings tjänsten. Om du vill skapa en riktig enhet kan du läsa [Anslut din enhet till-acceleratorn för fjärr styrning](iot-accelerators-connecting-devices.md).

I den här artikeln kan du se hur du:

>[!div class="checklist"]
> * Kontrol lera statusen för en simulerad enhet
> * Definiera hur en simulerad enhet svarar på ett metod anrop från lösningen för fjärrövervakning
> * Felsöka skript

## <a name="state-behavior"></a>Tillstånds beteende

[Simulerings](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) avsnittet i enhets modellens schema definierar det interna läget för en simulerad enhet:

- `InitialState`definierar initial värden för alla egenskaper för objektet enhets tillstånd.
- `Script`identifierar en JavaScript-fil som körs enligt ett schema för att uppdatera enhetens tillstånd.

I följande exempel visas definitionen av objektet enhets tillstånd för en simulerad kylnings enhet:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

Statusen för den simulerade enheten, som definieras i `InitialState` avsnittet, lagras i minnet av simulerings tjänsten. Tillståndsinformationen skickas som indata till `main` funktionen som definierats i **chiller-01-state.js**. I det här exemplet kör simulerings tjänsten **chiller-01-state.js** -filen var femte sekund. Skriptet kan ändra statusen för den simulerade enheten.

Följande visar dispositionen för en typisk `main` funktion:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

`context`Parametern har följande egenskaper:

- `currentTime`som en sträng med format`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, till exempel`Simulated.Chiller.123`
- `deviceModel`, till exempel`Chiller`

`state`Parametern innehåller enhetens status som underhålls av enhets simulerings tjänsten. Det här värdet är det `state` objekt som returnerades av föregående anrop till `main` .

I följande exempel visas en typisk implementering av `main` metoden för att hantera enhets status som underhålls av simulerings tjänsten:

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

I följande exempel visas hur `main` metoden kan simulera telemetri värden som varierar över tid:

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

Du kan visa hela [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) på GitHub.

## <a name="method-behavior"></a>Metod beteende

Avsnittet [CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) i enhets modellens schema definierar de metoder som en simulerad enhet svarar på.

I följande exempel visas en lista över de metoder som stöds av en simulerad kyl enhet:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

Varje metod har en associerad JavaScript-fil som implementerar metodens beteende.

Statusen för den simulerade enheten, som definieras i avsnittet i `InitialState` schemat, lagras i minnet av simulerings tjänsten. Tillståndsinformationen skickas som indata till `main` funktionen som definierats i JavaScript-filen när metoden anropas. Skriptet kan ändra statusen för den simulerade enheten.

Följande visar dispositionen för en typisk `main` funktion:

```javascript
function main(context, previousState, previousProperties) {

}
```

`context`Parametern har följande egenskaper:

- `currentTime`som en sträng med format`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, till exempel`Simulated.Chiller.123`
- `deviceModel`, till exempel`Chiller`

`state`Parametern innehåller enhetens status som underhålls av enhets simulerings tjänsten.

`properties`Parametern innehåller egenskaperna för den enhet som skrivs som rapporterade egenskaper till den IoT Hub enheten.

Det finns tre globala funktioner som du kan använda för att implementera beteendet för-metoden:

- `updateState`uppdatera det tillstånd som innehas av simulerings tjänsten.
- `updateProperty`så här uppdaterar du en enskild enhets egenskap.
- `sleep`pausa körningen för att simulera en tids krävande aktivitet.

I följande exempel visas en förkortad version av **IncreasePressure-method.js** -skriptet som används av de simulerade kyl enheterna:

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>Felsöka skriptfiler

Det går inte att koppla en fel sökare till JavaScript-tolken som används av enhets simulerings tjänsten för att köra tillstånds-och metod skript. Du kan dock Logga information i tjänst loggen. Med den inbyggda `log()` funktionen kan du spara information för att spåra och felsöka funktions körning.

Om det finns ett syntaxfel Miss lyckas tolken och skriver en `Jint.Runtime.JavaScriptException` post till tjänst loggen.

I artikeln om att [köra tjänsten lokalt](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) på GitHub visas hur du kör enhets simulerings tjänsten lokalt. Genom att köra tjänsten lokalt blir det enklare att felsöka dina simulerade enheter innan du distribuerar dem till molnet.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du definierar beteendet för en egen anpassad simulerad enhets modell. I den här artikeln har du visat hur du:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Kontrol lera statusen för en simulerad enhet
> * Definiera hur en simulerad enhet svarar på ett metod anrop från lösningen för fjärrövervakning
> * Felsöka skript

Nu när du har lärt dig hur du anger beteendet för en simulerad enhet är det föreslagna nästa steg att lära dig hur du [skapar en simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

Mer information om utvecklings lösningen finns i:

* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Felsökningsguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
