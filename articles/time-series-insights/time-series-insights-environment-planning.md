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
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: f73f814321abbb75624ac18c9191c69a99cfe925
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64693579"
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Planera Azure Time Series Insights-miljön

Den här artikeln beskriver hur du planerar din Azure Time Series Insights-miljö utifrån förväntade ingångshändelser och dina krav på datalagring.

## <a name="video"></a>Video

### <a name="in-this-video-we-cover-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>I den här videon beskriver vi Time Series Insights datakvarhållning och hur du planerar för den.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Bästa praxis

Det är bäst att komma igång med Time Series Insights om du vet hur mycket data som du förväntar dig att skicka per minut samt hur länge du behöver lagra dina data.  

Mer information om kapacitet och kvarhållning för båda SKU: er för Time Series Insights finns i [Time Series Insights priser](https://azure.microsoft.com/pricing/details/time-series-insights/).

Överväg följande attribut till bästa plan miljön för långsiktig framgång:

- Lagringskapacitet
- Datakvarhållningstid
- Kapacitet för ingångshändelser
- Forma dina händelser
- Se till att du har referensdata på plats

## <a name="understand-storage-capacity"></a>Förstå lagringskapacitet

Som standard behåller Time Series Insights data baserat på mängden lagringsutrymme som du har etablerat (enheter gånger mängden lagringsutrymme per enhet) och ingående.

## <a name="understand-data-retention"></a>Förstå datakvarhållning

Du kan konfigurera din Time Series Insights-miljö **datalagringstid** inställningen, aktivering av upp till 400 dagars kvarhållning.  Time Series Insights har två lägen, något som optimerats för att se till att din miljö har de mest uppdaterade data (på som standard), och en annan som optimerats för att säkerställa kvarhållning gränser är uppfyllda, där inkommande pausas om den totala lagringskapaciteten för den miljön med samma namn.  Du kan justera kvarhållning och växla mellan de två lägena konfigurationssida för den miljön i Azure-portalen.

Du kan konfigurera upp till 400 dagars kvarhållning av data i din Time Series Insights-miljö.

## <a name="configure-data-retention"></a>Konfigurera datakvarhållning

1. I den [Azure-portalen](https://portal.azure.com), Välj din Time Series Insights-miljö.

2. På den **Time Series Insights-miljö sidan**under den **inställningar** väljer **konfigurera**. 

3. I den **tiden för datakvarhållning (i dagar)** anger ett värde från 1 till 400.

   ![Konfigurera kvarhållning](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>Förstå kapacitet för ingångshändelser

Området kan fokusera på för att planera är kapacitet för ingångshändelser, vilket är en härledning av tilldelningen av per minut. 

En ingressed datapaketet paketstorleken är på 32 KB behandlas som 32 händelser från en begränsning perspektiv, var och en storlek 1 KB. Den maximala tillåtna händelsestorleken är 32 KB; datapaket som är större än 32 KB trunkeras.

I följande tabell sammanfattas kapacitet för ingångshändelser för varje SKU:

|SKU  |Antal händelser Per månad Per enhet  |Händelser storlek Per månad Per enhet  |Antal händelser Per minut Per enhet  | Storlek Per minut Per enhet   |
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

Information om hur du förhindrar begränsning och svarstid finns i [Minimera svarstid och begränsning](time-series-insights-environment-mitigate-latency.md).

## <a name="shaping-your-events"></a>Forma dina händelser

Det är viktigt att se till att sättet du skicka händelser till TSI har stöd för storleken på den miljö som du etablerar (däremot kan du mappa storleken på miljön till hur många händelser som TSI läser och storleken på varje händelse).  På samma sätt är det viktigt att tänka på de attribut som du kanske vill segmentera och filtrera efter vid frågor om dina data.  Med detta i åtanke, föreslår vi att granska den JSON som formar delen av vår [skicka händelser dokumentation](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).  Det är längst ned på sidan.  

## <a name="ensuring-you-have-reference-data-in-place"></a>Se till att du har referensdata på plats

En Referensdatauppsättning är en samling objekt som kan förbättra händelser från din händelsekälla. Tid tidsserieinsikter kopplar varje händelse från din händelsekälla till motsvarande dataraden i referensdatauppsättningen. Den här förhöjda händelsen är sedan tillgängliga för frågor. Den här kopplingen baseras på primärnyckel kolumnerna som definierats i referensdatauppsättningen.

Observera att referensdata är inte ansluten retroaktivt. Det innebär att endast nuvarande och framtida inkommande data är matchade och ansluten till referens datum har angetts, när den har konfigurerats och laddat upp.  Om du planerar att skicka stora mängder historiska data till TSD och inte överför eller skapa referensdata i TSD först, och du kan behöva göra om ditt arbete (tipset, inte roliga).  

Mer information om hur du skapar, ladda upp och hantera dina referensdata i TSD, gå till vår [referensdokumentation datauppsättning](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="business-disaster-recovery"></a>Haveriberedskap för företag

Time Series Insights ger hög tillgänglighet (HA) med hjälp av uppsägningar på nivån Azure-region utan ytterligare plattformsbelastning krävs för lösningen som en Azure-tjänst. Microsoft Azure-plattformen innehåller också funktioner för att hjälpa dig att skapa lösningar med funktioner för katastrofåterställning (DR) eller interregionala tillgänglighet. Om du vill ange globala kan utnyttja interregionala hög tillgänglighet för enheter eller användare, dessa Azure-DR-funktioner. Artikeln [Azure Business Continuity teknisk vägledning](../resiliency/resiliency-technical-guidance.md) beskrivs de inbyggda funktionerna i Azure för affärskontinuitet och Katastrofåterställning. Den [haveriberedskap och hög tillgänglighet för Azure-program](https://docs.microsoft.com/azure/architecture/resiliency/index) dokumentet innehåller vägledning för arkitektur om strategier för Azure-program att uppnå hög tillgänglighet och Katastrofåterställning.

Azure Time Series Insights har inte inbyggda haveriberedskap (BCDR).
Som standard har inbyggd återställning av både Azure IoT Hub och Event Hubs.

Om du vill veta mer om IoT Hub BCDR-principer kan gå [här](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr).  

Om du vill veta mer om Event hub BCDR-principer kan gå [här](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).

Kunder som behöver BCDR kan dock fortfarande att implementera en strategi med följande metod.
Genom att skapa en andra Time Series Insights-miljö i en Azure-region och skicka säkerhetskopieringshändelser till den här sekundära miljön från den primära händelsekällan, använder du en andra dedikerad konsumentgrupp och den händelsekälla BCDR riktlinjer.  

1. Skapa miljö i andra region.  Mer om hur du skapar en Time Series Insights-miljö [här](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Skapa en andra dedikerad konsumentgrupp för din händelsekälla och ansluta den händelsekällan till den nya miljön.  Glöm inte att ange den andra, dedikerad konsumentgruppen.  Du kan läsa mer om detta genom att göra något [dokumentation för IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) eller [dokumentation för Event hubs](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access).
1. Om din primära region skulle gå nedåt under en katastrofåterställning incident, växla över aktiviteter i säkerhetskopiering Time Series Insights-miljön.  

Det är **viktigt att notera** under alla redundansscenario det kan uppstå en fördröjning innan TSI kan starta bearbetning av meddelanden igen: Detta kan orsaka en topp i meddelandebehandling. Mer information ta en titt på [det här dokumentet](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [lägga till en Event Hub-händelsekälla](time-series-insights-how-to-add-an-event-source-eventhub.md) till Time Series Insights.

- Läs om hur du [konfigurera en IoT Hub-händelsekälla](time-series-insights-how-to-add-an-event-source-iothub.md).