---
title: Skapa en anpassad regel i Azure IoT Suite | Microsoft Docs
description: "Så här skapar du en anpassad regel i en IoT Suite förkonfigurerade lösning."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: d58c27234ea05a82aaa3e8d72f70c1449980df09
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="create-a-custom-rule-in-the-remote-monitoring-preconfigured-solution"></a>Skapa en anpassad regel i fjärråtkomst övervakning förkonfigurerade lösningen

## <a name="introduction"></a>Introduktion

Förkonfigurerade lösningar du kan konfigurera [regler som utlöses när en telemetri värdet för en enhet når ett visst tröskelvärde][lnk-builtin-rule]. [Använd dynamiska telemetri med fjärråtkomst övervakning förkonfigurerade lösningen] [ lnk-dynamic-telemetry] beskriver hur du lägger till anpassad telemetri värden som *ExternalTemperature* i lösningen. Den här artikeln visar hur du skapar en anpassad regel för dynamiskt telemetri typer i din lösning.

Den här kursen använder en enkel Node.js simulerad enhet för att generera dynamiska telemetri för att skicka till förkonfigurerade lösningens serverdel. Du sedan lägga till anpassade regler i den **RemoteMonitoring** Visual Studio-lösning och distribuera den här anpassade serverdel till din Azure-prenumeration.

För att slutföra den här kursen behöver du:

* En aktiv Azure-prenumeration. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [kostnadsfri utvärderingsversion av Azure][lnk_free_trial].
* [Node.js] [ lnk-node] version 0.12.x eller senare för att skapa en simulerad enhet.
* Visual Studio 2015 eller Visual Studio 2017 ändra förkonfigurerade lösningen tillbaka avslutas med din nya regler.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

Anteckna lösningens namn som du har valt för din distribution. Du behöver den här lösningsnamn senare i den här kursen.

[!INCLUDE [iot-suite-send-external-temperature](../../includes/iot-suite-send-external-temperature.md)]

Du kan stoppa Node.js-konsolprogram när du har verifierat att den skickar **ExternalTemperature** telemetri till förkonfigurerade lösningen. Behåll konsolfönstret öppna eftersom du kör den här Node.js-konsolprogram igen när du lägger till anpassad regel i lösningen.

## <a name="rule-storage-locations"></a>Lagringsplatser för regeln

Information om regler sparas på två platser:

* **DeviceRulesNormalizedTable** tabellen – den här tabellen lagras en normaliserade referens till de regler som definierats av lösningen portalen. När lösningen portal visas enheten regler, frågar den här tabellen för Regeldefinitioner.
* **DeviceRules** blob – den här blob lagrar alla regler som definierats för alla registrerade enheter och har definierats som en referens som indata till Azure Stream Analytics-jobb.
 
När du uppdaterar en befintlig regel eller definiera en ny regel i lösningen portal, har tabell- och blobbdata uppdaterats för att återspegla ändringarna. Regeldefinitionen visas i portalen hämtas från arkivet tabell och Regeldefinitionen refererar till Stream Analytics-jobb kommer från blob. 

## <a name="update-the-remotemonitoring-visual-studio-solution"></a>Uppdatera RemoteMonitoring Visual Studio-lösning

Följande steg visar hur du ändrar RemoteMonitoring Visual Studio-lösning för att inkludera en ny regel som använder den **ExternalTemperature** telemetri som skickats från den simulerade enheten:

1. Om du inte redan har gjort det klona den **azure iot-fjärr-övervakning** databasen till en lämplig plats på den lokala datorn med följande Git-kommando:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. Öppna filen RemoteMonitoring.sln i Visual Studio från den lokala kopian av den **azure iot-fjärr-övervakning** databasen.

3. Öppna filen Infrastructure\Models\DeviceRuleBlobEntity.cs och lägga till en **ExternalTemperature** egenskapen på följande sätt:

    ```csharp
    public double? Temperature { get; set; }
    public double? Humidity { get; set; }
    public double? ExternalTemperature { get; set; }
    ```

