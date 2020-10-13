---
title: Guide till service Developer – IoT Plug and Play | Microsoft Docs
description: Beskrivning av IoT-Plug and Play för tjänst utvecklare
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 2a61eefc9c065253bdac11665f0135e493584c0d
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945115"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT Plug and Play service Developer Guide

Med IoT-Plug and Play kan du bygga smarta enheter som annonserar sina funktioner i Azure IoT-program. IoT Plug and Play-enheter kräver ingen manuell konfiguration när en kund ansluter dem till IoT Plug and Play-aktiverade program.

Med IoT-Plug and Play kan du använda enheter som har meddelat sitt modell-ID med IoT-hubben. Du kan till exempel komma åt egenskaperna och kommandona för en enhet direkt.

Om du vill använda en IoT Plug and Play-enhet som är ansluten till din IoT-hubb använder du antingen en av IoT-tjänstens SDK: er eller IoT Hub REST API:

## <a name="service-sdks"></a>Tjänst-SDK:er

Använd Azure IoT service SDK: er i din lösning för att interagera med enheter och moduler. Du kan till exempel använda tjänst-SDK: erna för att läsa och uppdatera dubbla egenskaper och anropa kommandon. Språk som stöds är C#, Java, Node.js och python.

Med tjänst-SDK: er kan du komma åt enhets information från en lösning, till exempel ett skriv bord eller ett webb program. Tjänst-SDK: erna innehåller två namn rymder och objekt modeller som du kan använda för att hämta modell-ID:

- IoT Hub-tjänstens klient. I den här tjänsten exponeras modell-ID: t som en enhet med dubbla egenskaper.

- Klient för Digitals sammanflätade tjänster. Det nya digitala interformat-API: et fungerar på DTDL-modell konstruktioner [(Digitals definitions språk)](concepts-digital-twin.md) som komponenter, egenskaper och kommandon. De digitala dubbla API: erna gör det enklare för lösnings byggare att skapa IoT Plug and Play-lösningar.

