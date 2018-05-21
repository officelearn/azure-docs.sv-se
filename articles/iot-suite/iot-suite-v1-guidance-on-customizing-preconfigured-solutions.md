---
title: Anpassa förkonfigurerade lösningar | Microsoft Docs
description: Innehåller råd om hur du anpassar Azure IoT Suite förkonfigurerade lösningar.
services: ''
suite: iot-suite
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 4653ae53-4110-4a10-bd6c-7dc034c293a8
ms.service: iot-suite
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: cb5955111cb3954f71f11602042b5153ccee3473
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="customize-a-preconfigured-solution"></a>Anpassa en förkonfigurerad lösning

De förkonfigurerade lösningar som tillhandahålls med Azure IoT Suite visar tjänster inom suite arbetar tillsammans för att leverera en lösning för slutpunkt till slutpunkt. Det finns olika ställen där du kan utöka och anpassa lösning för specifika scenarier från den här startpunkten. I följande avsnitt beskrivs dessa vanliga anpassningspunkter.

## <a name="find-the-source-code"></a>Hitta källkoden

Källkoden för de förkonfigurerade lösningarna finns på GitHub i följande databaser:

* Fjärråtkomst övervakning: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* Förutsägande Underhåll: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)
* Anslutna fabriken: [https://github.com/Azure/azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory)

Källkoden för de förkonfigurerade lösningarna tillhandahålls för att demonstrera mönster och metoder som används för att implementera slutpunkt till slutpunkt-funktionerna i en IoT-lösning med hjälp av Azure IoT Suite. Du hittar mer information om hur du bygger och distribuera lösningar i GitHub-databaser.

## <a name="change-the-preconfigured-rules"></a>Ändra förinställda regler

Fjärråtkomst övervakning lösningen innehåller tre [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) jobb att hantera enhetsinformation, telemetri och regler logiken i lösningen.

De tre strömma analytics-jobb och deras syntax som beskrivs i mer detalj i den [fjärrövervaknings förkonfigurerade lösningen genomgången](iot-suite-v1-remote-monitoring-sample-walkthrough.md). 

Du kan redigera dessa jobb direkt om du vill ändra logiken eller lägga till kod som är specifik för ditt scenario. Du kan hitta Stream Analytics-jobb på följande sätt:

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Gå till resursgruppen med samma namn som din IoT-lösning. 
3. Välj det Azure Stream Analytics-jobb som du vill ändra. 
4. Stoppa jobbet genom att välja **stoppa** i en uppsättning kommandon. 
5. Redigera indata-, fråge- och utdata.
   
    En enkel ändring är att ändra frågan för den **regler** jobbet att använda en **”<”** i stället för en **”>”**. Lösning portal visas fortfarande **”>”** när du redigerar du en regel, men Observera hur beteendet vändas på grund av ändringen i det underliggande jobbet.
6. Starta jobbet

> [!NOTE]
> Instrumentpanelen för fjärråtkomst övervakning beror på specifika data så att ändringar av jobb kan orsaka instrumentpanelen misslyckas.

## <a name="add-your-own-rules"></a>Lägg till dina egna regler

Du kan använda Azure-portalen att lägga till nya jobb eller lägga till nya frågor till befintliga jobb förutom ändra förkonfigurerade Azure Stream Analytics-jobb.

## <a name="customize-devices"></a>Anpassa enheter

En av de vanligaste tillägget aktiviteter fungerar med enheter som är specifika för ditt scenario. Det finns flera metoder för att arbeta med enheter. De här metoderna omfattar ändra en simulerad enhet så att den matchar ditt scenario eller med hjälp av den [IoT-enhet SDK] [ IoT Device SDK] att ansluta den fysiska enheten till lösningen.

En stegvis vägledning för att lägga till enheter finns i [Iot Suite ansluta enheter](iot-suite-v1-connecting-devices.md) artikel och [remote övervakning C SDK-exempel](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring). Det här exemplet är avsedd att fungera med fjärråtkomst övervakning förkonfigurerade lösningen.

### <a name="create-your-own-simulated-device"></a>Skapa simulerade enheten

Ingår i den [fjärråtkomst övervakning lösning källkoden](https://github.com/Azure/azure-iot-remote-monitoring), är en .NET-simulatorn. Den här simulator är etablerad som en del av lösningen och du kan ändra den för att skicka olika metadata, telemetri och svara på olika kommandon och metoder.

Förkonfigurerade simulatorn i fjärråtkomst övervakning förkonfigurerade lösningen simulerar en kyligare enhet som skickar temperatur- och fuktighetskonsekvens telemetri. Du kan ändra simulator i den [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) projektet när du har forked GitHub-lagringsplatsen.

### <a name="available-locations-for-simulated-devices"></a>Tillgängliga platser för simulerad enheter

En standarduppsättning av platser finns i Seattle/Redmond, Washington, USA. Du kan ändra dessa platser i [SampleDeviceFactory.cs][lnk-sample-device-factory].

### <a name="add-a-desired-property-update-handler-to-the-simulator"></a>Lägg till en hanterare för att uppdatera önskade egenskapen simulatorn

Du kan ange ett värde för en önskad egenskap för en enhet i portalen för lösningen. Det är på enheten för att hantera egenskapen ansvar ändringsbegäran när enheten hämtar önskade egenskapens värde. Om du vill lägga till stöd för en egenskap ändras via en önskad egenskap som du behöver lägga till en hanterare simulatorn.

Simulatorn innehåller hanterare för den **SetPointTemp** och **TelemetryInterval** egenskaper som du kan uppdatera genom att ange önskade värden i lösningen portal.

I följande exempel visas hanterare för den **SetPointTemp** önskad egenskap i den **CoolerDevice** klass:

```csharp
protected async Task OnSetPointTempUpdate(object value)
{
    var telemetry = _telemetryController as ITelemetryWithSetPointTemperature;
    telemetry.SetPointTemperature = Convert.ToDouble(value);

    await SetReportedPropertyAsync(SetPointTempPropertyName, telemetry.SetPointTemperature);
}
```

Den här metoden uppdaterar telemetri punkt temperatur och rapporterar sedan ändringen tillbaka till IoT-hubb rapporterade egenskapen.

Du kan lägga till egna hanterare för egna egenskaper genom att följa mönstret i föregående exempel.

Du måste också binda egenskapen önskade till hanteraren som visas i följande exempel från den **CoolerDevice** konstruktorn:

```csharp
_desiredPropertyUpdateHandlers.Add(SetPointTempPropertyName, OnSetPointTempUpdate);
```

Observera att **SetPointTempPropertyName** är en konstant som definierats som ”Config.SetPointTemp”.

### <a name="add-support-for-a-new-method-to-the-simulator"></a>Lägga till stöd för en ny metod i simulatorn

Du kan anpassa simulator om du vill lägga till stöd för en ny [metod (direkt metod)][lnk-direct-methods]. Det finns två viktiga steg som krävs:

- Simulatorn måste meddela IoT-hubben i förkonfigurerade lösningen med information för metoden.
- Simulatorn måste innehålla kod för att hantera metodanropet när du anropar den från den **enhetsinformation** panelen i solution explorer eller via ett jobb.

Fjärråtkomst övervakning förkonfigurerade lösningen använder *rapporterade egenskaper* att skicka information om metoder som stöds till IoT-hubb. Lösningens serverdel finns en lista över alla metoder som stöds av varje enhet tillsammans med en historik över anrop av metoden. Du kan visa den här informationen om enheter och anropa metoder i lösningen portal.

Meddela IoT-hubb som en enhet har stöd för en metod, enheten måste lägga till information om metoden för att den **SupportedMethods** nod i rapporterade egenskaper:

```json
"SupportedMethods": {
  "<method signature>": "<method description>",
  "<method signature>": "<method description>"
}
```

Metodsignaturen har följande format: `<method name>--<parameter #0 name>-<parameter #1 type>-...-<parameter #n name>-<parameter #n type>`. Till exempel för att ange den **InitiateFirmwareUpdate** metoden förväntar sig en strängparameter med namnet **FwPackageURI**, Använd följande Metodsignaturen:

```json
InitiateFirmwareUpate--FwPackageURI-string: "description of method"
```

En lista över stöds parametertyper finns i **CommandTypes** klass i projektet infrastruktur.

Om du vill ta bort en metod som Metodsignaturen `null` i rapporterade egenskaperna.

> [!NOTE]
> Lösningens serverdel uppdateras bara information om metoder som stöds när den tar emot en *enhetsinformation* meddelande från enheten.

Följande kodexempel från den **SampleDeviceFactory** klass i projektet vanliga visar hur du lägger till en metod i listan över **SupportedMethods** i rapporterade egenskaperna som skickats av enheten:

```csharp
device.Commands.Add(new Command(
    "InitiateFirmwareUpdate",
    DeliveryType.Method,
    "Updates device Firmware. Use parameter 'FwPackageUri' to specifiy the URI of the firmware file, e.g. https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin",
    new[] { new Parameter("FwPackageUri", "string") }
));
```

Det här kodstycket lägger till information om den **InitiateFirmwareUpdate** metoden inklusive text som ska visas i lösningen portal och information om de nödvändiga Metodparametrarna.

Simulatorn skickar rapporterade egenskaper, inklusive listan över metoder som stöds, till IoT-hubb när simulatorn startar.

Lägg till en hanterare simulator koden för varje metod stöder. Du kan se de befintliga hanterarna i den **CoolerDevice** klass i projektet Simulator.WebJob. I följande exempel visas en hanterare **InitiateFirmwareUpdate** metod:

```csharp
public async Task<MethodResponse> OnInitiateFirmwareUpdate(MethodRequest methodRequest, object userContext)
{
    if (_deviceManagementTask != null && !_deviceManagementTask.IsCompleted)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "Device is busy"
        }, 409));
    }

    try
    {
        var operation = new FirmwareUpdate(methodRequest);
        _deviceManagementTask = operation.Run(Transport).ContinueWith(async task =>
        {
            // after firmware completed, we reset telemetry
            var telemetry = _telemetryController as ITelemetryWithTemperatureMeanValue;
            if (telemetry != null)
            {
                telemetry.TemperatureMeanValue = 34.5;
            }

            await UpdateReportedTemperatureMeanValue();
        });

        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "FirmwareUpdate accepted",
            Uri = operation.Uri
        }));
    }
    catch (Exception ex)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = ex.Message
        }, 400));
    }
}
```

Metoden hanterare namn måste börja med `On` följt av namnet på metoden. Den **methodRequest** parametern innehåller alla parametrar metodanropet från lösningens serverdel. Det returnera värdet måste vara av typen **aktivitet&lt;MethodResponse&gt;**. Den **BuildMethodResponse** verktyget metoden kan du skapa det returnera värdet.

I metoden-hanteraren kan du:

- Starta en asynkron åtgärd.
- Hämta egenskaper från den *enheten dubbla* i IoT-hubb.
- Uppdatera en enskild rapporterade egenskap med hjälp av den **SetReportedPropertyAsync** metod i den **CoolerDevice** klass.
- Uppdatera flera rapporterade egenskaper genom att skapa en **TwinCollection** instansen och anropar den **Transport.UpdateReportedPropertiesAsync** metod.

Exemplet för firmware-uppdatering utför följande steg:

- Kontrollerar att enheten kan acceptera begäran om uppdatering inbyggd programvara.
- Asynkront initierar uppdateringsåtgärden inbyggd programvara och återställer telemetrin när åtgärden har slutförts.
- Returnerar omedelbart ”FirmwareUpdate accepteras”-meddelande för att ange begäran togs emot av enheten.

### <a name="build-and-use-your-own-physical-device"></a>Skapa och använda enheten (fysiska)

Den [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) ange bibliotek för att ansluta flera typer av enheter (språk och operativsystem) i IoT-lösningar.

## <a name="modify-dashboard-limits"></a>Ändra gränserna för instrumentpanelen

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Antalet enheter som visas i instrumentpanelen listrutan

Standardinställningen är 200. Du kan ändra det här numret i [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Antal PIN-koder som ska visas i Bing Map-kontrollen

Standardinställningen är 200. Du kan ändra det här numret i [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Tidsperiod telemetri diagrammet

Standardvärdet är 10 minuter. Du kan ändra det här värdet i [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="feedback"></a>Feedback

Har du en anpassning du skulle vilja se omfattas i det här dokumentet? Lägg till förslag på funktioner till [User Voice](https://feedback.azure.com/forums/321918-azure-iot), eller en kommentar för den här artikeln. 

## <a name="next-steps"></a>Nästa steg

Mer information om alternativ för att anpassa förkonfigurerade lösningar finns:

* [Ansluta Logikappen i Azure IoT Suite Fjärrövervaknings förkonfigurerade lösningen][lnk-logicapp]
* [Använd dynamiska telemetri med fjärråtkomst övervakning förkonfigurerade lösningen][lnk-dynamic]
* [Enhetens information metadata i fjärråtkomst övervakning förkonfigurerade lösningen][lnk-devinfo]
* [Anpassa hur lösningen anslutna factory visar data från dina OPC UA-servrar][lnk-cf-customize]

[lnk-logicapp]: iot-suite-v1-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-cf-customize]:../iot-accelerators/iot-accelerators-connected-factory-customize.md