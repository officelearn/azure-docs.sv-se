---
title: ta med fil
description: ta med fil
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 07/26/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: dc87079083b8f07ad18f5f871bff64de8d492ebd
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285654"
---
## <a name="the-parts-of-the-device-model-schema"></a>Delar av schemat för enhetsmodellen

Varje enhetsmodell, till exempel en kylaggregat eller lastbil, definierar en typ av enhet som tjänsten simulering kan simulera. Varje enhetsmodell lagras i en JSON-fil med följande översta schema:

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

Du kan visa schemafiler för simulerade standardenheter i den [devicemodels mappen](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) på GitHub.

I följande tabell beskrivs de översta schema posterna:

| Schema-post | Beskrivning |
| -- | --- |
| `SchemaVersion` | Schemaversionen är alltid `1.0.0` och är specifik för formatet för den här filen. |
| `Id` | Ett unikt ID för den här modellen. |
| `Version` | Identifierar versionen av den nuvarande enhetsmodellen. |
| `Name` | Ett eget namn för den nuvarande enhetsmodellen. |
| `Description` | En beskrivning av den nuvarande enhetsmodellen. |
| `Protocol` | Anslutningsprotokoll enheten använder. Kan vara något av `AMQP`, `MQTT`, och `HTTP`. |

I följande avsnitt beskrivs i andra avsnitt i JSON-schema:

## <a name="simulation"></a>Simulering

I den `Simulation` avsnittet definierar du det interna tillståndet för den simulerade enheten. Alla telemetrivärden som skickas av enheten måste vara en del av den här enhetens tillstånd.

Definitionen av enhetens tillstånd har två element:

* `InitialState` definierar ursprungliga värden för alla egenskaper för enhetsobjekt för tillstånd.
* `Script` identifierar en JavaScript-fil som körs på ett schema för att uppdatera enhetens tillstånd. Du kan använda den här skriptfilen för att slumpgenerera telemetrivärden som skickats av enheten.

Läs mer om JavaScript-fil som uppdaterar tillstånd enhetsobjekt i [förstå beteendet för enhetsmodellen](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md).

Exemplet nedan visar definitionen av enhetsobjekt för tillstånd för en simulerad kylaggregat enhet:

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
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

Simulering säkerhetskopieringstjänst körs den **kylaggregat-01-state.js** fil var femte sekund för att uppdatera enhetens tillstånd. Du kan se JavaScript-filer för simulerade standardenheter i den [skriptmappen](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) på GitHub. Enligt konventionen används dessa JavaScript-filer har suffixet **-tillstånd** att skilja dem från de filer som implementera metoden beteenden.

## <a name="properties"></a>Egenskaper

Den `Properties` delen av schemat definierar egenskapsvärdena enheten rapporterar till lösningen. Exempel:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

När lösningen har startat frågar de simulerade enheterna för att skapa en lista över `Type` värden som ska användas i Användargränssnittet. Lösningen använder den `Latitiude` och `Longitude` egenskaper för att lägga till enhetens plats på kartan på instrumentpanelen.

## <a name="telemetry"></a>Telemetri

Den `Telemetry` matris visar en lista över alla telemetrityper den simulerade enheten skickar till lösningen.

I följande exempel skickar ett meddelande för JSON-telemetri var tionde sekund med `floor`, `vibration`, och `temperature` data från den hiss sensorer:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate` definierar strukturen för JSON-meddelanden som skickas från den simulerade enheten. Platshållare i `MessageTemplate` Använd syntax `${NAME}` där `NAME` är en nyckel från den [tillstånd enhetsobjekt](#simulation). Bör innehålla citattecken strängar, tal bör inte.

`MessageSchema` definierar schemat för meddelanden som skickas från den simulerade enheten. Meddelandeschema publiceras även till IoT Hub för att aktivera serverprogram att återanvända informationen för att tolka inkommande telemetri.

Du kan för närvarande kan endast använda JSON meddelandescheman. Fält som anges i schemat kan vara av följande typer:

* Objekt - serialiseras med JSON
* Binary - serialiseras base64
* Text
* Boolesk
* Integer
* Double-värde
* DateTime

Skicka telemetrimeddelanden med olika intervall genom att lägga till flera typer av telemetri till den `Telemetry` matris. I följande exempel skickar temperatur och fuktighet data var 10 sekunder och tillståndet för ljuset varje minut:

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
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

En simulerad enhet kan svara på moln till enhet metoder som anropas från en IoT-hubb. Den `CloudToDeviceMethods` -avsnittet i enheten modellen schema:

* Definierar de metoder som den simulerade enheten kan svara på.
* Identifierar den JavaScript-fil som innehåller logik för att köra.

Den simulerade enheten skickar listan med metoder som den stöder till IoT-hubb som den är ansluten till.

Läs mer om JavaScript-fil som implementerar beteendet för enheten i [förstå beteendet för enhetsmodellen](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md).

I följande exempel anger tre metoder och JavaScript-filer som implementerar dessa metoder:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
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

Du kan se JavaScript-filer för simulerade standardenheter i den [skriptmappen](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) på GitHub. Enligt konventionen används dessa JavaScript-filer har suffixet **-metoden** att skilja dem från de filer som implementerar tillstånd beteende.