---
title: Data i realtid visualisering av sensordata från Azure IoT Hub – Power BI | Microsoft Docs
description: Använd Power BI för att visualisera data för temperaturen och fuktigheten som samlats in från sensorn och skickas till din Azure IoT hub.
author: robinsh
keywords: realtid datavisualisering, live datavisualisering sensor datavisualisering
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: 7deb1b501d30c8af0cb190f4722d46435afa9b8e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065833"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualisera sensordata i realtid från Azure IoT Hub med Power BI

![Slutpunkt till slutpunkt-diagram](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig att visualisera sensordata i realtid som din Azure-IoT-hubb tar emot via Power BI. Om du vill prova att visualisera data i IoT hub med en webbapp, se [använda en webbapp för att visualisera sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Vad du gör

* Förbereda din IoT-hubb för åtkomst till data genom att lägga till en konsumentgrupp.

* Skapa, konfigurera och köra ett Stream Analytics-jobb för att överföra data från IoT hub till Power BI-kontot.

* Skapa och publicera en Power BI-rapport för att visualisera data.

## <a name="what-you-need"></a>Vad du behöver

* Slutför den [Raspberry Pi onlinesimulator](iot-hub-raspberry-pi-web-simulator-get-started.md) självstudien eller någon av enheten självstudiekurser; exempelvis [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md). De här artiklarna beskriver följande krav:
  
  * En aktiv Azure-prenumeration.
  * Azure IoT hub i din prenumeration.
  * Ett klientprogram som skickar meddelanden till din Azure IoT hub.

* Ett Power BI-konto. ([Prova Powerbi kostnadsfritt](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Skapa, konfigurera och köra ett Stream Analytics-jobb

Låt oss börja med att skapa ett Stream Analytics-jobb. När du skapar jobbet kan definiera du indata, utdata och den fråga som används för att hämta data.

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I den [Azure-portalen](https://portal.azure.com)väljer **skapa en resurs** > **Internet of Things** > **Stream Analytics-jobbet**.

2. Ange följande information för jobbet.

   **Jobbnamn**: Namnet på jobbet. Namnet måste vara globalt unikt.

   **Resursgrupp**: Använd samma resursgrupp som din IoT-hubb använder.

   **Plats**: Använd samma plats som resursgruppen.

   ![Skapa ett Stream Analytics-jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Välj **Skapa**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

1. Öppna Stream Analytics-jobb.

2. Under **Jobbtopologi**väljer **indata**.

3. I den **indata** väljer **Lägg till strömindata**och välj sedan **IoT Hub** från den nedrullningsbara listan. På den nya inkommande rutan anger du följande information:

   **Indataalias**: Ange ett unikt alias för indata.

   **Ange IoT Hub från prenumerationen**: Välj den här knappen.

   **Prenumeration**: Ange den prenumeration du använder den här självstudien.

   **IoT Hub**: Välj IoT-hubben som du använder för den här självstudien.

   **Slutpunkt**: Välj **Meddelanden**.

   **Namn på princip för delad åtkomst**: Välj namnet på den princip för delad åtkomst som du vill att Stream Analytics-jobbet ska användas för din IoT-hubb. Den här självstudien kan du välja *service*. Den *service* principen skapas som standard på nya IoT-hubbar och ger behörighet att skicka och ta emot på molnsidan som exponeras av IoT hub. Mer information finns i [åtkomstkontroll och behörigheter](iot-hub-devguide-security.md#access-control-and-permissions).

   **Principnyckel för delad åtkomst**: Det här fältet är fylls i automatiskt baserat på ditt val för delad åtkomst principens namn.

   **Konsumentgrupp**: Välja konsumentgrupp som du skapade tidigare.

   Lämna andra fält på standardvärden.

   ![Lägg till indata för ett Stream Analytics-jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Välj **Spara**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Under **Jobbtopologi**väljer **utdata**.

2. I den **utdata** väljer **Lägg till** och **Power BI**.

3. På den **Power BI – nya utdata** väljer **auktorisera** och följ anvisningarna för att logga in på ditt Power BI-konto.

4. När du har loggat in till Power BI, anger du följande information:

   **Utdataalias**: Ett unikt alias för utdata.

   **Gruppen arbetsyta**: Välj din mål-grupparbetsyta.

   **Namn på datauppsättning**: Ange ett namn på datauppsättning.

   **Tabellnamn**: Ange ett tabellnamn.

   ![Lägg till utdata till ett Stream Analytics-jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Välj **Spara**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurera frågan för Stream Analytics-jobbet

1. Välj **Fråga** under **Jobbtopologi**.

2. Ersätt `[YourInputAlias]` med inmatat alias för jobbet.

3. Ersätt `[YourOutputAlias]` med utdataalias för jobbet.

   ![Lägg till en fråga i ett Stream Analytics-jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Välj **Spara**.

### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

I Stream Analytics-jobb väljer **översikt**och välj sedan **starta** > **nu** > **starta**. När jobbet startar ändras jobbstatusen från **Stoppad** till **Körs**.

![Köra ett Stream Analytics-jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Skapa och publicera en Power BI-rapport för att visualisera data

1. Kontrollera exempelprogrammet som körs på din enhet. Om inte, du kan referera till självstudier under [konfigurera enheten](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Logga in på ditt [Power BI](https://powerbi.microsoft.com/en-us/)-konto.

3. Välj den arbetsyta du använde **Min arbetsyta**.

4. Välj **datauppsättningar**.

   Du bör se den datauppsättning som du angav när du skapade utdata för Stream Analytics-jobbet.

5. Den datauppsättning som du har skapat, Välj **Lägg till rapport** (den första ikonen till höger om datauppsättningsnamnet).

   ![Skapa en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Skapa ett linjediagram för att visa realtidstemperatur över tid.

   1. På den **visualiseringar** rutan på sidan Skapa, Välj raddiagramikonen att lägga till ett linjediagram.

   2. I rutan **Fält** expanderar du tabellen du angav när du skapade utdata för Stream Analytics-jobbet.

   3. Dra **EventEnqueuedUtcTime** till **Axel** i fönstret **Visualiseringar**.

   4. Dra **temperatur** till **Värden**.

      Ett linjediagram skapas. X-axeln visar datum och tid i UTC-tidszonen. Y-axeln visar temperatur från sensorn.

      ![Lägga till ett linjediagram för temperatur i en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Skapa ett annat linjediagram om du vill visa realtidsfuktighet över tid. Gör detta genom att följa samma steg som ovan och placera **EventEnqueuedUtcTime** på x-axeln och **fuktighet** på y-axeln.

   ![Lägga till ett linjediagram för fuktighet i en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Välj **spara** att spara rapporten.

9. Välj **rapporter** på den vänstra rutan och välj sedan den rapport som du just skapade.

10. Välj **filen** > **publicera på webben**.

11. Välj **skapa inbäddningskod**, och välj sedan **publicera**.

Du får se rapportlänken som du kan dela med vem som helst för Rapportåtkomst och ett kodfragment som du kan använda för att integrera rapporten på din blogg eller webbplats.

![Publicera en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Microsoft erbjuder även den [Power BI-mobilapparna](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) för att visa och interagera med dina Power BI-instrumentpaneler och rapporter på din mobila enhet.

## <a name="next-steps"></a>Nästa steg

Du har använt Power BI för att visualisera sensordata i realtid från Azure IoT hub.

Ett annat sätt att visualisera data från Azure IoT Hub finns [använda en webbapp för att visualisera sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
