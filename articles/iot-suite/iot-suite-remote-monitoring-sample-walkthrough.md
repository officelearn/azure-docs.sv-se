<properties
 pageTitle="Genomgång av den förkonfigurerade lösningen för fjärrövervakning | Microsoft Azure"
 description="En beskrivning av den förkonfigurerade fjärrövervakningslösningen i Azure IoT och dess arkitektur."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="dobett"/>

# Genomgång av den förkonfigurerade lösningen för fjärrövervakning

## Introduktion

Den [förkonfigurerade fjärrövervakningslösningen][lnk-preconfigured-solutions] i IoT Suite är en implementering av en övervakningslösning från slutpunkt till slutpunkt för flera datorer som körs på fjärrplatser. Lösningen kombinerar viktiga Azure-tjänster och erbjuder en allmän implementering av affärsscenariot. Du kan använda lösningen som utgångspunkt för en egen implementering. Du kan [anpassa][lnk-customize] lösningen efter dina specifika affärsbehov.

Den här artikeln beskriver några av de viktigaste elementen i fjärrövervakningslösningen så att du förstår hur den fungerar. Med den här kunskapen kan du sedan:

- Felsöka problem i lösningen.
- Planera hur lösningen kan anpassas för att uppfylla dina behov. 
- Utforma en egen IoT-lösning som använder Azure-tjänster.

## Logisk arkitektur

Följande diagram illustrerar de logiska komponenterna i den förkonfigurerade lösningen:

