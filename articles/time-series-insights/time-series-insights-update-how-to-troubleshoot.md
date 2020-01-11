---
title: Diagnostisera och Felsök en för hands versions miljö – Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du diagnostiserar och felsöker en Azure Time Series Insights Preview-miljö.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.openlocfilehash: 1fc3aa6caa6266d2cd42e4783e8e39d5cc92c220
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861582"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>Diagnostisera och Felsök en för hands versions miljö

I den här artikeln sammanfattas flera vanliga problem som kan uppstå när du arbetar med din Azure Time Series Insights för hands versions miljö. Artikeln beskriver också möjliga orsaker och lösningar för varje problem.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Problem: Jag kan inte hitta min miljö i Preview Explorer

Det här problemet kan inträffa om du inte har behörighet att komma åt Time Series Insightss miljön. Användare behöver en åtkomst roll på läsare-nivå för att visa sin Time Series Insightss miljö. Om du vill verifiera de aktuella åtkomst nivåerna och bevilja ytterligare åtkomst går du till avsnittet **data åtkomst principer** på Time Series Insights resursen i [Azure Portal](https://portal.azure.com/).

  [![kontrol lera principer för data åtkomst.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Problem: inga data visas i förhands gransknings Utforskaren

Det finns flera vanliga orsaker till varför dina data kanske inte visas i [Azure Time Series Insights Preview Explorer](https://insights.timeseries.azure.com/preview).

- Din händelse källa kanske inte tar emot data.

    Kontrol lera att händelse källan, som är en händelsehubben eller IoT Hub, tar emot data från taggar eller instanser. Verifiera genom att gå till översikts sidan för resursen i Azure Portal.

    [Översikt över instrument panelens mått för ![.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Händelsens källdata är inte i JSON-format.

    Time Series Insights stöder endast JSON-data. För JSON-exempel kan du läsa [JSON-former som stöds](./how-to-shape-query-json.md).

- Din händelse käll nyckel saknar en nödvändig behörighet.

  * För en IoT-hubb måste du ange den nyckel som har behörighet för **tjänst anslutning** .

    [![verifiera behörigheter för IoT Hub.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * Båda principerna **iothubowner** och **service** fungerar eftersom de har behörighet för **tjänst anslutning** .

  * För en Event Hub måste du ange den nyckel som har behörigheten **Lyssna** .
  
    [![granska händelse hubbens behörigheter.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * Både **Läs** -och **hanterings** principerna fungerar eftersom de har behörigheten **Lyssna** .

- Den angivna konsument gruppen är inte exklusiv för Time Series Insights.

    Under registreringen av en IoT-hubb eller Event Hub anger du den konsument grupp som används för att läsa data. Den här konsument gruppen måste vara unik per miljö. Om konsument gruppen delas, kopplas den underliggande händelsehubben automatiskt till en av läsarna. Ange en unik konsument grupp för Time Series Insights att läsa från.

- Din Time Series ID-egenskap som angavs vid tidpunkten för etableringen är felaktig, saknas eller är null.

    Det här problemet kan inträffa om egenskapen Time Series ID är felaktigt konfigurerad vid tidpunkten för etablering av miljön. Mer information finns [i metod tips för att välja ett Time Series-ID](./time-series-insights-update-how-to-id.md). För tillfället kan du inte uppdatera en befintlig Time Series Insights miljö för att använda ett annat tids serie-ID.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problem: vissa data visas, men vissa saknas

Du kan skicka data utan tids serie-ID.

- Det här problemet kan uppstå när du skickar händelser utan fältet Time Series ID i nytto lasten. Mer information finns i [JSON-former som stöds](./how-to-shape-query-json.md).
- Det här problemet kan bero på att din miljö är begränsad.

    > [!NOTE]
    > För närvarande har Time Series Insights stöd för en maximal inmatnings hastighet på 6 Mbps.

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problem: min händelse källas egenskaps namn för tidsstämpel fungerar inte

Se till att namnet och värdet överensstämmer med följande regler:

* Egenskaps namnet för tidsstämpeln är Skift läges känsligt.
* Värdet för egenskapen tidsstämpel som kommer från din händelse källa som en JSON-sträng har formatet `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Ett exempel på sådana en sträng är `“2008-04-12T12:53Z”`.

Det enklaste sättet att se till att namnet på Tidsstämpelns egenskap är infångat och fungerar korrekt är att använda Time Series Insights Preview Explorer. I Time Series Insights Preview Explorer använder du diagrammet för att välja en tids period när du har angett namnet på Tidsstämpelns egenskap. Högerklicka på markeringen och välj alternativet för att **utforska händelser** . Den första kolumn rubriken är namnet på Tidsstämpelns egenskap. Den bör ha `($ts)` bredvid ordet `Timestamp`i stället för:

* `(abc)`, vilket anger att Time Series Insights läser data värden som strängar.
* **Kalender** ikonen, som anger att Time Series Insights läser datavärdet som DateTime.
* `#`, vilket anger att Time Series Insights läser data värden som ett heltal.

Om egenskapen timestamp inte uttryckligen anges används en händelses IoT Hub-eller Event Hub-tid som standard tids stämpling.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problem: Jag kan inte visa data från min varma lagrings plats i Utforskaren

- Du kanske har samlat in ditt varmt Arkiv nyligen och data flödar fortfarande.
- Du kanske har tagit bort din varma lagrings plats, vilket innebär att du skulle ha förlorat data.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problem: Jag kan inte Visa eller redigera min tids serie modell

- Du kanske använder en Time Series Insights S1-eller S2-miljö.

   Tids serie modeller stöds bara i "betala per användning"-miljöer. Mer information om hur du kommer åt din S1-eller S2-miljö från Time Series Insights Preview Explorer finns [i visualisera data i Utforskaren](./time-series-insights-update-explorer.md).

   [![inga händelser i miljön.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Du kanske inte har behörighet att visa och redigera modellen.

   Användare behöver åtkomst på deltagar nivå för att redigera och visa sin tids serie modell. Om du vill kontrol lera de aktuella åtkomst nivåerna och bevilja ytterligare åtkomst går du till avsnittet **data åtkomst principer** i Time Series Insights resursen i Azure Portal.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Problem: alla mina instanser i Preview Explorer saknar överordnad

Det här problemet kan inträffa om din miljö inte har en definierad tids serie modell hierarki. Mer information finns i [arbeta med tids serie modeller](./time-series-insights-update-how-to-tsm.md).

  [![överordnade instanser visas en varning.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Läs [arbeta med tids serie modeller](./time-series-insights-update-how-to-tsm.md).

- Lär dig mer om [JSON-former som stöds](./how-to-shape-query-json.md).

- Granska [planering och gränser](./time-series-insights-update-plan.md) i Azure Time Series Insights för hands versionen.