| Plattform | IoT Hub tjänst klient | Klient för Digitals dubbla tjänster |
| -------- | ---------------------- | ---------------------------- |
| .NET     | [Dokumentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices) <br/> [Exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples)| [Exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/DigitalTwinClientSamples) |
| Java     | [Dokumentation](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?view=azure-java-stable&preserve-view=true) <br/> [Exempel](https://github.com/Azure/azure-iot-sdk-java/blob/master/service/iot-service-samples/pnp-service-sample)| [Exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/digitaltwin-service-samples) |
| Node.js  | [Dokumentation](https://docs.microsoft.com/javascript/api/azure-iothub/twin?view=azure-node-latest&preserve-view=true) <br/> [Urvalsundersökningar](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)| [Dokumentation](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/?view=azure-node-latest&preserve-view=true) <br/> [Urvalsundersökningar](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js) |
| Python   | [Dokumentation](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?view=azure-python&preserve-view=true) <br/> [Urvalsundersökningar](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/iothub_registry_manager_method_sample.py)| [Dokumentation](https://docs.microsoft.com/python/api/azure-iot-hub/azure.iot.hub.iothubdigitaltwinmanager?view=azure-python&preserve-view=true) <br/> [Urvalsundersökningar](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/samples/get_digital_twin_sample.py) |

## <a name="iot-hub-service-client-examples"></a>Exempel på IoT Hub tjänst klient

I det här avsnittet visas C#-exempel med hjälp av IoT Hub tjänst klienten och **RegistryManager** -och **ServiceClient** -klasserna. Du kan använda **RegistryManager** -klassen för att interagera med enhetens tillstånd med hjälp av enhets dubbla. Du kan också använda klassen **RegistryManager** för att [fråga efter enhets registreringar](..\iot-hub\iot-hub-devguide-query-language.md) i din IoT Hub. Du använder klassen **ServiceClient** för att anropa kommandon på enheten. [DTDL](concepts-digital-twin.md) -modellen för enheten definierar de egenskaper och kommandon som enheten implementerar. I kodfragmenten `deviceTwinId` innehåller variabeln enhets-ID för iot plug and Play-enheten som registrerats med IoT Hub.

### <a name="get-the-device-twin-and-model-id"></a>Hämta enhetens dubbla och modell-ID

För att hämta enhetens dubbla och modell-ID för IoT Plug and Play-enheten som är ansluten till din IoT-hubb:

```csharp
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(parameters.HubConnectionString);

Twin twin = await registryManager.GetTwinAsync(deviceTwinId);
Console.WriteLine($"Device twin: \n{JsonConvert.SerializeObject(twin, Formatting.Indented)}");
Console.WriteLine($"Model ID: {twin.ModelId}.");
```

### <a name="update-device-twin"></a>Uppdatera enhet, dubbla

Följande kodfragment visar hur du uppdaterar `targetTemperature` egenskapen på en enhet. Exemplet visar hur du måste hämta den dubbla appen `ETag` innan du uppdaterar den. Egenskapen definieras i standard komponenten för enheten:

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

// Update the twin
var twinPatch = new Twin();
twinPatch.Properties.Desired["targetTemperature"] = desiredTargetTemperature;

Console.WriteLine($"Update the targetTemperature property to {desiredTargetTemperature}.");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);
```

Följande fragment visar hur du uppdaterar `targetTemperature` egenskapen för en komponent. Exemplet visar hur du måste hämta den dubbla appen `ETag` innan du uppdaterar den. Egenskapen definieras i **Thermostat1** -gränssnittet:

```csharp
Twin twin = await registryManager.GetTwinAsync(deviceTwinId);

int desiredTargetTemperature = 60;

var twinPatch = CreatePropertyPatch("targetTemperature", desiredTargetTemperature, "thermostat1");

await registryManager.UpdateTwinAsync(deviceTwinId, twinPatch, twin.ETag);

// ...

private static Twin CreatePropertyPatch(string propertyName, object propertyValue, string componentName)
{
    var twinPatch = new Twin();
    twinPatch.Properties.Desired[componentName] = new
    {
        __t = "c"
    };
    twinPatch.Properties.Desired[componentName][propertyName] = JsonConvert.SerializeObject(propertyValue);
    return twinPatch;
}
```

För en egenskap i en komponent ser egenskaps korrigeringen ut som i följande exempel:

```json
{
"sampleComponentName":
  {
    "__t": "c",
    "samplePropertyName": 20
  }
}
```

### <a name="call-command"></a>Anrops kommando

Följande fragment visar hur du anropar `getMaxMinReport` kommandot som definierats i en standard komponent:

```csharp
ServiceClient serviceClient = ServiceClient.CreateFromConnectionString(parameters.HubConnectionString);

var commandInvocation = new CloudToDeviceMethod("getMaxMinReport") { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
  CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);

  Console.WriteLine($"Command getMaxMinReport was invoked." +
      $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine($"Unable to execute command getMaxMinReport on {deviceTwinId}.";
}
```

Följande fragment visar hur du anropar `getMaxMinReport` kommandot på en komponent. Kommandot definieras i **Thermostat1** -gränssnittet:

```csharp
// Create command name to invoke for component. The command is formatted as <component name>*<command name>
string commandToInvoke = "thermostat1*getMaxMinReport";
var commandInvocation = new CloudToDeviceMethod(commandToInvoke) { ResponseTimeout = TimeSpan.FromSeconds(30) };

// Set command payload
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));
string componentCommandPayload = JsonConvert.SerializeObject(since);
commandInvocation.SetPayloadJson(componentCommandPayload);

try
{
    CloudToDeviceMethodResult result = await serviceClient.InvokeDeviceMethodAsync(deviceTwinId, commandInvocation);
    Console.WriteLine($"Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {result.Status}. \nReport: {result.GetPayloadAsJson()}");
}
catch (DeviceNotFoundException)
{
    Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
}
```

## <a name="digital-twin-examples"></a>Digitala, dubbla exempel

Du använder klassen **DigitalTwinClient** för att interagera med enhetens tillstånd med hjälp av digitala dubbla. [DTDL](concepts-digital-twin.md) -modellen för enheten definierar de egenskaper och kommandon som enheten implementerar.

I det här avsnittet visas C#-exempel med hjälp av digitala dubbla API: er. Följande kodfragment använder följande klasser för att representera den digitala dubbla av termostat-och temperatur styrenhets enheterna:

```csharp
using Microsoft.Azure.Devices.Serialization;
using Newtonsoft.Json;
using System;

namespace Microsoft.Azure.Devices.Samples
{
  internal class ThermostatTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new ThermostatMetadata Metadata { get; set; }

    [JsonProperty("maxTempSinceLastReboot")]
    public double? MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public double? TargetTemperature { get; set; }
  }

  internal class ThermostatMetadata : DigitalTwinMetadata
  {
    [JsonProperty("maxTempSinceLastReboot")]
    public ReportedPropertyMetadata MaxTempSinceLastReboot { get; set; }

    [JsonProperty("targetTemperature")]
    public WritableProperty TargetTemperature { get; set; }
  }

  internal class ReportedPropertyMetadata
  {
    [JsonProperty("lastUpdateTime")]
    public DateTimeOffset LastUpdateTime { get; set; }
  }

  internal class TemperatureControllerTwin : BasicDigitalTwin
  {
    [JsonProperty("$metadata")]
    public new TemperatureControllerMetadata Metadata { get; set; }

    [JsonProperty("serialNumber")]
    public string SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public ThermostatTwin Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public ThermostatTwin Thermostat2 { get; set; }
  }

  internal class TemperatureControllerMetadata : DigitalTwinMetadata
  {
    [JsonProperty("serialNumber")]
    public ReportedPropertyMetadata SerialNumber { get; set; }

    [JsonProperty("thermostat1")]
    public WritableProperty Thermostat1 { get; set; }

    [JsonProperty("thermostat2")]
    public WritableProperty Thermostat2 { get; set; }
  }
}
```

`digitalTwinId`Variabeln innehåller enhets-ID: t för iot plug and Play-enheten registrerad i IoT Hub.

### <a name="get-the-digital-twin-and-model-id"></a>Hämta det digitala dubbla och modell-ID: t

För att hämta det digitala dubbla och modell-ID: t för IoT Plug and Play-enheten som är ansluten till din IoT-hubb:

```csharp
DigitalTwinClient digitalTwinClient = DigitalTwinClient.CreateFromConnectionString(parameters.HubConnectionString);
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
ThermostatTwin thermostatTwin = getDigitalTwinResponse.Body;
Console.WriteLine($"Model ID: {thermostatTwin.Metadata.ModelId}.");
Console.WriteLine($"Digital Twin: \n{JsonConvert.SerializeObject(thermostatTwin, Formatting.Indented)}");
```

### <a name="update-digital-twin"></a>Uppdatera Digital, delad

Följande kodfragment visar hur du uppdaterar `targetTemperature` egenskapen på en enhet. Egenskapen definieras i standard komponenten för enheten:

```csharp
var updateOperation = new UpdateOperationsUtility();

