---
title: Skapa en instrumentpanel för hälsodatatriage med Azure IoT Central | Microsoft-dokument
description: Lär dig att skapa en instrumentpanel för hälsodatatriage med hjälp av Azure IoT Central-programmallar.
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 99b27ec53d955079b5f73986408e698955c0969b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77021652"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Självstudiekurs: Skapa en instrumentpanel för Power BI-provider



När du bygger din kontinuerliga patientövervakningslösning kan du också skapa en instrumentpanel för ett sjukhusvårdsteam för att visualisera patientdata. I den här självstudien får du lära dig hur du skapar en Power BI-instrumentpanel för direktuppspelning i realtid från din IoT Central kontinuerlig patientövervakningsprogrammall.

>[!div class="mx-imgBorder"]
>![GIF-instrumentpanel](media/dashboard-gif-3.gif)

Den grundläggande arkitekturen kommer att följa denna struktur:

>[!div class="mx-imgBorder"] 
>![Trageinstrumentpanel för leverantör](media/dashboard-architecture.png)

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Exportera data från Azure IoT Central till Azure Event Hubs
> * Konfigurera en Power BI-direktuppspelningsdatauppsättning
> * Ansluta din Logic App till Azure Event Hubs
> * Strömma data till Power BI från logikappen
> * Skapa en instrumentpanel i realtid för patient vitala

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En Azure IoT Central kontinuerlig patientövervakning programmall. Om du inte redan har någon kan du följa stegen för att [distribuera en programmall](overview-iot-central-healthcare.md).

* Ett [namnområde för](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)Azure Event Hubs och Event Hub .

* Logikappen som du vill komma åt din eventhubb. Om du vill starta logikappen med en Azure Event Hubs-utlösare behöver du en [tom Logic App](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow).

