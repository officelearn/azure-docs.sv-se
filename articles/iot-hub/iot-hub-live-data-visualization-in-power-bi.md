---
title: Data visualisering i real tid av data frm Azure IoT Hub – Power BI
description: Använd Power BI för att visualisera temperatur-och fuktighets data som samlas in från sensorn och skickas till Azure IoT Hub.
author: robinsh
keywords: Real tids data visualisering, data visualisering i real tid, visualisering av sensor data
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: f0b909d10790511408e090546fd3359889ea5aca
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954633"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualisera real tids sensor data från Azure IoT Hub med Power BI

![Diagram från slut punkt till slut punkt](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

Du får lära dig att visualisera real tids sensor data som Azure IoT hub tar emot med hjälp av Power BI. Om du vill försöka visualisera data i din IoT-hubb med en webbapp läser du [Använd en webbapp för att visualisera real tids sensor data från Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Vad du gör

* Få din IoT Hub redo för data åtkomst genom att lägga till en konsument grupp.

* Skapa, konfigurera och kör ett Stream Analytics jobb för data överföring från din IoT-hubb till ditt Power BI-konto.

* Skapa och publicera en Power BI rapport för att visualisera data.

## <a name="what-you-need"></a>Vad du behöver

* Slutför själv studie kursen om [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) eller någon av enhets självstudierna. till exempel [Raspberry Pi med Node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Dessa artiklar behandlar följande krav:
  
  * En aktiv Azure-prenumeration.
  * En Azure IoT-hubb under din prenumeration.
  * Ett klient program som skickar meddelanden till Azure IoT Hub.

* Ett Power BI-konto. ([Prova Power BI kostnads fritt](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Skapa, konfigurera och köra ett Stream Analytics jobb

Vi börjar med att skapa ett Stream Analytics jobb. När du har skapat jobbet definierar du indata, utdata och den fråga som används för att hämta data.

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs** > **Sakernas Internet** > **Stream Analytics jobb**.

2. Ange följande information för jobbet.

   **Jobbnamn**: Jobbets namn. Namnet måste vara globalt unikt.

   **Resurs grupp**: Använd samma resurs grupp som din IoT Hub använder.

   **Plats**: Använd samma plats som din resurs grupp.

   ![Skapa ett Stream Analytics jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Välj **Skapa**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

1. Öppna Stream Analyticss jobbet.

2. Under **jobb sto pol Ogin**väljer du **indata**.

3. I rutan **indata** väljer du **Lägg till Stream-indata**och väljer sedan **IoT Hub** i list rutan. Ange följande information i fönstret nytt indata:

   **Indataområde**: Ange ett unikt alias för indatamängden.

   **Ange IoT Hub från din prenumeration**: Välj den här alternativ knappen.

   **Prenumeration**: Välj den Azure-prenumeration som du använder för den här självstudien.

   **IoT Hub**: Välj IoT Hub som du använder för den här kursen.

   **Slutpunkt**: Välj **Meddelanden**.

   **Namn på princip för delad åtkomst**: Välj namnet på den princip för delad åtkomst som du vill att Stream Analytics jobbet ska använda för din IoT-hubb. I den här självstudien kan du välja *tjänst*. *Tjänst* principen skapas som standard på nya IoT-hubbar och ger behörighet att skicka och ta emot på molnbaserade slut punkter som exponeras av IoT Hub. Läs mer i [åtkomst kontroll och behörigheter](iot-hub-devguide-security.md#access-control-and-permissions).

   **Princip nyckel för delad åtkomst**: det här fältet fylls i automatiskt baserat på ditt val för namnet på den delade åtkomst principen.

   **Konsument grupp**: Välj den konsument grupp som du skapade tidigare.

   Lämna standardvärdena för alla andra fält.

   ![Lägga till ininformation till ett Stream Analytics jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Välj **Spara**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Under **jobb sto pol Ogin**väljer du **utdata**.

2. I fönstret **utdata** väljer du **lägg till** och **Power BI**.

3. I fönstret **Power BI-nya utdata** väljer du **auktorisera** och följer anvisningarna för att logga in på ditt Power BI-konto.

4. När du har loggat in på Power BI anger du följande information:

   **Alias för utdata**: ett unikt alias för utdata.

   **Grupp arbets yta**: Välj mål grupps arbets yta.

   **Data uppsättnings namn**: Ange ett namn på data mängden.

   **Tabell namn**: Ange ett tabell namn.

   ![Lägga till utdata till ett Stream Analytics jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Välj **Spara**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurera frågan för Stream Analytics-jobbet

1. Välj **Fråga** under **Jobbtopologi**.

2. Ersätt `[YourInputAlias]` med inmatat alias för jobbet.

3. Ersätt `[YourOutputAlias]` med utdataalias för jobbet.

   ![Lägga till en fråga till ett Stream Analytics jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Välj **Spara**.

### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

I Stream Analytics jobb väljer du **Översikt**och väljer sedan **starta** > **nu** > **Starta**. När jobbet startar ändras jobbstatusen från **Stoppad** till **Körs**.

![Köra ett Stream Analytics jobb i Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Skapa och publicera en Power BI rapport för att visualisera data

1. Se till att exempel programmet körs på enheten. Om inte, kan du läsa självstudierna under [Konfigurera enheten](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Logga in på ditt [Power BI](https://powerbi.microsoft.com/en-us/)-konto.

3. Välj den arbets yta du använde, **min arbets yta**.

4. Välj **data uppsättningar**.

   Du bör se den data uppsättning som du angav när du skapade utdata för Stream Analytics jobbet.

5. För den data uppsättning som du har skapat väljer du **Lägg till rapport** (den första ikonen till höger om data uppsättningens namn).

   ![Skapa en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Skapa ett linjediagram för att visa realtidstemperatur över tid.

   1. I fönstret **visualiseringar** på sidan Skapa rapport väljer du linje diagram ikonen för att lägga till ett linje diagram.

   2. I rutan **Fält** expanderar du tabellen du angav när du skapade utdata för Stream Analytics-jobbet.

   3. Dra **EventEnqueuedUtcTime** till **Axel** i fönstret **Visualiseringar**.

   4. Dra **temperatur** till **Värden**.

      Ett linjediagram skapas. X-axeln visar datum och tid i UTC-tidszonen. Y-axeln visar temperatur från sensorn.

      ![Lägga till ett linje diagram för temperatur i en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Skapa ett annat linjediagram om du vill visa realtidsfuktighet över tid. Det gör du genom att följa samma steg ovan och placera **EventEnqueuedUtcTime** på x-axeln och **fuktighet** på y-axeln.

   ![Lägga till ett linje diagram för fuktighet i en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Spara rapporten genom att välja **Spara** .

9. Välj **rapporter** i den vänstra rutan och välj sedan den rapport som du nyss skapade.

10. Välj **fil** > **Publicera på webben**.

11. Välj **skapa inbäddnings kod**och välj sedan **publicera**.

Du har angett en rapport länk som du kan dela med alla för rapport åtkomst och ett kodfragment som du kan använda för att integrera rapporten i din blogg eller webbplats.

![Publicera en Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Microsoft erbjuder även [Power BI mobila appar](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) för att visa och interagera med dina Power BI instrument paneler och rapporter på din mobila enhet.

## <a name="next-steps"></a>Nästa steg

Du har använt Power BI för att visualisera sensor data i real tid från din Azure IoT Hub.

Ett annat sätt att visualisera data från Azure IoT Hub finns i [använda en webbapp för att visualisera sensor data i real tid från Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
