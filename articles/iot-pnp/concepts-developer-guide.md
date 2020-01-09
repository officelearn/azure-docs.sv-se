---
title: Guide för utvecklare – IoT Plug and Play Preview | Microsoft Docs
description: Beskrivning av enhets modellering för IoT Plug and Play-utvecklare
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 21001a22be6297595658ff347e9f8a7b04bb6faf
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531402"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>IoT Plug and Play Preview Modeling Developer Guide

Med IoT Plug and Play Preview kan du bygga enheter som annonserar sina funktioner i Azure IoT-program. IoT Plug and Play-enheter kräver ingen manuell konfiguration när en kund ansluter dem till IoT Plug and Play-aktiverade program. IoT Central är ett exempel på ett IoT Plug and Play-aktiverat program.

Om du vill skapa en IoT Plug and Play-enhet måste du skapa en enhets beskrivning. Beskrivningen görs med ett enkelt definitions språk som kallas DTDL (Digital flätad Definition Language).

## <a name="device-capability-model"></a>Enhets kapacitets modell

Med DTDL skapar du en _enhets kapacitets modell_ som beskriver delar av enheten. En typisk IoT-enhet består av:

- Anpassade delar, vilka är de saker som gör din enhet unik.
- Standard delar, som är saker som är gemensamma för alla enheter.

Dessa delar kallas _gränssnitt_ i en enhets kapacitets modell. Gränssnitt definierar information om varje del din enhet implementerar.

I följande exempel visas enhets kapacitets modellen för en termostat-enhet:

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
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

En kapacitets modell har vissa obligatoriska fält:

- `@id`: ett unikt ID i form av ett enkelt, enhetligt resurs namn.
- `@type`: deklarerar att det här objektet är en kapacitets modell.
- `@context`: anger den DTDL-version som används för kapacitets modellen.
- `implements`: visar de gränssnitt som enheten implementerar.

Varje post i listan över gränssnitt i avsnittet implementeringar har en:

- `name`: gränssnittets programmerings namn.
- `schema`: det gränssnitt som funktions modellen implementerar.

Det finns ytterligare valfria fält som du kan använda för att lägga till mer information i kapacitets modellen, till exempel visnings namn och beskrivning. Gränssnitt som är deklarerade i en kapacitets modell kan betraktas som komponenter i enheten. För en offentlig för hands version kan gränssnitts listan bara ha en post per schema.

## <a name="interface"></a>Gränssnitt

Med DTDL beskriver du funktionerna för enheten med hjälp av gränssnitt. Gränssnitt beskriver _Egenskaper_, _telemetri_och _kommandon_ som en del av din enhet implementerar:

- `Properties`. Egenskaper är data fält som representerar enhetens tillstånd. Använd egenskaper för att representera enhetens varaktiga tillstånd, till exempel vid inaktive ring av en kyl pump. Egenskaper kan också representera grundläggande enhets egenskaper, till exempel enhetens inbyggda program vara. Du kan deklarera egenskaper som skrivskyddade eller skrivbara.
- `Telemetry`. Telemetridata representerar mått från sensorer. När din enhet tar ett sensor mått bör den skicka en telemetri-händelse som innehåller sensor data.
- `Commands`. Kommandon representerar metoder som användarna av enheten kan köra på enheten. Till exempel ett återställnings kommando eller ett kommando för att aktivera eller inaktivera en fläkt.

I följande exempel visas gränssnittet för en termostat-enhet:

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

Ett gränssnitt har vissa obligatoriska fält:

- `@id`: ett unikt ID i form av ett enkelt, enhetligt resurs namn.
- `@type`: deklarerar att objektet är ett gränssnitt.
- `@context`: anger den DTDL-version som används för gränssnittet.
- `contents`: Visar egenskaper, telemetri och kommandon som utgör din enhet.

I det här enkla exemplet finns det bara ett enda telemetri-fält. En minimal fält Beskrivning har:

- `@type`: anger typen av funktion: `Telemetry`, `Property`eller `Command`.
- `name`: anger namnet på telemetri-värdet.
- `schema`: anger telemetrins datatyp. Det här värdet kan vara en primitiv typ, till exempel Double, Integer, Boolean eller String. Komplexa objekt typer, matriser och Maps stöds också.

Andra valfria fält, till exempel visnings namn och beskrivning, gör att du kan lägga till mer information i gränssnittet och funktionerna.

### <a name="properties"></a>Egenskaper

Som standard är egenskaperna skrivskyddade. Skrivskyddade egenskaper innebär att enhets rapportens egenskaps värde uppdateras till din IoT Hub. Din IoT Hub kan inte ange värdet för en skrivskyddad egenskap.

Du kan också markera en egenskap som skrivbar i ett gränssnitt. En enhet kan ta emot en uppdatering av en skrivbar egenskap från din IoT-hubb samt rapportering av egenskaps värde uppdateringar till hubben.

Enheterna behöver inte vara anslutna för att ange egenskaps värden. De uppdaterade värdena överförs när enheten ansluter till hubben. Det här beteendet gäller både skrivskyddade och skrivbara egenskaper.

