---
title: Planera omfattningen av din Azure Time Series Insights-miljö | Microsoft Docs
description: Den här artikeln beskriver hur du följa bästa praxis när du planerar en Azure Time Series Insights-miljö. Områden som omfattas är lagringskapacitet, datalagring, kapacitet för ingångshändelser, övervakning och företag affärskontinuitet och haveriberedskap recovery (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 9d9f9b7fe7aafa927f023e1d4e30e079a26f7fab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431049"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planera Azure Time Series Insights GA-miljön

Den här artikeln beskriver hur du planerar din miljö för allmän tillgänglighet (GA) av Azure Time Series Insights baserat på förväntade ingångshändelser och dina krav på datalagring.

## <a name="video"></a>Video

**Den här videon om du vill veta mer om kvarhållning av data i Azure Time Series Insights och hur du planerar för den**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Bästa praxis

Det är bäst att komma igång med Time Series Insights om du vet hur mycket data som du förväntar dig att skicka genom minut och hur länge du behöver lagra dina data.  

Mer information om kapacitet och kvarhållning för båda SKU: er för Time Series Insights finns i [Time Series Insights priser](https://azure.microsoft.com/pricing/details/time-series-insights/).

Du bäst planerar din Time Series Insights-miljö för långsiktig framgång genom att tänka på följande attribut:

- <a href="#storage-capacity">Lagringskapacitet</a>
- <a href="#data-retention">Datakvarhållningstid</a>
- <a href="#ingress-capacity">Kapacitet för ingångshändelser</a>
- <a href="#shape-your-events">Forma dina händelser</a>
- <a href="#ensure-that-you-have-reference-data">Se till att du har referensdata på plats</a>

## <a name="storage-capacity"></a>Lagringskapacitet

Som standard Time Series Insights behåller data baserat på mängden lagringsutrymme som du etablerar (enheter &#215; lagringsutrymme per enhet) och ingångsanspråk.

## <a name="data-retention"></a>Datakvarhållning

Du kan ändra den **datalagringstid** i Time Series Insights-miljön. Du kan aktivera upp till 400 dagars kvarhållning. 

Time Series Insights har två lägen. Ett läge optimerar för att säkerställa att din miljö har den senaste informationen. Det här läget aktiveras som standard. 

Det andra läget optimerar för att säkerställa att gräns för datakvarhållning är uppfyllda. I det andra läget pausas ingående om den totala lagringskapaciteten för miljön är uppfyllt. 

Du kan justera kvarhållning och växla mellan de två lägena på konfigurationssidan för den miljön i Azure-portalen.

Du kan konfigurera upp till 400 dagars kvarhållning av data i din Time Series Insights-miljö.

### <a name="configure-data-retention"></a>Konfigurera datakvarhållning

1. I den [Azure-portalen](https://portal.azure.com), Välj din Time Series Insights-miljö.

1. I den **Time Series Insights-miljö** fönstret under **inställningar**väljer **konfigurera**.

1. I den **tiden för datakvarhållning (i dagar)** , ange ett värde mellan 1 och 400.

   [![Konfigurera kvarhållning av säkerhetskopior](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Läs mer om hur du implementerar en lämplig policyn för datalagring i [konfigurera kvarhållning](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Kapacitet för ingångshändelser

Det andra området kan fokusera på för att planera din Time Series Insights-miljö är kapacitet för ingångshändelser. Kapacitet för ingångshändelser är en härledning av tilldelningen av per minut.

Från en begränsning perspektiv behandlas ett ingressed datapaket som har ett paket på 32 KB som 32 händelser, varje 1 KB stora. Den maximala tillåtna händelsestorleken är 32 KB. Datapaket som är större än 32 KB trunkeras.

I följande tabell sammanfattas ingress-kapacitet per enhet för varje Time Series Insights-SKU:

|SKU  |Händelseantal per månad  |Händelsestorleken per månad  |Händelseantal per minut  |Händelsestorleken per minut  |
|---------|---------|---------|---------|---------|
|S1     |   30 miljoner     |  30 GB     |  720    |  720 KB   |
|S2     |   300 miljoner    |   300 GB   | 7,200   | 7 200 KB  |

Du kan öka kapaciteten för en S1 eller S2 SKU till 10 enheter i en enda miljö. Du kan inte migrera från en S1-miljö till en S2. Du kan inte migrera från en S2-miljö till en S1.

Kapacitet för ingångshändelser först bestämma totalt inkommande du behöver på basis av per månad. Därefter fastställer vad din per minut behoven är. 

Spela upp en roll i per minut kapacitet begränsning och svarstid. Om du har en topp i din inkommande som varar mindre än 24 timmar kan Time Series Insights ”fånga upp” med en ingående hastighet för två gånger de priserna i tabellen ovan.

Till exempel om du har en enkel S1 SKU du inkommande data med en hastighet av 720 händelser per minut, och datahastighet toppar i mindre än en timme med en hastighet 1 440 händelser eller mindre, finns det inga märkbar svarstid i din miljö. Om du överskrider 1 440 händelser per minut under mer än en timme, kommer du troligen Upplev svarstid i data som är visualiserade och tillgängliga för frågor i din miljö.

Du kanske inte vet i förväg hur mycket data du förväntar dig att skicka. I det här fallet kan du hitta data telemetri för [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) och [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) i Azure portal-prenumerationen. Telemetri som kan hjälpa dig att avgöra hur du etablerar din miljö. Använd den **mått** fönstret i Azure-portalen för respektive händelsekällan för att se dess telemetri. Om du förstår din mått för datakällan av händelsen du mer effektivt planera och etablera din Time Series Insights-miljö.

### <a name="calculate-ingress-requirements"></a>Beräkna ingress-krav

Att beräkna dina ingress-krav:

- Kontrollera att din kapacitet för ingångshändelser befinner sig ovanför din genomsnittliga priset per minut och att din miljö är tillräckligt stor för att hantera dina förväntade ingångshändelser som motsvarar två gånger din kapacitet för mindre än en timme.

- Om inkommande toppar inträffa som senast längre än 1 timme, använda topp-hastighet som din medelvärde. Etablera en miljö med kapacitet för att behandla insamling.

### <a name="mitigate-throttling-and-latency"></a>Minimera begränsningar och svarstid

Information om hur du förhindrar begränsning och svarstid finns i [Minimera svarstid och begränsning](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Forma dina händelser

Det är viktigt att se till att det sätt som du skickar händelser till Time Series Insights har stöd för storleken på den miljö som du etablerar. (Däremot kan du mappa storleken på miljön till hur många händelser som Time Series Insights läser och storleken på varje händelse.) Det är också viktigt att tänka på de attribut som du kanske vill använda för att ta ett utsnitt och filtrera efter när du fråga data.

> [!TIP]
> Granska JSON forma dokumentation i [skicka händelser](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-that-you-have-reference-data"></a>Kontrollera att du har referensdata

En *refererar till datamängden* är en samling objekt som kan förbättra händelser från din händelsekälla. Tid tidsserieinsikter kopplar varje händelse från din händelsekälla till motsvarande datarad i din referens-datauppsättning. Förhöjda händelsen är sedan tillgängliga för frågor. Kopplingen baseras på den **primärnyckel** kolumner som har definierats i din referens-datauppsättning.

> [!NOTE]
> Referensdata inte är ansluten retroaktivt. Endast nuvarande och framtida inkommande data är matchade och ansluten till datamängden som referens när den har konfigurerats och laddat upp. Om du planerar att skicka en stor del av historiska data till Time Series Insights och inte första överföring eller skapa referensdata i Time Series Insights kan du behöva gör om ditt arbete (tips: du kommer inte).  

Läs mer om hur du skapar, ladda upp och hantera dina referensdata i Time Series Insights i vår [referensdokumentation datauppsättning](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Nästa steg

- Kom igång genom att skapa [en ny Time Series Insights-miljö i Azure-portalen](time-series-insights-get-started.md).

- Lär dig hur du [lägga till en händelsekälla för Event Hubs](time-series-insights-how-to-add-an-event-source-eventhub.md) till Time Series Insights.

- Läs om hur du [konfigurera en IoT Hub-händelsekälla](time-series-insights-how-to-add-an-event-source-iothub.md).
