---
title: "Genomgång av den förkonfigurerade lösningen för fjärrövervakning | Microsoft Docs"
description: "En beskrivning av den förkonfigurerade fjärrövervakningslösningen i Azure IoT och dess arkitektur."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: b28105f300723b542fa6d1aebc569439d5c73dc4
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Genomgång av den förkonfigurerade lösningen för fjärrövervakning

Den [förkonfigurerade fjärrövervakningslösningen][lnk-preconfigured-solutions] i IoT Suite är en implementering av en övervakningslösning från slutpunkt till slutpunkt för flera datorer som körs på fjärrplatser. I lösningen kombineras viktiga Azure-tjänster till en allmän implementering av affärsscenariot. Du kan använda lösningen som startpunkt för en egen implementering och [anpassa][lnk-customize] den efter dina egna affärsbehov.

Den här artikeln beskriver några av de viktigaste elementen i fjärrövervakningslösningen så att du förstår hur den fungerar. Med den här kunskapen kan du sedan:

* Felsöka problem i lösningen.
* Planera hur lösningen kan anpassas för att uppfylla dina behov. 
* Utforma en egen IoT-lösning som använder Azure-tjänster.

## <a name="logical-architecture"></a>Logisk arkitektur

Följande diagram illustrerar de logiska komponenterna i den förkonfigurerade lösningen:

