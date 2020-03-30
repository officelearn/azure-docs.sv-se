---
title: Diagnostisera och felsöka en förhandsgranskningsmiljö – Insikter i Azure Time Series | Microsoft-dokument
description: Lär dig hur du diagnostiserar och felsöker en förhandsversion av Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 667dee6365f38ae058e91c61c24838d8912df26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152676"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>Diagnostisera och felsöka en förhandsgranskningsmiljö

Den här artikeln sammanfattar flera vanliga problem som kan uppstå när du arbetar med din Azure Time Series Insights Preview-miljö. Artikeln beskriver också potentiella orsaker och lösningar för varje problem.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Problem: Jag kan inte hitta min miljö i förhandsgranskningsutforskaren

Det här problemet kan uppstå om du inte har behörighet att komma åt time series insights-miljön. Användarna behöver en åtkomstroll på läsaresnivå för att kunna visa sin time series insights-miljö. Om du vill verifiera de aktuella åtkomstnivåerna och bevilja ytterligare åtkomst går du till avsnittet **Dataåtkomstprinciper** på time series insights-resursen i [Azure-portalen](https://portal.azure.com/).

  [![Verifiera principer för dataåtkomst.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Problem: Inga data visas i förhandsgranskningsutforskaren

Det finns flera vanliga orsaker till att dina data kanske inte visas i [utforskaren för förhandsversionen](https://insights.timeseries.azure.com/preview)av Azure Time Series Insights .

- Händelsekällan kanske inte tar emot data.

    Kontrollera att händelsekällan, som är en händelsehubb eller en IoT-hubb, tar emot data från dina taggar eller instanser. Kontrollera genom att gå till översiktssidan för din resurs i Azure-portalen.

    [![Granska översikt över instrumentpanelsmått.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Händelsekälladata är inte i JSON-format.

    Time Series Insights stöder endast JSON-data. För JSON-exempel läser du [JSON-former som stöds](./how-to-shape-query-json.md).

- Händelsekällasnyckeln saknar en nödvändig behörighet.

  * För en IoT-hubb måste du ange nyckeln som har behörighet att **ansluta tjänsten.**

    [![Verifiera IoT-hubbbehörigheter.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * Både principer **iothubowner** och **service** arbete eftersom de har **service ansluta** behörighet.

  * För en händelsehubb måste du ange nyckeln som har **lyssningsbehörighet.**
  
    [![Granska behörigheter för händelsenav.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * Både **Read-** och **Manage-principerna** fungerar eftersom de har **behörigheten Lyssna.**

- Din konsumentgrupp som tillhandahålls är inte exklusiv för Time Series Insights.

    Under registreringen av en IoT-hubb eller händelsehubb anger du den konsumentgrupp som används för att läsa data. Denna konsumentgrupp måste vara unik per miljö. Om konsumentgruppen delas kopplar den underliggande händelsehubben automatiskt från en av läsarna slumpmässigt. Tillhandahålla en unik konsumentgrupp för Time Series Insights att läsa från.

- Egenskapen Time Series ID som angavs vid etableringstillfället är felaktig, saknad eller null.

    Det här problemet kan uppstå om egenskapen Time Series ID har konfigurerats felaktigt vid etablering av miljön. Mer information finns i [Metodtips för att välja ett tidsserie-ID](./time-series-insights-update-how-to-id.md). För närvarande kan du inte uppdatera en befintlig Time Series Insights-miljö för att använda ett annat Tidsserie-ID.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problem: Vissa data visas, men vissa saknas

Du kanske skickar data utan tidsserie-ID.

- Det här problemet kan uppstå när du skickar händelser utan fältet Tidsserie-ID i nyttolasten. Mer information finns i [JSON-former som stöds](./how-to-shape-query-json.md).
- Det här problemet kan uppstå eftersom din miljö begränsas.

    > [!NOTE]
    > För närvarande stöder Time Series Insights en maximal inmatningshastighet på 6 Mbit/s.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Problem: Data visades, men nu har inmatningen stoppats

- Nyckeln för händelsekällan kan ha återskapats och förhandsgranskningsmiljön behöver den nya händelsekällanyckeln.

Det här problemet uppstår när nyckeln som anges när du skapar händelsekällan inte längre är giltig. Du skulle se telemetri i din hubb men inga ingående mottagna meddelanden i Time Series Insights. Om du är osäker på om nyckeln har återskapats eller inte kan du söka i aktivitetsloggen för händelsehubbar efter "Skapa eller uppdatera namnområdesauktoriseringsregler" eller söka i "Skapa eller uppdatera IotHub-resurs" för IoT-hubb. 

Om du vill uppdatera förhandsversionen av Time Series Insights med den nya nyckeln öppnar du hubbresursen i Azure-portalen och kopierar den nya nyckeln. Navigera till din TSD-resurs och klicka på händelsekällor. 

   [![Uppdatera nyckel.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Välj de händelsekällor som har stoppats från, klistra in den nya nyckeln och klicka på Spara.

   [![Uppdatera nyckel.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problem: Händelsekällans tidsstämpelnamn fungerar inte

Kontrollera att namn och värde överensstämmer med följande regler:

* Egenskapsnamnet för tidsstämpeln är skiftlägeskänsligt.
* Egenskapsvärdet för tidsstämpel som kommer från händelsekällan `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`som en JSON-sträng har formatet . Ett exempel på en `“2008-04-12T12:53Z”`sådan sträng är .

Det enklaste sättet att se till att ditt tidsstämpelgenskapsnamn fångas in och fungerar som det ska är att använda utforskaren för förhandsversionen av Time Series Insights. I utforskaren för förhandsversionen av Time Series Insights använder du diagrammet för att välja en tidsperiod efter att du angett egenskapsnamnet för tidsstämpel. Högerklicka på markeringen och välj alternativet **utforska händelser.** Det första kolumnhuvudet är namnet på tidsstämpelns egenskap. Det bör `($ts)` ha bredvid ordet `Timestamp`, snarare än:

* `(abc)`, vilket anger att Time Series Insights läser datavärdena som strängar.
* **Kalenderikonen,** som anger att Time Series Insights läser datavärdet som datetime.
* `#`, vilket anger att Time Series Insights läser datavärdena som ett heltal.

Om egenskapen Tidsstämpel inte uttryckligen anges används en händelses IoT-hubb eller händelsenav Enqueued Time som standardtidsstämpel.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problem: Jag kan inte visa data från mitt varma arkiv i utforskaren

- Du kanske har etablerat ditt varma lager nyligen och data flödar fortfarande in.
- Du kan ha tagit bort din varma butik, i vilket fall du skulle ha förlorat data.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problem: Jag kan inte visa eller redigera min Tidsseriemodell

- Du kanske har åtkomst till en Time Series Insights S1- eller S2-miljö.

   Time Series-modeller stöds endast i pay-as-you-go-miljöer. Mer information om hur du kommer åt din S1- eller S2-miljö från utforskaren Förhandsgranskning av Time Series Insights finns [i Visualisera data i utforskaren](./time-series-insights-update-explorer.md).

   [![Inga händelser i miljön.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Du kanske inte har behörighet att visa och redigera modellen.

   Användare behöver åtkomst på deltagarnivå för att redigera och visa sin Tidsseriemodell. Om du vill verifiera de aktuella åtkomstnivåerna och bevilja ytterligare åtkomst går du till avsnittet **Dataåtkomstprinciper** på time series insights-resursen i Azure-portalen.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Problem: Alla mina instanser i förhandsgranskningsutforskaren saknar en överordnad

Det här problemet kan uppstå om din miljö inte har en tidsseriemodellhierarki definierad. Mer information finns i [Arbeta med tidsseriemodeller](./time-series-insights-update-how-to-tsm.md).

  [![Oparenterade instanser visar en varning.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Läs [arbete med time series-modeller](./time-series-insights-update-how-to-tsm.md).

- Läs mer om [JSON-former som stöds](./how-to-shape-query-json.md).

- Granska [planering och begränsningar](./time-series-insights-update-plan.md) i förhandsversionen av Azure Time Series Insights.
