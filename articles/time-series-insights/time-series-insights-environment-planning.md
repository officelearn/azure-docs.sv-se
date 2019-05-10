---
title: Planera omfattningen av din Azure Time Series Insights-miljö | Microsoft Docs
description: Den här artikeln beskriver hur du följa bästa praxis när du planerar en Azure Time Series Insights-miljö, inklusive lagringskapacitet, datalagring, kapacitet för ingångshändelser, övervakning och haveriberedskap (BCDR) för företag.
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: bf1f570319370fab99e2f52086bc81df259e3d35
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236531"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planera Azure Time Series Insights GA-miljön

Den här artikeln beskriver hur du planerar din miljö för allmän tillgänglighet (GA) av Azure Time Series Insights baserat på förväntade ingångshändelser och dina krav på datalagring.

## <a name="video"></a>Video

### <a name="learn-more-about-data-retention-in-azuretime-series-insights-and-how-to-plan-for-itbr"></a>Läs mer om kvarhållning av data i AzureTime Series Insights och hur du planerar för den.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Regelverk

Det är bäst att komma igång med Time Series Insights om du vet hur mycket data som du förväntar dig att skicka per minut samt hur länge du behöver lagra dina data.  

Mer information om kapacitet och kvarhållning för båda SKU: er för Time Series Insights finns i [Time Series Insights priser](https://azure.microsoft.com/pricing/details/time-series-insights/).

Överväg följande attribut till bästa plan miljön för långsiktig framgång:

- <a href="#understand-storage-capacity">Lagringskapacitet</a>
- <a href="#understand-data-retention">Datakvarhållningstid</a>
- <a href="#understand-ingress-capacity">Kapacitet för ingångshändelser</a>
- <a href="#shape-your-events">Forma dina händelser</a>
- <a href="#ensure-you-have-reference-data">Se till att du har referensdata på plats</a>

## <a name="understand-storage-capacity"></a>Förstå lagringskapacitet

Som standard behåller Azure Time Series Insights data baserat på mängden lagringsutrymme som du har etablerat (enheter gånger mängden lagringsutrymme per enhet) och ingående.

## <a name="understand-data-retention"></a>Förstå datakvarhållning

Du kan konfigurera din Time Series Insights-miljö **datalagringstid** inställningen, aktivering av upp till 400 dagars kvarhållning. Time Series Insights har två lägen, något som optimerats för att se till att din miljö har de mest uppdaterade data (på som standard), och en annan som optimerats för att säkerställa kvarhållning gränser är uppfyllda, där inkommande pausas om den totala lagringskapaciteten för den miljön med samma namn.  Du kan justera kvarhållning och växla mellan de två lägena konfigurationssida för den miljön i Azure-portalen.

Du kan konfigurera upp till 400 dagars kvarhållning av data i din Time Series Insights-miljö.

### <a name="configure-data-retention"></a>Konfigurera datakvarhållning

1. I den [Azure-portalen](https://portal.azure.com), Välj din Time Series Insights-miljö.

1. På den **Time Series Insights-miljö sidan**under den **inställningar** väljer **konfigurera**.

1. I den **tiden för datakvarhållning (i dagar)** anger ett värde från 1 till 400.

   [![Konfigurera kvarhållning av säkerhetskopior](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Läs mer om hur du implementerar en bevarandeprincip för lämpliga data genom att granska [konfigurera kvarhållning](./time-series-insights-how-to-configure-retention.md).

## <a name="understand-ingress-capacity"></a>Förstå kapacitet för ingångshändelser

Området kan fokusera på för att planera är kapacitet för ingångshändelser, vilket är en härledning av tilldelningen av per minut.

En ingressed datapaketet paketstorleken är på 32 KB behandlas som 32 händelser från en begränsning perspektiv, var och en storlek 1 KB. Den maximala tillåtna händelsestorleken är 32 KB; datapaket som är större än 32 KB trunkeras.

I följande tabell sammanfattas kapacitet för ingångshändelser för varje SKU:

|SKU  |Antal händelser / månad / enhet  |Händelser storlek / månad / enhet  |Händelser räknas / minut / enhet  | Storlek per minut / enhet   |
|---------|---------|---------|---------|---------|
|S1     |   30 miljoner     |  30 GB     |  720    |  720 KB   |
|S2     |   300 miljoner    |   300 GB   | 7,200   | 7 200 KB  |

Du kan öka kapaciteten för en S1 eller S2 SKU till 10 enheter i en enda miljö. Du kan inte migrera från en S1-miljö till en S2 eller från en S2-miljö till en S1.

För kapacitet för ingångshändelser, bör du först avgöra totalt inkommande du behöver på basis av per månad. Därefter fastställer du vilka dina per minut behov är, eftersom det är där begränsning och svarstid spela en roll.

Om du har en topp i din inkommande data som varar mindre än 24 timmar kan Time Series Insights ”missade” med en ingående hastighet på 2 x listade priserna ovan.

Till exempel om du har en enkel S1 SKU och inkommande data med en hastighet av 720 händelser per minut och topp i mindre än 1 timme med en hastighet av 1440 händelser eller mindre, skulle det finnas utan märkbar fördröjning till din miljö. Om du överskrider 1440 händelser per minut under mer än en timme, skulle du dock förmodligen uppleva fördröjning till data som är visualiserade och tillgängliga för frågor i din miljö.

Du kanske inte vet i förväg hur mycket data du förväntar dig att skicka. I det här fallet kan du hitta data telemetri för [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) och [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) i din Azure-portalen. Den här telemetrin kan hjälpa dig att avgöra hur du etablerar din miljö. Använd den **mått** sida i Azure-portalen för respektive händelsekällan för att se dess telemetri. Om du förstår din mått för datakällan av händelsen du mer effektivt planera och etablera din Time Series Insights-miljö.

### <a name="calculate-ingress-requirements"></a>Beräkna ingress-krav

- Bekräfta din kapacitet för ingångshändelser är över din genomsnittliga priset per minut och att din miljö är tillräckligt stor för att hantera dina förväntade ingångshändelser motsvarar 2 x din kapacitet för mindre än 1 timme.

- Om inkommande toppar uppstår som senast under längre tid än en timme, använda den topp-hastigheten som din medelvärde och etablera en miljö med kapacitet för att behandla insamling.

### <a name="mitigate-throttling-and-latency"></a>Minimera begränsningar och svarstid

Information om hur du förhindrar begränsning och fördröjning, Läs om hur du [Minimera svarstid och begränsning](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Forma dina händelser

Det är viktigt att se till att sättet du skicka händelser till TSI har stöd för storleken på den miljö som du etablerar (däremot kan du mappa storleken på miljön till hur många händelser som TSI läser och storleken på varje händelse). På samma sätt är det viktigt att tänka på de attribut som du kanske vill segmentera och filtrera efter vid frågor om dina data.

> [!TIP]
> Granska JSON forma dokumentation i [skicka händelser](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-you-have-reference-data"></a>Kontrollera att du har referensdata

En **Referensdatauppsättning** är en samling objekt som kan förbättra händelser från din händelsekälla. Tid tidsserieinsikter kopplar varje händelse från din händelsekälla till motsvarande dataraden i referensdatauppsättningen. Den här förhöjda händelsen är sedan tillgängliga för frågor. Den här kopplingen baseras på primärnyckel kolumnerna som definierats i referensdatauppsättningen.

Observera att referensdata är inte ansluten retroaktivt. Det innebär att endast nuvarande och framtida inkommande data är matchade och ansluten till referens datum har angetts, när den har konfigurerats och laddat upp.  Om du planerar att skicka stora mängder historiska data till TSD och inte överför eller skapa referensdata i TSD först, och du kan behöva göra om ditt arbete (tipset, inte roliga).  

Mer information om hur du skapar, ladda upp och hantera dina referensdata i TSD, gå till vår [referensdokumentation datauppsättning](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Nästa steg

- Kom igång genom att skapa [en ny Time Series Insights-miljö i Azure-portalen](time-series-insights-get-started.md).

- Lär dig hur du [lägga till en Event Hub-händelsekälla](time-series-insights-how-to-add-an-event-source-eventhub.md) till Time Series Insights.

- Läs om hur du [konfigurera en IoT Hub-händelsekälla](time-series-insights-how-to-add-an-event-source-iothub.md).
