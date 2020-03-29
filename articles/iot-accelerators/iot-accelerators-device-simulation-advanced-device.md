---
title: Skapa en avancerad simulerad enhetsmodell – Azure| Microsoft-dokument
description: I den här programguiden får du lära dig hur du skapar en avancerad enhetsmodell som kan användas med device simulation-lösningsaccelerator.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 03/18/2019
ms.author: troyhop
ms.openlocfilehash: 4401d4b93a27e76554368ce72d256b38de61df4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61449058"
---
# <a name="create-an-advanced-device-model"></a>Skapa en avancerad enhetsmodell

Den här programguiden beskriver JSON- och JavaScript-filerna som definierar en anpassad enhetsmodell. Artikeln innehåller några exempel på enhetsmodelldefinitionsfiler och visar hur du laddar upp dem till enhetssimuleringsinstansen. Du kan skapa avancerade enhetsmodeller för att simulera mer realistiska enhetsbeteenden för testningen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill följa stegen i den här programguiden behöver du en distribuerad instans av Enhetssimulering i din Azure-prenumeration.

Om du inte har distribuerat enhetssimulering ännu bör du slutföra snabbstarten [Distribuera och köra en IoT-enhetssimulering i Azure](quickstart-device-simulation-deploy.md).

### <a name="open-device-simulation"></a>Öppna enhetssimulering

