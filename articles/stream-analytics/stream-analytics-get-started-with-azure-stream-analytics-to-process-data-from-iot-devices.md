---
title: Bearbeta IoT-dataströmmar i realtid med Azure Stream Analytics
description: IoT-sensortaggar och dataströmmar med dataströmsanalys och realtidsbearbetning av data
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 0755131f7d8071e37eadc1339ebc5e122725fa71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426247"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Bearbeta IoT-dataströmmar i realtid med Azure Stream Analytics

I den här artikeln får du lära dig hur du skapar logik för databearbetning för att samla in data från IoT-enheter (Internet of Things). Du använder en verklig Internet of Things (IoT) användningsfall för att visa hur du bygger din lösning snabbt och ekonomiskt.

## <a name="prerequisites"></a>Krav

* Skapa en kostnadsfri [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/).
* Hämta exempelfråga och datafiler från [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot).

## <a name="scenario"></a>Scenario

Contoso, ett företag inom industriell automation, har helt automatiserat sin produktionsprocess. Maskinerna på den här anläggningen har sensorer som sänder dataströmmar i realtid. I detta scenario vill en av företagets fabrikschefer få realtidsinsikter från dessa sensordata för att identifiera mönster och vidta lämpliga åtgärder utifrån resultatet. Du kan använda Stream Analytics Query Language (SAQL) över sensordata för att hitta intressanta mönster från den inkommande dataströmmen.

I det här exemplet genereras data från en Texas Instruments sensortaggenhet. Nyttolasten för dessa data är i JSON-format och ser ut ungefär så här:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

I ett verkligt scenario har du flera hundra av dessa sensorer som genererar händelser som en dataström. I en idealisk situation skulle det finnas en gateway-enhet som kör kod för att skicka dessa händelser till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) eller [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/). Ditt Stream Analytics-jobb skulle mata in de här händelserna från Event Hubs och köra analysfrågor i realtid mot dataströmmarna. Sedan kunde du skicka resultaten till någon av de [utdatatyper som stöds](stream-analytics-define-outputs.md).

För att förenkla användningen av den här komma igång-guiden ingår en exempeldatafil med data från verkliga SensorTag-enheter. Du kan köra frågor på dessa exempeldata och se resultaten. I efterföljande självstudiekurser lär du dig hur du kopplar jobbet till in- och utdataenheter och hur du distribuerar det till Azure-tjänsten.

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. Välj + Skapa **en resurs** på den vänstra navigeringsmenyn i [Azure-portalen.](https://portal.azure.com) Välj sedan **Stream Analytics-jobb** från **Analytics**.
   
    ![Skapa ett nytt Stream Analytics-jobb](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Ange ett unikt jobbnamn och kontrollera att prenumerationen är korrekt för jobbet. Skapa en ny resursgrupp eller välj en befintlig från din prenumeration.

1. Välj en plats för jobbet. Använd samma plats för resursgruppen och alla resurser för att öka bearbetningshastigheten och minska kostnaderna. När du har gjort konfigurationerna väljer du **Skapa**.
   
    ![Information om att skapa ett nytt Stream Analytics-jobb](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Skapa en Azure Stream Analytics-fråga
Nästa steg efter att jobbet har skapats är att skriva en fråga. Du kan testa frågor mot exempeldata utan att ansluta en indata eller utdata till jobbet.

Ladda ner [HelloWorldASA-InputStream.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) från GitHub. Navigera sedan till ditt Azure Stream Analytics-jobb i Azure-portalen.

Välj **Fråga** under **Jobbtopologi** på den vänstra menyn. Välj sedan **Ladda upp exempelinmatning**. Ladda `HelloWorldASA-InputStream.json` upp filen och välj **Ok**.

![Frågepanelen Stream Analytics-instrumentpanel](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Observera att en förhandsgranskning av data fylls i automatiskt i **förhandsgranskningstabellen För indata.**

![Förhandsgranska exempelindata](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Fråga: Arkivera dina rådata

Den enklaste typen av fråga är en anslutningsfråga som arkiverar alla indata till dess angivna utdataenheter. Den här frågan är standardfrågan som fylls i i ett nytt Azure Stream Analytics-jobb.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Välj **Testa fråga** och visa resultaten i tabellen **Testresultat.**

![Testresultat för Stream Analytics-fråga](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Fråga: Filtrera dina data baserat på ett villkor

Låt oss försöka filtrera resultaten baserat på ett villkor. Vi vill visa resultat för endast de händelser som kommer från "sensorA."Vi vill visa resultat för endast de händelser som kommer från "sensorA".

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

Klistra in frågan i redigeraren och välj **Testa fråga** för att granska resultaten.

![Filtrera en dataström](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Fråga: Varna för att utlösa ett företagsarbetsflöde

Nu gör vi vår fråga mer detaljerad. För varje sensortyp vill vi övervaka genomsnittstemperaturen per 30-sekundersfönster och visa resultat bara om genomsnittet överstiger 100 grader.

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![30 sekunders-filterfråga](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Du bör se resultat som innehåller endast 245 rader och namn på sensorer där den genomsnittliga tempererade är större än 100. Den här frågan grupperar händelseströmmen efter **dspl**, vilket är sensornamnet, under ett **Rullande fönster** på 30 sekunder. Temporala frågor måste ange hur du vill att tiden ska gå framåt. Genom att använda **TIMESTAMP** BY-satsen har du angett kolumnen **OUTPUTTIME** för att associera tider med alla tidsberäkningar. Detaljerad information finns i funktionerna [Tidshantering](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) och [Fönsterning](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics).

### <a name="query-detect-absence-of-events"></a>Fråga: Identifiera avsaknad av händelser

Hur kan vi skriva en fråga för att hitta brist på inmatningshändelser? Låt oss hitta sista gången som en sensor skickade data och sedan inte skicka händelser för de kommande 5 sekunderna.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Identifiera avsaknad av händelser](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Här använder vi en **VÄNSTER YTTRE** koppling för samma dataström (självkoppling). För en **INRE** koppling returneras resultatet bara om en matchning hittas.  Om en händelse från vänster sida i kopplingen är omatchad i en **VÄNSTER YTTRE** koppling returneras en rad med NULL för alla kolumner i den högra sidan. Den här tekniken är väldigt användbar för att hitta frånvaro av händelser. Mer information finns i [JOIN](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics).

## <a name="conclusion"></a>Slutsats

Syftet med den här artikeln är att visa hur du skriver olika Frågor om Data analytics-frågor om frågespråk och se resultat i webbläsaren. Men detta är bara för att komma igång. Stream Analytics stöder olika typer av indata och utdata och kan även använda funktioner i Azure Machine Learning, vilket gör det till ett stabilt verktyg för att analysera dataströmmar. Mer information om hur du skriver frågor finns i artikeln om [vanliga frågemönster](stream-analytics-stream-analytics-query-patterns.md).

