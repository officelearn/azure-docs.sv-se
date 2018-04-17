---
title: Planera skalan för din Azure tid serien Insights-miljö | Microsoft Docs
description: Den här artikeln beskriver hur du följer metodtipsen när du planerar en Azure tid serien Insights miljö, inklusive lagringskapacitet, datalagring, ingång kapacitet och övervakning.
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 991db58db1bb07f338c0f80aa4db69ddb868dcab
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Planera din Azure tid serien Insights-miljö

Den här artikeln beskriver hur du planerar din Azure tid serien Insights miljö baserat på förväntade meddelanden om ingångs-hastighet och dina krav på datalagring.

## <a name="best-practices"></a>Bästa praxis

Om du vill komma igång med tiden serien insikter, är det bäst om du känner till hur mycket data som du förväntar dig att skicka per minut samt hur länge du behöver lagra data.  

Mer information om kapacitet och lagring för båda SKU: er för tid serien insikter finns [tid serien insikter priser](https://azure.microsoft.com/pricing/details/time-series-insights/).

Överväg följande attribut till bästa plan miljön för långsiktig framgång: 
- Lagringskapacitet
- Datakvarhållningstid
- Ingång kapacitet 
- Shaping händelserna
- Se till att du har referensdata på plats

## <a name="understand-storage-capacity"></a>Förstå lagringskapacitet
Som standard behåller tid serien insikter data baserat på hur mycket lagringsutrymme som du har etablerat (enheter gånger mängden lagringsutrymme per enhet) och ingångsanspråk.

## <a name="understand-data-retention"></a>Förstå datakvarhållning
Du kan konfigurera din miljö tid serien insikter **datalagringstid** inställningen att aktivera upp till 400 dagar kvarhållning.  Tid serien insikter har två lägen, som optimerar för din miljö har den senaste informationen (på som standard), och en annan som optimerats för att säkerställa kvarhållning gränser är uppfyllda, där ingång pausas om den övergripande lagringskapaciteten för den miljö med samma namn.  Du kan justera kvarhållning och växla mellan lägena i den miljö konfigurationssidan i Azure-portalen.

Du kan konfigurera högst 400 dagar för datalagring i miljön tid serien insikter.

## <a name="configure-data-retention"></a>Konfigurera datakvarhållning

1. I den [Azure-portalen](https://portal.azure.com), Välj tid serien insikter miljön.

2. På den **tid serien insikter miljö sidan**under den **inställningar** rubrik, Välj **konfigurera**. 

3. I den **datalagringstid (i dagar)** ange ett värde från 1 till 400.

   ![Konfigurera kvarhållning](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>Förstå meddelanden om ingångs-kapacitet

Andra området för att fokusera på för att planera är ingång kapacitet som är en härledning av fördelningen per minut. 

Bandbreddsbegränsning ur ett ingressed datapaketet paketstorleken på 32 KB behandlas som 32-händelser, varje storlek 1 KB. Den maximala tillåtna händelsestorleken är 32 KB; datapaket som är större än 32 KB trunkeras.

I följande tabell sammanfattas ingång kapacitet för varje SKU:

|SKU  |Antal händelser Per månad, Per enhet  |Händelser storleken Per månad, Per enhet  |Antal händelser Per minut Per enhet  | Storleken Per minut Per enhet   |
|---------|---------|---------|---------|---------|
|S1     |   30 miljoner     |  30 GB     |  700    |  700 KB   |
|S2     |   300 miljoner    |   300 GB   | 7,000   | 7 000 KB  |

Du kan öka kapaciteten för ett S1 eller S2 SKU till 10 enheter i en enda miljö. Du kan inte migrera från en S1-miljö till en S2 eller från en S2 miljö till ett S1. 

För ingång kapacitet bör du fastställa den totala ingång som du behöver på grundval av per månad. Därefter bestämmer vad din per minut behov, eftersom det är där begränsning och svarstid spelar roll.

Om du har en topp i dina data ingång varar mindre än 24 timmar kan tid serien insikter ”missade” i en ingång frekvens 2 x de listade priser ovan. 

Till exempel om du har en enda S1 SKU och ingång data med en hastighet på 700 händelser per minut och insamling för mindre än 1 timme med en hastighet 1400 händelser eller mindre skulle det vara någon märkbar fördröjning till din miljö. Men om du överskrider 1400 händelser per minut för mer än en timme, skulle förmodligen uppstår fördröjning till data som är visualiserade och tillgängliga för frågor i din miljö. 

Du kan inte vet i förväg hur mycket data du förväntar dig att skicka. I det här fallet kan du hitta data telemetri för [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) och [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) i Azure-portalen. Den här telemetri kan hjälpa dig att bestämma hur du etablerar din miljö. Använd den **mått** sida i Azure-portalen att visa dess telemetri respektive händelsekällan. Om du känner till ditt händelse källa mått du effektivare planera och etablera tid serien insikter miljön.

### <a name="calculate-ingress-requirements"></a>Beräkna ingång krav

- Bekräfta din ingång kapacitet är högre än din genomsnittligt per minut och att din miljö är tillräckligt stor för att hantera din förväntade ingång motsvarar 2 x din kapacitet för mindre än 1 timme.

- Om ingång toppar uppstår som senast längre än 1 timme, använda topp-hastighet som din genomsnittlig och tillhandahålla en miljö med kapacitet för att behandla insamling.
 
### <a name="mitigate-throttling-and-latency"></a>Minimera begränsning och svarstid

Information om hur du hindrar begränsning och svarstid finns [minimera svarstiden och begränsning](time-series-insights-environment-mitigate-latency.md). 

## <a name="shaping-your-events"></a>Shaping händelserna
Det är viktigt att kontrollera hur du skickar händelser till TSD stöds av storleken på den miljö du etablerar (däremot kan du mappa miljön till hur många händelser som TSD läser och storlek för varje händelse).  På samma sätt är det viktigt att tänka på de attribut som du kanske vill dela och filtrera efter när du frågar dina data.  Med detta i åtanke, föreslår vi granska JSON shaping avsnitt i vår *skicka händelser* dokumentation [dokumentation] (https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-send-events).  Det är längst ned på sidan.  

## <a name="ensuring-you-have-reference-data-in-place"></a>Se till att du har referensdata på plats
En referens datauppsättning är en samling objekt som utökar händelser från din händelsekälla. Tid serien insikter ingång motorn kopplar varje händelse från din händelsekällan med motsvarande dataraden i datauppsättningen referens. Den här förhöjda händelsen är sedan tillgängliga för frågor. Den här kopplingen baseras på primärnyckeln kolumnerna som definierats i datauppsättningen referens.

Observera att referensdata retroaktivt inte är ansluten. Detta innebär att endast aktuella och framtida ingång data är matchade och ansluten till uppsättningen med referens datum när den har konfigurerats och har överförts.  Om du planerar att skicka stora mängder historisk data till TSD och inte ladda upp eller skapa referensdata i TSD första, och du kan behöva nytt göra arbetet (tipset, inte roliga).  

Mer information om hur du skapar, ladda upp och hantera dina referensdata i TSD, gå till vår *referensdata* dokumentation [dokumentation] (https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-add-reference-data-set).


## <a name="next-steps"></a>Nästa steg
- [Hur du lägger till en Händelsehubb händelsekälla](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [Hur du lägger till en IoT-hubb händelsekälla](time-series-insights-how-to-add-an-event-source-iothub.md)
