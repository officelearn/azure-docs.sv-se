---
title: "Simulerade enhetsbeteende i fjärranslutna övervakningslösning - Azure | Microsoft Docs"
description: "Den här artikeln beskriver hur du använder JavaScript för att definiera beteendet för en simulerad enhet i fjärranslutna övervakningslösning."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/29/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: e5846893166c3e65b75e84d02849c2b8ab78e079
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="implement-the-device-model-behavior"></a>Implementera enhetsbeteende för modellen

Artikeln [förstå modellschemat enheten](iot-suite-remote-monitoring-device-schema.md) beskrivs det schema som definierar en simulerad enhetsmodell. Den artikeln som avses två typer av JavaScript-fil som implementerar beteendet för en simulerad enhet:

- **Tillstånd** JavaScript-filer som körs med fasta intervaller att uppdatera det interna tillståndet för enheten.
- **Metoden** JavaScript-filer som körs när lösningen anropar en metod på enheten.

I den här artikeln får du lära dig hur du:

>[!div class="checklist"]
> * Kontrollera statusen för en simulerad enhet
> * Definiera hur en simulerad enhet reponds till en metod anropa från fjärranslutna övervakningslösning
> * Felsök skripten

## <a name="state-behavior"></a>Tillstånd beteende

Den [simuleringen](iot-suite-remote-monitoring-device-schema.md#simulation) avsnitt i enheten modellschemat definierar det interna tillståndet för en simulerad enhet:

- `InitialState`definierar ursprungliga värden för alla egenskaper i objektet enhetens tillstånd.
- `Script`identifierar en JavaScript-fil som körs på ett schema för att uppdatera enhetens tillstånd.

I följande exempel visas definitionen av enhetsobjekt för tillstånd för en simulerad kylaggregat enhet:

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
  "Script": {
    "Type": "javascript",
    "Path": "chiller-01-state.js",
    "Interval": "00:00:05"
  }
}
```

Tillståndet för den simulerade enheten enligt definitionen i den `InitialState` avsnittet, hålls kvar i minnet av tjänsten simuleringen. Tillståndsinformationen skickas som indata till det `main` funktion som definierats i **kylaggregat-01-state.js**. I det här exemplet simuleringen-tjänsten körs på **kylaggregat-01-state.js** filen var femte sekund. Skriptet kan ändra tillståndet för den simulerade enheten.

Här visas en typisk kontur `main` funktionen:

```javascript
function main(context, previousState) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

Den `context` parametern har följande egenskaper:

- `currentTime`som en sträng med formatet`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, till exempel`Simulated.Chiller.123`
- `deviceModel`, till exempel`Chiller`

Den `state` parametern innehåller tillståndet för enheten som hanteras av tjänst för simuleringen. Det här värdet är den `state` objektet som returnerades av det föregående anropet till `main`.

I följande exempel visas en typisk implementering av den `main` metod för att hantera Enhetsstatus hanteras av tjänsten simuleringen:

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

function main(context, previousState) {

  restoreState(previousState);

  // Update state

  return state;
}
```

Följande exempel visar hur `main` metoden kan simulera telemetri värden som varierar över tid:

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


function main(context, previousState) {

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

Du kan visa det fullständiga [kylaggregat-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) på Github.

## <a name="method-behavior"></a>Metoden beteende

Den [CloudToDeviceMethods](iot-suite-remote-monitoring-device-schema.md#cloudtodevicemethods) avsnitt i enheten modellschemat definierar metoder som en simulerad enhet svarar.

I följande exempel visas en lista över metoder som stöds av en simulerad kylaggregat enhet:

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

Tillståndet för den simulerade enheten enligt definitionen i den `InitialState` avsnitt av schemat, hålls kvar i minnet av tjänsten simuleringen. Tillståndsinformationen skickas som indata till det `main` funktion som definierats i JavaScript-filen när metoden anropas. Skriptet kan ändra tillståndet för den simulerade enheten.

Här visas en typisk kontur `main` funktionen:

```javascript
function main(context, previousState) {

}
```

Den `context` parametern har följande egenskaper:

- `currentTime`som en sträng med formatet`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, till exempel`Simulated.Chiller.123`
- `deviceModel`, till exempel`Chiller`

Den `state` parametern innehåller tillståndet för enheten som hanteras av tjänst för simuleringen.

Det finns två globala funktioner som du kan använda för att implementera metodens beteende:

- `updateState`Uppdaterar status från tjänsten simuleringen.
- `sleep`Så här pausar körningen för att simulera en tidskrävande uppgift.

I följande exempel visas en förkortad version av den **IncreasePressure method.js** skript som används av simulerade kylaggregat enheter:

```javascript
function main(context, previousState) {

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

## <a name="debugging-script-files"></a>Skriptfiler för felsökning

Det går inte att koppla en felsökare till Javascript-tolken som används av tjänsten enhet simuleringen tillstånd och metoden skript ska köras. Du kan dock logga information i loggen för tjänsten. Inbyggt `log()` funktionen kan du spara information om du vill spåra och felsöka funktionen körning.

Om det finns ett syntaxfel tolkens misslyckas, och skriver en `Jint.Runtime.JavaScriptException` post i loggen för tjänsten.

Den [skapa en simulerad enhet](iot-suite-remote-monitoring-test.md) artikeln visar hur du kör tjänsten enhet simuleringen lokalt. Kör tjänsten lokalt gör det enklare att felsöka din simulerade enheter innan du distribuerar dem till molnet.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs hur du definierar beteendet för dina egna anpassade simulerade enhetsmodell. Den här artikeln visar dig hur till:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Kontrollera statusen för en simulerad enhet
> * Definiera hur en simulerad enhet reponds till en metod anropa från fjärranslutna övervakningslösning
> * Felsök skripten

Nu du har lärt dig hur du anger beteendet för en simulerad enhet, föreslagna nästa steg är att lära dig hur du [skapa en simulerad enhet](iot-suite-remote-monitoring-test.md).

Utvecklare om remote övervakningslösning, Läs mer:

* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Felsökningsguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->