![Logisk arkitektur](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="simulated-devices"></a>Simulerade enheter

I den förkonfigurerade lösningen representerar den simulerade enheten en kylningsenhet (till exempel en ventilations- eller luftkonditioneringsapparat i en byggnad eller lokal). När du distribuerar den förkonfigurerade lösningen etablerar du också automatiskt fyra simulerade enheter som körs i ett [Azure-webbjobb][lnk-webjobs]. De simulerade enheterna gör det enkelt att utforska lösningens beteende utan att du behöver distribuera några fysiska enheter. Om du vill distribuera en riktig fysisk enhet går du självstudiekursen [Ansluta enheten till den förkonfigurerade fjärrövervakningslösningen][lnk-connect-rm].

### <a name="device-to-cloud-messages"></a>Meddelanden från enheten till molnet

Varje simulerad enhet kan skicka följande typer av meddelanden till IoT Hub:

| Meddelande | Beskrivning |
| --- | --- |
| Start |När enheten startas skickar den ett **enhetsinformationsmeddelande** som innehåller information om enheten till backend-servern. Den här informationen är bland annat enhets-ID:t och en lista med kommandon och metoder som enheten har stöd för. |
| Närvaro |En enhet skickar regelbundet ett **närvaromeddelande** för att rapportera om den kan känna av närvaron av en sensor. |
| Telemetri |En enhet skickar regelbundet ett **telemetrimeddelande** för att rapporterar simulerade värden för temperaturen och fuktigheten som samlats in från enhetens simulerade sensorer. |

> [!NOTE]
> Listan med kommandon som stöds av enheten lagras i en Cosmos DB-databas och inte i enhetstvillingen.

### <a name="properties-and-device-twins"></a>Egenskaper och enhetstvillingar

De simulerade enheterna skickar följande enhetsegenskaper till [tvillingen][lnk-device-twins] i IoT Hub som *rapporterade egenskaper*. Enheten skickar rapporterade egenskaper vid start och som svar på ett kommando eller en metod om att **ändra enhetens tillstånd**.

| Egenskap | Syfte |
| --- | --- |
| Config.TelemetryInterval | Den frekvens (i sekunder) som enheten skickar telemetri med |
| Config.TemperatureMeanValue | Anger medelvärdet för telemetrin för simulerad temperatur |
| Device.DeviceID |Ett ID som antingen anges eller tilldelas när en enhet skapas i lösningen |
| Device.DeviceState | Tillståndet som rapporteras av enheten |
| Device.CreatedTime |Tiden då enheten skapades i lösningen |
| Device.StartupTime |Tidpunkten då enheten startades |
| Device.LastDesiredPropertyChange |Versionsnumret för den senast önskade egenskapsändringen |
| Device.Location.Latitude |Enhetens latitud |
| Device.Location.Longitude |Enhetens longitud |
| System.Manufacturer |Enhetstillverkare |
| System.ModelNumber |Enhetens modellnummer |
| System.SerialNumber |Enhetens serienummer |
| System.FirmwareVersion |Aktuell version av enhetens inbyggda programvara |
| System.Platform |Enhetens plattformsarkitektur |
| System.Processor |Processorn som kör enheten |
| System.InstalledRAM |Mängden RAM-minne som är installerat på enheten |

Simulatorn lägger till dessa egenskaper på simulerade enheter med exempelvärden. Varje gång simulatorn initierar en simulerad enhet rapporterar enheten fördefinierade metadata till IoT Hub som rapporterade egenskaper. Det är bara enheten som kan uppdatera rapporterade egenskaper. Om du vill ändra en rapporterad egenskap anger du den önskade egenskapen i lösningsportalen. Enheten ansvarar för följande uppgifter:

1. Att med jämna mellanrum hämta önskade egenskaper från IoT Hub.
2. Att uppdatera konfigurationen med önskade egenskapsvärden.
3. Att skicka tillbaka det nya värdet till hubben som en rapporterad egenskap.

Från instrumentpanelen i lösningen kan du använda *önskade egenskaper* till att ange egenskaper för en enhet med hjälp av [enhetstvillingen][lnk-device-twins]. En enhet läser vanligtvis av ett önskat egenskapsvärde från hubben, uppdaterar det interna tillståndet och rapporterar ändringen som en rapporterad egenskap.

> [!NOTE]
> I koden för den simulerade enheten är det bara de önskade egenskaperna **Desired.Config.TemperatureMeanValue** och **Desired.Config.TelemetryInterval** som används till att uppdatera de rapporterade egenskaper som skickas tillbaka till IoT Hub. Alla andra förfrågningar om att ändra önskade egenskaper ignoreras i den simulerade enheten.

### <a name="methods"></a>Metoder

De simulerade enheterna kan hantera följande metoder ([direkta metoder][lnk-direct-methods]) som anropas från lösningsportalen via IoT Hub:

| Metod | Beskrivning |
| --- | --- |
| InitiateFirmwareUpdate |Anger att enheten ska uppdatera den inbyggda programvaran |
| Starta om |Anger att enheten ska startas om |
| FactoryReset |Anger att enheten ska fabriksåterställas |

I vissa metoder används rapporterade egenskaper till att informera om förloppet. I metoden **InitiateFirmwareUpdate** simuleras till exempel att uppdateringen körs asynkront på enheten. Metoden returnerar omedelbart ett resultat på enheten medan den asynkrona uppgiften fortsätter att skicka statusuppdateringar till lösningens instrumentpanel med hjälp av rapporterade egenskaper.

### <a name="commands"></a>Kommandon

De simulerade enheterna kan hantera följande kommandon (meddelanden från molnet till enheten) som skickas från lösningsportalen via IoT Hub:

| Kommando | Beskrivning |
| --- | --- |
| PingDevice |Skickar en *ping* till enheten för att kontrollera att den är aktiv |
| StartTelemetry |Startar enheten som skickar telemetri |
| StopTelemetry |Stoppar enheten så att den inte skickar mer telemetri |
| ChangeSetPointTemp |Ändrar värdet för den angivna punkten som slumpmässiga data skapas kring |
| DiagnosticTelemetry |Utlöser enhetssimulatorn för att skicka ytterligare ett telemetrivärde (externalTemp) |
| ChangeDeviceState |Ändrar en egenskap för utökat läge för enheten och skickar meddelandet med enhetsinformation från enheten |

> [!NOTE]
> En jämförelse av dessa kommandon (meddelanden från molnet till enheten) och metoder (direkta metoder) finns i artikeln om [kommunikation mellan moln och enheter][lnk-c2d-guidance].

## <a name="iot-hub"></a>IoT Hub

[IoT Hub][lnk-iothub] matar in data som skickas från enheterna till molnet och gör dem tillgängliga för ASA-jobben (Azure Stream Analytics). Varje ASA-jobb använder en separat IoT Hub-konsumentgrupp för att läsa strömmen av meddelanden från dina enheter.

IoT Hub ansvarar även för följande uppgifter i lösningen:

- Att underhålla ett ID-register där ID:n och autentiseringsnycklar lagras för alla enheter som har behörighet att ansluta till portalen. Du kan aktivera och inaktivera enheter via ID-registret.
- Att skicka kommandon till dina enheter för lösningsportalens räkning.
- Att anropa metoder på dina enheter för lösningsportalens räkning.
- Att underhålla enhetstvillingar för alla registrerade enheter. De egenskapsvärden som rapporteras av en enhet lagras i enhetstvillingen. De önskade egenskaper som anges i lösningsportalen och som enheten ska hämta vid nästa anslutning lagras också där.
- Att schemalägga jobb där egenskaper ska anges för flera enheter eller där metoder ska anropas på flera enheter.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

I fjärrövervakningslösningen skickar [Azure Stream Analytics][lnk-asa] (ASA) meddelanden som tas emot av IoT Hub till andra serverkomponenter för bearbetning eller lagring. Olika ASA-jobb utför specifika funktioner baserat på innehållet i meddelandena.

**Jobb 1: Enhetsinformation** filtrerar meddelandena med enhetsinformation från den inkommande meddelandeströmmen och skickar dem till slutpunkten för en händelsehubb. En enhet skickar meddelanden med enhetsinformation vid starten och som svar på ett **SendDeviceInfo**-kommando. Det här jobbet använder följande frågedefinition för att identifiera **device-info**-meddelanden:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Det här jobbet skickar sina utdata till en händelsehubb för vidare bearbetning.

**Jobb 2: Regler** utvärderar inkommande telemetrivärden för temperatur och fuktighet mot tröskelvärdena för varje enhet. Tröskelvärdena anges i regelredigeraren som finns i lösningsportalen. Varje enhet/värde-par lagras med en tidsstämpel i en blobb som Stream Analytics läser in som **referensdata**. Jobbet jämför icke-tomma värden mot det angivna tröskelvärdet för enheten. Om det överskrider ” >”-villkoret returnerar jobbet en **larmhändelse** som anger att tröskelvärdet överskridits och som visar enheten, värdet och tidstämpeln. Det här jobbet använder följande frågedefinition för att identifiera telemetrimeddelanden som ska utlösa ett larm:

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

Jobbet skickar sina utdata till en händelsehubb för vidare bearbetning och sparar information om varje avisering i Blob Storage där lösningsportalen kan läsa aviseringarna.

**Jobb 3: Telemetri** används på inkommande telemetriströmmar för enheten på två sätt. Med det första skickas alla telemetrimeddelanden från enheterna till permanent blobblagring för långsiktig lagring. Med det andra beräknas värden för genomsnittlig, minsta och högsta fuktighet under en glidande femminutersperiod. Dessa data skickas sedan till blobblagring. Lösningsportalen läser av telemetridata från Blob Storage och fyller i diagrammen. Det här jobbet använder följande frågedefinition:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Händelsehubbar

ASA-jobben för **enhetsinformation** och **regler** skickar sina data till Event Hubs för bearbetning i **händelseprocessorn** som körs i webbjobbet.

## <a name="azure-storage"></a>Azure Storage

Lösningen använder Azure-blobblagring för att bevara alla rådata och sammanfattade telemetridata från enheterna i lösningen. Portalen läser av telemetridata från Blob Storage och fyller i diagrammen. När aviseringar ska visas läser lösningsportalen av data från Blob Storage som registreras när telemetrivärden överskrider de konfigurerade tröskelvärdena. I lösningen används också Blob Storage till att registrera de tröskelvärden du anger i lösningsportalen.

## <a name="webjobs"></a>Webbjobb

Förutom att fungera som värdar för enhetssimulatorerna fungerar WebJobs i lösningen även som värdar för **händelseprocessorn** som körs i ett Azure WebJob som hanterar svar från kommandon. Svarsmeddelanden från kommandon används för att uppdatera enhetens kommandohistorik (lagras i Cosmos DB-databasen).

## <a name="cosmos-db"></a>Cosmos DB

Lösningen använder en Cosmos DB-databas för att lagra information om de enheter som är anslutna till lösningen. I den här informationen ingår historiken för de kommandon som skickas till enheter från lösningsportalen och för de metoder som anropas från lösningsportalen.

## <a name="solution-portal"></a>Lösningsportal

Lösningsportalen är en webbapp som ingår i distributionen av den förkonfigurerade lösningen. Några viktiga sidor i lösningsportalen är instrumentpanelen och enhetslistan.

### <a name="dashboard"></a>Instrumentpanel

På den här sidan i webbappen används javascript-baserade PowerBI-kontroller (se [PowerBI-visuals-databasen](https://www.github.com/Microsoft/PowerBI-visuals)) till att visualisera telemetridata från enheterna. Lösningen använder ASA-telemetrijobbet för att skriva telemetridata till blobblagring.

### <a name="device-list"></a>Enhetslista

På den här sidan i lösningsportalen kan du göra följande:

* Etablera en ny enhet. Den här åtgärden anger det unika enhets-ID:t och genererar autentiseringsnyckeln. Den skriver information om enheten till både IoT Hub-identitetsregistret och den lösningsspecifika Cosmos DB-databasen.
* Hantera enhetsegenskaper. Den här åtgärden används för att visa befintliga egenskaper och uppdatera dem med nya egenskaper.
* Skicka kommandon till en enhet.
* Visa kommandohistoriken för en enhet.
* Aktivera och inaktivera enheter.

## <a name="next-steps"></a>Nästa steg

Följande blogginlägg på TechNet innehåller mer information om den förkonfigurerade lösningen för fjärrövervakning:

* [IoT Suite - Under The Hood - Remote Monitoring](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
* [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

Läs följande artiklar om du vill fortsätta och lära dig mer om IoT Suite:

* [Ansluta enheten till den förkonfigurerade fjärrövervakningslösningen][lnk-connect-rm]
* [Behörigheter på webbplatsen azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twins]:  ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