int desiredTargetTemperature = 60;

// Get the current value of the targetTemperature property
HttpOperationResponse<ThermostatTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
    .GetDigitalTwinAsync<ThermostatTwin>(digitalTwinId);
double? currentTargetTemperature = getDigitalTwinResponse.Body.TargetTemperature;

// Has the targetTemperature property previously been set?
if (currentTargetTemperature != null)
{
  // Update the existing property
  // Prepend the property path with a '/'
  updateOperation.AppendReplacePropertyOp($"/targetTemperature", desiredTargetTemperature);
}
else
{
  // Add a new property
  // Prepend the property path with a '/'
  updateOperation.AppendAddPropertyOp($"/targetTemperature", desiredTargetTemperature);
}

// Update the targetTemperature property on the digital twin
HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

Följande fragment visar hur du uppdaterar `targetTemperature` egenskapen för en komponent. Egenskapen definieras i **Thermostat1** -komponenten:

```csharp
int desiredTargetTemperature = 60;

var updateOperation = new UpdateOperationsUtility();

// Look at when the property was updated and what was it set to.
HttpOperationResponse<TemperatureControllerTwin, DigitalTwinGetHeaders> getDigitalTwinResponse = await digitalTwinClient
  .GetDigitalTwinAsync<TemperatureControllerTwin>(digitalTwinId);

ThermostatTwin thermostat1 = getDigitalTwinResponse.Body.Thermostat1;

if (thermostat1 != null)
{
  // Thermostat1 is present in the TemperatureController twin. You can add/replace the component-level property "targetTemperature"
  double? currentComponentTargetTemperature = getDigitalTwinResponse.Body.Thermostat1.TargetTemperature;
  if (currentComponentTargetTemperature != null)
  {
      DateTimeOffset targetTemperatureDesiredLastUpdateTime = getDigitalTwinResponse.Body.Thermostat1.Metadata.TargetTemperature.LastUpdateTime;

      // The property path to be replaced should be prepended with a '/'
      updateOperation.AppendReplacePropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
  else
  {
      // The property path to be added should be prepended with a '/'
      updateOperation.AppendAddPropertyOp("/thermostat1/targetTemperature", desiredTargetTemperature);
  }
}
else
{
    // Thermostat1 is not present in the TemperatureController twin. Add the component.
    var componentProperty = new Dictionary<string, object> { { "targetTemperature", desiredTargetTemperature }, { "$metadata", new object() } };

    // The property path to be replaced should be prepended with a '/'
    updateOperation.AppendAddComponentOp("/thermostat1", componentProperty);
}

HttpOperationHeaderResponse<DigitalTwinUpdateHeaders> updateDigitalTwinResponse = await digitalTwinClient
    .UpdateDigitalTwinAsync(digitalTwinId, updateOperation.Serialize());

Console.WriteLine($"Update {digitalTwinId} digital twin response: {updateDigitalTwinResponse.Response.StatusCode}.");
```

