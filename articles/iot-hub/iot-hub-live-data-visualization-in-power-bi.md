---
title: Datavisualisering i realtid av data frm Azure IoT Hub – Power BI
description: Använd Power BI för att visualisera temperatur- och fuktighetsdata som samlas in från sensorn och skickas till din Azure IoT-hubb.
author: robinsh
keywords: visualisering av data i realtid, livedatavisualisering, visualisering av sensordata
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: f0b909d10790511408e090546fd3359889ea5aca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954633"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualisera sensordata i realtid från Azure IoT Hub med Power BI

![Diagram från på sluten tid](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig hur du visualiserar sensordata i realtid som din Azure IoT-hubb tar emot med Power BI. Om du vill försöka visualisera data i din IoT-hubb med en webbapp läser [du Använda en webbapp för att visualisera sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Vad du gör

* Gör din IoT-hubb redo för dataåtkomst genom att lägga till en konsumentgrupp.

* Skapa, konfigurera och kör ett Stream Analytics-jobb för dataöverföring från din IoT-hubb till ditt Power BI-konto.

* Skapa och publicera en Power BI-rapport för att visualisera data.

## <a name="what-you-need"></a>Vad du behöver

* Slutför [Raspberry Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) handledning eller en av enheten tutorials; till exempel [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Dessa artiklar omfattar följande krav:
  
  * En aktiv Azure-prenumeration.
  * En Azure IoT-hubb under din prenumeration.
  * Ett klientprogram som skickar meddelanden till din Azure IoT-hubb.

* Ett Power BI-konto. (Prova[Power BI gratis)](https://powerbi.microsoft.com/)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Skapa, konfigurera och köra ett Stream Analytics-jobb

Låt oss börja med att skapa ett Stream Analytics-jobb. När du har skapat jobbet definierar du indata, utdata och den fråga som används för att hämta data.

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I [Azure-portalen](https://portal.azure.com)väljer du Skapa ett > **resurs-Internet of Things** > **Stream Analytics-jobb**. **Create a resource**

2. Ange följande information för jobbet.

   **Jobbnamn**: Jobbets namn. Namnet måste vara globalt unikt.

   **Resursgrupp**: Använd samma resursgrupp som IoT-hubben använder.

   **Plats**: Använd samma plats som resursgruppen.

   ![Skapa ett Stream Analytics-jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Välj **Skapa**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

1. Öppna stream analytics-jobbet.

2. Under **Jobbtopologi**väljer du **Indata**.

3. I fönstret **Ingångar** väljer du **Lägg till indata**för ström och väljer sedan **IoT Hub** i listrutan. Ange följande information i det nya indatafönstret:

   **Indataalias:** Ange ett unikt alias för indata.

   **Ange IoT Hub från din prenumeration:** Välj den här alternativknappen.

   **Prenumeration:** Välj den Azure-prenumeration som du använder för den här självstudien.

   **IoT Hub:** Välj den IoT Hub du använder för den här självstudien.

   **Slutpunkt**: Välj **Meddelanden**.

   **Principnamn för delad åtkomst**: Välj namnet på den princip för delad åtkomst som du vill att Stream Analytics-jobbet ska användas för din IoT-hubb. För den här självstudien kan du välja *tjänst*. *Tjänstprincipen* skapas som standard på nya IoT-hubbar och ger behörighet att skicka och ta emot på slutpunkter på molnsidan som exponeras av IoT-hubben. Mer information finns i [Åtkomstkontroll och behörigheter](iot-hub-devguide-security.md#access-control-and-permissions).

   **Principnyckel för delad åtkomst**: Det här fältet fylls i automatiskt baserat på ditt val för principnamnet för delad åtkomst.

   **Konsumentgrupp**: Välj den konsumentgrupp som du skapade tidigare.

   Lämna alla andra fält som standard.

   ![Lägga till en indata till ett Stream Analytics-jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Välj **Spara**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Under **Jobbtopologi**väljer du **Utdata**.

2. Välj **Lägg till** och **Power BI**i fönstret **Utdata** .

3. I **fönstret Power BI - Ny utdata** väljer du **Auktorisera** och följer anvisningarna för att logga in på ditt Power BI-konto.

4. När du har loggat in på Power BI anger du följande information:

   **Utdataalias:** Ett unikt alias för utdata.

   **Grupparbetsyta:** Välj målgruppsarbetsyta.

   **Datauppsättningsnamn**: Ange ett datauppsättningsnamn.

   **Tabellnamn:** Ange ett tabellnamn.

   ![Lägga till en utdata till ett Stream Analytics-jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Välj **Spara**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurera frågan för Stream Analytics-jobbet

1. Välj **Fråga** under **Jobbtopologi**.

2. Ersätt `[YourInputAlias]` med inmatat alias för jobbet.

3. Ersätt `[YourOutputAlias]` med utdataalias för jobbet.

   ![Lägga till en fråga i ett Stream Analytics-jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Välj **Spara**.

### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

I stream analytics-jobbet väljer du **Översikt**och väljer sedan **Starta** > **nu** > **Start**. När jobbet startar ändras jobbstatusen från **Stoppad** till **Körs**.

![Köra ett Stream Analytics-jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Skapa och publicera en Power BI-rapport för att visualisera data

1. Kontrollera att exempelprogrammet körs på enheten. Om inte, kan du läsa självstudierna under [Installationsprogrammet för enheten](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Logga in på ditt [Power BI](https://powerbi.microsoft.com/en-us/)-konto.

3. Välj arbetsytan du använde, **Min arbetsyta**.

4. Välj **Datauppsättningar**.

   Du bör se den datauppsättning som du angav när du skapade utdata för Stream Analytics-jobbet.

5. För den datauppsättning som du skapade väljer du **Lägg till rapport** (den första ikonen till höger om datauppsättningens namn).

   ![Skapa en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Skapa ett linjediagram för att visa realtidstemperatur över tid.

   1. I fönstret **Visualiseringar** på sidan för att skapa rapporter markerar du linjediagramikonen för att lägga till ett linjediagram.

   2. I rutan **Fält** expanderar du tabellen du angav när du skapade utdata för Stream Analytics-jobbet.

   3. Dra **EventEnqueuedUtcTime** till **Axel** i fönstret **Visualiseringar**.

   4. Dra **temperatur** till **Värden**.

      Ett linjediagram skapas. X-axeln visar datum och tid i UTC-tidszonen. Y-axeln visar temperatur från sensorn.

      ![Lägga till ett linjediagram för temperatur i en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Skapa ett annat linjediagram om du vill visa realtidsfuktighet över tid. Det gör du genom att följa samma steg ovan och placera **EventEnqueuedUtcTime** på x-axeln och **luftfuktigheten** på y-axeln.

   ![Lägga till ett linjediagram för fuktighet i en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Välj **Spara** om du vill spara rapporten.

9. Välj **Rapporter** i den vänstra rutan och välj sedan den rapport som du just skapade.

10. Välj **Filpublicera** > **på webben**.

11. Välj **Skapa inbäddningskod**och välj sedan **Publicera**.

Du får den rapportlänk som du kan dela med vem som helst för rapportåtkomst och ett kodavsnitt som du kan använda för att integrera rapporten i din blogg eller webbplats.

![Publicera en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Microsoft erbjuder även [Power BI-mobilapparna](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) för visning och interaktion med dina Power BI-instrumentpaneler och rapporter på din mobila enhet.

## <a name="next-steps"></a>Nästa steg

Du har använt Power BI för att visualisera sensordata i realtid från azure IoT-hubben.

Ett annat sätt att visualisera data från Azure IoT Hub finns i [Använda en webbapp för att visualisera sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
