---
title: 'Självstudiekurs: Övervaka ett IoT-enhetsutrymme – Azure Digital Twins| Microsoft-dokument'
description: Lär dig mer om att etablera rumsliga resurser och övervaka arbetsvillkor med Azure Digital Twins med hjälp av stegen i den här självstudien.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 6cf6a8f7de181a81d60028e33ba2631815c8ca04
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75895371"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins-preview"></a>Självstudiekurs: Etablera dina byggnads- och övervakararbetsvillkor med Förhandsversionen av Azure Digital Twins

Den här självstudien visar hur du använder Azure Digital Twins Preview för att övervaka dina utrymmen för önskade temperaturförhållanden och komfortnivå. När du har [konfigurerat exempelbyggnaden](tutorial-facilities-setup.md) kan du etablera din byggnad och köra egna funktioner på dina sensordata med hjälp av stegen i den här självstudien.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Definiera villkor för övervakning.
> * Skapa användardefinierad funktion (UDF).
> * Simulera sensordata.
> * Hämta resultat från en användardefinierad funktion.

## <a name="prerequisites"></a>Krav

Den här självstudien förutsätter att du har [slutfört Azure Digital Twins-konfigurationen](tutorial-facilities-setup.md). Innan du fortsätter bör du kontrollera att du har:

