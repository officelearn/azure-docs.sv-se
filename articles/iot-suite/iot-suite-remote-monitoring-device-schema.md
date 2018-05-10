---
title: Enheten schemat i fjärranslutna övervakningslösning - Azure | Microsoft Docs
description: Den här artikeln beskriver JSON-schema som definierar en simulerad enhet i fjärranslutna övervakningslösning.
services: iot-suite
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
ms.openlocfilehash: 24aeb9c3f73d04a3d05f09ebd2ba0859a38e7ad8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="understand-the-device-model-schema"></a>Förstå modellschemat enhet

Du kan använda simulerade enheterna i fjärranslutna övervakningslösning för att testa sitt beteende. När du distribuerar fjärråtkomst övervakningslösning etableras en samling med simulerade enheter automatiskt. Du kan anpassa befintliga simulerade enheter eller skapa egna.

Den här artikeln beskriver modellschemat enheten som anger funktioner och beteendet för en simulerad enhet. Enhetsmodellen lagras i en JSON-fil.

I följande artiklar är relaterade till den aktuella artikeln:

* [Implementera modellen enhetsbeteende](iot-suite-remote-monitoring-device-behavior.md) beskriver JavaScript-filer som används för att implementera beteendet för en simulerad enhet.
* [Skapa en ny simulerade enhet](iot-suite-remote-monitoring-test.md) placeras alla tillsammans och visar hur du distribuerar en ny typ av simulerade enhet till din lösning.

I den här artikeln kan du se hur du:

>[!div class="checklist"]
> * Använda en JSON-fil för att definiera en simulerad enhetsmodell
> * Ange egenskaperna för den simulerade enheten
> * Ange den simulerade enheten skickar telemetri
> * Ange de moln till enhet metoder enheten svarar

## <a name="the-parts-of-the-device-model-schema"></a>Delar av modellschemat enhet

Varje enhetsmodell, till exempel en kylaggregat eller lastbil, definierar en typ av simulerade enhet kan ansluta till den fjärranslutna övervakningslösning. Varje enhetsmodell lagras i en JSON-fil med följande översta schema:

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

I följande tabell beskrivs posterna översta schemat:

| Schema-post | Beskrivning |
| -- | --- |
| `SchemaVersion` | Schemaversionen är alltid `1.0.0` och är specifik för formatet för den här filen. |
| `Id` | Ett unikt ID för den här modellen. |
| `Version` | Identifierar versionen av enhetsmodellen. |
| `Name` | Ett eget namn för enhetsmodellen. |
| `Description` | En beskrivning av enheten. |
| `Protocol` | Anslutningsprotokoll enheten använder. Kan vara något av `AMQP`, `MQTT`, och `HTTP`. |

I följande avsnitt beskrivs i andra avsnitt i JSON-schema:

## <a name="simulation"></a>Simulering

I den `Simulation` avsnittet kan du definiera det interna tillståndet för den simulerade enheten. Alla telemetri värden som skickas av enheten måste vara en del av den här enhetens tillstånd.

Definitionen av enhetens tillstånd har två element:

* `InitialState` definierar ursprungliga värden för alla egenskaper i objektet enhetens tillstånd.
* `Script` identifierar en JavaScript-fil som körs på ett schema för att uppdatera enhetens tillstånd. Du kan använda den här skriptfilen för att Slumpa telemetri-värden som skickas av enheten.

Läs mer om JavaScript-fil som uppdaterar tillstånd enhetsobjekt i [förstå beteendet enheten modellen](iot-suite-remote-monitoring-device-behavior.md).

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
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

Simuleringen tjänsten körs på **kylaggregat-01-state.js** filen var femte sekund om du vill uppdatera enhetens tillstånd. Du kan se JavaScript-filer för simulerade standardenheter i den [skriptmappen](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) på GitHub. Enligt konventionen är dessa JavaScript-filer som har suffixet **-tillstånd** att skilja dem från de filer som implementera metoden beteenden.

## <a name="properties"></a>Egenskaper

Den `Properties` delen av schemat definierar egenskapsvärden enheten rapporterar till lösningen. Exempel:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

När lösningen har startat frågar simulerade enheterna för att skapa en lista över `Type` värden som ska användas i Användargränssnittet. Lösningen använder den `Latitiude` och `Longitude` egenskaper för att lägga till platsen för enheten i kartan på instrumentpanelen.

## <a name="telemetry"></a>Telemetri

Den `Telemetry` matris visar en lista över alla telemetri typer simulerade enheten skickar till lösningen.

I följande exempel skickar meddelandet JSON telemetri var 10: e sekund med `floor`, `vibration`, och `temperature` data från den snabba sensorer:

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

`MessageTemplate` definierar strukturen i JSON-meddelande som skickas av den simulerade enheten. Platshållare i `MessageTemplate` Använd syntax `${NAME}` där `NAME` är en nyckel från den [tillstånd enhetsobjekt](#simulation). Ska innehålla citattecken strängar, siffror bör inte.

`MessageSchema` definierar schemat för meddelanden från den simulerade enheten. Meddelandet schemat publiceras också IoT-hubb att backend-programmen kan använda informationen för att tolka inkommande telemetri.

För närvarande kan du bara använda JSON meddelandescheman. Fälten som anges i schemat kan vara av följande typer:

* Objekt - serialiseras med JSON
* Binär - serialiseras base64
* Text
* Boolesk
* Integer
* Dubbel
* DateTime

Skicka telemetri meddelanden med olika intervall genom att lägga till flera telemetri-typer till den `Telemetry` matris. I följande exempel skickar temperatur- och fuktighetskonsekvens data var 10 sekunder och tillståndet för enstaka minuten:

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

En simulerad enhet kan svara på moln till enhet metoder anropas från fjärranslutna övervakningslösning. Den `CloudToDeviceMethods` -avsnittet i enheten modellen schemat:

* Definierar metoder som den simulerade enheten kan svara.
* Identifierar den JavaScript-fil som innehåller logiken som att köra.

Den simulerade enheten skickar listan över metoder som stöds till fjärranslutna övervakningslösning.

Läs mer om den JavaScript-fil som implementerar beteendet för enheten i [förstå beteendet enheten modellen](iot-suite-remote-monitoring-device-behavior.md).

I följande exempel anger tre metoder och JavaScript-filer som använder dessa metoder:

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

Du kan se JavaScript-filer för simulerade standardenheter i den [skriptmappen](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) på GitHub. Enligt konventionen är dessa JavaScript-filer som har suffixet **-metoden** att skilja dem från de filer som implementerar tillstånd beteende.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs hur du skapar dina egna anpassade simulerade enhetsmodell. Den här artikeln visar dig hur till:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Använda en JSON-fil för att definiera en simulerad enhetsmodell
> * Ange egenskaperna för den simulerade enheten
> * Ange den simulerade enheten skickar telemetri
> * Ange de moln till enhet metoder enheten svarar

Nu du har lärt dig om JSON-schema, föreslagna nästa steg är att lära dig hur du [implementera beteendet för den simulerade enheten](iot-suite-remote-monitoring-device-behavior.md).

Utvecklare om remote övervakningslösning, Läs mer:

* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Felsökningsguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->