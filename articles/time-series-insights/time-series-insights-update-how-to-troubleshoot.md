---
title: Diagnostisera och felsöka Azure Time Series Insights Preview | Microsoft Docs
description: Lära dig mer om att diagnostisera och felsöka med förhandsversionen av Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 381ecd4681bf1d7abfc8939e256bf84db9bc2172
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60460624"
---
# <a name="diagnose-and-troubleshoot"></a>Diagnostisera och felsöka

Den här artikeln sammanfattas flera vanliga problem som kan uppstå när du arbetar med förhandsversionen av Azure Time Series Insights-miljö. Artikeln beskriver också möjliga orsaker och lösningar för varje problem.

## <a name="problem-i-cant-find-my-environment-in-the-time-series-insights-preview-explorer"></a>Problem: Jag kan inte hitta min miljö i förhandsversionen av Time Series Insights explorer

Det här problemet kan inträffa om du inte har behörighet att komma åt Time Series Insights-miljö. Användarna måste rollen Läsare behörighet att visa sin Time Series Insights-miljö. För att kontrollera de aktuella åtkomstnivåerna och bevilja ytterligare åtkomst, gå till avsnittet principerna för dataåtkomst i Time Series Insights-resurs i den [Azure-portalen](https://portal.azure.com/).

  ![Miljö][1]

## <a name="problem-no-data-is-seen-in-the-time-series-insights-preview-explorer"></a>Problem: Inga data visas i förhandsversionen av Time Series Insights explorer

Det finns flera vanliga orsaker varför du inte kanske se dina data i den [förhandsversion av Azure Time Series Insights explorer](https://insights.timeseries.azure.com/preview).

- Din händelsekälla kan inte få data.

    Kontrollera att din händelsekälla som är en event hub eller en IoT-hubb, tar emot data från din taggar eller instanser. Kontrollera, gå till översiktssidan för din resurs i Azure-portalen.

    ![Dashboard-insights][2]

- Källdata händelse är inte i JSON-format.

    Time Series Insights har stöd för JSON-data. JSON-exempel finns [stöds JSON-former](./how-to-shape-query-json.md).

- Din nyckel för event källa saknas i en behörighet som krävs.

  * För en IoT-hubb, måste du ange den nyckel som har **tjänsten ansluta** behörighet.

    ![Konfiguration][3]

  * Som visas i den föregående bilden är båda principerna **iothubowner** och **service** fungerar eftersom de har **tjänsten ansluta** behörighet.
  * För en händelsehubb, måste du ange den nyckel som har **lyssna** behörighet.
  
    ![Behörigheter][4]

  * Som visas i den föregående bilden är båda de **läsa** och **hantera** principer fungerar eftersom de har **lyssna** behörighet.

- Din konsumentgrupp som angetts är inte exklusivt för Time Series Insights.

    Under registreringen av en IoT hub eller event hub, kan du ange konsumentgrupp som används för att läsa data. Dela inte konsumentgruppen. Om konsumentgruppen delas underliggande event hub automatiskt kopplas från någon av läsarna slumpmässigt. Ange en unik konsumentgrupp för Time Series Insights att läsa från.

- Din Time Series-ID-egenskapen angetts vid tidpunkten för etablering är felaktigt, saknas eller är null.

    Det här problemet kan inträffa om Time Series-ID-egenskapen är felaktigt konfigurerad vid tidpunkten för etablering i miljön. Mer information finns i [bästa praxis för att välja en Time-ID](./time-series-insights-update-how-to-id.md). Just nu, kan inte du uppdatera en befintlig Time Series Insights-miljö för att använda en annan Time Series-ID.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problem: Vissa data visas, men vissa saknas

Du kan skicka data utan att tid serien ID.

- Det här problemet kan uppstå när du skickar händelser utan att tid serien ID-fältet i nyttolasten. Mer information finns i [stöds JSON-former](./how-to-shape-query-json.md).

- Det här problemet kan uppstå miljön har begränsats.

    > [!NOTE]
    > För närvarande stöder Time Series Insights tariffen maximala inmatning 6 Mbit/s.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem: Min händelsekälla tidsstämpel egenskapen name inställningen fungerar inte

Se till att namnet och värdet överensstämmer med följande regler:

* Egenskapsnamnet för tidsstämpeln är skiftlägeskänsligt.
* Tidsstämpel-egenskapsvärde som kommer från din händelsekälla som en JSON-sträng har formatet `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Ett exempel på sådana en sträng är `“2008-04-12T12:53Z”`.

Det enklaste sättet att säkerställa att din egenskapsnamn för tidsstämpeln är skapas och fungerar är att använda förhandsversionen av Time Series Insights explorer. Använd diagrammet för att välja en viss tidsperiod när du har angett egenskapsnamn för tidsstämpeln i förhandsversionen av Time Series Insights-Utforskaren. Högerklicka på markeringen och välj den **utforska händelser** alternativet. Första kolumnrubriken är egenskapsnamn för tidsstämpeln. Den bör ha `($ts)` bredvid ordet `Timestamp`, snarare än:

* `(abc)`, vilket betyder att Time Series Insights läser datavärdena som strängar.
* Kalenderikonen, vilket betyder att Time Series Insights läser datavärdet är datetime.
* `#`, vilket betyder att Time Series Insights läser datavärdena som ett heltal.

Om tidsstämpel-egenskapen inte är uttryckligen anges, används en händelse IoT hub eller event hub Kötid som standard tidsstämpel.

## <a name="problem-i-cant-edit-or-view-my-time-series-model"></a>Problem: Jag kan inte redigera eller visa min Tidsseriemodell

- Du kan komma åt en Time Series Insights S1 eller S2-miljö.

   Time Series-modeller stöds endast i PAYG-miljöer. Mer information om hur du kommer åt din S1/S2-miljö från förhandsversionen av Time Series Insights explorer finns i [visualisera data i Utforskaren](./time-series-insights-update-explorer.md).

   ![Access][5]

- Du kanske inte har behörighet att visa och redigera modellen.

   Användarna måste deltagare behörighet att redigera och visa deras Tidsseriemodell. Kontrollera de aktuella åtkomstnivåerna och bevilja ytterligare åtkomst, gå till avsnittet principerna för dataåtkomst på din Time Series Insights-resurs i Azure-portalen.

## <a name="problem-all-my-instances-in-the-time-series-insights-preview-explorer-dont-have-a-parent"></a>Problem: Alla instanser i förhandsversionen av Time Series Insights explorer har inte en överordnad

Det här problemet kan inträffa om din miljö inte har en Tidsseriemodell-hierarki som har definierats. Mer information finns i [arbeta med Time Series-modeller](./time-series-insights-update-how-to-tsm.md).

  ![Time Series-modeller][6]

## <a name="next-steps"></a>Nästa steg

- Läs [arbeta med Time Series-modeller](./time-series-insights-update-how-to-tsm.md).
- Läs [stöds JSON-former](./how-to-shape-query-json.md).

<!-- Images -->
[1]: media/v2-update-diagnose-and-troubleshoot/environment.png
[2]: media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png
[3]: media/v2-update-diagnose-and-troubleshoot/configuration.png
[4]: media/v2-update-diagnose-and-troubleshoot/permissions.png
[5]: media/v2-update-diagnose-and-troubleshoot/access.png
[6]: media/v2-update-diagnose-and-troubleshoot/tsm.png