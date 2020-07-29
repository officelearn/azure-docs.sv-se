---
title: Guide för utvecklare – IoT Plug and Play Preview | Microsoft Docs
description: Beskrivning av IoT-Plug and Play för utvecklare
author: rido-min
ms.author: rmpablos
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ef221ea068f2786a4a84f20a29e80dd7176f06c6
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337423"
---
# <a name="iot-plug-and-play-preview-developer-guide"></a>IoT Plug and Play för hands versions guide

Med IoT Plug and Play Preview kan du bygga smarta enheter som annonserar sina funktioner i Azure IoT-program. IoT Plug and Play-enheter kräver ingen manuell konfiguration när en kund ansluter dem till IoT Plug and Play-aktiverade program.

Den här guiden beskriver de grundläggande steg som krävs för att skapa en enhet som följer [IoT plug and Play-konventioner](concepts-convention.md)och tillgängliga REST-API: er som du kan använda för att interagera med enheten.

Följ de här-stegen om du vill bygga en IoT Plug and Play-enhet:

1. Se till att enheten använder antingen MQTT-eller MQTT över WebSockets-protokollet för att ansluta till Azure IoT Hub.
1. Skapa en [DTDL-modell (Digital-definitions språk)](https://github.com/Azure/opendigitaltwins-dtdl) för att beskriva enheten. Mer information finns i [förstå komponenter i IoT plug and Play-modeller](concepts-components.md).
1. Uppdatera enheten för att tillkännage `model-id` som en del av enhets anslutningen.
1. Implementera telemetri, egenskaper och kommandon med [IoT plug and Play konventioner](concepts-convention.md)

När enhets implementeringen är klar kan du använda [Azure IoT Explorer](howto-use-iot-explorer.md) för att kontrol lera att enheten följer IoT plug and Play-konventionerna.

> [!Tip]
> Alla kodfragment i den här artikeln använder C#, men begreppen gäller för alla tillgängliga SDK: er för C, python, Node och Java.

## <a name="model-id-announcement"></a>Meddelande om modell-ID

För att meddela modell-ID måste enheten inkludera den i anslutnings informationen:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

Den nya `ClientOptions` överlagringen är tillgänglig i alla `DeviceClient` metoder som används för att initiera en anslutning.

Modell-ID-meddelandet har lagts till i nästa versioner av SDK: erna

|SDK|Version|
|---|-------|
|C – SDK|1.3.9|
|.NET|1.27.0|
|Java|1.14.0|
|Nod|1.17.0|
|Python|2.1.4|

## <a name="implement-telemetry-properties-and-commands"></a>Implementera telemetri, egenskaper och kommandon

Som beskrivs i [förstå komponenter i IoT plug and Play-modeller](concepts-components.md)måste enhets byggare bestämma om de vill använda komponenter för att beskriva sina enheter. När du använder komponenter måste enheterna följa reglerna som beskrivs i det här avsnittet.

### <a name="telemetry"></a>Telemetri

Modeller utan komponenter kräver ingen särskild egenskap.

När du använder komponenter måste enheterna ange en meddelande egenskap med namnet på komponenten:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await deviceClient.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Skrivskyddade egenskaper

Modeller utan komponenter kräver ingen särskild konstruktion:

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Enheten "delad" uppdateras med nästa rapporterade egenskap:

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

När du använder komponenter måste du skapa egenskaper i komponent namnet:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Enheten "delad" uppdateras med nästa rapporterade egenskap:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Skrivbara egenskaper

Dessa egenskaper kan ställas in av enheten eller uppdateras av lösningen. Om lösningen uppdaterar en egenskap får klienten ett meddelande som ett återanrop i `DeviceClient` . För att följa IoT Plug and Play-konventionerna måste enheten meddela tjänsten att egenskapen har tagits emot.

#### <a name="report-a-writable-property"></a>Rapportera en skrivbar egenskap

När en enhet rapporterar en skrivbar egenskap måste den innehålla de `ack` värden som definieras i konventionerna.

Så här rapporterar du en skrivbar egenskap utan komponenter:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Enheten "delad" uppdateras med nästa rapporterade egenskap:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Om du vill rapportera en skrivbar egenskap från en komponent måste den dubbla innehålla en markör:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Enheten "delad" uppdateras med nästa rapporterade egenskap:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Prenumerera på önskade egenskaps uppdateringar

Tjänster kan uppdatera önskade egenskaper som utlöser ett meddelande på de anslutna enheterna. Det här meddelandet innehåller uppdaterade önskade egenskaper, inklusive versions numret som identifierar uppdateringen. Enheter måste svara med samma `ack` meddelande som rapporterade egenskaper.

Modeller utan komponenter se den enskilda egenskapen och skapa rapporterad `ack` med den mottagna versionen:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Enheten, som visar egenskapen i de önskade och rapporterade avsnitten:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Modeller med komponenter tar emot önskade egenskaper omslutna med komponent namnet och bör rapportera tillbaka den `ack` rapporterade egenskapen:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Enhetens dubbla för komponenter visar de önskade och rapporterade avsnitten enligt följande:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Kommandon

Modeller utan komponenter får kommando namnet som det anropades av tjänsten.

Modeller med komponenter kommer att få det kommando namn som föregås av komponenten och `*` avgränsaren.

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>Nytto laster för begäran och svar

Kommandon använder typer för att definiera sina nytto laster för begäran och svar. En enhet måste deserialisera inkommande indataparameter och serialisera svaret. I följande exempel visas hur du implementerar ett kommando med komplexa typer som definieras i nytto lasterna:

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

Följande kodfragment visar hur en enhet implementerar den här kommando definitionen, inklusive de typer som används för att aktivera serialisering och deserialisering:

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> Namnen på begäran och svaren finns inte i de serialiserade nytto lasterna som överförs via kabeln.

## <a name="interact-with-the-device"></a>Interagera med enheten 

Med IoT-Plug and Play kan du använda enheter som har meddelat sitt modell-ID med IoT-hubben. Du kan till exempel komma åt egenskaperna och kommandona för en enhet direkt.

Om du vill använda en IoT Plug and Play-enhet som är ansluten till din IoT-hubb använder du antingen IoT Hub REST API eller någon av IoT-språksdk: erna. I följande exempel används IoT Hub REST API. Den aktuella versionen av API: t är `2020-05-31-preview` . Lägg `?api-version=2020-05-31` till i dina rest PI-anrop.

Om din termostat-enhet anropas `t-123` får du alla egenskaper för alla gränssnitt som implementerats av enheten med ett REST API GET-anrop:

```REST
GET /digitalTwins/t-123
```

Det här anropet inkluderar JSON `$metadata.$model` -egenskapen med modell-ID: t som visas i enheten.

Alla egenskaper för alla gränssnitt nås med `GET /DigitalTwin/{device-id}` REST API-mallen där `{device-id}` är identifieraren för enheten:

```REST
GET /digitalTwins/{device-id}
```

Du kan anropa kommandon för IoT Plug and Play Device direkt. Om `Thermostat` komponenten i `t-123` enheten har ett `restart` kommando kan du anropa den med ett REST API post-anrop:

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

Oftare kan kommandon anropas med hjälp av den här REST API mallen:

- `device-id`: enhetens identifierare.
- `component-name`: namnet på gränssnittet från avsnittet Implements i enhetens kapacitets modell.
- `command-name`: namnet på kommandot.

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om enhets modellering är här några ytterligare resurser:

- [Digitalt flätat definitions språk (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C (enhets-SDK)](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [Modell komponenter](./concepts-components.md)