För att köra enhetssimulering i webbläsaren navigerar du först till [Microsoft Azure IoT-lösningsacceleratorer](https://www.azureiotsolutions.com).

Du kan bli ombedd att logga in med dina autentiseringsuppgifter för Azure-prenumeration.

Klicka sedan på **Starta** på panelen för enhetssimulering som du distribuerade i [distribuera och kör en IoT-enhetssimulering i Azure-snabbstart.](quickstart-device-simulation-deploy.md)

## <a name="device-models"></a>Enhetsmodeller

Varje simulerad enhet tillhör en specifik enhetsmodell som definierar simuleringsbeteendet. Det här problemet inkluderar hur ofta du skickar telemetri, vilken typ av meddelanden som ska skickas och metoder som stöds.

Du definierar en enhetsmodell med en JSON-enhetsdefinitionsfil och en uppsättning JavaScript-filer. Dessa JavaScript-filer definierar simuleringsbeteendet, till exempel slumpmässig telemetri och metodlogiken.

En typisk enhetsmodell har:

* En JSON-fil för varje enhetsmodell (till exempel elevator.json).
* En JavaScript-beteendeskriptfil för varje enhetsmodell (till exempel hiss-state.js)
* En JavaScript-metodskriptfil för varje enhetsmetod (till exempel elevator-go-down.js)

> [!NOTE]
> Alla enhetsmodeller definierar inte metoder. Därför kan en enhetsmodell ha metodskript eller inte. Alla enhetsmodeller måste dock ha ett beteendeskript.

## <a name="device-definition-file"></a>Enhetsdefinitionsfil

Varje enhetsdefinitionsfil innehåller information om en simulerad enhetsmodell, inklusive följande information:

* Enhetsmodellnamn: sträng.
* Protokoll: AMQP | MQTT | HTTP.
* Det ursprungliga enhetstillståndet.
* Hur ofta enhetstillståndet ska uppdateras.
* Vilken JavaScript-fil som ska användas för att uppdatera enhetstillståndet.
* En lista över telemetrimeddelanden som ska skickas, var och en med en viss frekvens.
* Schemat för telemetrimeddelandena som används av backend-programmet för att tolka den mottagna telemetrin.
* En lista över metoder som stöds och JavaScript-filen som ska användas för att simulera varje metod.

### <a name="file-schema"></a>Filschema

Schemaversionen är alltid "1.0.0" och är specifik för formatet för den här filen:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Beskrivning av enhetsmodell

Följande egenskaper beskriver enhetsmodellen. Varje typ har en unik identifierare, en semantisk version, ett namn och en beskrivning:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT-protokoll

IoT-enheter kan ansluta med olika protokoll. Med simuleringen kan du använda antingen **AMQP,** **MQTT**eller **HTTP:**

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Simulerat enhetstillstånd

Varje simulerad enhet har ett internt tillstånd som måste definieras. Tillståndet definierar också de egenskaper som kan rapporteras i telemetri. En kylmaskin kan till exempel ha ett ursprungligt tillstånd som:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

En rörlig enhet med flera sensorer kan ha fler egenskaper, till exempel:

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

Enhetstillståndet hålls i minnet av simuleringstjänsten och tillhandahålls som indata till JavaScript-funktionen. JavaScript-funktionen kan bestämma:

* Så här ignorerar du tillståndet och genererar några slumpmässiga data.
* Så här uppdaterar du enhetstillståndet på något realistiskt sätt för ett visst scenario.

Funktionen som genererar tillståndet tar också emot som indata:

* Enhets-ID.
* Enhetsmodellen.
* Den aktuella tiden. Det här värdet gör det möjligt att generera olika data per enhet och tid.

### <a name="generating-telemetry-messages"></a>Generera telemetrimeddelanden

Simuleringstjänsten kan skicka flera telemetrityper för varje enhet. Telemetri innehåller vanligtvis data från enhetstillståndet. Ett simulerat rum kan till exempel skicka information om temperatur och luftfuktighet var tionde sekund. Observera platshållarna i följande kodavsnitt, som automatiskt ersätts med värden från enhetstillståndet:

```json
"Telemetry": [
    {
        "Interval": "00:00:10",
        "MessageTemplate":
            "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
        "MessageSchema": {
            "Name": "RoomComfort;v1",
            "Format": "JSON",
            "Fields": {
                "temperature": "double",
                "temperature_unit": "text",
                "humidity": "integer"
            }
        }
    }
],
```

Platshållarna använder en särskild syntax **${NAME}** där **NAME** är en nyckel från enhetstillståndsobjektet som returneras av **JavaScript-huvudfunktionen.** Strängar bör citeras, medan siffror inte borde.

#### <a name="message-schema"></a>Schema för meddelanden

Varje meddelandetyp måste ha ett väldefinierat schema. Meddelandeschemat publiceras också i IoT Hub, så att backend-program kan återanvända informationen för att tolka den inkommande telemetrin.

Schemat stöder JSON-format, vilket möjliggör enkel tolkning, omvandling och analys, över flera system och tjänster.

Fälten i schemat kan vara av följande typer:

* Objekt - serialiserad med JSON
* Binary - serialiserad med bas64
* Text
* Boolean
* Integer
* Double
* DateTime

### <a name="supported-methods"></a>Metoder som stöds

Simulerade enheter kan också reagera på metodanrop, i vilket fall de kör viss logik och ger viss respons. På samma sätt som simuleringen lagras metodlogiken i en JavaScript-fil och kan interagera med enhetstillståndet. Ett exempel:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Skapa en enhetsdefinitionsfil

I den här guiden ser du hur du skapar en enhetsmodell för en drönare. Drönaren kommer slumpmässigt att flyga runt en första uppsättning koordinater som ändrar plats och höjd.

Kopiera följande JSON till en textredigerare och spara den som **drone.json**.

### <a name="device-definition-json-example"></a>JSON-exempel för enhetsdefinition

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>Beteendeskriptfiler

Koden i beteendeskriptfilen flyttar drönaren. Skriptet ändrar drönarens höjd och plats genom att manipulera enhetens i minnesläge.

JavaScript-filerna måste ha en **huvudfunktion** som accepterar två parametrar:

* Ett **kontextobjekt** som innehåller tre egenskaper:
    * **currentTime** som en sträng med format **yyyy-MM-dd'T'HH:mm:sszzz**.
    * **deviceId**. **Simulerad.Hiss.123**.
    * **deviceModel**. Till exempel **Hiss**.
* Ett **tillståndsobjekt** som är värdet som returneras av funktionen i föregående anrop. Det här enhetstillståndet underhålls av simuleringstjänsten och används för att generera telemetrimeddelanden.

**Huvudfunktionen** returnerar det nya enhetstillståndet. Ett exempel:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Skapa en beteendeskriptfil

Kopiera följande JavaScript till en textredigerare och spara det som **drone-state.js**.

### <a name="device-model-javascript-simulation-example"></a>Exempel på JavaScript-simulering av enhetsmodell

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
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
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
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
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>Skapa en metodskriptfil

Metodskript liknar beteendeskript. De definierar enhetens beteende när en specifik metod för moln till enhet anropas.

Drönarrekallningsskriptet ställer in drönarens koordinater till en fast punkt för att simulera drönaren som återvänder hem.

Kopiera följande JavaScript till en textredigerare och spara det som **droneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>Exempel på JavaScript-simulering av enhetsmodell

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
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
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>Felsöka skriptfiler

Du kan inte koppla en felsökare till en beteendefil som körs, men det är möjligt att skriva information till tjänstloggen med hjälp av **loggfunktionen.** För syntaxfel misslyckas tolken och skriver information om undantaget till loggen.

Exempel på loggning:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>Distribuera en avancerad enhetsmodell

Om du vill distribuera din avancerade enhetsmodell laddar du upp de filer som din enhetssimuleringsinstans:

Välj **Enhetsmodeller** på menyraden. På sidan **Enhetsmodeller** visas de enhetsmodeller som är tillgängliga i den här instansen av Enhetssimulering:

![Enhetsmodeller](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Klicka på **+ Lägg till enhetsmodeller** i det övre högra hörnet på sidan:

![Lägga till enhetsmodell](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Klicka på **Avancerat** för att öppna fliken avancerad enhetsmodell:

![Fliken Avancerat](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Klicka på **Bläddra** och välj de JSON- och JavaScript-filer som du har skapat. Var noga med att markera alla tre filerna. Om någon fil saknas misslyckas valideringen:

![Bläddra bland filer](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Om dina filer passerar validering, klicka på **Spara** och din enhetsmodell är klar att användas i en simulering. Annars åtgärdar du eventuella fel och laddar om filerna:

![Spara](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Nästa steg

I den här programguiden fick du lära dig om enhetsmodellfilerna som används i Enhetssimulering och hur du skapar en avancerad enhetsmodell. Därefter kanske du vill utforska hur du [använder Time Series Insights för att visualisera telemetri som skickas från device simulation-lösningsacceleratorn](https://docs.microsoft.com/azure/iot-accelerators/iot-accelerators-device-simulation-time-series-insights).
