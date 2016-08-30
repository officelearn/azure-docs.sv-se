<properties
 pageTitle="Genomgång av den förkonfigurerade lösningen för fjärrövervakning | Microsoft Azure"
 description="En beskrivning av den förkonfigurerade fjärrövervakningslösningen i Azure IoT och dess arkitektur."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="03/02/2016"
 ms.author="stevehob"/>

# Genomgång av den förkonfigurerade lösningen för fjärrövervakning

## Introduktion

Den förkonfigurerade fjärrövervakningslösningen i IoT Suite är en enkel övervakningslösning från slutpunkt till slutpunkt för ett affärsscenario med flera datorer på fjärranslutna platser. Lösningen kombinerar viktiga Azure IoT Suite-tjänster för att erbjuda en generell implementering av affärsscenariot. Lösningen är en bra utgångspunkt för kunder som vill implementera den här typen av IoT-lösning för att uppfylla egna specifika affärsbehov.

## Logisk arkitektur

Följande diagram illustrerar de logiska komponenterna i den förkonfigurerade lösningen:

![](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


### Simulerade enheter

I den förkonfigurerade lösningen representerar den simulerade enheten en kylningsenhet (till exempel en ventilations- eller luftkonditioneringsapparat i en byggnad eller lokal). Varje simulerad enhet skickar följande telemetrimeddelanden till IoT Hub:


| Meddelande  | Beskrivning |
|----------|-------------|
| Start  | När enheten startar skickar den ett meddelande med **enhetsinformation** som innehåller information om enheten, t.ex. enhets-ID, enhetsmetadata, en lista över kommandon som enheten stöder och enhetens aktuella konfiguration. |


De simulerade enheterna skickar följande enhetsegenskaper som metadata:

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


De simulerade enheterna kan hantera följande kommandon som skickas från en IoT-hubb:

| Kommando                | Beskrivning                                         |
|------------------------|-----------------------------------------------------|
| PingDevice             | Skickar en _ping_ till enheten för att kontrollera att den är aktiv   |
| StartTelemetry         | Startar enheten som skickar telemetri                 |
| StopTelemetry          | Stoppar enheten så att den inte skickar mer telemetri             |
| ChangeSetPointTemp     | Ändrar värdet för den angivna punkten som slumpmässiga data skapas kring |
| DiagnosticTelemetry    | Utlöser enhetssimulatorn för att skicka ytterligare ett telemetrivärde (externalTemp) |
| ChangeDeviceState      | Ändrar en egenskap för utökat läge för enheten och skickar meddelandet med enhetsinformation från enheten |


Enhetskommandot bekräftas via IoT-hubben.


### Azure Stream Analytics-jobb


**Jobb 1: Enhetsinformation** filtrerar meddelandena med enhetsinformation från den inkommande meddelandeströmmen och skickar dem till slutpunkten för en händelsehubb. En enhet skickar meddelanden med enhetsinformation vid starten och som svar på ett **SendDeviceInfo**-kommando. Det här jobbet använder följande frågedefinition:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

**Jobb 2: Regler** utvärderar inkommande telemetrivärden för temperatur och fuktighet mot tröskelvärdena för varje enhet. Tröskelvärdena anges i regelredigeraren som ingår i lösningen. Varje enhet/värde-par lagras efter tidsstämpel i en blobb som läses in i Stream Analytics som **referensdata**. Jobbet jämför icke-tomma värden mot det angivna tröskelvärdet för enheten. Om det överskrider ” >”-villkoret returnerar jobbet en **larmhändelse** som anger att tröskelvärdet överskridits och som visar enheten, värdet och tidstämpeln. Det här jobbet använder följande frågedefinition:

```
WITH AlarmsData AS 
(
SELECT
     Stream.DeviceID,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.DeviceID,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.DeviceID = Ref.DeviceID
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

**Jobb 3: Telemetri** används på inkommande telemetriströmmar för enheten på två sätt. Det första skickar alla telemetrimeddelanden från enheterna till beständig Blob Storage. Det andra beräknar genomsnitt, minsta och högsta fuktighetsvärden under en glidande femminutersperiod. Informationen skickas även till Blob Storage. Det här jobbet använder följande frågedefinition:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM 
      [IoTHubStream] 
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    *
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    DeviceId,
    AVG (Humidity) AS [AverageHumidity], 
    MIN(Humidity) AS [MinimumHumidity], 
    MAX(Humidity) AS [MaxHumidity], 
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM
    [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    DeviceId, 
    SlidingWindow (mi, 5)
```

### Händelseprocessor

**Händelseprocessorn** hanterar meddelanden med enhetsinformation och kommandosvar. Den använder:

- Enhetsinformationsmeddelanden för att uppdatera enhetsregistret (lagras i DocumentDB-databasen) med den aktuella enhetsinformationen.
- Kommandosvarsmeddelanden för att uppdatera kommandohistoriken för enheten (lagras i DocumentDB-databasen).

## Vi sätter igång

Det här avsnittet beskriver komponenterna i lösningen, beskriver deras avsedda användning och innehåller exempel.

### Instrumentpanelen för fjärrövervakning
Den här sidan i webbappen använder PowerBI-baserade JavaScript-kontroller (se [PowerBI-visual-databasen](https://www.github.com/Microsoft/PowerBI-visuals)) för att visualisera utdata från Stream Analytics-jobb i Blob Storage.


### Portalen för enhetsadministration

Med den här webbappen kan du:

- Etablera en ny enhet som anger det unika enhets-ID:t och som genererar autentiseringsnyckeln.
- Hantera enhetsegenskaper till exempel för att visa befintliga egenskaper och uppdatera med nya egenskaper.
- Skicka kommandon till en enhet.
- Visa kommandohistoriken för en enhet.

### Observera molnlösningens beteende
Du kan visa dina etablerade resurser genom att gå till [Azure-portalen](https://portal.azure.com) och sedan navigera till resursgruppen med det lösningsnamn som du har angett.

![](media/iot-suite-remote-monitoring-sample-walkthrough/azureportal_01.png)

Första gången du kör exemplet finns det fyra förkonfigurerade, simulerade enheter:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_01.png)

Du kan använda enhetsadministrationsportalen för att lägga till en ny simulerad enhet:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_02.png)

Till en början har den nya enheten statusen **Väntande** på enhetsadministrationsportalen:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_03.png)

När appen har slutfört distributionen av den simulerade enheten ändras enhetens status till **Kör** på enhetsadministrationsportalen som du ser i följande skärmbild. Stream Analytics-jobbet **DeviceInfo** skickar information om enhetens status från enheten till enhetsadministrationsportalen.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_04.png)

Från lösningsportalen kan du skicka kommandon som **ChangeSetPointTemp** till enheten:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_05.png)

När enheten rapporterar att den har kört kommandot ändras statusen till **Lyckades**:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_06.png)

Från lösningsportalen kan du söka efter enheter med specifika egenskaper, till exempel ett modellnummer:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_07.png)

Du kan inaktivera en enhet och när den har inaktiverats kan du ta bort den:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_08.png)


## Nästa steg

Följande blogginlägg på TechNet innehåller mer information om den förkonfigurerade lösningen för fjärrövervakning:

- [IoT Suite - Under The Hood - Remote Monitoring](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [IoT Suite - Remote Monitoring - Adding Live and Simulated Devices](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)


<!--HONumber=jun16_HO2-->


