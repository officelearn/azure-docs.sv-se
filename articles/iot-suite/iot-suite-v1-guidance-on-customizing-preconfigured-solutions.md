---
title: Anpassning av förkonfigurerade lösningar | Microsoft Docs
description: Innehåller information om hur du anpassar förkonfigurerade Azure IoT Suite-lösningar.
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
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47107048"
---
# <a name="customize-a-preconfigured-solution"></a>Anpassa en förkonfigurerad lösning

De förkonfigurerade lösningarna som medföljer Azure IoT Suite visar tjänster inom programsviten samarbetar för att leverera en lösning för slutpunkt till slutpunkt. Det finns olika ställen där du kan utöka och anpassa lösningen för specifika scenarier från den här startpunkten. I följande avsnitt beskrivs dessa vanliga anpassningspunkter.

## <a name="find-the-source-code"></a>Se källkoden

Källkoden för de förkonfigurerade lösningarna finns på GitHub i följande databaser:

* Fjärrövervakning: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* Förebyggande underhåll: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)
* Ansluten fabrik: [https://github.com/Azure/azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory)

Källkoden för de förkonfigurerade lösningarna tillhandahålls för att demonstrera mönster och metoder som används för att implementera funktionerna för slutpunkt till slutpunkt i en IoT-lösning med hjälp av Azure IoT Suite. Du hittar mer information om hur du skapar och distribuerar lösningar i GitHub-lagringsplatser.

## <a name="change-the-preconfigured-rules"></a>Ändra de förinställda reglerna

Fjärrövervakningslösningen innehåller tre [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) jobb för att hantera enhetsinformationen, telemetri och regler för logik i lösningen.

De tre stream analytics-jobb och deras syntax beskrivs i detalj i de [förkonfigurerade genomgång av lösningen för fjärrövervakning](iot-suite-v1-remote-monitoring-sample-walkthrough.md). 

Du kan redigera dessa jobb direkt om du vill ändra logiken eller Lägg till logik som är specifik för ditt scenario. Du kan hitta Stream Analytics-jobb på följande sätt:

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Navigera till resursgruppen med samma namn som din IoT-lösning. 
3. Välj det Azure Stream Analytics-jobb som du vill ändra. 
4. Stoppa jobbet genom att välja **stoppa** i uppsättningen med kommandon. 
5. Redigera indata-, fråge- och utdata.
   
    En enkel ändring är att ändra frågan för den **regler** jobbet att använda en **”<”** i stället för en **”>”**. Visar fortfarande lösningsportalen **”>”** när du redigerar du en regel, men Observera hur beteendet vändas på grund av ändring i underliggande jobbet.
6. Starta jobbet

> [!NOTE]
> På instrumentpanelen för fjärrövervakning beror på specifika data, så att ändringar av jobben kan orsaka instrumentpanelen för att misslyckas.

## <a name="add-your-own-rules"></a>Lägg till dina egna regler

Förutom att ändra de förkonfigurerade Azure Stream Analytics-jobb kan använda du Azure-portalen för att lägga till nya jobb eller lägga till nya frågor i befintliga jobb.

## <a name="customize-devices"></a>Anpassa enheter

En av de vanligaste aktiviteterna för tillägget fungerar med enheter som är specifik för ditt scenario. Det finns flera metoder för att arbeta med enheter. De här metoderna omfattar ändra en simulerad enhet så att den matchar ditt scenario eller med hjälp av den [IoT Device SDK] [ IoT Device SDK] att ansluta din fysiska enhet till lösningen.

En stegvis guide för att lägga till enheter, finns det [Iot Suite ansluta enheter](iot-suite-v1-connecting-devices.md) artikeln och [C SDK-exempel för fjärrövervakning](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring). Det här exemplet är utformade att fungera med den förkonfigurerade lösningen för fjärrövervakning.

### <a name="create-your-own-simulated-device"></a>Skapa din egen simulerad enhet

Ingår i den [källkoden för lösningen för fjärrövervakning](https://github.com/Azure/azure-iot-remote-monitoring), är en .NET-simulator. Den här simulatorn är det som tillhandahålls som en del av lösningen och du kan ändra den för att skicka olika metadata, telemetri, och svara på olika kommandon och metoder.

Förkonfigurerade simulatorn i den förkonfigurerade lösningen för fjärrövervakning simulerar en mer lågfrekvent enhet som genererar telemetri om temperatur och fuktighet. Du kan ändra simulatorn i den [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) projektet när du har Förgrenat GitHub-lagringsplatsen.

### <a name="available-locations-for-simulated-devices"></a>Tillgängliga platser för simulerade enheter

En standarduppsättning av platser finns i Seattle/Redmond, Washington, USA. Du kan ändra de här platserna i [SampleDeviceFactory.cs][lnk-sample-device-factory].

### <a name="add-a-desired-property-update-handler-to-the-simulator"></a>Lägg till en önskad egenskap uppdateringshanteraren i simulatorn

Du kan ange ett värde för en önskad egenskap för en enhet i lösningsportalen. Det är ansvar för enheten för att hantera egenskapen ändringsbegäran när enheten hämtar önskade egenskapsvärden. Om du vill lägga till stöd för en egenskap ändras via en önskad egenskap, som du behöver lägga till en hanterare till simulatorn.

Simulatorn innehåller hanterare för den **SetPointTemp** och **TelemetryInterval** egenskaper som du kan uppdatera genom att ange önskade värden i lösningsportalen.

I följande exempel visas hanteraren för den **SetPointTemp** önskade egenskapen i den **CoolerDevice** klass:

```csharp
protected async Task OnSetPointTempUpdate(object value)
{
    var telemetry = _telemetryController as ITelemetryWithSetPointTemperature;
    telemetry.SetPointTemperature = Convert.ToDouble(value);

    await SetReportedPropertyAsync(SetPointTempPropertyName, telemetry.SetPointTemperature);
}
```

Den här metoden uppdaterar punkt temperaturen som telemetri och rapporterar ändringen tillbaka till IoT Hub genom att ange en rapporterad egenskap.

Du kan lägga till egna hanterare för egna egenskaper genom att följa mönstret i föregående exempel.

Du måste också binda önskad egenskap till hanteraren som visas i följande exempel från den **CoolerDevice** konstruktorn:

```csharp
_desiredPropertyUpdateHandlers.Add(SetPointTempPropertyName, OnSetPointTempUpdate);
```

Observera att **SetPointTempPropertyName** är en konstant som definierats som ”Config.SetPointTemp”.

### <a name="add-support-for-a-new-method-to-the-simulator"></a>Lägger till stöd för en ny metod i simulatorn

Du kan anpassa simulator för att lägga till stöd för en ny [metod (direct method)][lnk-direct-methods]. Det finns två viktiga steg som krävs:

- Simulatorn måste meddela IoT-hubben i den förkonfigurerade lösningen med information om metoden.
- Simulatorn måste inkludera kod som hanterar metodanropet när du anropar det från den **enhetsinformation** panelen i solution explorer eller via ett jobb.

Den förkonfigurerade lösningen använder *rapporterade egenskaper* du skicka information om metoder som stöds till IoT hub. Lösningens backend-server har en lista över alla metoder som stöds av varje enhet tillsammans med en historik över metodanropen. Du kan visa den här informationen om enheter och anropa metoder i lösningsportalen.

Meddela IoT-hubben att en enhet har stöd för en metod, enheten måste lägga till information om metoden för att den **SupportedMethods** nod i rapporterade egenskaper:

```json
"SupportedMethods": {
  "<method signature>": "<method description>",
  "<method signature>": "<method description>"
}
```

Metodsignaturen har följande format: `<method name>--<parameter #0 name>-<parameter #1 type>-...-<parameter #n name>-<parameter #n type>`. Till exempel vill ange den **InitiateFirmwareUpdate** metoden förväntar sig en strängparameter med namnet **FwPackageURI**, Använd följande Metodsignaturen:

```json
InitiateFirmwareUpate--FwPackageURI-string: "description of method"
```

En lista över typer av parameterdefinitioner som stöds finns i den **CommandTypes** klass i projektet infrastruktur.

Om du vill ta bort en metod, inställd Metodsignaturen `null` i rapporterade egenskaper.

> [!NOTE]
> Lösningens backend-server uppdaterar endast information om metoder som stöds när den får en *enhetsinformation* meddelande från enheten.

Följande kodexempel från den **SampleDeviceFactory** klass i projektet vanliga visar hur du lägger till en metod i listan över **SupportedMethods** i de rapporterade egenskaperna som skickas av enheten:

```csharp
device.Commands.Add(new Command(
    "InitiateFirmwareUpdate",
    DeliveryType.Method,
    "Updates device Firmware. Use parameter 'FwPackageUri' to specifiy the URI of the firmware file, e.g. https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin",
    new[] { new Parameter("FwPackageUri", "string") }
));
```

Det här kodfragmentet lägger till information om den **InitiateFirmwareUpdate** metoden, inklusive text som ska visas i lösningsportalen och information om de nödvändiga metod parametrarna.

Simulatorn skickar rapporterade egenskaper, inklusive lista över metoder som stöds, till IoT Hub när simulatorn startar.

Lägg till en hanterare i simuleringskod för varje metod stöder. Du kan se de befintliga hanterarna i den **CoolerDevice** klass i projektet Simulator.WebJob. I följande exempel visas hanteraren för **InitiateFirmwareUpdate** metod:

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

Metodnamn för hanteraren måste börja med `On` följt av namnet på metoden. Den **methodRequest** parametern innehåller alla parametrar metodanropet från lösningens serverdel. Det returnera värdet måste vara av typen **uppgift&lt;MethodResponse&gt;**. Den **BuildMethodResponse** verktyget metod kan du skapa det returnera värdet.

I metoden-hanteraren kan du:

- Starta en asynkron uppgift.
- Hämta önskade egenskaper från den *enhetstvillingen* i IoT Hub.
- Uppdatera en enskild rapporterad egenskap med hjälp av den **SetReportedPropertyAsync** -metod i den **CoolerDevice** klass.
- Uppdatera flera rapporterade egenskaper genom att skapa en **TwinCollection** instans och anropa den **Transport.UpdateReportedPropertiesAsync** metod.

Föregående exempel för uppdatering av inbyggd programvara utför följande steg:

- Kontrollerar att enheten kan acceptera begäran om uppdatering av inbyggd programvara.
- Asynkront initierar uppdateringsåtgärden inbyggd programvara och återställer telemetri när åtgärden har slutförts.
- Returnerar omedelbart ”FirmwareUpdate accepterat”-meddelande som anger att begäran togs emot av enheten.

### <a name="build-and-use-your-own-physical-device"></a>Skapa och använda din egen (fysiska) enheten

Den [Azure IoT SDK: er](https://github.com/Azure/azure-iot-sdks) tillhandahåller bibliotek för att ansluta ett stort antal typer av enheter (språk och operativsystem) i IoT-lösningar.

## <a name="modify-dashboard-limits"></a>Ändra gränserna för instrumentpanelen

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Antalet enheter som visas i listrutan för instrumentpanelen

Standardinställningen är 200. Du kan ändra det här värdet i [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Antal PIN-koder att visa i Bing Map-kontroll

Standardinställningen är 200. Du kan ändra det här värdet i [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Tid då telemetri graph

Standardvärdet är 10 minuter. Du kan ändra det här värdet i [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="feedback"></a>Feedback

Har du en anpassning du skulle vilja se tas upp i det här dokumentet? Lägg till förslag på funktioner till [User Voice](https://feedback.azure.com/forums/321918-azure-iot), eller kommentera den här artikeln. 

## <a name="next-steps"></a>Nästa steg

Mer information om alternativ för att anpassa de förkonfigurerade lösningarna finns:

* [Anslut Logikappen till Azure IoT Suite förkonfigurerade lösningen för fjärrövervakning][lnk-logicapp]
* [Använd dynamisk telemetri med den förkonfigurerade lösningen för fjärrövervakning][lnk-dynamic]
* [Information för enhetsmetadata i den förkonfigurerade lösningen för fjärrövervakning][lnk-devinfo]
* [Anpassa visningen av data från dina OPC UA-servrar i lösningen ansluten fabrik][lnk-cf-customize]

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