![Logisk arkitektur](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


## Simulerade enheter

I den förkonfigurerade lösningen representerar den simulerade enheten en kylningsenhet (till exempel en ventilations- eller luftkonditioneringsapparat i en byggnad eller lokal). När du distribuerar den förkonfigurerade lösningen etablerar du också automatiskt fyra simulerade enheter som kör i ett [Azure-webbjobb][lnk-webjobs]. De simulerade enheterna gör det enkelt att utforska lösningens beteende utan att du behöver distribuera några fysiska enheter. Om du vill distribuera en riktig fysisk enhet går du självstudiekursen [Ansluta enheten till den förkonfigurerade fjärrövervakningslösningen][lnk-connect-rm].

Varje simulerad enhet kan skicka följande typer av meddelanden till IoT Hub:

| Meddelande  | Beskrivning |
|----------|-------------|
| Start  | När enheten startas skickar den ett **enhetsinformationsmeddelande** som innehåller information om enheten till backend-servern. Dessa data innehåller enhets-ID:t, enhetsmetadata, en lista över kommandon som enheten stöder och enhetens aktuella konfiguration. |
| Närvaro | En enhet skickar regelbundet ett **närvaromeddelande** för att rapportera om den kan känna av närvaron av en sensor. |
| Telemetri | En enhet skickar regelbundet ett **telemetrimeddelande** för att rapporterar simulerade värden för temperaturen och fuktigheten som samlats in från enhetens simulerade sensorer. |


De simulerade enheterna skickar följande enhetsegenskaper i ett **enhetsinformationsmeddelande**:

| Egenskap               |  Syfte |
|------------------------|--------- |
| Enhets-ID              | ID som antingen anges eller tilldelas när en enhet skapas i lösningen. |
| Tillverkare           | Enhetstillverkare |
| Modellnummer           | Enhetens modellnummer |
| Serienummer          | Enhetens serienummer |
| Inbyggd programvara               | Aktuell version av enhetens inbyggda programvara |
| Plattform               | Enhetens plattformsarkitektur |
| Processor              | Processorn som kör enheten |
| Installerat RAM-minne          | Mängden RAM-minne som är installerat på enheten |
| Hubbaktiverat tillstånd      | IoT Hub-tillståndsegenskap för enheten |
| Genereringstid           | Tiden då enheten skapades i lösningen |
| Uppdateringstid           | Tiden då egenskaperna senast uppdaterades för enheten |
| Latitud               | Enhetens latitud |
| Longitud              | Enhetens longitud |

Simulatorn lägger till dessa egenskaper på simulerade enheter med exempelvärden.  Varje gång simulatorn initierar en simulerad enhet skickar enheten fördefinierade metadata till IoT Hub. Observera att detta skriver över metadatauppdateringar som görs på enhetsportalen.


De simulerade enheterna kan hantera följande kommandon som skickas från instrumentpanelen för lösningen via IoT Hub:

| Kommando                | Beskrivning                                         |
|------------------------|-----------------------------------------------------|
| PingDevice             | Skickar en _ping_ till enheten för att kontrollera att den är aktiv   |
| StartTelemetry         | Startar enheten som skickar telemetri                 |
| StopTelemetry          | Stoppar enheten så att den inte skickar mer telemetri             |
| ChangeSetPointTemp     | Ändrar värdet för den angivna punkten som slumpmässiga data skapas kring |
| DiagnosticTelemetry    | Utlöser enhetssimulatorn för att skicka ytterligare ett telemetrivärde (externalTemp) |
| ChangeDeviceState      | Ändrar en egenskap för utökat läge för enheten och skickar meddelandet med enhetsinformation från enheten |

Bekräftelser av enhetskommandon till lösningens backend-komponenter sker genom IoT Hub.

## IoT Hub

[IoT-hubben][lnk-iothub] matar in data som skickas från enheten till molnet och gör dem tillgängliga för ASA-jobben (Azure Stream Analytics). IoT Hub skickar också kommandon till dina enheter för enhetsportalens räkning. Varje ASA-jobb använder en separat IoT Hub-konsumentgrupp för att läsa strömmen av meddelanden från dina enheter.

## Azure Stream Analytics

I fjärrövervakningslösningen skickar [Azure Stream Analytics][lnk-asa] (ASA) meddelanden som tas emot av IoT-hubben till andra backend-komponenter för bearbetning eller lagring. Olika ASA-jobb utför specifika funktioner baserat på innehållet i meddelandena.

**Jobb 1: Enhetsinformation** filtrerar meddelandena med enhetsinformation från den inkommande meddelandeströmmen och skickar dem till slutpunkten för en händelsehubb. En enhet skickar meddelanden med enhetsinformation vid starten och som svar på ett **SendDeviceInfo**-kommando. Det här jobbet använder följande frågedefinition för att identifiera **device-info**-meddelanden:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Det här jobbet skickar sina utdata till en händelsehubb för vidare bearbetning.

**Jobb 2: Regler** utvärderar inkommande telemetrivärden för temperatur och fuktighet mot tröskelvärdena för varje enhet. Tröskelvärdena anges i regelredigeraren som är tillgänglig på instrumentpanelen för lösningen. Varje enhet/värde-par lagras med en tidsstämpel i en blobb som Stream Analytics läser in som **referensdata**. Jobbet jämför icke-tomma värden mot det angivna tröskelvärdet för enheten. Om det överskrider ” >”-villkoret returnerar jobbet en **larmhändelse** som anger att tröskelvärdet överskridits och som visar enheten, värdet och tidstämpeln. Det här jobbet använder följande frågedefinition för att identifiera telemetrimeddelanden som ska utlösa ett larm:

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

Jobbet skickar sina utdata till en händelsehubb för vidare bearbetning och sparar information om varje avisering i blobblagring där lösningens instrumentpanel kan läsa aviseringarna.

**Jobb 3: Telemetri** används på inkommande telemetriströmmar för enheten på två sätt. Med det första skickas alla telemetrimeddelanden från enheterna till permanent blobblagring för långsiktig lagring. Med det andra beräknas värden för genomsnittlig, minsta och högsta fuktighet under en glidande femminutersperiod. Dessa data skickas sedan till blobblagring. Instrumentpanelen för lösningen läser telemetriinformationen från blobblagring och fyller i diagrammen. Det här jobbet använder följande frågedefinition:

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

## Händelsehubbar

ASA-jobben för **enhetsinformation** och **regler** skickar sina data till Event Hubs för bearbetning i **händelseprocessorn** som körs i webbjobbet.

## Azure Storage

Lösningen använder Azure-blobblagring för att bevara alla rådata och sammanfattade telemetridata från enheterna i lösningen. Instrumentpanelen läser telemetriinformationen från blobblagring och fyller i diagrammen. För att visa aviseringar läser instrumentpanelen data från blobblagring som registrerar när telemetrivärden överskrider de konfigurerade tröskelvärdena. Lösningen använder också blobblagring för att registrera de tröskelvärden som du anger på instrumentpanelen.

## Webbjobb

Förutom att fungera som värdar för enhetssimulatorerna är webbjobben i lösningen även värdar för **händelseprocessorn** som körs i ett Azure-webbjobb som hanterar enhetsinformationsmeddelanden och kommandosvar. Den använder:

- Enhetsinformationsmeddelanden för att uppdatera enhetsregistret (lagras i DocumentDB-databasen) med den aktuella enhetsinformationen.
- Kommandosvarsmeddelanden för att uppdatera kommandohistoriken för enheten (lagras i DocumentDB-databasen).

## DocumentDB

Lösningen använder en DocumentDB-databas för att lagra information om de enheter som är anslutna till lösningen. Informationen omfattar enhetsmetadata och historiken för kommandon som skickas till enheter från instrumentpanelen.

## Webbappar

### Instrumentpanelen för fjärrövervakning
Den här sidan i webbappen använder javascript-baserade PowerBI-kontroller (se [PowerBI-visuals-databas](https://www.github.com/Microsoft/PowerBI-visuals)) för att visualisera telemetridata från enheterna. Lösningen använder ASA-telemetrijobbet för att skriva telemetridata till blobblagring.


### Portalen för enhetsadministration

Med den här webbappen kan du:

- Etablera en ny enhet. Den här åtgärden anger det unika enhets-ID:t och genererar autentiseringsnyckeln. Den skriver information om enheten till både IoT Hub-identitetsregistret och den lösningsspecifika DocumentDB-databasen.
- Hantera enhetsegenskaper. Den här åtgärden används för att visa befintliga egenskaper och uppdatera dem med nya egenskaper.
- Skicka kommandon till en enhet.
- Visa kommandohistoriken för en enhet.
- Aktivera och inaktivera enheter.

## Nästa steg

Följande blogginlägg på TechNet innehåller mer information om den förkonfigurerade lösningen för fjärrövervakning:

- [IoT Suite - Under The Hood - Remote Monitoring](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

Läs följande artiklar om du vill fortsätta och lära dig mer om IoT Suite:

- [Ansluta enheten till den förkonfigurerade fjärrövervakningslösningen][lnk-connect-rm]
- [Behörigheter på webbplatsen azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md


<!--HONumber=sep16_HO1-->


