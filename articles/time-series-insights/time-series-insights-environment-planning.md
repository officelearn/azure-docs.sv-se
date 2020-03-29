---
title: Planera din GA-miljö - Azure Time Series Insights | Microsoft-dokument
description: Metodtips för att förbereda, konfigurera och distribuera din Azure Time Series Insights GA-miljö.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 972bb2a804057037deedb448674abafcc175b21f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314818"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planera din AZURE Time Series Insights GA-miljö

I den här artikeln beskrivs hur du planerar din Azure Time Series Insights -miljö för allmän tillgänglighet (GA) baserat på din förväntade inträngningshastighet och dina datalagringskrav.

## <a name="video"></a>Video

**Titta på det här videoklippet om du vill veta mer om datalagring i Azure Time Series Insights och hur du planerar för det:**<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Bästa praxis

För att komma igång med Azure Time Series Insights är det bäst om du vet hur mycket data du förväntar dig att skicka för minut och hur länge du behöver lagra dina data.  

Mer information om kapacitet och kvarhållning för både Tidsseriestatistik SKU:er finns i [Time Series Insights prissättning](https://azure.microsoft.com/pricing/details/time-series-insights/).

Om du vill planera din Time Series Insights-miljö på bästa sätt bör du tänka på följande attribut:

- [Lagringskapacitet](#storage-capacity)
- [Datakvarhållningstid](#data-retention)
- [Inträngningskapacitet](#ingress-capacity)
- [Forma dina evenemang](#shape-your-events)
- [Se till att du har referensdata på plats](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Lagringskapacitet

Som standard behåller Time Series Insights data baserat på hur mycket lagringsutrymme du etablerar (enheter &#215; mängden lagringsutrymme per enhet) och ingående.

## <a name="data-retention"></a>Datakvarhållning

Du kan ändra **tidsinställningen för datalagring** i azure time series insights-miljön. Du kan aktivera upp till 400 dagars kvarhållning. 

Azure Time Series Insights har två lägen:

* Ett läge optimerar för de senaste data. Den tillämpar en princip för att **rensa gamla data** och lämnar senaste data tillgängliga med instansen. Det här läget är aktiverat som standard. 
* Den andra optimerar data för att ligga under de konfigurerade kvarhållningsgränserna. **Pausa inträngning** förhindrar att nya data inträds när de markeras som **lagringsgränsen överskred beteendet**.

Du kan justera kvarhållning och växla mellan de två lägena på miljöns konfigurationssida i Azure-portalen.

> [!IMPORTANT]
> Du kan konfigurera högst 400 dagars datalagring i din Azure Time Series Insights GA-miljö.

### <a name="configure-data-retention"></a>Konfigurera datakvarhållning

1. Välj din Time Series Insights-miljö i [Azure-portalen.](https://portal.azure.com)

1. Välj **Lagringskonfiguration**under **Inställningar**i **miljöfönstret Tidsseriestatistik.**

1. Ange ett värde mellan 1 och 400 i rutan **Datakvarhållningstid (i dagar).**

   [![Konfigurera kvarhållning](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> Mer information om hur du implementerar en lämplig datalagringsprincip finns i [Så här konfigurerar du kvarhållning](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Inträngningskapacitet

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>Miljöplanering

Det andra området att fokusera på för planering av time series insights-miljön är inträngningskapacitet. Ingresskapacitet är ett derivat av tilldelningen per minut.

Ur ett begränsningsperspektiv behandlas ett ingående datapaket med en paketstorlek på 32 kB som 32 händelser, var och en 1 KB i storlek. Den högsta tillåtna händelsestorleken är 32 KB. Datapaket som är större än 32 kB trunkeras.

Du kan öka kapaciteten för en S1 eller S2 SKU till 10 enheter i en enda miljö. Du kan inte migrera från en S1-miljö till en S2. Du kan inte migrera från en S2-miljö till en S1.

För inträngningskapacitet bestämmer du först den totala inträngningen du behöver per månad. Bestäm sedan vad dina behov per minut är. 

Begränsning och svarstid spelar en roll i kapaciteten per minut. Om du har en topp i datainträngningen som varar mindre än 24 timmar kan Time Series Insights "komma ikapp" med en inträngningshastighet på två gånger så hög som i tabellen föregående.

Om du till exempel har en enda S1 SKU, du inträder data med en hastighet av 720 händelser per minut och datahastigheten ökar i mindre än en timme med en hastighet av 1 440 händelser eller mindre, finns det ingen märkbar svarstid i din miljö. Om du överskrider 1 440 händelser per minut i mer än en timme kommer det dock troligen att uppstå svarstid i data som visualiseras och är tillgängliga för frågor i din miljö.

Du kanske inte vet i förväg hur mycket data du förväntar dig att driva. I det här fallet kan du hitta datatelemetri för [Azure IoT Hub](../iot-hub/iot-hub-metrics.md) och [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) i din Azure portal-prenumeration. Telemetrin kan hjälpa dig att avgöra hur du etablerar din miljö. Använd **fönstret Mått** i Azure-portalen för respektive händelsekälla för att visa dess telemetri. Om du förstår dina mått på händelsekällan kan du mer effektivt planera och etablera din Time Series Insights-miljö.

### <a name="calculate-ingress-requirements"></a>Beräkna ingående krav

Så här beräknar du ingresskraven:

- Kontrollera att din inträngningskapacitet är högre än din genomsnittliga minutfrekvens och att din miljö är tillräckligt stor för att hantera din förväntade inträngning motsvarande två gånger din kapacitet under mindre än en timme.

- Om ingående toppar inträffar som varar längre än 1 timme använder du topphastigheten som ditt genomsnitt. Etablera en miljö med kapacitet att hantera topphastigheten.

### <a name="mitigate-throttling-and-latency"></a>Minska begränsning och svarstid

Information om hur du förhindrar begränsning och svarstid finns i [Minska svarstid och begränsning](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Forma dina händelser

Det är viktigt att se till att sättet du skickar händelser till Time Series Insights stöder storleken på den miljö du etablerar. (Omvänt kan du mappa miljöns storlek till hur många händelser Time Series Insights läser och storleken på varje händelse.) Det är också viktigt att tänka på de attribut som du kanske vill använda för att segmentera och filtrera efter när du frågar dina data.

> [!TIP]
> Granska JSON-formningsdokumentationen i [Skicka händelser](time-series-insights-send-events.md).

## <a name="ensure-that-you-have-reference-data"></a>Se till att du har referensdata

En *referensdatauppsättning* är en samling objekt som utökar händelserna från händelsekällan. Ingress-motorn Time Series Insights ansluter till varje händelse från händelsekällan med motsvarande datarad i referensdatauppsättningen. Den utökade händelsen är sedan tillgänglig för fråga. Kopplingen baseras på kolumnerna **primärnyckel** som definieras i referensdatauppsättningen.

> [!NOTE]
> Referensdata är inte sammanfogade retroaktivt. Endast aktuella och framtida ingående data matchas och sammanfogas till referensdatauppsättningen när den har konfigurerats och överförts. Om du planerar att skicka en stor mängd historiska data till Time Series Insights och inte först ladda upp eller skapa referensdata i Time Series Insights kan du behöva göra om ditt arbete (tips: inte roligt).  

Om du vill veta mer om hur du skapar, laddar upp och hanterar dina referensdata i Time Series Insights läser du [dokumentationen för referensdatauppsättning](time-series-insights-add-reference-data-set.md).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Nästa steg

- Kom igång genom att skapa [en ny Time Series Insights-miljö i Azure-portalen](time-series-insights-get-started.md).

- Lär dig hur du [lägger till en händelsehubbarshändelsekälla](time-series-insights-how-to-add-an-event-source-eventhub.md) i Time Series Insights.

- Läs om hur du [konfigurerar en IoT Hub-händelsekälla](time-series-insights-how-to-add-an-event-source-iothub.md).
