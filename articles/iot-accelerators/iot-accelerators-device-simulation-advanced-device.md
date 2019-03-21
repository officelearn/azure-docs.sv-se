---
title: Skapa en avancerad simulerade enhetsmodell – Azure | Microsoft Docs
description: Lär dig hur du skapar en avancerad enhetsmodell för användning med Enhetssimulering lösningsaccelerator i den här guiden.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 03/18/2019
ms.author: troyhop
ms.openlocfilehash: 4401d4b93a27e76554368ce72d256b38de61df4c
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286133"
---
# <a name="create-an-advanced-device-model"></a>Skapa en avancerad enhetsmodell

Den här guiden beskriver JSON och JavaScript-filer som definierar en anpassad enhet-modell. Artikeln innehåller vissa enheten model definition exempelfilerna och visar hur du laddar upp dem till din Enhetssimulering-instans. Du kan skapa avancerade modeller för att simulera mer realistisk enhetsbeteenden för testet.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen i den här guiden, måste en distribuerad Enhetssimulering-instans i Azure-prenumerationen.

Om du inte har distribuerat enhetssimulering ännu bör du slutföra snabbstarten [Distribuera och köra en IoT-enhetssimulering i Azure](quickstart-device-simulation-deploy.md).

### <a name="open-device-simulation"></a>Öppna enhetssimulering

