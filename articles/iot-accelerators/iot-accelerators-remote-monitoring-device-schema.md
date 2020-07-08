---
title: Enhets schema i fjärr styrnings lösningen – Azure | Microsoft Docs
description: I den här artikeln beskrivs JSON-schemat som definierar en simulerad enhet i lösningen för fjärrövervakning.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ac681bb13ccea49c7a2f566a6fcdb6adb8cec5bb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81683750"
---
# <a name="understand-the-device-model-schema"></a>Förstå schemat för enhetsmodellen

Du kan använda simulerade enheter i lösningen för fjärrövervakning för att testa beteendet. Lösningen för fjärrövervakning innehåller en tjänst för enhets simulering för att köra simulerade enheter. När du distribuerar lösningen för fjärrövervakning allokeras en samling simulerade enheter automatiskt. Du kan anpassa befintliga simulerade enheter eller skapa egna.

I den här artikeln beskrivs enhets modellens schema som anger funktioner och beteende för en simulerad enhet. Enhets modellen lagras i en JSON-fil.

> [!NOTE]
> Det här enhets modell schemat är bara för simulerade enheter som finns i enhets simulerings tjänsten. Om du vill skapa en riktig enhet kan du läsa [Anslut din enhet till-acceleratorn för fjärr styrning](iot-accelerators-connecting-devices.md).

Följande artiklar är relaterade till den aktuella artikeln:

* [Implementera enhets modell beteendet](iot-accelerators-remote-monitoring-device-behavior.md) beskriver de JavaScript-filer som används för att implementera beteendet för en simulerad enhet.
* Genom att [skapa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md) ser du allt tillsammans och visar hur du distribuerar en ny simulerad enhets typ till din lösning.

I den här artikeln kan du se hur du:

>[!div class="checklist"]
> * Använd en JSON-fil för att definiera en simulerad enhets modell
> * Ange de egenskaper som den simulerade enheten
> * Ange telemetri som den simulerade enheten skickar
> * Ange de metoder för moln-till-enhet som enheten svarar på

## <a name="the-parts-of-the-device-model-schema"></a>Delarna i enhets modellens schema

Varje enhets modell, till exempel en kyl-eller Last bil, definierar en typ av enhet som simulerings tjänsten kan simulera. Varje enhets modell lagras i en JSON-fil med följande schema på den översta nivån:

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

Du kan visa Schemafiler för de simulerade standard enheterna i [mappen devicemodels](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) på GitHub.

I följande tabell beskrivs schema posterna på högsta nivån:

| Schema post | Beskrivning |
| -- | --- |
| `SchemaVersion` | Schema versionen är alltid `1.0.0` och är speciell för filens format. |
| `Id` | Ett unikt ID för den här enhets modellen. |
| `Version` | Identifierar versionen för enhets modellen. |
| `Name` | Ett eget namn för enhets modellen. |
| `Description` | En beskrivning av enhets modellen. |
| `Protocol` | Anslutnings protokollet som enheten använder. Kan vara en av `AMQP` , `MQTT` och `HTTP` . |

I följande avsnitt beskrivs de andra avsnitten i JSON-schemat:

## <a name="simulation"></a>Simulering

I `Simulation` avsnittet definierar du det interna läget för den simulerade enheten. Alla telemetri värden som skickas av enheten måste vara en del av det här enhets läget.

Definitionen av enhetens tillstånd har två element:

* `InitialState`definierar initial värden för alla egenskaper för objektet enhets tillstånd.
* `Script`identifierar en JavaScript-fil som körs enligt ett schema för att uppdatera enhetens tillstånd. Du kan använda den här skript filen för att slumpmässiga de telemetri-värden som skickas av enheten.

Om du vill veta mer om JavaScript-filen som uppdaterar enhetens tillstånds objekt, se [förstå enhets modellens beteende](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

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
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

Simulerings tjänsten kör **chiller-01-state.js** -filen var femte sekund för att uppdatera enhetens tillstånd. Du kan se JavaScript-filerna för de simulerade standard enheterna i [mappen Scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) på GitHub. I konvention har dessa JavaScript **-** filer suffix för att skilja dem från filerna som implementerar metod beteenden.

## <a name="properties"></a>Egenskaper

I- `Properties` delen av schemat definieras de egenskaps värden som enheten rapporterar till lösningen. Ett exempel:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

När lösningen startar frågar den upp alla simulerade enheter för att bygga en lista över `Type` värden som ska användas i användar gränssnittet. Lösningen använder- `Latitude` och- `Longitude` egenskaperna för att lägga till enhetens plats till kartan på instrument panelen.

## <a name="telemetry"></a>Telemetri

I `Telemetry` matrisen visas alla typer av telemetri som den simulerade enheten skickar till lösningen.

I följande exempel skickas ett JSON-telemetri var 10: e sekund med `floor` , `vibration` och `temperature` data från hissens sensorer:

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

`MessageTemplate`definierar strukturen för JSON-meddelandet som skickas av den simulerade enheten. Plats hållarna i `MessageTemplate` använder syntaxen `${NAME}` där `NAME` är en nyckel från [objektet enhets tillstånd](#simulation). Strängar ska anges i citat tecken.

`MessageSchema`definierar schemat för det meddelande som skickas av den simulerade enheten. Meddelande schemat publiceras också till IoT Hub för att göra det möjligt för Server dels program att återanvända informationen för att tolka inkommande telemetri.

För närvarande kan du bara använda JSON-meddelande scheman. Fälten som visas i schemat kan vara av följande typer:

* Objekt – serialiserat med JSON
* Binärt serialiserat med base64
* Text
* Boolesk
* Integer
* Double
* DateTime

Om du vill skicka telemetri med olika intervall lägger du till flera typer av telemetri i `Telemetry` matrisen. I följande exempel skickas temperatur-och fuktighets data var 10: e sekund och status för ljuset varje minut:

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

En simulerad enhet kan svara på moln-till-enhet-metoder som anropas från en IoT-hubb. `CloudToDeviceMethods`Avsnittet i enhets modellens schema fil:

* Definierar de metoder som den simulerade enheten kan svara på.
* Identifierar den JavaScript-fil som innehåller den logik som ska köras.

Den simulerade enheten skickar listan över metoder som den stöder till IoT-hubben som den är ansluten till.

Mer information om JavaScript-filen som implementerar enhetens funktions sätt finns i [förstå enhets modellens beteende](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

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

Du kan se JavaScript-filerna för de simulerade standard enheterna i [mappen Scripts](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) på GitHub. I konvention har dessa JavaScript-filer suffixet **-metod** för att skilja dem från filerna som implementerar tillstånds beteendet.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du skapar en egen anpassad simulerad enhets modell. I den här artikeln har du visat hur du:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Använd en JSON-fil för att definiera en simulerad enhets modell
> * Ange de egenskaper som den simulerade enheten
> * Ange telemetri som den simulerade enheten skickar
> * Ange de metoder för moln-till-enhet som enheten svarar på

Nu när du har lärt dig om JSON-schemat är det föreslagna nästa steg att lära dig hur du [implementerar beteendet för den simulerade enheten](iot-accelerators-remote-monitoring-device-behavior.md).

Mer information om utvecklings lösningen finns i:

* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Felsökningsguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
