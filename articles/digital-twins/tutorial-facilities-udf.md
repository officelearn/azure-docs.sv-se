---
title: Övervaka ett utrymme med Azure Digital Twins | Microsoft Docs
description: Lär dig mer om att etablera rumsliga resurser och övervaka arbetsvillkor med Azure Digital Twins med hjälp av stegen i den här självstudien.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/26/2018
ms.author: dkshir
ms.openlocfilehash: cf45cb8de0e40dfe5f5772dcb1a0be2aa7585fd6
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156688"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins"></a>Självstudie: Etablera din byggnad och övervaka arbetsvillkor med Azure Digital Twins

Den här kursen visar hur du använder Azure Digital Twins för att övervaka dina utrymmen för önskad temperatur och komfort. När du har [konfigurerat exempelbyggnaden](tutorial-facilities-setup.md) kan du etablera din byggnad och köra egna funktioner på dina sensordata med hjälp av stegen i den här självstudien.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Definiera villkor för övervakning
> * Skapa en användardefinierad funktion
> * Simulera sensordata
> * Hämta resultat från användardefinierad funktion

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien förutsätter att du har [konfigurerat Azure Digital Twins-konfigurationen](tutorial-facilities-setup.md). Innan du fortsätter bör du kontrollera att du har:
- ett [Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F),
- en instans av Digital Twins som körs, 
- [Digital Twins C#-exempel](https://github.com/Azure-Samples/digital-twins-samples-csharp) nedladdade och extraherade på din arbetsdator, 
- [.NET Core SDK version 2.1.403 eller senare](https://www.microsoft.com/net/download) på utvecklingsdatorn för att skapa och köra exemplet. Kör `dotnet --version` för att kontrollera att rätt version är installerad. 
- [Visual Studio Code](https://code.visualstudio.com/) för att utforska exempelkoden. 

## <a name="define-conditions-to-monitor"></a>Definiera villkor för övervakning
Du kan definiera en uppsättning specifika villkor för att övervaka i enheten eller sensordata, som kallas för **matchare**. Du kan därefter anropa funktioner som kallas för *användardefinierade funktioner*, som kör anpassad logik på data som kommer från dina utrymmen och enheter när villkoren som anges av matcharna inträffar. Mer information finns i [Databearbetning och användardefinierade funktioner](concepts-user-defined-functions.md). 

Från exempelprojektet **_occupancy-quickstart_** öppnar du filen **_src\actions\provisionSample.yaml_** i Visual Studio Code. Tänk på att avsnittet börjar med typen **matchare**. Varje post under den här typen skapar en matchare med det angivna **namnet**, som övervakar en sensor av typen **dataTypeValue**. Observera hur den relaterar till området med namnet *Focus Room A1*, som har en **enhetsnod** som innehåller några **sensorer**. För att etablera en matchare som spårar en av dessa sensorer ska dess **dataTypeValue** stämma överens med den sensorns **dataType**. 

Lägg till följande matchare under befintliga matchare, och se till att nycklarna justeras och att utrymmena inte ersätts av flikar:

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Detta spårar sensorn *SAMPLE_SENSOR_TEMPERATURE* som du la till i [den första självstudien](tutorial-facilities-setup.md). Observera att dessa rader även finns i *provisionSample.yaml* som kommenterade rader. Du kan helt enkelt ta bort kommentarer från dem genom att ta bort tecknet # framför varje rad. 

<a id="udf" />

## <a name="create-a-user-defined-function"></a>Skapa en användardefinierad funktion
Med användardefinierade funktioner eller UDF:er kan du anpassa bearbetningen av dina sensordata. De är anpassad JavaScript-kod som kan köras i din Digital Twins-instans när vissa villkor som beskrivs av matchare sker. Du kan skapa *matchare* och *användardefinierade funktioner* för varje sensor som du vill övervaka. Mer detaljerad information finns i [Databearbetning och användardefinierade funktioner](concepts-user-defined-functions.md). 

I exempelfilen *provisionSample.yaml* letar du efter ett avsnitt som börjar med typen **userdefinedfunctions**. Det här avsnittet etablerar en användardefinierad funktion med ett angivet **namn** som agerar utifrån listan över matchare under **matcherNames**. Lägg märke till hur du kan ange en egen JavaScript-fil för den användardefinierade funktionen som **skriptet**. Lägg också märke till avsnittet **roleassignments**. Det tilldelar rollen som *utrymmesadministratör* till en användardefinierad funktion. På så sätt kan komma åt de händelser som kommer från någon av de etablerade utrymmena. 

1. Konfigurera en användardefinierad funktion för att inkludera temperaturmatcharen genom att lägga till eller avkommentera följande rad i `matcherNames`-noden i filen *provisionSample.yaml*:

    ```yaml
            - Matcher Temperature
    ```

1. Öppna filen **_src\actions\userDefinedFunctions\availability.js_** i redigeringsprogrammet. Det här är filen som det hänvisas till i **skriptelementet** i *provisionSample.yaml*. Den användardefinierade funktionen i den här filen söker efter villkor när ingen rörelse identifieras i rummet, samt när koldioxidnivåerna är under 1 000 ppm. Ändra JavaScript-filen för att övervaka temperatur utöver andra villkor. Lägg till följande rader med kod för att leta efter villkor när inga rörelse identifieras i rummet, koldioxidnivåerna är lägre än 1 000 ppm och temperaturen är lägre än cirka 25 grader Celsius (78 grader Fahrenheit).

   > [!NOTE]
   > Det här avsnittet ändrar filen *src\actions\userDefinedFunctions\availability.js* så att du kan lära dig ett detaljerat sätt att skriva en användardefinierad funktion. Du kan dock välja att direkt använda filen [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) i din konfiguration. Den här filen innehåller alla ändringar som krävs för den här självstudien. Om du använder den här filen i stället ska du se till att använda rätt filnamn för **_skriptnyckeln_** i [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    1. Lägg till följande rader för temperatur under kommentaren `// Add your sensor type here` överst i filen:

        ```JavaScript
            var temperatureType = "Temperature";
            var temperatureThreshold = 78;
        ```
   
    1. Lägg till följande rader efter instruktionen som definierar `var motionSensor`, under kommentaren `// Add your sensor variable here`:

        ```JavaScript
            var temperatureSensor = otherSensors.find(function(element) {
                return element.DataType === temperatureType;
            });
        ```
    
    1. Lägg till följande rader efter instruktionen som definierar `var carbonDioxideValue`, under kommentaren `// Add your sensor latest value here`:

        ```JavaScript
            var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
        ```
    
    1. Ta bort följande rader med kod under kommentaren `// Modify this line to monitor your sensor value`: 

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
    
    1. Ta bort följande rader med kod under kommentaren `// Modify these lines as per your sensor`:

        ```JavaScript
            var availableFresh = "Room is available and air is fresh";
            var noAvailableOrFresh = "Room is not available or air quality is poor";
        ```

       Ersätt dem med följande rader:

        ```JavaScript
            var alert = "Room with fresh air and comfortable temperature is available.";
            var noAlert = "Either room is occupied, or working conditions are not right.";
        ```
    
    1. Ta bort följande *if-else*-kodblock efter under kommentaren `// Modify this code block for your sensor`:

        ```JavaScript
            // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
            if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
                log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
                setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);

                // Set up custom notification for air quality
                parentSpace.Notify(JSON.stringify(availableFresh));
            }
            else {
                log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
                setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

                // Set up custom notification for air quality
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
    
    1. Spara filen. 
    
1. Öppna kommandofönstret och gå till mappen **_occupancy-quickstart\src_**. Kör följande kommando för att etablera din graf för rumslig intelligens och användardefinierad funktion. 

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > För att förhindra obehörig åtkomst till hanterings-API:et för Digital Twins kräver programmet **_occupancy-quickstart_** att du loggar in med autentiseringsuppgifterna för ditt Azure-konto. Dina autentiseringsuppgifter sparas under en kort period, så du kanske inte måste logga in varje gång. När programmet körs för första gången, samt när dina sparade autentiseringsuppgifter upphör att gälla efter det, dirigeras du till en inloggningssida där du får en sessionsspecifik kod som ska anges på sidan. Följ anvisningarna för att logga in på ditt Azure-konto.


1. När ditt konto har autentiserats börjar programmet skapa ett exempel på en rumslig graf enligt konfigurationen i *provisionSample.yaml*. Vänta tills etableringen har slutförts. Det tar några minuter. När det är klart ska du notera meddelandena i kommandofönstret och hur den rumsliga grafen har skapats. Observera hur den skapar en IoT-hubb på rotnoden eller `Venue`. 

1. Från utdata i kommandofönstret kopierar du värdet för `ConnectionString` under avsnittet `Devices` till Urklipp. Du behöver det här värdet för att simulera enhetsanslutning i följande avsnitt.

    ![Etablera exempel](./media/tutorial-facilities-udf/run-provision-sample.png)

> [!TIP]
> Om du får ett felmeddelande liknande ”I/O-åtgärden avbröts eftersom en tråd avslutades eller på grund av att ett program begärde det” under etableringen kan du försöka att köra kommandot igen. Detta kan inträffa om HTTP-klientens tidsgräns gått ut på grund av ett nätverksproblem.

<a id="simulate" />

## <a name="simulate-sensor-data"></a>Simulera sensordata
I det här avsnittet använder du projektet med namnet *device-connectivity* i det här exemplet för att simulera sensordata för att identifiera rörelse, temperatur och koldioxid. Det här projektet genererar slumpmässiga värden för sensorerna och skickar dem till IoT-hubben med hjälp av enhetens anslutningssträng.

1. I ett separat kommandofönster navigerar du till Digital Twins-exemplet och sedan till mappen **_device-connectivity_**.

1. Kör detta kommando för att kontrollera att projektets beroenden är rätt:

    ```cmd/sh
    dotnet restore
    ```

1. Öppna filen *appSettings.json* i redigeringsprogrammet och redigera följande värden:
    1. *DeviceConnectionString*: tilldela värdet för `ConnectionString` i utdatafönstret i föregående avsnitt. Se till att kopiera den här strängen helt, inom citattecken, för att simulatorn ska ansluta ordentligt till IoT-hubben.

    1. *HardwareId* inom matrisen *Sensorer*: Eftersom du simulerar händelser från sensorer som etablerats på din Digital Twins-instans bör maskinvaru-ID och namnen på sensorerna i den här filen matcha med `sensors`-noden i filen *provisionSample.yaml*. Lägg till en ny post för temperatursensorn. Noden **Sensorer** i *appSettings.json* bör se ut så här:

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

   > [!NOTE] 
   > Eftersom simuleringsexemplet inte kommunicerar direkt med din instans av Digital Twins måste du inte autentisera.

## <a name="get-results-of-user-defined-function"></a>Hämta resultat från användardefinierad funktion
Den användardefinierade funktionen körs varje gång din instans tar emot data för enheten och sensorn. I det här avsnittet ombeds din Digital Twins-instans att hämta resultatet för den användardefinierade funktionen. Du ser i nära realtid när det finns ett tillgängligt rum, luften är frisk och temperaturen är rätt. 

1. Öppna kommandofönstret som du använde för att etablera exemplet eller ett nytt kommandofönster och navigera till mappen **_occupancy-quickstart\src_** i exemplet igen. 

1. Kör följande kommando och logga in när du tillfrågas:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

Utdatafönstret visar hur den användardefinierade funktionen körs och fångar upp händelser från enhetssimuleringen. 

   ![Köra UDF](./media/tutorial-facilities-udf/udf-running.png)

Beroende på om de övervakade villkoren uppfylls eller inte anger den användardefinierade funktionen värdet för utrymmet med det relevanta meddelande vi såg i [avsnittet ovan](#udf), som `GetAvailableAndFreshSpaces`-funktionen skriver ut på konsolen. 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte vill utforska Azure Digital Twins vidare kan du ta bort resurser som du har skapat i den här självstudien:

1. På den vänstra menyn i [Azure-portalen](http://portal.azure.com) klickar du på **Alla resurser**, väljer din Digital Twins-resursgrupp och **tar bort** den.
2. Om du vill kan fortsätta att ta bort exempelprogrammen på arbetsdatorn också. 


## <a name="next-steps"></a>Nästa steg

Nu när du har etablerat dina utrymmen och skapat ett ramverk för att utlösa anpassade meddelanden kan du gå vidare till någon av följande självstudier. 

> [!div class="nextstepaction"]
> [Självstudie: Ta emot meddelanden från dina Azure Digital Twins-utrymmen med hjälp av Logic Apps](tutorial-facilities-events.md)

Eller:

> [!div class="nextstepaction"]
> [Självstudie: Visualisera och analysera händelser från dina Azure Digital Twins-utrymmen med hjälp av Time Series Insights](tutorial-facilities-analyze.md)
