---
title: Skicka händelser till en Azure Time Series Insights-miljö | Microsoft Docs
description: Lär dig hur du konfigurerar en event hub och kör ett exempelprogram för push-händelser som du kan visa i Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 424476b91537c60a6d7f0f9a854453353bf98633
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557027"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Skicka händelser till en Time Series Insights-miljö med hjälp av en event hub

Den här artikeln förklarar hur du skapar och konfigurerar en event hub i Azure Event Hubs och sedan kör ett exempelprogram för push-händelser. Om du har en befintlig händelsehubb som innehåller händelser i JSON-format, hoppa över den här självstudien och visa din miljö i [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Skapa en Event Hub

1. Läs hur du skapar en event hub i den [dokumentation om Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
1. I sökrutan söker du efter **Händelsehubbar**. Välj i den returnerade listan **Händelsehubbar**.
1. Välj din event hub.
1. När du skapar en event hub skapar du egentligen ett händelsehubbnamnområde. Om du inte har skapat en händelsehubb i namnområdet, på menyn, under **entiteter**, skapa en händelsehubb.  

    ![Listan över händelsehubbar][1]

1. När du skapar en event hub, markerar du den i listan över händelsehubbar.
1. I menyn, under **entiteter**väljer **Händelsehubbar**.
1. Välj namnet på händelsehubben för att konfigurera den.
1. Under **entiteter**väljer **konsumentgrupper**, och välj sedan **konsumentgrupp**.

    ![Skapa en konsumentgrupp][2]

1. Se till att skapa en konsumentgrupp som enbart används av din Time Series Insights-händelsekälla.

    > [!IMPORTANT]
    > Kontrollera att den här konsumentgruppen inte används av andra tjänster (till exempel Azure Stream Analytics-jobb eller en annan Time Series Insights-miljö). Om konsumentgruppen används av den andra påverkas negativt tjänster, Läs-och skrivåtgärder både för den här miljön och för andra tjänster. Om du använder **$Default** som konsumentgrupp, andra läsare potentiellt kan återanvända din konsumentgrupp.

1. I menyn, under **inställningar**väljer **principer för delad åtkomst**, och välj sedan **Lägg till**.

    ![Välj principer för delad åtkomst och välj sedan knappen Lägg till][3]

1. I den **Lägg till ny princip för delad åtkomst** fönstret Skapa en delad åtkomst med namnet **MySendPolicy**. Du använder denna princip för delad åtkomst för att skicka händelser i den C# exemplen senare i den här artikeln.

    ![I rutan princip ange MySendPolicy][4]

1. Under **anspråk**väljer den **skicka** kryssrutan.

## <a name="add-a-time-series-insights-instance"></a>Lägg till en Time Series Insights-instans

Time Series Insights-uppdateringen använder instanser för att lägga till kontextuella data i inkommande telemetridata. Data är ansluten när en fråga körs med hjälp av en **Time Series-ID**. Den **Time Series-ID** för exemplet windmills projektet som vi använder senare i den här artikeln är **Id**. Mer information om Time Series Insight-instanser och **Time Series-ID**, se [Time Series modeller](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Skapa en händelsekälla för Time Series Insights

1. Om du inte har skapat en händelsekälla kan slutföra stegen till [skapa en händelsekälla](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Ange ett värde för `timeSeriesId`. Mer information om **Time Series-ID**, se [Time Series modeller](./time-series-insights-update-tsm.md).

### <a name="push-events"></a>Push-händelser (windmills exemplet)

1. I sökfältet söker du efter **Händelsehubbar**. Välj i den returnerade listan **Händelsehubbar**.

1. Välj din event hub.

1. Gå till **delade åtkomstprinciper** > **RootManageSharedAccessKey**. Kopiera värdet för **anslutning förekomster av textsträngen primär nyckel**.

    ![Kopiera värdet för primärnyckelns anslutningssträng][5]

1. Gå till https://tsiclientsample.azurewebsites.net/windFarmGen.html. URL: en körs windmill simulerade enheter.
1. I den **Händelsehubbens anslutningssträng** rutan på webbsidan, klistra in anslutningssträngen som du kopierade i [skicka händelser](#push-events).
  
    ![Klistra in primärnyckelns anslutningssträng i Event Hub-anslutningssträngen][6]

1. Välj **Klicka om du vill starta**. Simulatorn genererar instans JSON som du kan använda direkt.

1. Gå tillbaka till din event hub i Azure-portalen. På den **översikt** bör du se de nya händelserna tas emot av event hub:

    ![En översikt översiktssidan händelsehubb som visar mått för event hub][7]

<a id="json"></a>

## <a name="supported-json-shapes"></a>JSON-former som stöds

### <a name="sample-1"></a>Exempel 1

#### <a name="input"></a>Indata

Ett enkelt JSON-objekt:

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```

#### <a name="output-one-event"></a>Utdata: En händelse

|id|tidsstämpel|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>Exempel 2

#### <a name="input"></a>Indata

En JSON-matris med två JSON-objekt. Varje JSON-objekt konverteras till en händelse.

```json
[
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    },
    {
        "id":"device2",
        "timestamp":"2016-01-17T01:17:00Z"
    }
]
```

#### <a name="output-two-events"></a>Utdata: Två händelser

|id|tidsstämpel|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>Exempel 3

#### <a name="input"></a>Indata

En JSON-objekt med en kapslad JSON-matris som innehåller två JSON-objekt:

```json
{
    "location":"WestUs",
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
}
```

#### <a name="output-two-events"></a>Utdata: Två händelser

Egenskapen **plats** kopieras till varje händelse.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Exempel 4

#### <a name="input"></a>Indata

En JSON-objekt med en kapslad JSON-matris som innehåller två JSON-objekt. Denna indata visar att globala egenskaperna kan representeras av komplexa JSON-objekt.

```json
{
    "location":"WestUs",
    "manufacturer":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z",
            "data":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z",
            "data":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```

#### <a name="output-two-events"></a>Utdata: Två händelser

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|tryck|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visa din miljö i Time Series Insights explorer](https://insights.timeseries.azure.com)

<!-- Images -->
[1]: media/send-events/updated.png
[2]: media/send-events/consumer-group.png
[3]: media/send-events/shared-access-policy.png
[4]: media/send-events/shared-access-policy-2.png
[5]: media/send-events/sample-code-connection-string.png
[6]: media/send-events/updated_two.png
[7]: media/send-events/telemetry.png
