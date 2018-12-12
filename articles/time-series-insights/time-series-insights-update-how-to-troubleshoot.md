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
ms.date: 12/06/2018
ms.openlocfilehash: 4fdb7cf0007af5f92794ebe5f616c1c8a28af0e4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099673"
---
# <a name="how-to-diagnose-and-troubleshoot"></a>Diagnostisera och felsöka

Den här artikeln sammanfattas flera vanliga problem som kan uppstå arbeta med din Azure Time Series Insights (TSI)-miljö. Artikeln beskriver också möjliga orsaker och lösningar för var och en.

## <a name="problem-i-cant-find-my-environment-in-the-time-series-insights-preview-explorer"></a>Problem: Jag kan inte hitta min miljö i explorer Time Series Insights (förhandsversion)

Detta kan inträffa om du inte har behörighet att komma åt TSI-miljö. Användare behöver ”” administratörsnivå läsarroll för att visa sin TSI-miljö. Du kan verifiera de aktuella åtkomstnivåerna och bevilja ytterligare åtkomst genom att gå till avsnittet principerna för dataåtkomst på TSI-resurs i [Azure-portalen](https://portal.azure.com/).

  ![miljö][1]

## <a name="problem-no-data-is-seen-in-the-time-series-insights-preview-explorer"></a>Problemet: Inga data visas i explorer Time Series Insights (förhandsversion)

Det finns flera vanliga orsaker varför du inte kanske se dina data i den [Azure TSI (förhandsversion) explorer](https://insights.timeseries.azure.com/preview):

1. Din händelsekälla kan inte emot data.

    Kontrollera att din händelsekälla (Händelsehubb eller IoT Hub) tar emot data från taggarna / instanser. Du kan göra det genom att gå till översiktssidan för din resurs i Azure-portalen.

    ![instrumentpanelen insikter][2]

1. Källdata händelse är inte i JSON-format

    Azure TSI har stöd för JSON-data. JSON-exempel finns [stöds JSON-former](./how-to-shape-query-json.md).

1. Din nyckel för event källa saknas för en behörighet som krävs

    * Du måste ange den nyckel som har tjänsten anslutningsbehörighet för en IoT-hubb.

    ![konfiguration][3]

    * Som visas i den föregående bilden är någon av principerna *iothubowner* och tjänsten skulle fungera, eftersom båda har anslutningsbehörighet för tjänsten.
    * Du måste ange den nyckel som har lyssna-behörighet för en händelsehubb.
  
    ![behörigheter][4]

    * Som visas i den föregående bilden är någon av principerna **läsa** och **hantera** skulle fungera, eftersom båda har **lyssna** behörighet.

1. Din konsumentgrupp som angetts är inte bara TSI

    Under registreringen av en IoT Hub eller Event Hub, kan du ange konsumentgrupp som ska användas för läsning av data. Konsumentgruppen måste inte delas. Om konsumentgruppen delas underliggande Event Hub automatiskt kopplas från någon av läsarna slumpmässigt. Ange en unik konsumentgrupp för TSI att läsa från.

1. Din Time Series ID-egenskapen angetts vid tidpunkten för etablering är felaktigt, saknas eller är null

    Detta kan inträffa om den **Time Series-ID** egenskapen är felaktigt konfigurerad vid tidpunkten för etablering i miljön. Finns det [bästa praxis för att välja en Time-ID](./time-series-insights-update-how-to-id.md). Just nu, du kan inte uppdatera en befintlig update-miljö för Time Series Insights om du vill använda en annan **Time Series-ID**.

## <a name="problem-some-data-is-shown-but-some-is-missing"></a>Problem: Vissa data visas, men vissa saknas

1. Du kan skicka data utan Time Series-ID

    Det här felet kan uppstå när du skickar händelser utan att tid serien ID-fältet i nyttolasten. Se [stöds JSON-former](./how-to-shape-query-json.md) för mer information.

1. Detta kan inträffa eftersom din miljö har begränsats.

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

Om den **tidsstämpel** egenskapen har inte uttryckligen angetts, vi ska använda en händelse IoT Hub eller Event Hub **Kötid** som standard tidsstämpel.

## <a name="problem-i-cant-edit-or-view-my-time-series-model"></a>Problem: Jag kan inte redigera eller visa min Tidsseriemodell

1. Du kan komma åt en Time Series Insights S1 eller S2-miljö

   Time Series-modeller stöds bara i **PAYG** miljöer. Mer information om hur du kommer åt din S1/S2-miljö från förhandsversionen av Time Series Insights explorer finns i [visualisera data i Utforskaren](./time-series-insights-update-explorer.md).

   ![access][5]

1. Du kanske inte har behörighet att visa och redigera modellen

   Användarna måste ”bidragsgivare” åtkomst på Redigera och visa deras Tidsseriemodell. Du kan kontrollera de aktuella åtkomstnivåerna och bevilja ytterligare åtkomst genom att gå till avsnittet principerna för dataåtkomst på din Time Series Insights-resurs i Azure Portal.

## <a name="problem-all-my-instances-in-time-series-insights-preview-explorer-dont-have-a-parent"></a>Problem: Alla instanser i Time Series Insights (förhandsversion) explorer inte har en överordnad

Detta kan inträffa om din miljö inte har en **Tidsseriemodell** hierarki som har definierats. Se den här artikeln för mer information om [hur du arbetar med Time Series modeller](./time-series-insights-update-how-to-tsm.md).

  ![TSM][6]

## <a name="next-steps"></a>Nästa steg

Läs [hur du arbetar med Time Series modeller](./time-series-insights-update-how-to-tsm.md).

Läs [stöds JSON-former](./how-to-shape-query-json.md).

<!-- Images -->
[1]: media/v2-update-diagnose-and-troubleshoot/environment.png
[2]: media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png
[3]: media/v2-update-diagnose-and-troubleshoot/configuration.png
[4]: media/v2-update-diagnose-and-troubleshoot/permissions.png
[5]: media/v2-update-diagnose-and-troubleshoot/access.png
[6]: media/v2-update-diagnose-and-troubleshoot/tsm.png