4. Lägg till i samma fil, en **ExternalTemperatureRuleOutput** egenskapen på följande sätt:

    ```csharp
    public string TemperatureRuleOutput { get; set; }
    public string HumidityRuleOutput { get; set; }
    public string ExternalTemperatureRuleOutput { get; set; }
    ```

5. Öppna filen Infrastructure\Models\DeviceRuleDataFields.cs och Lägg till följande **ExternalTemperature** egenskapen när den befintliga **fuktighet** egenskapen:

    ```csharp
    public static string ExternalTemperature
    {
        get { return "ExternalTemperature"; }
    }
    ```

6. Uppdatera i samma fil i **_availableDataFields** metod för att inkludera **ExternalTemperature** på följande sätt:

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

## <a name="rebuild-and-redeploy-the-solution"></a>Återskapa och distribuera lösningen igen.

Du kan nu distribuera den uppdaterade lösningen till din Azure-prenumeration.

1. Öppna en upphöjd kommandotolk och navigera till roten i den lokala kopian av databasen azure iot-fjärr-övervakning.

2. För att distribuera din uppdaterade lösning, kör du följande kommando ersätter **{distributionsnamnet}** med namnet på din förkonfigurerade lösningsdistribution som du antecknade tidigare:

    ```
    build.cmd cloud release {deployment name}
    ```

## <a name="update-the-stream-analytics-job"></a>Uppdatera Stream Analytics-jobbet

När installationen är klar, kan du uppdatera Stream Analytics-jobbet om du vill använda de nya definitionerna.

1. Navigera till den resursgrupp som innehåller din förkonfigurerade lösning resurser i Azure-portalen. Den här resursgruppen har samma namn som du angav för lösningen under distributionen.

2. Navigera till {distributionsnamnet}-regler Stream Analytics-jobbet. 

3. Klicka på **stoppa** att stoppa Stream Analytics-jobbet körs. (Du måste vänta tills direktuppspelningsjobbet att stoppa innan du kan redigera frågan).

4. Klicka på **frågan**. Redigera en fråga som inkluderar den **Välj** -uttrycket för **ExternalTemperature**. I följande exempel visar slutförd fråga med det nya **Välj** instruktionen:

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

5. Klicka på **spara** att ändra frågan uppdaterade regeln.

6. Klicka på **starta** att starta Stream Analytics-jobbet körs igen.

## <a name="add-your-new-rule-in-the-dashboard"></a>Lägg till din nya regel i instrumentpanelen

Du kan nu lägga till den **ExternalTemperature** regeln till en enhet i instrumentpanelen för lösningen.

1. Gå till portalen för lösningen.

2. Navigera till den **enheter** panelen.

3. Leta upp den anpassa enhet som du skapade som skickar **ExternalTemperature** telemetri och på den **enhetsinformation** klickar du på **Lägg till regel**.

4. Välj **ExternalTemperature** i **datafält**.

5. Ange **tröskelvärdet** -56. Klicka på **spara och visa regler**.

6. Gå tillbaka till instrumentpanelen för att visa larm historiken.

7. I konsolträdet du lämnat öppna börjar Node.js-konsolprogram om du vill börja skicka **ExternalTemperature** telemetridata.

8. Observera att den **larm historik** tabell visas nya larm när den nya regeln utlöses.
 
## <a name="additional-information"></a>Ytterligare information

Ändra operatorn  **>**  är mer komplexa och utöver de steg som beskrivs i den här självstudiekursen. Du kan ändra Stream Analytics-jobbet om du vill använda den operator som du vill, avspeglar den operatorn i lösningen portal är en mer komplicerad uppgift. 

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du skapar egna regler, kan du lära dig mer om förkonfigurerade lösningar:

- [Ansluta Logikappen i Azure IoT Suite Fjärrövervaknings förkonfigurerade lösningen][lnk-logic-app]
- [Enhetens information metadata för fjärranslutna övervakningen förkonfigurerade lösningen][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[lnk-builtin-rule]: iot-suite-getstarted-preconfigured-solutions.md#view-alarms
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md
[lnk-logic-app]: iot-suite-logic-apps-tutorial.md