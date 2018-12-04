---
title: Diagnostisera och felsöka Azure Time Series Insights (förhandsversion) | Microsoft Docs
description: Förstå hur du diagnostiserar och felsöka med Azure Time Series Insights (förhandsversion)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: d13d373169287a0ec5931d5437b0a3bc70ecd79a
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856853"
---
# <a name="how-to-diagnose-and-troubleshoot"></a>Diagnostisera och felsöka

Den här artikeln sammanfattas flera vanliga problem som kan uppstå arbeta med din Azure Time Series Insights (TSI)-miljö. Artikeln beskriver också möjliga orsaker och lösningar för var och en.

## <a name="problem-no-data-is-seen-in-the-time-series-insights-preview-explorer"></a>Problemet: Inga data visas i Time Series Insights (förhandsversion) Explorer

Det finns flera vanliga orsaker varför du inte kanske se dina data i Azure TSI-Utforskaren:

1. Din händelsekälla kan inte emot data.

    Kontrollera att din händelsekälla (Händelsehubb eller IoT Hub) tar emot data från taggarna / instanser. Du kan göra det genom att gå till översiktssidan för din resurs i Azure-portalen.

    ![instrumentpanelen insikter][1]

1. Källdata händelse är inte i JSON-format

    Azure TSI har stöd för JSON-data. JSON-exempel finns [stöds JSON-former](./how-to-shape-query-json.md).

1. Din nyckel för event källa saknas för en behörighet som krävs

    ![konfiguration][2]

    * Du måste ange den nyckel som har tjänsten anslutningsbehörighet för en IoT-hubb.
    * Som visas i den föregående bilden är någon av principerna *iothubowner* och tjänsten skulle fungera, eftersom båda har anslutningsbehörighet för tjänsten.
    * Du måste ange den nyckel som har lyssna-behörighet för en händelsehubb.
    * I föregående bild visas något av principerna läsa och hantera skulle fungera, eftersom båda Listen behörighet.

    ![behörigheter][3]

1. Din konsumentgrupp som angetts är inte bara TSI

    Under registreringen av am IoT-hubb eller en händelsehubb kan ange du konsumentgrupp som ska användas för läsning av data. Den här konsumentgruppen måste inte delas. Om konsumentgruppen delas underliggande event hub automatiskt kopplas från någon av läsarna slumpmässigt. Ange en unik konsumentgrupp för TSI att läsa från.

## <a name="problem-some-data-is-shown-but-some-is-missing"></a>Problem: Vissa data visas, men vissa saknas

Detta kan inträffa eftersom din miljö har begränsats.

> [!NOTE]
> För närvarande stöder Azure TSI tariffen maximala inmatning 6 Mbit/s.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem: Min händelsekälla tidsstämpel egenskapen name inställningen fungerar inte

Se till att namnet och värdet överensstämmer med följande regler:

* Den **tidsstämpel** egenskapsnamnet är skiftlägeskänsligt.
* Den **tidsstämpel** egenskapsvärde som kommer från din händelsekälla som en JSON-sträng som ska ha formatet `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Ett exempel på sådana en sträng är `“2008-04-12T12:53Z”`.

Det enklaste sättet att säkerställa att din egenskapsnamn för tidsstämpeln är skapas och fungerar är att använda TSI-Utforskaren. Använd diagrammet för att välja en viss tidsperiod när du har angett egenskapsnamn för tidsstämpeln i TSI-Utforskaren. Högerklicka på markeringen och välj den **utforska händelser** alternativet. Första kolumnrubriken ska vara din **tidsstämpel** egenskapsnamn och bör innehålla en `($ts)` bredvid ordet `Timestamp`, snarare än:

* `(abc)`, som visar att TSI läser datavärdena som strängar
* Kalenderikonen, vilket visar att TSI läser datavärdet är datetime
* `#`, som visar att TSI läser datavärdena som ett heltal

## <a name="problem-my-time-series-id-property-is-incorrect-missing-or-null"></a>Problem: Min Time Series-ID-egenskapen är felaktigt, saknas eller är null

Detta kan inträffa om den **Time Series-ID** egenskapen är felaktigt konfigurerad vid tidpunkten för etablering i miljön. Se den [bästa praxis för att välja en Time-ID](./time-series-insights-update-how-to-id.md) artikel när du väljer en **Time Series-ID**. Just nu, du kan inte uppdatera en befintlig miljö TSI (förhandsversion) för att använda en annan **Time Series-ID**.

## <a name="problem-all-my-instances-in-time-series-insights-preview-explorer-dont-have-a-parent"></a>Problem: Alla instanser i Time Series Insights (förhandsversion) Explorer inte har en överordnad

Detta kan inträffa om din miljö inte har en **Tidsseriemodell** hierarki som har definierats. Se den här artikeln för mer information om [hur du arbetar med Time Series modeller](./time-series-insights-update-how-to-tsm.md).

## <a name="next-steps"></a>Nästa steg

* Läs [hur du arbetar med Time Series modeller](./time-series-insights-update-how-to-tsm.md).

* Läs [stöds JSON-former](./how-to-shape-query-json.md).

<!-- Images -->
[1]: media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png
[2]: media/v2-update-diagnose-and-troubleshoot/configuration.png
[3]: media/v2-update-diagnose-and-troubleshoot/permissions.png