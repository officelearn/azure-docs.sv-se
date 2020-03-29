---
title: Utvecklarguide - IoT Plug and Play Preview | Microsoft-dokument
description: Beskrivning av enhetsmodellering för IoT Plug and Play-utvecklare
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605216"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>Utvecklarguide för IoT Plug and Play Preview-modellering

Med IoT Plug and Play Preview kan du skapa enheter som annonserar deras funktioner till Azure IoT-program. IoT Plug and Play-enheter kräver inte manuell konfiguration när en kund ansluter dem till IoT Plug and Play-aktiverade program. IoT Central är ett exempel på ett IoT Plug and Play-aktiverat program.

Om du vill skapa en IoT Plug and Play-enhet måste du skapa en enhetsbeskrivning. Beskrivningen görs med ett enkelt definitionsspråk som kallas Digital Twins Definition Language (DTDL).

## <a name="device-capability-model"></a>Modell för enhetskapacitet

Med DTDL skapar du en _enhetskapacitetsmodell_ för att beskriva enhetens delar. En typisk IoT-enhet består av:

- Anpassade delar, som är de saker som gör din enhet unik.
- Standarddelar, som är saker som är gemensamma för alla enheter.

Dessa delar kallas _gränssnitt_ i en enhetskapacitetsmodell. Gränssnitt definierar information om varje del som enheten implementerar.

I följande exempel visas enhetskapacitetsmodellen för en termostatenhet:

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

En kapacitetsmodell har några obligatoriska fält:

- `@id`: ett unikt ID i form av ett enkelt enhetligt resursnamn.
- `@type`: deklarerar att det här objektet är en kapacitetsmodell.
- `@context`: anger den DTDL-version som används för kapacitetsmodellen.
- `implements`: visar de gränssnitt som enheten implementerar.

Varje post i listan över gränssnitt i avsnittet implementerar har en:

- `name`: Gränssnittets programmeringsnamn.
- `schema`: gränssnittet som kapacitetsmodellen implementerar.

Det finns ytterligare valfria fält som du kan använda för att lägga till mer information till kapacitetsmodellen, till exempel visningsnamn och beskrivning. Gränssnitt som deklareras i en kapacitetsmodell kan ses som komponenter i enheten. För offentlig förhandsversion kan gränssnittslistan bara ha en post per schema.

## <a name="interface"></a>Gränssnitt

Med DTDL beskriver du enhetens funktioner med hjälp av gränssnitt. Gränssnitt beskriver _egenskaper,_ _telemetri_och _kommandon_ som en del av enheten implementerar:

- `Properties`. Egenskaper är datafält som representerar enhetens tillstånd. Använd egenskaper för att representera enhetens hållbara tillstånd, till exempel en kylvätskepumps på-off-tillstånd. Egenskaper kan också representera grundläggande enhetsegenskaper, till exempel enhetens version av den inbyggda programvaran. Du kan deklarera egenskaper som skrivskyddade eller skrivbara.
- `Telemetry`. Telemetrifält representerar mätningar från sensorer. När enheten mäter en sensor bör den skicka en telemetrihändelse som innehåller sensordata.
- `Commands`. Kommandon representerar metoder som användare av enheten kan köra på enheten. Till exempel ett återställningskommando eller ett kommando för att slå på eller stänga av en fläkt.

I följande exempel visas gränssnittet för en termostatenhet:

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Ett gränssnitt har några obligatoriska fält:

- `@id`: ett unikt ID i form av ett enkelt enhetligt resursnamn.
- `@type`: deklarerar att det här objektet är ett gränssnitt.
- `@context`: anger den DTDL-version som används för gränssnittet.
- `contents`: visar de egenskaper, telemetri och kommandon som utgör enheten.

I det här enkla exemplet finns det bara ett enda telemetrifält. En minimal fältbeskrivning har en:

- `@type`: anger vilken typ `Telemetry`av `Property`kapacitet: , , eller `Command`.
- `name`: anger namnet på telemetrivärdet.
- `schema`: anger datatypen för telemetrin. Det här värdet kan vara en primitiv typ, till exempel dubbel, heltal, boolesk eller sträng. Komplexa objekttyper, matriser och kartor stöds också.

Andra valfria fält, till exempel visningsnamn och beskrivning, gör att du kan lägga till mer information i gränssnittet och funktionerna.

### <a name="properties"></a>Egenskaper

Som standard är egenskaperna skrivskyddade. Skrivskyddade egenskaper innebär att enheten rapporterar egenskapsvärdeuppdateringar till din IoT-hubb. IoT-hubben kan inte ange värdet för en skrivskyddad egenskap.

Du kan också markera en egenskap som skrivbar i ett gränssnitt. En enhet kan ta emot en uppdatering till en skrivbar egenskap från din IoT-hubb samt rapportera egenskapsvärdeuppdateringar till din hubb.

Enheter behöver inte vara anslutna till angivna egenskapsvärden. De uppdaterade värdena överförs när enheten ansluter nästa gång till navet. Detta gäller både skrivskyddade och skrivbara egenskaper.

