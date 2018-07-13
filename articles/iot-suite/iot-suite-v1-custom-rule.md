---
title: Skapa en anpassad regel i Azure IoT Suite | Microsoft Docs
description: Så här skapar du en anpassad regel i en förkonfigurerade IoT Suite-lösning.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 9bf2a13035de141766fd935966ce18459dccdaab
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38590522"
---
# <a name="create-a-custom-rule-in-the-remote-monitoring-preconfigured-solution"></a>Skapa en anpassad regel i den förkonfigurerade lösningen för fjärrövervakning

## <a name="introduction"></a>Introduktion

Du kan konfigurera i de förkonfigurerade lösningarna [regler som utlöser när ett telemetri-värdet för en enhet når ett visst tröskelvärde][lnk-builtin-rule]. [Använd dynamisk telemetri med den fjärrövervakning förkonfigurerade lösningen] [ lnk-dynamic-telemetry] beskriver hur du kan lägga till anpassad telemetrivärden, till exempel *ExternalTemperature* i lösningen. Den här artikeln visar hur du skapar en anpassad regel för dynamiskt telemetrityper i din lösning.

Den här självstudien använder en enkel Node.js-simulerad enhet för att generera dynamisk telemetri ska skickas till den förkonfigurerade lösningen för serverdelen. Du sedan lägga till anpassade regler i den **RemoteMonitoring** Visual Studio-lösning och distribuera den här anpassade serverdel till din Azure-prenumeration.

För att slutföra den här kursen behöver du:

* En aktiv Azure-prenumeration. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [kostnadsfri utvärderingsversion av Azure][lnk_free_trial].
* [Node.js] [ lnk-node] version 0.12.x eller senare för att skapa en simulerad enhet.
* Visual Studio 2015 eller Visual Studio 2017 för att ändra tillbaka den förkonfigurerade lösningen avslutas med din nya regler.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

Anteckna det lösningsnamn som du har valt för din distribution. Du behöver den här lösningen senare i den här självstudien.

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

Du kan stoppa Node.js-konsolapp när du har verifierat att den skickar **ExternalTemperature** telemetri till den förkonfigurerade lösningen. Låt konsolfönstret öppet eftersom du kör den här Node.js-konsolapp igen när du lägger till den anpassade regeln till lösningen.

## <a name="rule-storage-locations"></a>Lagringsplatser för regeln

Information om regler sparas på två platser:

* **DeviceRulesNormalizedTable** tabellen – den här tabellen lagras en normaliserad referens till regler som definieras av lösningsportalen. När lösningsportalen visar enhetsregler, frågar den här tabellen för Regeldefinitioner.
* **DeviceRules** blob – den här bloben lagrar alla regler som definierats för alla registrerade enheter och definieras som en referens som indata för Azure Stream Analytics-jobb.
 
När du uppdaterar en befintlig regel eller definiera en ny regel i lösningsportalen, har tabell- och blobblagring uppdaterats för att återspegla ändringarna. Regeldefinitionen visas i portalen kommer från arkivet för tabell och Regeldefinitionen som refereras av Stream Analytics-jobb kommer från blob. 

## <a name="update-the-remotemonitoring-visual-studio-solution"></a>Uppdatera RemoteMonitoring Visual Studio-lösningen

Följande steg visar hur du ändrar RemoteMonitoring Visual Studio-lösningen för att inkludera en ny regel som använder den **ExternalTemperature** telemetri som skickas från den simulerade enheten:

1. Om du inte redan har gjort det, klona den **azure-iot-remote-monitoring** databasen till en lämplig plats på den lokala datorn med följande Git-kommando:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. Öppna filen RemoteMonitoring.sln i Visual Studio från din lokala kopia av den **azure-iot-remote-monitoring** lagringsplats.

3. Öppna filen Infrastructure\Models\DeviceRuleBlobEntity.cs och lägga till en **ExternalTemperature** egenskap enligt följande:

    ```csharp
    public double? Temperature { get; set; }
    public double? Humidity { get; set; }
    public double? ExternalTemperature { get; set; }
    ```

4. Lägg till i samma fil, en **ExternalTemperatureRuleOutput** egenskap enligt följande:

    ```csharp
    public string TemperatureRuleOutput { get; set; }
    public string HumidityRuleOutput { get; set; }
    public string ExternalTemperatureRuleOutput { get; set; }
    ```

5. Öppna filen Infrastructure\Models\DeviceRuleDataFields.cs och Lägg till följande **ExternalTemperature** egenskapen efter den befintliga **fuktighet** egenskapen:

    ```csharp
    public static string ExternalTemperature
    {
        get { return "ExternalTemperature"; }
    }
    ```

6. I samma fil, uppdaterar den **_availableDataFields** metoden och ta **ExternalTemperature** på följande sätt:

    ```csharp
    private static List<string> _availableDataFields = new List<string>
    {                    
        Temperature, Humidity, ExternalTemperature
    };
    ```