Använd inte egenskaper för att skicka telemetri från enheten. Till exempel kan en ReadOnly-egenskap, till exempel `temperatureSetting=80`, betyda att enhetens temperatur har angetts till 80 och att enheten försöker nå eller stanna vid den här temperaturen.

För skrivbara egenskaper, returnerar enhets programmet en önskad status kod, version och beskrivning för att indikera om den har tagit emot och applicerat egenskap svärdet.

### <a name="telemetry"></a>Telemetri

Som standard dirigerar IoT Hub alla telemetri-meddelanden från enheter till den inbyggda slut punkten för service som är aktive rads [(**meddelanden/händelser**)](../iot-hub/iot-hub-devguide-messages-read-builtin.md) som är kompatibel med [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/).

Du kan använda [IoT Hub anpassade slut punkter och regler för routning](../iot-hub/iot-hub-devguide-messages-d2c.md) för att skicka telemetri till andra destinationer, till exempel Blob Storage eller andra händelse nav. Regler för routning använder meddelande egenskaper för att välja meddelanden.

### <a name="commands"></a>Kommandon

Kommandon är antingen synkrona eller asynkrona. Ett synkront kommando måste köras inom 30 sekunder som standard och enheten måste vara ansluten när kommandot anländer. Om enheten svarar i tid, eller om enheten inte är ansluten, Miss lyckas kommandot.

Använd asynkrona kommandon för långvariga åtgärder. Enheten skickar förlopps information med telemetri-meddelanden. Dessa förlopps meddelanden har följande huvud egenskaper:

- `iothub-command-name`: kommando namnet, till exempel `UpdateFirmware`.
- `iothub-command-request-id`: det begär ande-ID som genereras på Server sidan och skickas till enheten i det första anropet.
- `iothub-interface-id`: ID för gränssnittet som det här kommandot är definierat på, till exempel `urn:example:AssetTracker:1`.
 `iothub-interface-name`: instans namnet för det här gränssnittet, till exempel `myAssetTracker`.
- `iothub-command-statuscode`: den status kod som returneras från enheten, till exempel `202`.

## <a name="register-a-device"></a>Registrera en enhet

IoT Plug and Play gör det enkelt att annonsera enhetens funktioner. Med IoT Plug and Play måste du registrera din enhets kapacitets modell när enheten ansluter till IoT Hub. Registreringen gör det möjligt för kunder att använda IoT Plug and Play funktioner på enheten.

Den här guiden visar hur du registrerar en enhet med hjälp av Azure IoT-enhetens SDK för C.

För varje gränssnitt som enheten implementerar måste du skapa ett gränssnitt och ansluta det till dess implementering.

För termostat-gränssnittet som visas tidigare, med hjälp av C SDK, skapar du och ansluter ditt termostat-gränssnitt till implementeringen:

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

När du har skapat ett gränssnitt registrerar du enhetens kapacitets modell och gränssnitt med IoT-hubben:

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaceHandleList, 2,
    null, null);
```

## <a name="use-a-device"></a>Använd en enhet

Med IoT-Plug and Play kan du använda enheter som har registrerat sina funktioner med IoT Hub. Du kan till exempel komma åt egenskaperna och kommandona för en enhet direkt.

Om du vill använda en IoT Plug and Play-enhet som är ansluten till din IoT-hubb använder du antingen IoT Hub REST API eller någon av IoT-språksdk: erna. I följande exempel används IoT Hub REST API.

Om du vill hämta värdet för en enhets egenskap, t. ex. den inbyggda program varan (`fwVersion`) i `DeviceInformation`-gränssnittet i termostat, använder du de digitala dubbla REST API.

Om din termostat-enhet kallas `t-123`får du alla egenskaper som implementeras av enheten med ett REST API GET-anrop:

```REST
GET /digitalTwins/t-123/interfaces
```

I allmänhet är alla egenskaper tillgängliga med den här REST API-mallen där `{device-id}` är identifieraren för enheten:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Om du känner till namnet på gränssnittet och vill hämta egenskaper för det just det gränssnittet, kan du ange en definition av begäran till ett särskilt gränssnitt efter namn:

```REST
GET /digitalTwins/t-123/interfaces/info
```

I allmänhet kan egenskaper för ett särskilt gränssnitt nås via den här REST API-mallen där `device-id` är identifieraren för enheten och `{interface-name}` är namnet på gränssnittet:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

Du kan anropa kommandon för IoT Plug and Play Device direkt. Om `Thermostat`-gränssnittet i `t-123`s enheten har ett `restart`-kommando kan du anropa det med ett REST API POST-anrop:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

Oftare kan kommandon anropas med hjälp av den här REST API mallen:

- `device-id`: enhetens identifierare.
- `interface-name`: namnet på gränssnittet från avsnittet Implements i enhetens kapacitets modell.
- `command-name`: namnet på kommandot.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om enhets modellering är här några ytterligare resurser:

- [Digitalt elektroniskt definitions språk (DTDL)](https://aka.ms/DTDL)
- [C-enhets-SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](https://docs.microsoft.com/rest/api/iothub/device)
