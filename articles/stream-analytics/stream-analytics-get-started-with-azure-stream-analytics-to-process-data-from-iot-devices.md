---
title: "IoT-realtidsdataströmmar och Azure Stream Analytics | Microsoft Docs"
description: "IoT-sensortaggar och dataströmmar med dataströmsanalys och realtidsbearbetning av data"
keywords: "iot-lösning, komma igång med iot"
services: stream-analytics
documentationcenter: 
author: SnehaGunda
manager: jhubbard
editor: cgronlun
ms.assetid: 3e829055-75ed-469f-91f5-f0dc95046bdb
ms.service: stream-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: sngun
ms.openlocfilehash: a4b2fda6c5cc5ea341618ec5fa8638a5c887bf84
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Komma igång med Azure Stream Analytics för bearbetning av data från IoT-enheter
I den här självstudiekursen lär du dig hur du skapar logiken för bearbetning av dataströmmar för datainsamling från IoT-enheter (Internet of Things). Vi använder verkliga IoT-användningsfall (Internet of Things) för att demonstrera hur du snabbt och billigt kan skapa din lösning.

## <a name="prerequisites"></a>Förutsättningar
* [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/)
* Du kan ladda ned exempel på frågefiler och datafiler från [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Scenario
Contoso, ett företag inom industriell automation, har helt automatiserat sin produktionsprocess. Maskinerna på den här anläggningen har sensorer som sänder dataströmmar i realtid. I detta scenario vill en av företagets fabrikschefer få realtidsinsikter från dessa sensordata för att identifiera mönster och vidta lämpliga åtgärder utifrån resultatet. Vi ska använda Stream Analytics Query Language (SAQL) mot sensorinformationen för att identifiera intressanta mönster i den inkommande dataströmmen.

Här genereras data från en enhet med Texas Instruments SensorTag. Nyttolasten för dessa data är i JSON-format och ser ut ungefär så här:

    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  

I ett verkligt scenario har du flera hundra av dessa sensorer som genererar händelser som en dataström. I en idealisk situation skulle det finnas en gateway-enhet som kör kod för att skicka dessa händelser till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) eller [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/). Ditt Stream Analytics-jobb skulle mata in de här händelserna från Event Hubs och köra analysfrågor i realtid mot dataströmmarna. Sedan kunde du skicka resultaten till någon av de [utdatatyper som stöds](stream-analytics-define-outputs.md).

