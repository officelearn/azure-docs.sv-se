---
title: Hur man skickar händelser till en Azure Time Series Insights-miljö | Microsoft Docs
description: Den här självstudien beskrivs hur du skapar och konfigurerar event hub och kör ett exempelprogram för push-händelser som ska visas i Azure Time Series Insights.
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
ms.openlocfilehash: c583c2211297acd83f88d23b2b8cbd9f8207927f
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52867619"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Skicka händelser till en Time Series Insights-miljö med hjälp av Event Hub

Den här artikeln förklarar hur du skapar och konfigurerar event hub och kör ett exempelprogram för push-händelser. Om du har en befintlig händelsehubb med händelser i JSON-format, hoppa över den här självstudien och visa din miljö i [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Skapa en Event Hub

1. För att skapa en Event Hub, följer du instruktionerna från Event Hub [dokumentation](https://docs.microsoft.com/azure/event-hubs/).
1. Sök efter `Event Hub` i sökfältet. Klicka på **Händelsehubbar** i den returnerade listan.
1. Välj din Event Hub genom att klicka på dess namn.
1. När du skapar en Event Hub skapar du egentligen en Event Hub-Namespace.  Om du har än att skapa en Händelsehubb i Namespace, skapa en under entiteter.  

    ![uppdaterad][1]

1. När du har skapat en Event Hub, klickar du på dess namn.
1. Under **entiteter** i fönstret i mitten konfiguration klickar du på **Händelsehubbar** igen.
1. Välj namnet på Händelsehubben för att konfigurera den.

    ![Consumer-group][2]

1. Under **entiteter**väljer **konsumentgrupper**.
1. Se till att skapa en konsumentgrupp som enbart används av din TSI-händelsekälla.

    > [!IMPORTANT]
    > Kontrollera att den här konsumentgruppen inte används av någon annan tjänst (till exempel Stream Analytics-jobb eller en annan TSI-miljö). Om konsumentgruppen används av andra påverkas tjänster, Läsåtgärd negativt för den här miljön och andra tjänster. Om du använder `$Default` som konsumentgrupp, det kan leda till eventuell återanvändning av andra läsare.

1. Under den **inställningar** väljer **åtkomstprinciper för filresursen**.
1. Skapa på event hub **MySendPolicy** som används för att skicka händelser i den C# exemplet.

    ![delade = åtkomst-ett][3]

    ![delad åtkomst-två][4]

## <a name="add-time-series-insights-instances"></a>Lägga till Time Series Insights-instanser

TSI-uppdateringen använder instanser för att lägga till kontextuella data i inkommande telemetridata. Data är ansluten på frågan med en **Time Series-ID**. Den **Time Series-ID** för exemplet windmills projektet är `Id`. Mer information om Time Series-instanser och **Time Series-ID: N**bör du läsa [Time Series modeller](./time-series-insights-update-tsm.md).

### <a name="create-time-series-insights-event-source"></a>Skapa händelsekälla för Time Series Insights

1. Om du inte har skapat en händelsekälla följer du [dessa instruktioner](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub) för att skapa en händelsekälla.
1. Ange den `timeSeriesId` – avser [Time Series modeller](./time-series-insights-update-tsm.md) mer information om **Time Series-ID: N**.

### <a name="push-events-sample-windmills"></a>Push-händelser (exempel windmills)

1. Sök efter händelsehubb i sökfältet. Klicka på **Händelsehubbar** i den returnerade listan.
1. Välj din event hub genom att klicka på dess namn.
1. Gå till **delade åtkomstprinciper** och sedan **RootManageSharedAccessKey**. Kopiera den **anslutning förekomster av textsträngen primär nyckel**

   ![connection-string][5]

1. Gå till https://tsiclientsample.azurewebsites.net/windFarmGen.html. Den här lösningen körs windmill simulerade enheter.
1. Klistra in anslutningssträngen som du kopierade från steg tre i den **Händelsehubbens anslutningssträng**.

    ![connection-string][6]

1. Klicka på **Klicka om du vill starta**. Simulatorn kommer även att skapa en instans JSON som du kan använda direkt.
1. Gå tillbaka till din Event Hub. Du bör se de nya händelser som tas emot av hubben:

   ![telemetri][7]

<a id="json"></a>

## <a name="supported-json-shapes"></a>JSON-former som stöds

### <a name="sample-1"></a>Exempel 1

#### <a name="input"></a>Indata

Ett enda JSON-objekt.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```

#### <a name="output---one-event"></a>Resultat – en händelse

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

#### <a name="output---two-events"></a>Resultat – två händelser

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

#### <a name="output---two-events"></a>Resultat – två händelser

Observera att egenskapen ”plats” kopieras till varje händelse.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>Exempel 4

#### <a name="input"></a>Indata

Ett JSON-objekt med en kapslad JSON-matris som innehåller två JSON-objekt. Denna indata visar att de globala egenskaperna kan representeras av komplexa JSON-objekt.

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

#### <a name="output---two-events"></a>Resultat – två händelser

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|tryck|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visa din miljö i Time Series Insights Explorer](https://insights.timeseries.azure.com).

<!-- Images -->
[1]: media/send-events/updated.png
[2]: media/send-events/consumer-group.png
[3]: media/send-events/shared-access-policy.png
[4]: media/send-events/shared-access-policy-2.png
[5]: media/send-events/sample-code-connection-string.png
[6]: media/send-events/updated_two.png
[7]: media/send-events/telemetry.png