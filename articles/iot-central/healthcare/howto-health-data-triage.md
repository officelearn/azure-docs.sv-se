---
title: Skapa en hälso data prioritering-instrumentpanel med Azure IoT Central | Microsoft Docs
description: Lär dig att bygga en hälso data prioritering-instrumentpanel med Azure IoT Central programmallar.
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 99b27ec53d955079b5f73986408e698955c0969b
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77021652"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Självstudie: Bygg en Power BI leverantörs instrument panel



När du skapar en kontinuerlig övervaknings lösning för patienter kan du också skapa en instrument panel för ett sjukhus för att visualisera patient data. I den här självstudien får du lära dig hur du skapar en Power BI direkt uppspelnings instrument panel i real tid från din IoT Central Mall för kontinuerlig övervakning av patienter.

>[!div class="mx-imgBorder"]
>GIF-![instrument panel](media/dashboard-gif-3.gif)

Den grundläggande arkitekturen följer den här strukturen:

>[!div class="mx-imgBorder"] 
>![providern prioritering Dashboard](media/dashboard-architecture.png)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Exportera data från Azure IoT Central till Azure Event Hubs
> * Konfigurera en Power BI strömmande data uppsättning
> * Anslut din Logic app till Azure Event Hubs
> * Strömma data till Power BI från din Logic app
> * Bygg en real tids instrument panel för patients viktigare

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En Azure IoT Central-Mall för kontinuerlig övervakning av patienter. Om du inte redan har en, kan du följa stegen för att [distribuera en Programmall](overview-iot-central-healthcare.md).