* Ett Power BI-tjänstkonto. Om du inte redan har ett konto kan du [skapa ett kostnadsfritt utvärderingskonto för Power BI-tjänsten](https://app.powerbi.com/). Om du inte har använt Power BI tidigare kan det vara bra att gå igenom [Kom igång med Power BI](https://docs.microsoft.com/power-bi/service-get-started).

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Konfigurera en kontinuerlig dataexport till Azure Event Hubs
Du måste först konfigurera en kontinuerlig dataexport från din Azure IoT Central-appmall till Azure Event Hub i din prenumeration. Du kan göra det genom att följa stegen i den här Azure IoT Central-självstudien för [export till händelsehubbar](https://docs.microsoft.com/azure/iot-central/core/howto-export-data). Du behöver bara exportera för telemetri för den här självstudien.

## <a name="create-a-power-bi-streaming-dataset"></a>Skapa en Strömningsdatauppsättning för Power BI

1. Logga in på ditt Power BI-konto.

2. Skapa en ny strömmande datauppsättning på önskad arbetsyta genom att välja knappen **+ Skapa** i det övre högra hörnet i verktygsfältet. Du måste skapa en separat datauppsättning för varje patient som du vill ha på instrumentpanelen.

    >[!div class="mx-imgBorder"] 
    >![Skapa strömmande datauppsättning](media/create-streaming-dataset.png)

3. Välj **API** för datauppsättningens källa.

4. Ange ett **namn** (till exempel en patients namn) för datauppsättningen och fyll sedan i värdena från din ström. Du kan se ett exempel nedan baserat på värden som kommer från de simulerade enheterna i den kontinuerliga patientövervakningsapplikationsmallen. Exemplet har två patienter:

    * Teddy Silvers, som har data från Smart Knee Brace
    * Yesenia Sanford, som har data från Smart Vitals Patch

    >[!div class="mx-imgBorder"] 
    >![Ange datauppsättningsvärden](media/enter-dataset-values.png)

Om du vill veta mer om direktuppspelning av datauppsättningar i Power BI kan du läsa det här dokumentet om [direktuppspelning i realtid i Power BI](https://docs.microsoft.com/power-bi/service-real-time-streaming).

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Ansluta din Logic App till Azure Event Hubs
Om du vill ansluta din Logic App till Azure Event Hubs kan du följa instruktionerna i det här dokumentet om [hur du skickar händelser med Azure Event Hubs och Azure Logic Apps](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action). Här är några föreslagna parametrar:

|Parameter|Värde|
|---|---|
|Innehållstyp|application/json|
|Intervall|3|
|Frequency|Sekund|

I slutet av det här steget bör logikappdesignern se ut så här:

>[!div class="mx-imgBorder"] 
>![Logic Apps ansluter till eventhubbar](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Strömma data till Power BI från logikappen
Nästa steg blir att tolka data som kommer från händelsehubben för att strömma dem till Power BI-datauppsättningar som du tidigare har skapat.

1. Innan du kan göra detta måste du förstå den JSON-nyttolast som skickas från enheten till din Event Hub. Du kan göra det genom att titta på det här [exempelschemat](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#telemetry) och ändra det så att det matchar schemat eller använder [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) för att granska meddelandena. Om du använder de kontinuerliga patientövervakningsapplikationerna kommer dina meddelanden att se ut så här:

**Smart Vitals Patch telemetri**

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

**Smart knä stag telemetri**

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

**Egenskaper**

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

2. Nu när du har inspekterat dina JSON-nyttolaster går du tillbaka till logikappdesignern och väljer **+ Nytt steg**. Sök och lägg till **Initialize-variabeln** som nästa steg och ange följande parametrar:

    |Parameter|Värde|
    |---|---|
    |Namn|Namn på gränssnitt|
    |Typ|String|

    Tryck på **Save** (Spara). 

3. Lägg till en annan variabel som kallas **Brödtext** med Typ som **Sträng**. Logikappen har lagt till följande åtgärder:

    >[!div class="mx-imgBorder"]
    >![Initiera variabler](media/initialize-string-variables.png)
    
4. Välj **+ Nytt steg** och lägg till en **Parse JSON-åtgärd.** Byt namn på detta till **Parse Properties**. För innehållet väljer du **Egenskaper** som kommer från händelsehubben. Välj **Använd exempelnyttolaster för att generera schemat** längst ned och klistra in exempelnyttolasten i avsnittet Egenskaper ovan.

5. Välj sedan åtgärden **Ange variabel** och uppdatera **variabeln Gränssnittsnamn** med **iothub-interface-namnet** från de tolkade JSON-egenskaperna.

6. Lägg till en **delad** kontroll som nästa åtgärd och välj variabeln **Gränssnittsnamn** som parameter på. Du kommer att använda detta för att kanalisera data till rätt datauppsättning.

7. I ditt Azure IoT Central-program letar du reda på gränssnittsnamnet för hälsodata för Smart Vitals Patch och hälsodata för Smart Knee Brace från **enhetsmallvyn.** Skapa två olika ärenden för **switchkontrollen** för varje gränssnittsnamn och byt namn på kontrollen på rätt sätt. Du kan ange att standardfallet ska använda **avsluta** kontrollen och välja vilken status du vill visa.

    >[!div class="mx-imgBorder"] 
    >![Delad kontroll](media/split-by-interface.png)

8. För **Fallet Smart Vitals Patch** lägger du till en **Parse JSON-åtgärd.** För innehållet väljer du **Innehåll** som kommer från händelsehubben. Kopiera och klistra in exempelnyttolaster för Smart Vitals Patch ovan för att generera schemat.

9. Lägg till en **variabel åtgärd och** uppdatera variabeln **Brödtext** med **brödtexten** från den tolkade JSON i steg 7.

10. Lägg till en **villkorskontroll** som nästa åtgärd och ställ in villkoret på **Brödtext**, **innehåller**, **HeartRate**. Detta kommer att se till att du har rätt uppsättning data som kommer från Smart Vitals Patch innan du fyller i Power BI-datauppsättningen. Steg 7-9 kommer att se ut så här:

    >[!div class="mx-imgBorder"] 
    >![Smart Vitals lägger till villkor](media/smart-vitals-pbi.png)

11. Lägg till en åtgärd som **anropar raden Lägg till i en Power** BI-funktion för true-fallet. **True** Du måste logga in på Power BI för detta. Falskt **False** fall kan återigen använda **avsluta** kontrollen.

12. Välj lämplig **arbetsyta,** **datauppsättning**och **tabell**. Mappa de parametrar som du angav när du skapade din strömmande datauppsättning i Power BI till de tolkade JSON-värden som kommer från händelsehubben. Dina ifyllda åtgärder ska se ut så här:

    >[!div class="mx-imgBorder"] 
    >![Lägga till rader i Power BI](media/add-rows-yesenia.png)

13. För **smart knästödsväpningsfodralet** lägger du till en **Parse JSON-åtgärd** för att tolka innehållet, liknande steg 7. Lägg sedan **till rader i en datauppsättning** för att uppdatera nalle Silvers-datauppsättningen i Power BI.

    >[!div class="mx-imgBorder"] 
    >![Smart Vitals lägger till villkor](media/knee-brace-pbi.png)

14. Tryck på **Spara** och kör sedan logikappen.

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Skapa en instrumentpanel i realtid för patient vitala
Gå nu tillbaka till Power BI och välj **+ Skapa** för att skapa en ny **instrumentpanel**. Ge instrumentpanelen ett namn och tryck på **Skapa**.

Markera de tre punkterna i det övre navigeringsfältet och välj sedan **+ Lägg till panel**.

>[!div class="mx-imgBorder"] 
>![Lägga till panel på instrumentpanelen](media/add-tile.png)

Välj den typ av panel som du vill lägga till och anpassa appen hur du vill.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta att använda det här programmet tar du bort resurserna med följande steg:

1. Från Azure-portalen kan du ta bort resurser för eventhubben och logikappar som du har skapat.

2. För ditt IoT Central-program går du till fliken Administration och väljer **Ta bort**.

## <a name="next-steps"></a>Nästa steg

* Granska den [kontinuerliga patientövervakningsarkitekturvägledningen](concept-continuous-patient-monitoring-architecture.md).