### <a name="call-command"></a>Anrops kommando

Följande fragment visar hur du anropar `getMaxMinReport` kommandot som definierats i en standard komponent:

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
  HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
    .InvokeCommandAsync(digitalTwinId, "getMaxMinReport", JsonConvert.SerializeObject(since));

  Console.WriteLine($"Command getMaxMinReport was invoked. \nDevice returned status: {invokeCommandResponse.Body.Status}." +
    $"\nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
  if (e.Response.StatusCode == HttpStatusCode.NotFound)
  {
    Console.WriteLine($"Unable to execute command getMaxMinReport on {digitalTwinId}.");
  }
}
```

Följande fragment visar hur du anropar `getMaxMinReport` kommandot på en komponent. Kommandot definieras i **Thermostat1** -gränssnittet:

```csharp
DateTimeOffset since = DateTimeOffset.Now.Subtract(TimeSpan.FromMinutes(2));

try
{
    HttpOperationResponse<DigitalTwinCommandResponse, DigitalTwinInvokeCommandHeaders> invokeCommandResponse = await digitalTwinClient
        .InvokeComponentCommandAsync(digitalTwinId, "thermostat1", "getMaxMinReport", JsonConvert.SerializeObject(since));

    Console.WriteLine("Command getMaxMinReport was invoked on component thermostat1." +
        $"\nDevice returned status: {invokeCommandResponse.Body.Status}. \nReport: {invokeCommandResponse.Body.Payload}");
}
catch (HttpOperationException e)
{
    if (e.Response.StatusCode == HttpStatusCode.NotFound)
    {
        Console.WriteLine("Unable to execute command getMaxMinReport on component thermostat1.");
    }
}
```

## <a name="rest-api"></a>REST-API

I följande exempel används IoT Hub REST API för att interagera med en ansluten IoT Plug and Play-enhet. Den aktuella versionen av API: t är `2020-09-30` . Lägg `?api-version=2020-09-30` till i dina rest PI-anrop.

> [!NOTE]
> Modulernas dubbla stöds inte för närvarande av `digitalTwins` API: et.

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

## <a name="read-device-telemetry"></a>Läsa telemetri för enhet

IoT Plug and Play-enheter skickar telemetri som definierats i DTDL-modellen till IoT Hub. Som standard dirigerar IoT Hub telemetri till en Event Hubs-slutpunkt där du kan använda den. Läs mer i [använda IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter](../iot-hub/iot-hub-devguide-messages-d2c.md).

Följande kodfragment visar hur du läser Telemetrin från standard slut punkten för Event Hubs. Koden i det här kodfragmentet hämtas från IoT Hub snabb start [Skicka telemetri från en enhet till en IoT-hubb och läsa den med ett Server dels program](../iot-hub/quickstart-send-telemetry-dotnet.md):

```csharp
await using EventHubConsumerClient consumer = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, connectionString, EventHubName);