För att förenkla användningen av den här komma igång-guiden ingår en exempeldatafil med data från verkliga SensorTag-enheter. Du kan köra frågor på dessa exempeldata och se resultaten. I efterföljande självstudiekurser lär du dig hur du kopplar jobbet till in- och utdataenheter och hur du distribuerar det till Azure-tjänsten.

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb
1. I [Azure-portalen](http://portal.azure.com), klickar du på plustecknet och skriver sedan **STREAM ANALYTICS** i textfönstret till höger. Välj sedan **Stream Analytics-jobbet** i resultatlistan.
   
    ![Skapa ett nytt Stream Analytics-jobb](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)
2. Ange ett unikt jobbnamn och kontrollera att prenumerationen är korrekt för jobbet. Skapa sedan en ny resursgrupp eller välj en befintlig på din prenumeration.
3. Markera sedan en plats för jobbet. Det rekommenderas att välja samma plats som resursgruppen och avsett lagringskonto vid dataöverföring för snabb bearbetning och minskade kostnader.
   
    ![Information om att skapa ett nytt Stream Analytics-jobb](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)
   
   > [!NOTE]
   > Du bör endast skapa det här lagringskontot en gång per region. Den här lagringen delas mellan alla Stream Analytics-jobb som skapas i en region.
   > 
   > 
4. Markera kryssrutan om du vill placera jobbet på instrumentpanelen och klicka sedan på **SKAPA**.
   
    ![jobbskapande pågår](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03a.png)
5. Du bör se ”Distribution startas...” längst upp till höger i webbläsarfönstret. Snart ändras det till ett slutfört fönster såsom visas nedan.
   
    ![jobbskapande pågår](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03b.png)

## <a name="create-an-azure-stream-analytics-query"></a>Skapa en Azure Stream Analytics-fråga
När jobbet har skapats är det dags att öppna det och skapa en fråga. Du kan enkelt komma åt ditt jobb genom att klicka på panelen för det.

![Jobbpanel](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

I fönstret **Jobbtopologi** klickar du i rutan **FRÅGA** om du vill gå till frågeredigeraren. Via redigeraren **FRÅGA** kan du ange en T-SQL-fråga som utför transformationen av inkommande data.

![Frågeruta](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

### <a name="query-archive-your-raw-data"></a>Fråga: Arkivera dina rådata
Den enklaste typen av fråga är en anslutningsfråga som arkiverar alla indata till dess angivna utdataenheter. Hämta exempeldatafilen från [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) till en plats på datorn. 

1. Klistra in frågan från filen PassThrough.txt. 
   
    ![Testa indataströmmen](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)
2. Klicka på de tre punkterna bredvid indata och välj rutan **Överför exempeldata från filen**.
   
    ![Testa indataströmmen](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06a.png)
3. Detta leder till att en ruta öppnas till höger, välj datafilen HelloWorldASA InputStream.json från din hämtade plats och klicka på **OK** längst ned i fönstret.
   
    ![Testa indataströmmen](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06b.png)
4. Klicka på kugghjulet **Testa** till vänster i den övre delen av fönstret och bearbeta testfrågan mot exempeldatauppsättningen. Ett resultatfönster öppnas under frågan när bearbetningen är klar.
   
    ![Testresultat](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Fråga: Filtrera dina data baserat på ett villkor
Nu ska vi prova att filtrera resultatet baserat på ett villkor. Vi vill bara visa resultat för de händelser som kommer från ”SensorA.” Frågan finns i filen Filtering.txt.

![Filtrera en dataström](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Observera att den skiftlägeskänsliga frågan jämför ett strängvärde. Klicka på kugghjulet **Testa** igen för att köra frågan. Frågan ska returnera 389 rader från 1860 händelser.

![Andra utdataresultat från frågetest](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Fråga: Varna för att utlösa ett företagsarbetsflöde
Nu gör vi vår fråga mer detaljerad. För varje sensortyp vill vi övervaka genomsnittstemperaturen per 30-sekundersfönster och visa resultat bara om genomsnittet överstiger 100 grader. Vi skriver följande fråga och klickar på **Testa** för att se resultaten. Frågan finns i filen ThresholdAlerting.txt.

![30 sekunders-filterfråga](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Du bör nu endast se resultat som innehåller 245 rader och namnen på de sensorer där genomsnittstemperaturen överstiger 100 grader. Den här frågan grupperar händelseströmmen efter **dspl**, vilket är sensornamnet, under ett **Rullande fönster** på 30 sekunder. Tidsfrågor ska ange hur vi vill att tid ska behandlas. Med hjälp av **TIMESTAMP BY**-satsen har vi angett att kolumnen **OUTPUTTIME** ska associera tider med alla temporala beräkningar. Mer detaljerad information finns i MSDN-artiklarna om [tidshantering](https://msdn.microsoft.com/library/azure/mt582045.aspx) och [fönsterfunktioner](https://msdn.microsoft.com/library/azure/dn835019.aspx).

### <a name="query-detect-absence-of-events"></a>Fråga: Identifiera avsaknad av händelser
Hur kan vi skriva en fråga för att hitta brist på inmatningshändelser? Vi kan till exempel ta reda på den senaste gången en sensor skickade data, men sedan inte skickade några händelser under följande fem sekunder. Frågan finns i filen AbsenseOfEvent.txt.

![Identifiera avsaknad av händelser](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Här använder vi en **VÄNSTER YTTRE** koppling för samma dataström (självkoppling). För en **INRE** koppling returneras resultatet bara om en matchning hittas.  Om en händelse från vänster sida i kopplingen är omatchad i en **VÄNSTER YTTRE** koppling returneras en rad med NULL för alla kolumner i den högra sidan. Den här tekniken är väldigt användbar för att hitta frånvaro av händelser. Mer information om [JOIN](https://msdn.microsoft.com/library/azure/dn835026.aspx) finns i MSDN-dokumentationen.

## <a name="conclusion"></a>Sammanfattning
Syftet med de här självstudierna är att demonstrera hur du skriver olika Stream Analytics Query Language-frågor och visar resultatet i webbläsaren. Men det här är bara början. Du kan göra så mycket mer med Stream Analytics. Stream Analytics stöder olika typer av indata och utdata och kan även använda funktioner i Azure Machine Learning, vilket gör det till ett stabilt verktyg för att analysera dataströmmar. Du kan lära dig mer om Stream Analytics med vår [utbildningskarta](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/). Mer information om hur du skriver frågor finns i artikeln om [vanliga frågemönster](stream-analytics-stream-analytics-query-patterns.md).