- Ett [Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En instans av Digital Twins som körs. 
- [Digital Twins C#-exemplen](https://github.com/Azure-Samples/digital-twins-samples-csharp) har laddats ner och extraherats på din arbetsmaskin. 
- [.NET Core SDK version 2.1.403 eller senare](https://www.microsoft.com/net/download) på utvecklingsdatorn för att skapa och köra exemplet. Kör `dotnet --version` för att kontrollera att rätt version är installerad. 
- [Visual Studio Code](https://code.visualstudio.com/) för att utforska exempelkoden. 

>[!TIP]
> Använd ett unikt Digital Twins-förekomstnamn om du etablerar en ny instans.

## <a name="define-conditions-to-monitor"></a>Definiera villkor för övervakning

Du kan definiera en uppsättning specifika villkor för att övervaka i enheten eller sensordata, som kallas för *matchare*. Du kan sedan definiera funktioner som kallas för *användardefinierade funktioner*. Användardefinierade funktioner kör anpassad logik på data som kommer från dina utrymmen och enheter, när villkoren som anges av matcharna förekommer. Mer information finns i [Databearbetning och användardefinierade funktioner](concepts-user-defined-functions.md). 

Från exempelprojektet **occupancy-quickstart** öppnar du filen **src\actions\provisionSample.yaml** i Visual Studio Code. Tänk på att avsnittet börjar med typen **matchare**. Varje post under den här typen som skapar en matchare med det angivna **namnet**. Matcharen övervakar en sensor av typen **dataTypeValue**. Observera hur den relaterar till området med namnet *Focus Room A1*, som har en **enhetsnod** som innehåller några sensorer. Om du vill etablera en matchare som spårar en av dessa sensorer ser du till att dess **dataTypeValue** matchar sensorns **dataType**. 

Lägg till följande matchare under de befintliga matcharna. Kontrollera att nycklarna är justerade och att blankstegen inte ersätts av tabbar. Dessa rader finns också i filen *provisionSample.yaml* som kommenterade rader. Du kan ta bort kommentaren från dem genom att ta bort tecknet `#` före varje rad.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Denna matcher kommer `SAMPLE_SENSOR_TEMPERATURE` att spåra sensorn som du lagt till i [den första handledningen](tutorial-facilities-setup.md). 

## <a name="create-a-user-defined-function"></a>Skapa en användardefinierad funktion

Du kan använda användardefinierade funktioner för att anpassa bearbetningen av dina sensordata. De är anpassad JavaScript-kod som kan köras i din Azure Digital Twins-instans när vissa villkor som beskrivs av matchare sker. Du kan skapa matchare och användardefinierade funktioner för varje sensor som du vill övervaka. Mer information finns i [Databearbetning och användardefinierade funktioner](concepts-user-defined-functions.md). 

Leta efter ett avsnitt som börjar med typen **userdefinedfunctions**i *exempelet provisionSample.yaml.* I det här avsnittet etableras en användardefinierad funktion med ett angivet **namn**. Den här användardefinierade funktionen fungerar som listan över matchare under **matcherNames**. Lägg märke till hur du kan ange en egen JavaScript-fil för den användardefinierade funktionen som **skriptet**.

Lägg också märke till avsnittet **roleassignments**. Det tilldelar rollen Space Administrator (Utrymmesadministratör) till den användardefinierade funktionen. Med den här rollen kommer den åt de händelser som kommer från någon av de etablerade utrymmena. 

1. Konfigurera UDF så att den inkluderar temperaturmatchningen genom att `matcherNames` lägga till eller ta bort kommentera följande rad i noden i *filen provisionSample.yaml:*

    ```yaml
            - Matcher Temperature
    ```

1. Öppna filen **src\actions\userDefinedFunctions\availability.js** i redigeringsprogrammet. Det här är filen som refereras i **skriptelementet** i *provisionSample.yaml*. Den användardefinierade funktionen i den här filen söker efter villkor när ingen rörelse identifieras i rummet, samt när koldioxidnivåerna är under 1 000 ppm. 

   Ändra JavaScript-filen för att övervaka temperatur och andra villkor. Lägg till följande rader med kod för att leta efter villkor när inga rörelse identifieras i rummet, koldioxidnivåerna är lägre än 1 000 ppm och temperaturen är lägre än cirka 25 grader Celsius (78 grader Fahrenheit).

   >[!NOTE]
   > Det här avsnittet ändrar filen *src\actions\userDefinedFunctions\availability.js* så att du kan lära dig ett detaljerat sätt att skriva en användardefinierad funktion. Du kan dock välja att direkt använda filen [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) i din konfiguration. Den här filen innehåller alla ändringar som krävs för den här självstudien. Om du använder den här filen i stället ska du se till att använda rätt filnamn för **skriptnyckeln** i [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    a. Lägg till följande rader för temperatur under kommentaren `// Add your sensor type here` överst i filen:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. Lägg till följande rader efter instruktionen som definierar `var motionSensor`, under kommentaren `// Add your sensor variable here`:

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. Lägg till följande rader efter instruktionen som definierar `var carbonDioxideValue`, under kommentaren `// Add your sensor latest value here`:

    ```JavaScript
        var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
    ```

    d. Ta bort följande rader med kod under kommentaren `// Modify this line to monitor your sensor value`:

     ```JavaScript
        if(carbonDioxideValue === null || motionValue === null) {
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
            return;
        }
    ```

    Ersätt dem med följande rader:

    ```JavaScript
        if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
            return;
        }
    ```

    e. Ta bort följande rader med kod under kommentaren `// Modify these lines as per your sensor`:

    ```JavaScript
        var availableFresh = "Room is available and air is fresh";
        var noAvailableOrFresh = "Room is not available or air quality is poor";
    ```

    Ersätt dem med följande rader:

    ```JavaScript
        var alert = "Room with fresh air and comfortable temperature is available.";
        var noAlert = "Either room is occupied, or working conditions are not right.";
    ```

    f. Ta bort följande *if-else*-kodblock efter under kommentaren `// Modify this code block for your sensor`:

    ```JavaScript
        // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
        if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
            log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);
        }
        else {
            log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

            // Set up custom notification for poor air quality
            parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
        }
    ```

    Och ersätt den med följande *if-else*-block:

    ```JavaScript
        // If sensor values are within range and room is available
        if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
            log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

            // Set up notification for this alert
            parentSpace.Notify(JSON.stringify(alert));
        }
        else {
            log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
        }
    ```

    De ändrade UDF:erna söker efter ett villkor där ett rum blir tillgängligt vars koldioxidmängd och temperatur ligger inom de tillåtna gränserna. Det ska generera ett meddelande med instruktionen `parentSpace.Notify(JSON.stringify(alert));` när det här villkoret är uppfyllt. Det anger värdet för det övervakade utrymmet oavsett om villkoret är uppfyllt, med motsvarande meddelande.

    g. Spara filen.

1. Öppna ett kommandofönster och gå till mappen **occupancy-quickstart\src**. Kör följande kommando för att etablera din graf för rumslig information och användardefinierade funktion:

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   >[!IMPORTANT]
   > För att förhindra obehörig åtkomst till hanterings-API:et för Digital Twins kräver programmet **occupancy-quickstart** att du loggar in med autentiseringsuppgifterna för ditt Azure-konto. Dina autentiseringsuppgifter sparas under en kort period, så du kanske inte måste logga in varje gång. Första gången som programmet körs, och när dina sparade autentiseringsuppgifter upphör att gälla efter det, dirigerar programmet dig till en inloggningssida och ger en sessionsspecifik kod att ange på den sidan. Följ anvisningarna för att logga in på ditt Azure-konto.

1. När ditt konto har autentiserats börjar programmet skapa ett rumsligt exempeldiagram som konfigurerats i *provisionSample.yaml*. Vänta tills etableringen har slutförts. Det tar några minuter. Därefter ska du notera meddelandena i kommandofönstret och hur den rumsliga grafen har skapats. Observera hur programmet skapar en IoT-hubb på rotnoden eller i `Venue`.

1. Från utdata i kommandofönstret kopierar du värdet för `ConnectionString` under avsnittet `Devices` till Urklipp. Du behöver det här värdet för att simulera enhetsanslutning i nästa avsnitt.

    [![Etablera exempel](./media/tutorial-facilities-udf/run-provision-sample.png)](./media/tutorial-facilities-udf/run-provision-sample.png#lightbox)

>[!TIP]
> Om du får ett felmeddelande liknande ”I/O-åtgärden avbröts eftersom en tråd avslutades eller på grund av att ett program begärde det” under etableringen kan du försöka att köra kommandot igen. Detta kan inträffa om HTTP-klientens tidsgräns gått ut på grund av ett nätverksproblem.

## <a name="simulate-sensor-data"></a>Simulera sensordata

I det här avsnittet använder du ett projekt med namnet *device-connectivity* i exemplet. Du simulerar sensordata för att identifiera rörelse, temperatur och koldioxid. Det här projektet genererar slumpmässiga värden för sensorerna och skickar dem till IoT-hubben med hjälp av enhetens anslutningssträng.

1. I ett separat kommandofönster går du till Azure Digital Twins-exemplet och sedan till mappen **device-connectivity**.

1. Kör detta kommando för att kontrollera att projektets beroenden är rätt:

    ```cmd/sh
    dotnet restore
    ```

1. Öppna [filen appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) i redigeraren och redigera följande värden:

   a. **DeviceConnectionString**: tilldela värdet för `ConnectionString` i utdatafönstret i föregående avsnitt. Kopiera strängen helt, inom citattecknen, så att simulatorn kan ansluta till IoT-hubben.

   b. **HardwareId** i matrisen **Sensorer:** Eftersom du simulerar händelser från sensorer som etablerats till din Azure Digital Twins-instans, `sensors` bör maskinvaru-ID och namnen på sensorerna i den här filen matcha noden för *filen provisionSample.yaml.*

      Lägg till en ny post för temperatursensorn. **Noden Sensorer** i *appsettings.json* ska se ut så här:

      ```JSON
      "Sensors": [{
        "DataType": "Motion",
        "HardwareId": "SAMPLE_SENSOR_MOTION"
      },{
        "DataType": "CarbonDioxide",
        "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
      },{
        "DataType": "Temperature",
        "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
      }]
      ```

1. Kör följande kommando för att börja simulera enhetshändelser för temperatur, rörelse och koldioxid:

    ```cmd/sh
    dotnet run
    ```

   >[!NOTE]
   > Eftersom simuleringsexemplet inte kommunicerar direkt med din instans av Digital Twins måste du inte autentisera.

## <a name="get-results-of-the-user-defined-function"></a>Hämta resultat från den användardefinierad funktionen

Den användardefinierade funktionen körs varje gång din instans tar emot data för enheten och sensorn. I det här avsnittet ombeds din Azure Digital Twins-instans att hämta resultatet för den användardefinierade funktionen. Du kommer att meddelas i nära realtid, när ett rum är tillgängligt, att luften är frisk och temperaturen är rätt. 

1. Öppna kommandofönstret som du använde för att etablera exemplet eller ett nytt kommandofönster och gå till mappen **occupancy-quickstart\src i exemplet igen**.

1. Kör följande kommando och logga in när du tillfrågas:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

Utdatafönstret visar hur den användardefinierade funktionen körs och fångar upp händelser från enhetssimuleringen. 

   [![Utdata för den användardefinierade funktionen](./media/tutorial-facilities-udf/adt-tutorial-udf-running.png)](./media/tutorial-facilities-udf/adt-tutorial-udf-running.png#lightbox)

Om det övervakade villkoret uppfylls anger den användardefinierade funktionen värdet för utrymmet med relevant meddelande, som vi såg [tidigare](#create-a-user-defined-function). Funktionen `GetAvailableAndFreshSpaces` skriver ut meddelandet på konsolen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte vill utforska Azure Digital Twins nu kan du ta bort resurser som du har skapat i den här självstudien:

1. På den vänstra menyn i [Azure-portalen](https://portal.azure.com) väljer du **Alla resurser**, väljer din Digital Twins-resursgrupp och **Ta bort**.

    >[!TIP]
    > Om det inträffade problem när du skulle ta bort Digital Twins-instansen finns det nu en tjänstuppdatering som åtgärdar det. Försök att ta bort instansen igen.

2. Ta bort exempelprogrammen på datorn om det behövs.

## <a name="next-steps"></a>Nästa steg

Nu när du har etablerat dina utrymmen och skapat ett ramverk för att utlösa anpassade meddelanden kan du gå till någon av följande självstudier:

> [!div class="nextstepaction"]
> [Självstudie: Ta emot meddelanden från dina Azure Digital Twins-utrymmen med hjälp av Logic Apps](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [Självstudie: Visualisera och analysera händelser från dina Azure Digital Twins-utrymmen med hjälp av Time Series Insights](tutorial-facilities-analyze.md)