Console.WriteLine("Listening for messages on all partitions");

try
{
    await foreach (PartitionEvent partitionEvent in consumer.ReadEventsAsync(cancellationToken))
    {
        Console.WriteLine("Message received on partition {0}:", partitionEvent.Partition.PartitionId);

        string data = Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray());
        Console.WriteLine("\t{0}:", data);

        Console.WriteLine("Application properties (set by device):");
        foreach (var prop in partitionEvent.Data.Properties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }

        Console.WriteLine("System properties (set by IoT Hub):");
        foreach (var prop in partitionEvent.Data.SystemProperties)
        {
            Console.WriteLine("\t{0}: {1}", prop.Key, prop.Value);
        }
    }
}
catch (TaskCanceledException)
{
    // This is expected when the token is signaled; it should not be considered an
    // error in this scenario.
}
```

Följande utdata från föregående kod visar den temperatur telemetri som skickas av IoT Plug and Play-enheten (No-Component **termostat** IoT) som bara har standard komponenten. `dt-dataschema`Egenskapen system visar modell-ID:

```cmd/sh
Message received on partition 1:
        { "temperature": 25.5 }:
Application properties (set by device):
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:30:58
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:Thermostat;1
        content-type: application/json
        content-encoding: utf-8
```

Följande utdata från föregående kod visar de temperatur-telemetri som skickas av **TemperatureController** IoT plug and Play-enheten med flera komponenter. `dt-subject`Egenskapen system visar namnet på komponenten som skickade telemetri. I det här exemplet är de två komponenterna `thermostat1` och `thermostat2` enligt definitionen i DTDL-modellen. `dt-dataschema`Egenskapen system visar modell-ID:

```cmd/sh
Message received on partition 1:
        {"temperature":11.1}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat1
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
Message received on partition 1:
        {"temperature":41.2}:
Application properties (set by device):
System properties (set by IoT Hub):
        dt-subject: thermostat2
        iothub-connection-device-id: my-pnp-device
        iothub-connection-auth-method: {"scope":"device","type":"sas","issuer":"iothub","acceptingIpFilterRule":null}
        iothub-connection-auth-generation-id: 637375045610235418
        iothub-enqueuedtime: 05/10/2020 14:23:36
        iothub-message-source: Telemetry
        dt-dataschema: dtmi:com:example:TemperatureController;1
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-device-twin-change-notifications"></a>Läs enhets meddelanden med dubbla ändringar

