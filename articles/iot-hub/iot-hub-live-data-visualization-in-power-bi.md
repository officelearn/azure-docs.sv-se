---
title: Data i realtid visualisering av sensordata från Azure IoT Hub – Power BI | Microsoft Docs
description: Använd Power BI för att visualisera data för temperaturen och fuktigheten som samlats in från sensorn och skickas till din Azure IoT hub.
author: rangv
manager: ''
keywords: realtid datavisualisering, live datavisualisering sensor datavisualisering
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: a3c54fe635fe0f8988c321684a815e9896922587
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38235513"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualisera sensordata i realtid från Azure IoT Hub med Power BI

![Slutpunkt till slutpunkt-diagram](media/iot-hub-get-started-e2e-diagram/4.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig att visualisera sensordata i realtid som din Azure-IoT-hubb tar emot av Power BI. Om du vill prova visualisera data i IoT hub med Web Apps kan du se [Använd Azure Web Apps att visualisera sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Vad du gör

- Förbereda din IoT-hubb för åtkomst till data genom att lägga till en konsumentgrupp.
- Skapa, konfigurera och köra ett Stream Analytics-jobb för att överföra data från IoT hub till Power BI-kontot.
- Skapa och publicera en Power BI-rapport för att visualisera data.

## <a name="what-you-need"></a>Vad du behöver

- Självstudien [konfigurera enheten](iot-hub-raspberry-pi-kit-node-get-started.md) slutförts som omfattar följande krav:
  - En aktiv Azure-prenumeration.
  - Azure IoT hub i din prenumeration.
  - Ett klientprogram som skickar meddelanden till din Azure IoT hub.
- En Power BI-konto. ([Prova Powerbi kostnadsfritt](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Skapa, konfigurera och köra ett Stream Analytics-jobb

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I [Azure Portal](https://portal.azure.com) klickar du på **Skapa en resurs** > **Sakernas Internet** > **Stream Analytics-jobb**.
1. Ange följande information för jobbet.

   **Jobbnamn**: Jobbets namn. Namnet måste vara globalt unikt.

   **Resursgrupp**: Använd samma resursgrupp som din IoT-hubb använder.

   **Plats**: Använd samma plats som resursgruppen.

   **Fäst på instrumentpanelen**: Välj det här alternativet för enkel åtkomst till IoT-hubben från instrumentpanelen.

   ![Skapa ett Stream Analytics-jobb i Azure](media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

1. Öppna Stream Analytics-jobb.
1. Under **Jobbtopologi** klickar du på **Indata**.
1. I den **indata** fönstret klickar du på **Lägg till**, och ange sedan följande information:

   **Inmatat alias**: det unika aliaset för indata.

   **Källan**: Välj **IoT-hubb**.

   **Konsumentgrupp**: Välj konsumentgrupp som du nyss skapade.
1. Klicka på **Skapa**.

   ![Lägg till indata för ett Stream Analytics-jobb i Azure](media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Under **Jobbtopologi** klickar du på **Utdata**.
1. I den **utdata** fönstret klickar du på **Lägg till**, och ange sedan följande information:

   **Utdataalias**: Utdatas unika alias.

   **Mottagare**: Välj **Power BI**.
1. Klicka på **auktorisera**, och sedan logga in på ditt Power BI-konto.
1. När det har auktoriserats, anger du följande information:

   **Gruppen arbetsyta**: Välj din mål-grupparbetsyta.

   **Namn på datauppsättning**: Ange ett namn på datauppsättning.

   **Tabellnamn**: Ange ett tabellnamn.
1. Klicka på **Skapa**.

   ![Lägg till utdata till ett Stream Analytics-jobb i Azure](media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurera frågan för Stream Analytics-jobbet

1. Under **Jobbtopologi** klickar du på **Fråga**.
1. Ersätt `[YourInputAlias]` med inmatat alias för jobbet.
1. Ersätt `[YourOutputAlias]` med utdataalias för jobbet.
1. Klicka på **Spara**.

   ![Lägg till en fråga i ett Stream Analytics-jobb i Azure](media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

I Stream Analytics-jobbet klickar du på **Starta** > **Nu** > **Starta**. När jobbet startar ändras jobbstatusen från **Stoppad** till **Körs**.

![Köra ett Stream Analytics-jobb i Azure](media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Skapa och publicera en Power BI-rapport för att visualisera data

1. Kontrollera exempelprogrammet som körs på din enhet. Om inte, du kan referera till självstudier under [konfigurera enheten](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).
1. Logga in på ditt [Power BI](https://powerbi.microsoft.com/en-us/)-konto.
1. Gå till arbetsytan grupp som du angav när du skapade utdata för Stream Analytics-jobbet.
1. Klicka på **strömmande datauppsättningar**.

   Du bör se den listade datauppsättningen som du angav när du skapade utdata för Stream Analytics-jobbet.
1. Under **ÅTGÄRDER** klickar du på den första ikonen för att skapa en rapport.

   ![Skapa en Microsoft Power BI-rapport](media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

1. Skapa ett linjediagram för att visa realtidstemperatur över tid.
   1. På sidan Skapa, lägger du till ett linjediagram.
   1. I rutan **Fält** expanderar du tabellen du angav när du skapade utdata för Stream Analytics-jobbet.
   1. Dra **EventEnqueuedUtcTime** till **Axel** i fönstret **Visualiseringar**.
   1. Dra **temperatur** till **Värden**.

      Nu skapas ett linjediagram. X-axeln visar datum och tid i UTC-tidszonen. Y-axeln visar temperatur från sensorn.

      ![Lägga till ett linjediagram för temperatur i en Microsoft Power BI-rapport](media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

1. Skapa ett annat linjediagram om du vill visa realtidsfuktighet över tid. Gör detta genom att följa samma steg som ovan och placera **EventEnqueuedUtcTime** på x-axeln och **fuktighet** på y-axeln.

   ![Lägga till ett linjediagram för fuktighet i en Microsoft Power BI-rapport](media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

1. Klicka på **Spara** för att spara rapporten.
1. Klicka på **filen** > **publicera på webben**.
1. Klicka på **skapa inbäddningskod**, och klicka sedan på **publicera**.

Du får se rapportlänken att du kan dela med vem som helst för Rapportåtkomst och ett kodfragment för att integrera rapporten på din blogg eller webbplats.

![Publicera en Microsoft Power BI-rapport](media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

Microsoft erbjuder även den [Power BI-mobilapparna](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) för att visa och interagera med dina Power BI-instrumentpaneler och rapporter på din mobila enhet.

## <a name="next-steps"></a>Nästa steg

Du har använt Power BI för att visualisera sensordata i realtid från Azure IoT hub.
Det finns ett annat sätt att visualisera data från Azure IoT Hub. Se [Använd Azure Web Apps att visualisera sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
