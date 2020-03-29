---
title: Enhetsschema i fjärrövervakningslösning - Azure | Microsoft-dokument
description: I den här artikeln beskrivs JSON-schemat som definierar en simulerad enhet i fjärrövervakningslösningen.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 0f9669d491648ecc621aab27d0908dcc3dc84438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65823333"
---
# <a name="understand-the-device-model-schema"></a>Förstå schemat för enhetsmodellen

Du kan använda simulerade enheter i lösningen för fjärrövervakning för att testa dess beteende. Remote Monitoring-lösningen innehåller en enhetssimuleringstjänst för att köra simulerade enheter. När du distribuerar lösningen för fjärrövervakning etableras en samling simulerade enheter automatiskt. Du kan anpassa befintliga simulerade enheter eller skapa egna.

I den här artikeln beskrivs enhetsmodellschemat som anger funktionerna och beteendet för en simulerad enhet. Enhetsmodellen lagras i en JSON-fil.

> [!NOTE]
> Det här enhetsmodellschemat är endast för simulerade enheter som finns i enhetssimuleringstjänsten. Om du vill skapa en riktig enhet läser du [Anslut enheten till lösningsacceleratorn för fjärrövervakning](iot-accelerators-connecting-devices.md).

Följande artiklar är relaterade till den aktuella artikeln:

* [Implementera enhetsmodellbeteendet](iot-accelerators-remote-monitoring-device-behavior.md) beskriver de JavaScript-filer som du använder för att implementera beteendet hos en simulerad enhet.
* [Skapa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md) sätter ihop allt och visar hur du distribuerar en ny simulerad enhetstyp till din lösning.

I den här artikeln kan du se hur du:

>[!div class="checklist"]
> * Använda en JSON-fil för att definiera en simulerad enhetsmodell
> * Ange vilka egenskaper den simulerade enheten
> * Ange vilken telemetri som den simulerade enheten skickar
> * Ange de metoder från molnet till enheten som enheten svarar på

## <a name="the-parts-of-the-device-model-schema"></a>Delar av enhetsmodellschemat

Varje enhetsmodell, till exempel en kylmaskin eller lastbil, definierar en typ av enhet som simuleringstjänsten kan simulera. Varje enhetsmodell lagras i en JSON-fil med följande schema på den översta nivån:

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

Du kan visa schemafilerna för standardsimulerade enheter i [devicemodels-mappen](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) på GitHub.

I följande tabell beskrivs schemaposterna på den översta nivån:

| Schemapost | Beskrivning |
| -- | --- |
| `SchemaVersion` | Schemaversionen är `1.0.0` alltid och är specifik för filens format. |
| `Id` | Ett unikt ID för den här enhetsmodellen. |
| `Version` | Identifierar versionen av enhetsmodellen. |
| `Name` | Ett eget namn för enhetsmodellen. |
| `Description` | En beskrivning av enhetsmodellen. |
| `Protocol` | Anslutningsprotokollet som enheten använder. Kan vara `AMQP`en `MQTT`av `HTTP`, och . |

I följande avsnitt beskrivs de andra avsnitten i JSON-schemat:

## <a name="simulation"></a>Simulering

I `Simulation` avsnittet definierar du det interna tillståndet för den simulerade enheten. Alla telemetrivärden som skickas av enheten måste vara en del av det här enhetstillståndet.

Definitionen av enhetstillståndet har två element:

* `InitialState`definierar initiala värden för alla egenskaper för enhetstillståndsobjektet.
* `Script`identifierar en JavaScript-fil som körs enligt ett schema för att uppdatera enhetstillståndet. Du kan använda den här skriptfilen för att randomisera telemetrivärden som skickas av enheten.

Mer information om JavaScript-filen som uppdaterar enhetstillståndsobjektet finns [i Förstå enhetsmodellfunktionen](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

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
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

Simuleringstjänsten kör **filen chiller-01-state.js** var femte sekund för att uppdatera enhetstillståndet. Du kan se JavaScript-filerna för standardsimulerade enheter i [skriptmappen](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) på GitHub. Enligt konvention har dessa **JavaScript-filer suffixet -tillstånd** för att skilja dem från de filer som implementerar metodbeteenden.

## <a name="properties"></a>Egenskaper

Avsnittet `Properties` i schemat definierar egenskapsvärdena som enheten rapporterar till lösningen. Ett exempel:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

När lösningen startar frågar den alla simulerade enheter `Type` för att skapa en lista med värden som ska användas i användargränssnittet. Lösningen använder `Latitude` egenskaperna `Longitude` och för att lägga till enhetens placering på kartan på instrumentpanelen.

## <a name="telemetry"></a>Telemetri

Matrisen `Telemetry` visar alla telemetrityper som den simulerade enheten skickar till lösningen.

I följande exempel skickas ett JSON-telemetrimeddelande `floor`var `vibration`10:e sekund med , och `temperature` data från hissens sensorer:

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

`MessageTemplate`definierar strukturen för JSON-meddelandet som skickas av den simulerade enheten. De platshållare `MessageTemplate` som använder `${NAME}` `NAME` syntaxen där är en nyckel från [enhetstillståndsobjektet](#simulation). Strängar bör citeras, bör siffror inte.

`MessageSchema`definierar schemat för meddelandet som skickas av den simulerade enheten. Meddelandeschemat publiceras också i IoT Hub för att göra det möjligt för serverdprogram att återanvända informationen för att tolka den inkommande telemetrin.

För närvarande kan du bara använda JSON-meddelandescheman. Fälten i schemat kan vara av följande typer:

* Objekt - serialiserad med JSON
* Binary - serialiserad med bas64
* Text
* Boolean
* Integer
* Double
* DateTime

Om du vill skicka telemetrimeddelanden med olika intervall lägger `Telemetry` du till flera telemetrityper i matrisen. Följande exempel skickar temperatur- och fuktighetsdata var 10:e sekund och ljusets tillstånd varje minut:

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

En simulerad enhet kan svara på metoder från molnet till enheten som anropas från en IoT-hubb. Avsnittet `CloudToDeviceMethods` i schemafilen för enhetsmodell:

* Definierar de metoder som den simulerade enheten kan svara på.
* Identifierar JavaScript-filen som innehåller logiken som ska köras.

Den simulerade enheten skickar listan över metoder som den stöder till IoT-hubben som den är ansluten till.

Mer information om JavaScript-filen som implementerar enhetens beteende finns i [Förstå enhetsmodellbeteendet](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

I följande exempel anges tre metoder som stöds och de JavaScript-filer som implementerar dessa metoder:

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

Du kan se JavaScript-filerna för standardsimulerade enheter i [skriptmappen](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) på GitHub. Enligt konvention har dessa **JavaScript-filer suffixmetoden** för att skilja dem från de filer som implementerar tillståndsbeteende.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du skapar en egen anpassad simulerad enhetsmodell. Den här artikeln visade hur du:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Använda en JSON-fil för att definiera en simulerad enhetsmodell
> * Ange vilka egenskaper den simulerade enheten
> * Ange vilken telemetri som den simulerade enheten skickar
> * Ange de metoder från molnet till enheten som enheten svarar på

Nu när du har lärt dig om JSON-schemat är det föreslagna nästa steget att lära dig hur du [implementerar beteendet för din simulerade enhet](iot-accelerators-remote-monitoring-device-behavior.md).

Mer information om lösningen för fjärrövervakning finns i:

* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Felsökningsguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