Du kan konfigurera IoT Hub att generera enhets dubbla ändrings meddelanden som ska vidarebefordras till en slut punkt som stöds. Läs mer i [använda IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter > händelser som inte är telemetri](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Den kod som visas i föregående C#-kodfragment genererar följande utdata när IoT Hub genererar enhets dubbla ändrings meddelanden för en termostat-enhet utan komponent. Program egenskaperna `iothub-message-schema` och `opType` ger dig information om typen av ändrings meddelande:

```cmd/sh
Message received on partition 1:
        {"version":3,"properties":{"reported":{"maxTempSinceLastReboot":9.6,"$metadata":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:17:41.7408552Z"}},"$version":2}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:17:41.7408552+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:17:41
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 61394e8ba7d
        content-type: application/json
        content-encoding: utf-8
```

Den kod som visas i föregående C#-kodfragment genererar följande utdata när IoT Hub genererar enhets dubbla ändrings meddelanden för en enhet med komponenter. I det här exemplet visas utdata när en temperatur sensor enhet med en termostat-komponent genererar meddelanden. Program egenskaperna `iothub-message-schema` och `opType` ger dig information om typen av ändrings meddelande:

```cmd/sh
Message received on partition 1:
        {"version":5,"properties":{"reported":{"thermostat1":{"__t":"c","maxTempSinceLastReboot":9.6},"$metadata":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","thermostat1":{"$lastUpdated":"2020-10-06T10:27:59.515972Z","__t":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"},"maxTempSinceLastReboot":{"$lastUpdated":"2020-10-06T10:27:59.515972Z"}}},"$version":4}}}:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:27:59.5159720+00:00
        iothub-message-schema: twinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:27:59
        iothub-message-source: twinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 615051f364e
        content-type: application/json
        content-encoding: utf-8
```

## <a name="read-digital-twin-change-notifications"></a>Läs digitala dubbla ändrings meddelanden

Du kan konfigurera IoT Hub att generera digitala dubbla ändrings meddelanden som ska vidarebefordras till en slut punkt som stöds. Läs mer i [använda IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter > händelser som inte är telemetri](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Den kod som visas i föregående C#-kodfragment genererar följande utdata när IoT Hub genererar digitala dubbla ändrings meddelanden för en termostat-enhet utan komponent. Program egenskaperna `iothub-message-schema` och `opType` ger dig information om typen av ändrings meddelande:

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-06T10:39:16.0209836Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":34.9}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:39:16.0209836+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:39:16
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 6169857bf8c
        content-type: application/json-patch+json
        content-encoding: utf-8
```

Den kod som visas i föregående C#-kodfragment genererar följande utdata när IoT Hub genererar digitala dubbla ändrings meddelanden för en enhet med komponenter. I det här exemplet visas utdata när en temperatur sensor enhet med en termostat-komponent genererar meddelanden. Program egenskaperna `iothub-message-schema` och `opType` ger dig information om typen av ändrings meddelande:

```cmd/sh
Message received on partition 1:
        [{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-06T10:41:44.8312666Z"}},"maxTempSinceLastReboot":29.1}}]:
Application properties (set by device):
        hubName: my-pnp-hub
        deviceId: my-pnp-device
        operationTimestamp: 2020-10-06T10:41:44.8312666+00:00
        iothub-message-schema: digitalTwinChangeNotification
        opType: updateTwin
System properties (set by IoT Hub):
        iothub-connection-device-id: my-pnp-device
        iothub-enqueuedtime: 06/10/2020 10:41:44
        iothub-message-source: digitalTwinChangeEvents
        user-id: System.ArraySegment`1[System.Byte]
        correlation-id: 616f108f0e3
        content-type: application/json-patch+json
        content-encoding: utf-8
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om enhets modellering är här några ytterligare resurser:

- [Digitalt flätat definitions språk (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C (enhets-SDK)](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [Modell komponenter](./concepts-components.md)
- [Installera och Använd DTDL redigerings verktyg](howto-use-dtdl-authoring-tools.md)
