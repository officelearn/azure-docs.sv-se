---
title: Simulerad enhet i lösning för fjärrövervakning – Azure | Microsoft-dokument
description: I den här artikeln beskrivs hur du använder JavaScript för att definiera beteendet hos en simulerad enhet i fjärrövervakningslösningen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c39ca0a018bd22844cf7e5350e6d3586319aac16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890851"
---
# <a name="implement-the-device-model-behavior"></a>Implementera enhetsmodellbeteendet

Artikeln [Förstå enhetsmodellschemat](iot-accelerators-remote-monitoring-device-schema.md) beskrev schemat som definierar en simulerad enhetsmodell. Den artikeln hänvisade till två typer av JavaScript-fil som implementerar beteendet hos en simulerad enhet:

- **Stat** JavaScript-filer som körs med fasta intervall för att uppdatera enhetens interna tillstånd.
- **Metod** JavaScript-filer som körs när lösningen anropar en metod på enheten.

> [!NOTE]
> Enhetsmodellbeteenden är endast för simulerade enheter som finns i enhetssimuleringstjänsten. Om du vill skapa en riktig enhet läser du [Anslut enheten till lösningsacceleratorn för fjärrövervakning](iot-accelerators-connecting-devices.md).

I den här artikeln kan du se hur du:

>[!div class="checklist"]
> * Styra tillståndet för en simulerad enhet
> * Definiera hur en simulerad enhet svarar på ett metodanrop från lösningen för fjärrövervakning
> * Felsöka skripten

## <a name="state-behavior"></a>Tillståndsbeteende

[Avsnittet Simulering](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) i enhetsmodellschemat definierar det interna tillståndet för en simulerad enhet:

- `InitialState`definierar initiala värden för alla egenskaper för enhetstillståndsobjektet.
- `Script`identifierar en JavaScript-fil som körs enligt ett schema för att uppdatera enhetstillståndet.

I följande exempel visas definitionen av enhetstillståndsobjektet för en simulerad kylmaskinsenhet:

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

Tillståndet för den simulerade enheten, `InitialState` enligt definitionen i avsnittet, hålls i minnet av simuleringstjänsten. Tillståndsinformationen skickas som indata till den `main` funktion som definieras i **kylaggregat-01-state.js**. I det här exemplet kör simuleringstjänsten **filen chiller-01-state.js** var femte sekund. Skriptet kan ändra tillståndet för den simulerade enheten.

Följande visar konturerna `main` av en typisk funktion:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

Parametern `context` har följande egenskaper:

- `currentTime`som en sträng med format`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, till exempel`Simulated.Chiller.123`
- `deviceModel`, till exempel`Chiller`

Parametern `state` innehåller enhetens tillstånd enligt enhetens simuleringstjänst. Det här `state` värdet är det objekt `main`som returnerades av föregående anrop till .

I följande exempel visas en `main` typisk implementering av metoden för att hantera enhetstillståndet som upprätthålls av simuleringstjänsten:

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

I följande exempel `main` visas hur metoden kan simulera telemetrivärden som varierar över tiden:

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

Du kan visa hela [kylaggregatet-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) på GitHub.

## <a name="method-behavior"></a>Metodbeteende

Avsnittet [CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) i enhetsmodellschemat definierar de metoder som en simulerad enhet svarar på.

I följande exempel visas en lista över metoder som stöds av en simulerad kylaggregat:

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

Tillståndet för den simulerade enheten, `InitialState` enligt definitionen i avsnittet i schemat, lagras i minnet av simuleringstjänsten. Tillståndsinformationen skickas som indata till den `main` funktion som definieras i JavaScript-filen när metoden anropas. Skriptet kan ändra tillståndet för den simulerade enheten.

Följande visar konturerna `main` av en typisk funktion:

```javascript
function main(context, previousState, previousProperties) {

}
```

Parametern `context` har följande egenskaper:

- `currentTime`som en sträng med format`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, till exempel`Simulated.Chiller.123`
- `deviceModel`, till exempel`Chiller`

Parametern `state` innehåller enhetens tillstånd enligt enhetens simuleringstjänst.

Parametern `properties` innehåller egenskaperna för den enhet som skrivs som rapporterade egenskaper till IoT Hub-enhetstvillingen.

Det finns tre globala funktioner som du kan använda för att implementera metodens beteende:

- `updateState`för att uppdatera det tillstånd som innehas av simuleringstjänsten.
- `updateProperty`för att uppdatera en enskild enhetsegenskap.
- `sleep`för att pausa körningen för att simulera en tidskrävande uppgift.

I följande exempel visas en förkortad version av skriptet **IncreasePressure-method.js** som används av de simulerade kylaggregaten:

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

Det går inte att koppla en felsökare till Javascript-tolken som används av enhetssimuleringstjänsten för att köra tillstånds- och metodskript. Du kan dock logga information i serviceloggen. Med den `log()` inbyggda funktionen kan du spara information för att spåra och felsöka funktionskörning.

Om det finns ett syntaxfel misslyckas tolken och skriver en `Jint.Runtime.JavaScriptException` post till serviceloggen.

Artikeln [Kör tjänsten lokalt](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) på GitHub visar hur du kör enhetssimuleringstjänsten lokalt. Om du kör tjänsten lokalt blir det enklare att felsöka dina simulerade enheter innan du distribuerar dem till molnet.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du definierar beteendet hos din egen anpassade simulerade enhetsmodell. Den här artikeln visade hur du:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Styra tillståndet för en simulerad enhet
> * Definiera hur en simulerad enhet svarar på ett metodanrop från lösningen för fjärrövervakning
> * Felsöka skripten

Nu när du har lärt dig hur du anger beteendet för en simulerad enhet, är det föreslagna nästa steget att lära dig hur du [skapar en simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

Mer information om lösningen för fjärrövervakning finns i:

* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Felsökningsguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