* Ett Azure [Event Hubs-namnområde och händelsehubben](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

* Den Logic-app som du vill använda för att komma åt Händelsehubben. Om du vill starta din Logic-app med en Azure Event Hubs-utlösare behöver du en [Tom Logic-app](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow).

* Ett Power BI-tjänst konto. Om du inte redan har en, kan du [skapa ett kostnads fritt utvärderings konto för Power BI-tjänst](https://app.powerbi.com/). Om du inte har använt Power BI tidigare kan det vara bra att gå igenom [Kom igång med Power BI](https://docs.microsoft.com/power-bi/service-get-started).

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Konfigurera en kontinuerlig data export till Azure Event Hubs
Du måste först konfigurera en kontinuerlig data export från din Azure IoT Central-app-mall till Azure Event Hub i din prenumeration. Det kan du göra genom att följa stegen i den här Azure IoT Central själv studie kursen för [att exportera till Event Hubs](https://docs.microsoft.com/azure/iot-central/core/howto-export-data). Du behöver bara exportera för telemetri för den här självstudien.

## <a name="create-a-power-bi-streaming-dataset"></a>Skapa en Power BI strömmande data uppsättning

1. Logga in på ditt Power BI-konto.

2. På din önskade arbets yta skapar du en ny strömmande data uppsättning genom att välja knappen **+ skapa** i det övre högra hörnet i verktygsfältet. Du måste skapa en separat data uppsättning för varje patient som du vill ha på din instrument panel.

    >[!div class="mx-imgBorder"] 
    >![skapa strömmande data uppsättning](media/create-streaming-dataset.png)

3. Välj **API** för källan till din data uppsättning.

4. Ange ett **namn** (till exempel ett namn på en patient) för din data uppsättning och fyll sedan i värdena från data strömmen. Du kan se ett exempel nedan baserat på värden som kommer från de simulerade enheterna i program mal len kontinuerlig övervakning av patienter. Exemplet har två patienter:

    * Teddy silver, som har data från den smarta Knee-klammern
    * Yesenia Sanford, som har data från den smarta viktiga uppdateringen

    >[!div class="mx-imgBorder"] 
    >![ange data uppsättnings värden](media/enter-dataset-values.png)

Om du vill veta mer om strömmande data uppsättningar i Power BI kan du läsa det här dokumentet för [strömning i real tid i Power BI](https://docs.microsoft.com/power-bi/service-real-time-streaming).

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Anslut din Logic app till Azure Event Hubs
Om du vill ansluta din Logic app till Azure Event Hubs kan du följa instruktionerna som beskrivs i det här dokumentet för att [skicka händelser med Azure Event Hubs och Azure Logic Apps](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action). Här följer några föreslagna parametrar:

|Parameter|Värde|
|---|---|
|Innehålls typ|application/json|
|Intervall|3|
|Frequency|Sekund|

I slutet av det här steget bör din Logic Apps designer se ut så här:

>[!div class="mx-imgBorder"] 
>![Logic Apps ansluter till Event Hubs](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Strömma data till Power BI från din Logic app
Nästa steg är att parsa de data som kommer från Händelsehubben för att strömma dem till de Power BI data uppsättningar som du har skapat tidigare.

1. Innan du kan göra detta måste du förstå den JSON-nyttolast som skickas från din enhet till Händelsehubben. Du kan göra detta genom att titta på det här [exempel schemat](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#telemetry) och ändra det så att det matchar ditt schema eller använder [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) för att granska meddelandena. Om du använder de kontinuerliga programmen för patient övervakning ser dina meddelanden ut så här:

**Smart viktigare patch-telemetri**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**Telemetri för smart Knee-klammer**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**Egenskaperna**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

2. Nu när du har kontrollerat dina JSON-nyttolaster går du tillbaka till din Logic Apps designer och väljer **+ nytt steg**. Sök och Lägg till **initiera variabel** som nästa steg och ange följande parametrar:

    |Parameter|Värde|
    |---|---|
    |Namn|Namn på gränssnitt|
    |Typ|String|

    Tryck på **Save** (Spara). 

3. Lägg till en annan variabel med namnet **Body** med typen **String**. Din Logic app kommer att ha följande åtgärder tillagda:

    >[!div class="mx-imgBorder"]
    >![initiera variabler](media/initialize-string-variables.png)
    
4. Välj **+ nytt steg** och Lägg till en **parsa JSON** -åtgärd. Byt namn på detta för att **parsa egenskaper**. För innehållet väljer du **Egenskaper** som kommer från händelsehubben. Välj **Använd exempel nytto last för att generera schemat** längst ned och klistra in exempel nytto lasten från avsnittet Egenskaper ovan.

5. Sedan väljer du åtgärden **Ställ in variabel** och uppdaterar variabeln **gränssnitts namn** med **iothub-Interface-Name** från de parsade JSON-egenskaperna.

6. Lägg till en **delad** kontroll som nästa åtgärd och välj variabeln **gränssnitts namn** som parametern on. Du kommer att använda den här för att tratta data till rätt data uppsättning.

7. I ditt Azure IoT Central-program hittar du gränssnitts namnet för hälso tillstånds data för smarta viktigare och den smarta Knee-klammern från vyn **enhets mallar** . Skapa två olika fall för **växel** kontrollen för varje gränssnitts namn och Byt namn på kontrollen på rätt sätt. Du kan ställa in standard fallet för att använda **avslutnings** kontrollen och välja vilken status som du vill visa.

    >[!div class="mx-imgBorder"] 
    >](media/split-by-interface.png) för ![delad kontroll

8. Lägg till en **parsa JSON** -åtgärd för den **smarta viktigare korrigerings** situationen. För innehållet väljer du **innehåll** som kommer från händelsehubben. Kopiera och klistra in exempel nytto lasterna för den smarta viktiga korrigerings filen ovan för att generera schemat.

9. Lägg till en **uppsättnings variabel** åtgärd och uppdatera **Body** -variabeln med **bröd texten** från den parsade JSON-filen i steg 7.

10. Lägg till en **villkors** kontroll som nästa åtgärd och ange villkoret till **Body**, **innehåller**, **HeartRate**. På så sätt ser du till att du har rätt uppsättning data från den smarta viktiga uppdateringen innan du fyller i Power BI data uppsättningen. Steg 7-9 ser ut så här:

    >[!div class="mx-imgBorder"] 
    >![smarta viktigare Lägg till villkor](media/smart-vitals-pbi.png)

11. För det **sanna** fallet för villkoret lägger du till en åtgärd som anropar **Lägg till rader i en data uppsättning** Power BI funktion. Du måste logga in på Power BI för detta. Ditt **falska** ärende kan använda **avslutnings** kontrollen igen.

12. Välj lämplig **arbets yta**, **data uppsättning**och **tabell**. Mappa de parametrar som du angav när du skapade din strömmande data uppsättning i Power BI till de parsade JSON-värdena som kommer från Händelsehubben. Dina fyllda åtgärder bör se ut så här:

    >[!div class="mx-imgBorder"] 
    >![lägga till rader i Power BI](media/add-rows-yesenia.png)

13. Lägg till en **parsa JSON** -åtgärd för att parsa innehållet, på samma sätt som steg 7, för det **smarta Knee** . **Lägg sedan till rader till en data uppsättning** för att uppdatera din Teddy silver-datauppsättning i Power BI.

    >[!div class="mx-imgBorder"] 
    >![smarta viktigare Lägg till villkor](media/knee-brace-pbi.png)

14. Tryck på **Spara** och kör sedan din Logic app.

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Bygg en real tids instrument panel för patients viktigare
Gå nu tillbaka till Power BI och välj **+ skapa** för att skapa en ny **instrument panel**. Ge instrument panelen ett namn och tryck på **skapa**.

Välj de tre punkterna i det övre navigerings fältet och välj sedan **+ Lägg till panel**.

>[!div class="mx-imgBorder"] 
>![lägga till panel i instrument panelen](media/add-tile.png)

Välj den typ av panel som du vill lägga till och anpassa appen som du vill.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort dina resurser med följande steg:

1. Från Azure Portal kan du ta bort Händelsehubben och Logic Apps resurser som du har skapat.

2. För ditt IoT Central-program går du till fliken Administration och väljer **ta bort**.

## <a name="next-steps"></a>Nästa steg

* Läs [rikt linjerna för den kontinuerliga övervakningen av patient övervakning](concept-continuous-patient-monitoring-architecture.md).