För att köra enhetssimulering i webbläsaren navigerar du först till [Microsoft Azure IoT-lösningsacceleratorer](https://www.azureiotsolutions.com).

Du kan bli ombedd att logga in med dina autentiseringsuppgifter för Azure-prenumeration.

Klicka sedan på **starta** på panelen för Enhetssimulering som du distribuerade i de [distribuera och kör en IoT-enhetssimulering i Azure](quickstart-device-simulation-deploy.md) Snabbstart.

## <a name="device-models"></a>Enhetsmodeller

Varje simulerad enhet som tillhör en viss enhetsmodell som definierar beteendet simulering. Detta inkluderar hur ofta du vill skicka telemetri, vilken typ av meddelanden att skicka och metoderna som stöds.

Du definierar en enhetsmodell som använder en definitionsfil för JSON-enhet och en uppsättning JavaScript-filer. Dessa JavaScript-filer definierar beteendet simulering, till exempel slumpmässiga telemetri och metoden logiken.

En typisk enhetsmodell har:

* En JSON-fil för varje enhetsmodell (till exempel elevator.json).
* En JavaScript beteende skriptfilen för varje enhetsmodell (till exempel hiss-state.js)
* En JavaScript-metod skriptfilen för varje enhetsmetod (till exempel hiss-go-down.js)

> [!NOTE]
> Inte alla enhetsmodeller definierar metoder. Därför en enhetsmodell kanske eller kanske inte metoden skript. Alla enhetsmodeller måste dock ha ett beteende-skript.

## <a name="device-definition-file"></a>Definitionsfilen för enhet

Varje enhet definitionsfilen innehåller information om en simulerad enhet-modell, inklusive följande information:

* Enhetens modellnamn: sträng.
* Protokoll: AMQP | MQTT | HTTP.
* Inledande enhetens tillstånd.
* Hur ofta att uppdatera enhetens tillstånd.
* Vilken JavaScript-fil som ska användas för att uppdatera enhetens tillstånd.
* En lista över telemetrimeddelanden att skicka, var och en med en specifik frekvens.
* Schemat för telemetrimeddelanden som används av serverprogrammet för att parsa den telemetri som tagits emot.
* En lista över metoder som stöds och JavaScript-fil du använder för att simulera varje metod.

### <a name="file-schema"></a>Filschemat

Schemaversionen är alltid ”1.0.0” och är specifikt för formatet för den här filen:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Enhetsbeskrivning för modellen

Följande egenskaper beskriver den nuvarande enhetsmodellen. Varje typ har en unik identifierare, en semantiska version, ett namn och en beskrivning:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT-protokoll

IoT-enheter kan ansluta med olika protokoll. Simuleringen kan du använda antingen **AMQP**, **MQTT**, eller **HTTP**:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Simulerade enhetens tillstånd

Varje simulerad enhet har ett internt tillstånd, som måste definieras. Tillståndet definierar också de egenskaper som kan rapporteras i telemetri. En kylaggregat kan till exempel ha ett inledande tillstånd som:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

En glidande enhet med flera sensorer kan ha fler egenskaper, till exempel:

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

Enhetens tillstånd lagras i minnet av tjänsten simulering och anges som indata till JavaScript-funktion. JavaScript-funktion kan bestämma:

* Ignorera tillståndet och generera lite slumpmässiga data.
* Att uppdatera enhetens tillstånd designomfattningen realistisk för ett visst scenario.

Den funktion som genererar tillståndet tar också emot som indata:

* Enhets-ID.
* Enhetsmodellen.
* Den aktuella tiden. Det här värdet gör det möjligt att generera olika genom enheten och tid.

### <a name="generating-telemetry-messages"></a>Generera telemetrimeddelanden

Tjänsten simulering kan skicka flera typer av telemetri för varje enhet. Telemetri innehåller normalt data från enhetens tillstånd. En simulerad lokal kan exempelvis skicka information om temperatur och fuktighet var tionde sekund. Observera platshållare i följande kodfragment som automatiskt ersätts med värden från enhetens tillstånd:

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

Platshållarna använder en särskild syntax **${NAME}** där **namn** är en nyckel från enhetens tillstånd objektet som returnerades av JavaScript **huvudsakliga** funktion. Strängar bör tillämpas, medan siffror får inte.

#### <a name="message-schema"></a>Meddelandeschema

Varje meddelandetyp måste ha ett väldefinierade schema. Meddelandeschema publiceras också till IoT-hubb, så att serverprogram kan återanvända informationen för att tolka inkommande telemetri.

Schemat har stöd för JSON-format, vilket möjliggör enkelt parsa, omvandling och analys, i flera system och tjänster.

Fält som anges i schemat kan vara av följande typer:

* Objekt - serialiseras med JSON
* Binary - serialiseras base64
* Text
* Boolesk
* Integer
* Double
* DateTime

### <a name="supported-methods"></a>Metoder som stöds

Simulerade enheter kan också reagera på metodanrop, i så fall de köra logik och ge några svar. På samma sätt som simuleringen metoden logiken lagras i en JavaScript-fil och kan interagera med enhetens tillstånd. Exempel:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Skapa en definitionsfil för enhet

I den här How-to-till-guiden lär du dig att skapa en enhetsmodell för en drönare. Drönare att slumpmässigt flyga runt en grunduppsättning med koordinaterna ändra plats och höjd.

Kopiera följande JSON till en textredigerare och spara den som **drone.json**.

### <a name="device-definition-json-example"></a>Device definition JSON-exempel

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

## <a name="behavior-script-files"></a>Beteende-skriptfiler

Koden i skriptfilen beteende flyttar drönare. Skriptet ändrar drönare upphöjning och plats genom att ändra enhetens minne tillståndet.

JavaScript-filer måste ha en **huvudsakliga** funktion som tar två parametrar:

* En **kontext** objekt som innehåller tre egenskaper:
    * **currentTime** som en sträng med formatet **åååå-MM-dd'T'HH:mm:sszzz**.
    * **deviceId**. Till exempel **Simulated.Elevator.123**.
    * **deviceModel**. Till exempel **Elevator**.
* En **tillstånd** objekt som är värdet som returneras av funktionen i det föregående anropet. Den här enhetens tillstånd är underhålls av tjänsten simulering och används för att generera telemetrimeddelanden.

Den **huvudsakliga** funktionen returnerar det nya enhetens tillståndet. Exempel:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Skapa en skriptfil för beteende

Kopiera följande JavaScript i en textredigerare och spara den som **drönare state.js**.

### <a name="device-model-javascript-simulation-example"></a>Enheten modellen JavaScript simulering exempel

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

## <a name="create-a-method-script-file"></a>Skapa en skriptfil för metod

Metoden skript liknar beteende skript. De definierar beteendet enheten när ett visst moln till enhetsmetoden anropas.

Drönare återkallande skriptet anger den drönare koordinater till en fast tidpunkt att simulera drönare returnerar start.

Kopiera följande JavaScript i en textredigerare och spara den som **droneRecall method.js**.

### <a name="device-model-javascript-simulation-example"></a>Enheten modellen JavaScript simulering exempel

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

## <a name="debugging-script-files"></a>Felsökning skriptfiler

Även om du inte bifoga en felsökare till en fil som körs beteende, är det möjligt att skriva information till tjänsten log med den **log** funktion. Syntaxfel, vilken tolk som misslyckas och skriver information om undantaget till loggen.

Loggning exempel:

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

För att distribuera din avancerade enhetsmodell, överför du filerna Enhetssimulering-instans:

Välj **Enhetsmodeller** på menyraden. Den **enhetsmodeller** sidan listar enhetsmodeller som är tillgängliga i den här instansen av Enhetssimulering:

![Enhetsmodeller](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Klicka på **+ Lägg till enhetsmodeller** i det övre högra hörnet på sidan:

![Lägga till enhetsmodell](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Klicka på **Avancerat** att öppna fliken avancerade modellen:

![Fliken Avancerat](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Klicka på **Bläddra** och välj JSON och JavaScript-filer som du skapade. Glöm inte att markera alla tre filer. Valideringen misslyckas om någon en fil saknas:

![Bläddra efter filer](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Om dina filer valideras, klickar du på **spara** och din enhetsmodell är redo att användas i en simulering. I annat fall åtgärda eventuella fel och ladda upp filer:

![Spara](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Nästa steg

I den här guiden beskrivs om enheten modellfiler som används i Enhetssimulering och hur du skapar en avancerad enhetsmodell. Därefter kan du utforska hur du [Använd Time Series Insights för att visualisera telemetri som skickas från lösningsaccelerator Enhetssimulering](https://docs.microsoft.com/azure/iot-accelerators/iot-accelerators-device-simulation-time-series-insights).