7. Öppna filen Infrastructure\Repository\DeviceRulesRepository.cs och ändra den **BuildBlobEntityListFromTableRows** metoden på följande sätt:

    ```csharp
    else if (rule.DataField == DeviceRuleDataFields.Humidity)
    {
        entity.Humidity = rule.Threshold;
        entity.HumidityRuleOutput = rule.RuleOutput;
    }
    else if (rule.DataField == DeviceRuleDataFields.ExternalTemperature)
    {
      entity.ExternalTemperature = rule.Threshold;
      entity.ExternalTemperatureRuleOutput = rule.RuleOutput;
    }
    ```

## <a name="rebuild-and-redeploy-the-solution"></a>Återskapa och distribuera om lösningen.

Du kan nu distribuera den uppdaterade lösningen till din Azure-prenumeration.

1. Öppna en upphöjd kommandotolk och navigera till roten i den lokala kopian av databasen azure-iot-remote-monitoring.

2. För att distribuera din uppdaterade lösning, kör du följande kommando ersätter **{deployment-name}** med namnet på din förkonfigurerade lösningsdistribution som du antecknade tidigare:

    ```
    build.cmd cloud release {deployment name}
    ```

## <a name="update-the-stream-analytics-job"></a>Uppdatera Stream Analytics-jobbet

När distributionen är klar kan du uppdatera Stream Analytics-jobbet om du vill använda de nya regeln definitionerna.

1. Navigera till resursgruppen som innehåller din förkonfigurerade lösningsresurser i Azure-portalen. Den här resursgruppen har samma namn du angav för lösningen under distributionen.

2. Gå till {deployment-name}-regler för Stream Analytics-jobb. 

3. Klicka på **stoppa** att stoppa Stream Analytics-jobb från att köras. (Du måste vänta tills det direktuppspelade jobbet att stoppa innan du kan redigera frågan).

4. Klicka på **fråga**. Redigera en fråga som inkluderar den **Välj** instruktionen för **ExternalTemperature**. I följande exempel visas fullständig frågan med den nya **Välj** instruktionen:

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
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'ExternalTemperature' as ReadingType,
         Stream.ExternalTemperature as Reading,
         Ref.ExternalTemperature as Threshold,
         Ref.ExternalTemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.ExternalTemperature IS NOT null AND Stream.ExternalTemperature > Ref.ExternalTemperature
    )
     
    SELECT *
    INTO DeviceRulesMonitoring
    FROM AlarmsData
     
    SELECT *
    INTO DeviceRulesHub
    FROM AlarmsData
    ```

5. Klicka på **spara** att ändra den uppdaterade Regelfråga.

6. Klicka på **starta** att starta Stream Analytics-jobbet körs igen.

## <a name="add-your-new-rule-in-the-dashboard"></a>Lägg till din nya regel i instrumentpanelen

Du kan nu lägga till den **ExternalTemperature** regeln till en enhet i lösningens instrumentpanel.

1. Gå till portalen för lösningen.

2. Navigera till den **enheter** panelen.

3. Leta upp den anpassade enhet du har skapat och som skickar **ExternalTemperature** telemetri och på den **enhetsinformation** panelen, klickar du på **Lägg till regel**.

4. Välj **ExternalTemperature** i **datafält**.

5. Ange **tröskelvärdet** -56. Klicka sedan på **spara och visa regler**.

6. Gå tillbaka till instrumentpanelen och visa larmhistoriken.

7. I konsolfönstret du lämnas öppna börjar Node.js-konsolapp för att börja skicka **ExternalTemperature** telemetridata.

8. Observera att den **Larmhistorik** tabellen visar nya larm när den nya regeln utlöses.
 
## <a name="additional-information"></a>Ytterligare information

Ändra operatorn **>** är mer komplexa och mycket mer än de steg som beskrivs i den här självstudien. Du kan ändra Stream Analytics-jobbet om du vill använda den operator som du vill, vilket speglar operatorn i lösningsportalen är en mer komplicerad uppgift. 

## <a name="next-steps"></a>Nästa steg
Nu när du har sett hur du skapar anpassade regler, kan du läsa mer om de förkonfigurerade lösningarna:

- [Anslut Logikappen till Azure IoT Suite förkonfigurerade lösningen för fjärrövervakning][lnk-logic-app]
- [Förkonfigurerad lösning för enhetsmetadata information i den fjärrövervakning][lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[lnk-builtin-rule]: iot-suite-v1-getstarted-preconfigured-solutions.md#view-alarms
[lnk-dynamic-telemetry]: iot-suite-v1-dynamic-telemetry.md
[lnk-logic-app]: iot-suite-v1-logic-apps-tutorial.md