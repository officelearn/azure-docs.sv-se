<properties
    pageTitle="Komma igång med Azure Stream Analytics för bearbetning av data från IoT-enheter. | Stream Analytics"
    description="IoT-sensortaggar och dataströmmar med dataströmsanalys och realtidsbearbetning av data"
    keywords="iot-lösning, komma igång med iot"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="paulettm"
    editor="cgronlun"
/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="hero-article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="08/11/2016"
    ms.author="jeffstok"
/>

# Komma igång med Azure Stream Analytics för bearbetning av data från IoT-enheter

I den här självstudiekursen lär du dig hur du skapar logiken för bearbetning av dataströmmar för datainsamling från IoT-enheter (Internet of Things). Vi använder verkliga IoT-användningsfall (Internet of Things) för att demonstrera hur du snabbt och billigt kan skapa din lösning.

## Krav

-   [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/)
-   Du kan ladda ned exempel på frågefiler och datafiler från [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## Scenario

Contoso är ett företag inom industriell automation och har helt automatiserat sin produktionsprocess. Maskinerna på den här anläggningen har sensorer som sänder dataströmmar i realtid. I detta scenario vill en av företagets fabrikschefer få realtidsinsikter från dessa sensordata för att identifiera mönster och vidta lämpliga åtgärder utifrån resultatet. Vi ska använda Stream Analytics Query Language (SAQL) mot sensorinformationen för att identifiera intressanta mönster i den inkommande dataströmmen.

Här genereras data från en Texas Instruments Sensor Tag-enhet.

![Texas Instruments Sensor Tag](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

Nyttolasten för dessa data är i JSON-format och ser ut ungefär så här:

    
    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  
    
I ett verkligt scenario har du flera hundra av dessa sensorer som genererar händelser som en dataström. I en idealisk situation skulle det finnas en gateway-enhet som körde kod för att skicka dessa händelser till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) eller [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/). Ditt Stream Analytics-jobb skulle mata in de här händelserna från Event Hubs och köra analysfrågor i realtid mot dataströmmarna. Sedan kunde du skicka resultaten till någon av de [utdatatyper som stöds](stream-analytics-define-outputs.md).

I den här Komma igång-guiden tillhandahåller vi för enkelhetens skull en exempeldatafil som hämtats från verkliga SensorTag-enheter och som du kan köra olika frågor och visa resultaten på. I efterföljande självstudiekurser lär du dig hur du kopplar jobbet till in- och utdataenheter och hur du distribuerar det till Azure-tjänsten.

## Skapa Stream Analytics-jobbet

I [Azure Portal](http://manage.windowsazure.com) öppnar du Stream Analytics och klickar på **Nytt** i det nedre vänstra hörnet på sidan för att skapa ett nytt analysjobb.

![Skapa ett nytt Stream Analytics-jobb](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

Klicka på **Snabbregistrering**.

För inställningen **Lagringskonto för regional övervakning** väljer du **Skapa nytt lagringskonto** och ger kontot ett unikt namn. Azure Stream Analytics använder det här kontot för att lagra övervakningsinformation för alla framtida jobb.

> [AZURE.NOTE] Du bör endast skapa det här lagringskontot en gång per region. Lagringen delas mellan alla Stream Analytics-jobb som skapats i den regionen.

Klicka på **Skapa Stream Analytics-jobb** längst ned på sidan.

![Konfiguration av lagringskonto](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.jpg)

## Azure Stream Analytics-fråga

Öppna Frågeredigeraren genom att klicka på fliken Fråga. Fliken Fråga innehåller en SQL-fråga som utför transformationen av inkommande data.

## Arkivera dina rådata

Den enklaste formen av frågan är en anslutningsfråga som arkiverar alla indata till dess angivna utdataenheter.

![Arkivera jobbfråga](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

Fortsätt och ladda ned exempeldatafilen från [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) till en plats på datorn. Kopiera och klistra in frågan från filen **PassThrough.txt**. Klicka på knappen Testa nedan och välj datafilen med namnet **HelloWorldASA-InputStream.json** från platsen som du laddade ned filen till.

![Testknappen i Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

![Testa indataströmmen](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

Du kan se resultaten av frågan i webbläsaren, som visas nedan.

![Testresultat](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

## Filtrera data baserat på ett villkor

Nu ska vi prova att filtrera resultatet baserat på ett villkor. Vi vill bara visa resultat för de händelser som kommer från ”SensorA”. Frågan finns i **Filtering.txt**-filen.

![filtrera en dataström](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Observera att vi här jämför ett strängvärde och att det är skiftlägeskänsligt. Klicka på knappen **Kör om** för att köra frågan. Frågan bör endast returnera 389 rader från 1 860 händelser.

![Andra utdataresultat från frågetest](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

## Varna för att utlösa företagsarbetsflöde

Nu gör vi vår fråga mer detaljerad. Om vi vill övervaka genomsnittstemperaturen per 30-sekundersfönster och visa resultat bara om genomsnittet överstiger 100 grader, skriver vi frågan nedan för varje sensortyp och klickar på **Kör om** för att se resultaten. Frågan finns i filen **ThresholdAlerting.txt**.

![30 sekunders-filterfråga](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Som du ser innehåller resultatet bara 245 rader med de sensorer där medeltemperaturen är högre än 100. I den här frågan har vi grupperat händelseströmmen efter **dspl**, som är sensornamnet och under ett **Rullande fönster** på 30 sekunder. När vi kör den här typen av temporala frågor är det viktigt att ange hur vi vill behandla tiden för att gå framåt. Med hjälp av **TIMESTAMP BY**-satsen har vi angett ”time”-kolumnen som ett sätt att behandla tiden för alla temporala beräkningar. Mer detaljerad information finns i MSDN-ämnena om [tidshantering](https://msdn.microsoft.com/library/azure/mt582045.aspx) och [fönsterfunktioner](https://msdn.microsoft.com/library/azure/dn835019.aspx).

![Temp över 100](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

## Identifiera avsaknad av händelser

Hur kan vi skriva en fråga för att hitta brist på inmatningshändelser? Det är rätt enkelt att göra det. Vi kan till exempel ta reda på den sista gången en sensor skickade data men sedan inte skickade några händelser under den följande minuten. Frågan finns i filen **AbsenseOfEvent.txt**.

![Identifiera avsaknad av händelser](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-12.png)

Här använder vi en **LEFT OUTER JOIN**-koppling (vänster yttre) för samma dataström (självkoppling). För en inre koppling returneras resultatet bara om en matchning hittas.  Men om en händelse från vänster sida i kopplingen är omatchad i en **LEFT OUTER**-koppling (vänster yttre) returneras en rad med Null för alla kolumner i den högra raden. Den här tekniken är väldigt användbar för att hitta frånvaro av händelser. Mer information om [JOIN](https://msdn.microsoft.com/library/azure/dn835026.aspx) finns i MSDN-dokumentationen.

![kopplingsresultatet](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-13.png)

## Slutsats

Syftet med de här självstudierna är att demonstrera hur du skriver olika Stream Analytics-frågespråkfrågor och visar resultatet i webbläsaren. Men det här är bara början. Det finns så mycket mer som du kan göra med Stream Analytics. Stream Analytics stöder olika typer av indata och utdata och kan även dra nytta av funktioner i Azure Machine Learning, vilket gör det till ett stabilt verktyg för att analysera dataströmmar. Fortsätt gärna att utforska Stream Analytics med hjälp av vår [utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/), och om du vill lära dig mer om hur du skriver frågor rekommenderar vi artikeln om [vanliga frågemönster](./stream-analytics-stream-analytics-query-patterns.md).



<!--HONumber=sep16_HO1-->


