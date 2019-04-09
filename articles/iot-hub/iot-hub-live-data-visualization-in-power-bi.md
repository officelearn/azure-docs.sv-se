---
title: Data i realtid visualisering av sensordata från Azure IoT Hub – Power BI | Microsoft Docs
description: Använd Power BI för att visualisera data för temperaturen och fuktigheten som samlats in från sensorn och skickas till din Azure IoT hub.
author: robinsh
keywords: realtid datavisualisering, live datavisualisering sensor datavisualisering
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 4/11/2018
ms.author: robinsh
ms.openlocfilehash: 5349a8a81c1d1361637b4fc9cf83e1ee83f5276a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265516"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualisera sensordata i realtid från Azure IoT Hub med Power BI

![Slutpunkt till slutpunkt-diagram](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig att visualisera sensordata i realtid som din Azure-IoT-hubb tar emot via Power BI. Om du vill prova toe visualisera data i IoT hub med Web Apps, se [Använd Azure Web Apps att visualisera sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Vad du gör

* Förbereda din IoT-hubb för åtkomst till data genom att lägga till en konsumentgrupp.

* Skapa, konfigurera och köra ett Stream Analytics-jobb för att överföra data från IoT hub till Power BI-kontot.

* Skapa och publicera en Power BI-rapport för att visualisera data.

## <a name="what-you-need"></a>Vad du behöver

* Självstudien [konfigurera enheten](iot-hub-raspberry-pi-kit-node-get-started.md) slutförts som omfattar följande krav:
  
  * En aktiv Azure-prenumeration.
  * Azure IoT hub i din prenumeration.
  * Ett klientprogram som skickar meddelanden till din Azure IoT hub.

* Ett Power BI-konto. ([Prova Powerbi kostnadsfritt](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Skapa, konfigurera och köra ett Stream Analytics-jobb

Låt oss börja med att skapa ett Stream Analytics-jobb. När du skapar jobbet kan definiera du indata, utdata och den fråga som används för att hämta data.

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I [Azure Portal](https://portal.azure.com) klickar du på **Skapa en resurs** > **Sakernas Internet** > **Stream Analytics-jobb**.

2. Ange följande information för jobbet.

   **Jobbnamn**: Namnet på jobbet. Namnet måste vara globalt unikt.

   **Resursgrupp**: Använd samma resursgrupp som din IoT-hubb använder.

   **Plats**: Använd samma plats som resursgruppen.

   **Fäst på instrumentpanelen**: Markera det här alternativet för enkel åtkomst till IoT-hubben från instrumentpanelen.

   ![Skapa ett Stream Analytics-jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

3. Klicka på **Skapa**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

1. Öppna Stream Analytics-jobb.

2. Klicka på **Indata** under **Jobbtopologi**.

3. I den **indata** fönstret klickar du på **Lägg till strömindata**, och ange sedan följande information:

   **Indataalias**: Det unika aliaset för indata och välj **ger IoT Hub-inställningar manuellt** nedan.

   **Källa**: Välj **IoT-hubb**.
   
   **Slutpunkt**: Klicka på **meddelanden**.

   **Konsumentgrupp**: Välja konsumentgrupp som du nyss skapade.

4. Klicka på **Skapa**.

   ![Lägg till indata för ett Stream Analytics-jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Under **Jobbtopologi** klickar du på **Utdata**.

2. I den **utdata** fönstret klickar du på **Lägg till** och **Power BI**, och ange sedan följande information:

   **Utdataalias**: Utdatas unika alias.

   **Gruppen arbetsyta**: Välj din mål-grupparbetsyta.

   **Namn på datauppsättning**: Ange ett namn på datauppsättning.

   **Tabellnamn**: Ange ett tabellnamn.

3. Klicka på **auktorisera**, och sedan logga in på ditt Power BI-konto.

4. Klicka på **Skapa**.

   ![Lägg till utdata till ett Stream Analytics-jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurera frågan för Stream Analytics-jobbet

1. Under **Jobbtopologi** klickar du på **Fråga**.

2. Ersätt `[YourInputAlias]` med inmatat alias för jobbet.

3. Ersätt `[YourOutputAlias]` med utdataalias för jobbet.

4. Klicka på **Spara**.

   ![Lägg till en fråga i ett Stream Analytics-jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

I Stream Analytics-jobbet klickar du på **Starta** > **Nu** > **Starta**. När jobbet startar ändras jobbstatusen från **Stoppad** till **Körs**.

![Köra ett Stream Analytics-jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Skapa och publicera en Power BI-rapport för att visualisera data

1. Kontrollera exempelprogrammet som körs på din enhet. Om inte, du kan referera till självstudier under [konfigurera enheten](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Logga in på ditt [Power BI](https://powerbi.microsoft.com/en-us/)-konto.

3. Klicka på den arbetsyta du använde **Min arbetsyta**.

4. Klicka på **Datauppsättningar**.

   Du bör se den datauppsättning som du angav när du skapade utdata för Stream Analytics-jobbet.

5. För den datauppsättning som du har skapat, klickar du på **Lägg till rapport** (den första ikonen till höger om datauppsättningsnamnet).

   ![Skapa en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

6. Skapa ett linjediagram för att visa realtidstemperatur över tid.

   1. På sidan Skapa, lägger du till ett linjediagram.

   2. I rutan **Fält** expanderar du tabellen du angav när du skapade utdata för Stream Analytics-jobbet.
   
   3. Dra **EventEnqueuedUtcTime** till **Axel** i fönstret **Visualiseringar**.
   
   4. Dra **temperatur** till **Värden**.

      Ett linjediagram skapas. X-axeln visar datum och tid i UTC-tidszonen. Y-axeln visar temperatur från sensorn.

      ![Lägga till ett linjediagram för temperatur i en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

7. Skapa ett annat linjediagram om du vill visa realtidsfuktighet över tid. Gör detta genom att följa samma steg som ovan och placera **EventEnqueuedUtcTime** på x-axeln och **fuktighet** på y-axeln.

   ![Lägga till ett linjediagram för fuktighet i en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

8. Klicka på **Spara** för att spara rapporten.

9. Klicka på **rapporter** på den vänstra rutan och klicka sedan på rapporten som du just skapade.

10. Klicka på **filen** > **publicera på webben**.

11. Klicka på **skapa inbäddningskod**, och klicka sedan på **publicera**.

Du får se rapportlänken att du kan dela med vem som helst för Rapportåtkomst och ett kodfragment för att integrera rapporten på din blogg eller webbplats.

![Publicera en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

Microsoft erbjuder även den [Power BI-mobilapparna](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) för att visa och interagera med dina Power BI-instrumentpaneler och rapporter på din mobila enhet.

## <a name="next-steps"></a>Nästa steg

Du har använt Power BI för att visualisera sensordata i realtid från Azure IoT hub.

Det finns ett annat sätt att visualisera data från Azure IoT Hub. Se [Använd Azure Web Apps att visualisera sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
