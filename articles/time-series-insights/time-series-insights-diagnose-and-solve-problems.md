---
title: Diagnostisera och lösa problem i Azure tid serien insikter | Microsoft Docs
description: Den här artikeln beskriver hur du diagnostisera, felsöka och lösa vanliga problem som kan uppstå i din Azure tid serien Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: venkatja
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.openlocfilehash: dbd32d57206b611a37b5349e5971d2efe272ee1a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292882"
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Diagnostisera och lösa problem i miljön tid serien insikter

## <a name="problem-1-no-data-is-shown"></a>Problem 1: Inga data visas
Det finns flera vanliga orsaker varför du inte kanske se dina data i den [Azure tid serien insikter Explorer](https://insights.timeseries.azure.com):

### <a name="possible-cause-a-event-source-data-is-not-in-json-format"></a>Möjlig orsak A: händelse källdata är inte i JSON-format
Azure tid serien insikter stöder JSON-data. JSON-exempel finns [stöds JSON former](time-series-insights-send-events.md#supported-json-shapes).

### <a name="possible-cause-b-event-source-key-is-missing-a-required-permission"></a>Möjlig orsak B: händelse källa nyckel saknas en nödvändig behörighet
* För en IoT-hubb måste du ange den nyckel som har **tjänsten ansluta** behörighet.

   ![IoT-hubb tjänsten ansluta behörighet](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Som visas i föregående bild, antingen principer **iothubowner** och **service** skulle fungera eftersom båda **tjänsten ansluta** behörighet.
   
* För en händelsehubb, måste du ange den nyckel som har **lyssna** behörighet.

   ![Event hub lyssna behörighet](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Som visas i föregående bild, antingen principer **läsa** och **hantera** skulle fungera eftersom båda **lyssna** behörighet.

### <a name="possible-cause-c-the-consumer-group-provided-is-not-exclusive-to-time-series-insights"></a>Möjlig orsak C: konsumentgruppen tillhandahålls inte är bara tiden serien insikter
Under registreringen av am IoT-hubb eller en händelsehubb kan ange du konsumentgrupp som ska användas för att läsa data. Den här konsumentgrupp måste **inte** delas. Om konsumentgruppen delas underliggande händelsehubben automatiskt kopplas från en läsare slumpmässigt. Ange en unik konsumentgrupp för tid serien insikter att läsa från.

## <a name="problem-2-some-data-is-shown-but-some-is-missing"></a>Problem 2: Vissa data visas, men vissa saknas
När du kan se data delvis, men data som släpar efter, finns det flera möjligheter att tänka på:

### <a name="possible-cause-a-your-environment-is-getting-throttled"></a>Möjlig orsak A: miljön komma begränsas
Detta är ett vanligt problem när miljöer etableras efter skapandet av en händelsekälla med data.  Azure IoT-hubbar och händelser NAV lagra data till sju dagar.  TSD startar alltid från den äldsta händelsen (FIFO) inom händelsekällan.  Så om du har fem miljoner händelser i en händelsekälla när du ansluter till en S1 enskild enhet TSD miljö TSD läser en miljon händelser per dag.  Detta visas ska se ut som om TSD har fem dagar efter fördröjning vid en första titt.  Verkligen det som händer är att miljön har begränsats.  Om du har gamla händelser i dina händelsekälla att närma sig på två sätt:

- Ändra din händelsekälla kvarhållning gränser för att ta bort gamla händelser som du inte vill ska visas i TSD
- Etablera en större miljö (i antal enheter) för att öka genomflödet av gamla händelser.  Miljön ska med exemplet ovan, om du har ökat samma S1 miljö till fem enheter under en dag, missade till nu inom dagen.  Om din stabilt tillstånd Händelsproduktion är 1 miljon eller mindre händelser per dag, kan du minska kapaciteten för händelsen till en enhet när den har fångats.  

Gränsen tillämpas baserat på SKU miljötyp och kapacitet. Alla händelsekällor i miljön dela den här kapaciteten. Om händelsekällan för din IoT-hubb eller händelsehubb är push-överföring av data utöver de tvingande gränserna, ser du begränsning och en fördröjning.

I följande diagram visas en gång serien insikter miljö som har en SKU S1 och en kapacitet på 3. Det kan 3 miljoner ingångshändelser per dag.

![Aktuell miljö artikelnummerkapaciteten](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Anta till exempel att den här miljön är vill föra in meddelanden från en händelsehubb. Observera den ingång hastighet som visas i följande diagram:

![Exempel meddelanden om ingångs-sats för en händelsehubb](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

I diagrammet visas de dagliga ingång frekvensen är ~ 67,000 meddelanden. Denna kurs översätter ungefär 46 meddelanden varje minut. Om hub händelsemeddelande förenklas till en enda gång serien insikter händelse, ser den här miljön ingen begränsning. Om hub händelsemeddelande förenklas till 100 tid serien insikter händelser, ska sedan 4,600 händelser vara inhämtas varje minut. En S1 SKU-miljö som har en kapacitet på 3 kan endast 2 100 ingångshändelser varje minut (1 miljon händelser per dag = 700 händelser per minut på 3 enheter = 2 100 händelser per minut). Därför kan du se en fördröjning på grund av begränsning. 

En översikt över hur förenkla logik fungerar, se [stöds JSON former](time-series-insights-send-events.md#supported-json-shapes).

### <a name="recommended-resolution-steps-for-excessive-throttling"></a>Rekommenderade Lösningssteg för långa begränsning
Om du vill åtgärda fördröjningen ökar du kapaciteten SKU för din miljö. Mer information finns i [så här skalar du tid serien insikter miljön](time-series-insights-how-to-scale-your-environment.md).

### <a name="possible-cause-b-initial-ingestion-of-historical-data-is-causing-slow-ingress"></a>Möjlig orsak B: inledande införandet av historiska data som orsakar långsam ingång
Om du ansluter en befintlig datakälla för händelsen, är det troligt att din IoT-hubb eller event hub redan har data i den. Miljön startar att data från början av den händelsekälla kvarhållningsperiod för meddelandet.

Detta är standardinställningen och inte kan åsidosättas. Du kan engagera begränsning och det kan ta en stund att titta på mata in historiska data.

#### <a name="recommended-resolution-steps-of-large-initial-ingestion"></a>Rekommenderad lösning stegen för stor inledande införandet
Om du vill åtgärda fördröjningen gör du följande:
1. Öka artikelnummerkapaciteten till det högsta tillåtna värdet (10 i det här fallet). När kapaciteten ökas startar processen ingång fånga upp mycket snabbare. Kan du visualisera hur snabbt du Ja via Tillgänglighetsdiagrammet i den [tid serien insikter explorer](https://insights.timeseries.azure.com). Du debiteras för bättre kapacitet.
2. När fördröjningen fångats, minska artikelnummerkapaciteten tillbaka till normal ingång-hastighet.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem 3: Min händelsekällans *tidsstämpel egenskapsnamn* inställningen fungerar inte
Kontrollera att namnet och värdet överensstämmer med följande regler:
* Egenskapsnamnet tidsstämpel är _skiftlägeskänsliga_.
* Egenskapsvärdet tidsstämpel som kommer från din händelsekälla som JSON-strängen ska ha formatet _åååå-MM-ddTHH. FFFFFFFK_. Ett exempel på sådana sträng är ”2008-04-12T12:53Z”.

Det enklaste sättet att se till att din *tidsstämpel egenskapsnamn* avbildas och fungerar korrekt är att använda TSD explorer.  I TSD-explorer med hjälp av diagram, Välj en tidsperiod när du har angett den *tidsstämpel egenskapsnamn*.  Högerklicka på markeringen och välj den *utforska händelser* alternativet.  Första kolumnrubriken ska vara din *tidsstämpel egenskapsnamn* som måste ha en *($ts)* bredvid ordet *tidsstämpel*, i stället:
- *(abc)* , vilket skulle innebära att TSD läser datavärden som strängar
- *Ikon för kalender*, vilket skulle innebära att TSD läser datavärdet som *datetime*
- *#*, vilket skulle innebära att TSD läser datavärdena som ett heltal


## <a name="next-steps"></a>Nästa steg
- Om du behöver ytterligare hjälp kan du starta ett samtal på den [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). 
- Du kan också använda [Azure-supporten](https://azure.microsoft.com/support/options/) för assisterad supportalternativ.