Använd inte egenskaper för att skicka telemetri från enheten. En skrivskyddsegenskap som `temperatureSetting=80` ska till exempel innebära att enhetstemperaturen har ställts in på 80 och att enheten försöker komma till eller stanna kvar vid den här temperaturen.

För skrivbara egenskaper returnerar enhetsprogrammet en önskad tillståndsstatuskod, version och beskrivning för att ange om det tog emot och tillämpade egenskapsvärdet.

### <a name="telemetry"></a>Telemetri

Som standard dirigerar IoT Hub alla telemetrimeddelanden från enheter till den [inbyggda tjänstinriktade slutpunkten **(meddelanden/händelser)**](../iot-hub/iot-hub-devguide-messages-read-builtin.md) som är kompatibel med [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/).

Du kan använda [IoT Hubs anpassade slutpunkter och routningsregler](../iot-hub/iot-hub-devguide-messages-d2c.md) för att skicka telemetri till andra mål, till exempel blob-lagring eller andra händelsehubbar. Routningsregler använder meddelandeegenskaper för att markera meddelanden.

### <a name="commands"></a>Kommandon

Kommandon är antingen synkrona eller asynkrona. Ett synkront kommando måste köras inom 30 sekunder som standard och enheten måste vara ansluten när kommandot anländer. Om enheten svarar i tid, eller om enheten inte är ansluten, misslyckas kommandot.

Använd asynkrona kommandon för tidskrävande åtgärder. Enheten skickar förloppsinformation med hjälp av telemetrimeddelanden. Dessa förloppsmeddelanden har följande rubrikegenskaper:

- `iothub-command-name`: Kommandonamnet, till `UpdateFirmware`exempel .
- `iothub-command-request-id`: det begärande-ID som genereras på serversidan och skickas till enheten i det första samtalet.
- `iothub-interface-id`: ID för gränssnittet som det här `urn:example:AssetTracker:1`kommandot har definierats på, till exempel .
 `iothub-interface-name`: förekomstnamnet på det `myAssetTracker`här gränssnittet, till exempel .
- `iothub-command-statuscode`: statuskoden som returneras från `202`enheten, till exempel .

## <a name="register-a-device"></a>Registrera en enhet

IoT Plug and Play gör det enkelt att annonsera enhetens funktioner. Med IoT Plug and Play måste du registrera enhetens kapacitetsmodell när enheten har anslutits till IoT Hub. Registrering gör det möjligt för kunder att använda IoT Plug and Play-funktionerna på din enhet.

Den här guiden visar hur du registrerar en enhet med Azure IoT Device SDK för C.

För varje gränssnitt som enheten implementerar måste du skapa ett gränssnitt och ansluta det till dess implementering.

För termostatgränssnittet som visats tidigare, med hjälp av C SDK, skapar och ansluter du termostatgränssnittet till dess implementering:

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

Upprepa den här koden för varje gränssnitt som enheten implementerar.

När du har skapat ett gränssnitt registrerar du enhetens kapacitetsmodell och gränssnitt med din IoT-hubb:

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaces, 2,
    null, null);
```

## <a name="use-a-device"></a>Använda en enhet

Med IoT Plug and Play kan du använda enheter som har registrerat sina funktioner med din IoT-hubb. Du kan till exempel komma åt egenskaperna och kommandona för en enhet direkt.

Om du vill använda en IoT Plug and Play-enhet som är ansluten till din IoT-hubb använder du antingen IoT Hub REST API eller en av IoT-språk-SDK:erna. I följande exempel används IoT Hub REST API. Den aktuella versionen `2019-07-01-preview`av API:et är . Lägg `?api-version=2019-07-01-preview` till dina REST PI-samtal.

För att få värdet av en enhetsegenskap, till exempel firmwareversionen (`fwVersion`) i `DeviceInformation` gränssnittet i termostaten, använder du det digitala tvillingarna REST API.

Om termostatenheten `t-123`anropas får du alla egenskaper på alla gränssnitt som implementeras av enheten med ett REST API GET-anrop:

```REST
GET /digitalTwins/t-123/interfaces
```

Mer allmänt är att alla egenskaper på alla gränssnitt `{device-id}` används med den här REST API-mallen där enhetens identifierare är identifieraren:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Om du känner till namnet `deviceInformation`på gränssnittet, till exempel , och vill hämta egenskaper för det specifika gränssnittet, scope begäran till ett visst gränssnitt efter namn:

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

Mer allmänt kan egenskaper för ett visst gränssnitt nås `device-id` via den här REST `{interface-name}` API-mallen där är identifieraren för enheten och är namnet på gränssnittet:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

Du kan ringa IoT Plug and Play-enhetskommandon direkt. Om `Thermostat` gränssnittet i `t-123` enheten `restart` har ett kommando kan du anropa det med ett REST API POST-anrop:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

Mer allmänt kan kommandon anropas via den här REST API-mallen:

- `device-id`: identifieraren för enheten.
- `interface-name`: namnet på gränssnittet från avsnittet implementerar i enhetskapacitetsmodellen.
- `command-name`: Namnet på kommandot.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om enhetsmodellering finns det här några ytterligare resurser:

- [Digitalt tvillingdefinitionsspråk (DTDL)](https://aka.ms/DTDL)
- [C (enhets-SDK)](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
