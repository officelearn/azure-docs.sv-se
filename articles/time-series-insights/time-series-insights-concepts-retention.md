---
title: Förstå datakvarhållning i Azure Time Series Insights-miljön | Microsoft Docs
description: Den här artikeln beskrivs två inställningar som styr datalagring i din Azure Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.custom: seodec18
ms.openlocfilehash: c44b09e15a227e11426d2798fc071778ca47ebd3
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557471"
---
# <a name="understand-data-retention-in-time-series-insights"></a>Förstå datakvarhållning i Time Series Insights

Den här artikeln beskrivs två inställningar som påverkar datalagring i miljön för Time Series Insights (TSI).

## <a name="video"></a>Video: 

### <a name="in-this-video-we-cover-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>I den här videon beskriver vi Time Series Insights datakvarhållning och hur du planerar för den.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Alla TSI-miljöer har en inställning som styr **datalagringstid**. Värdet som sträcker sig från 1 och 400 dagar. Data tas bort baserat på miljön storage kvarhållning eller kapacitet (1 400), beroende på vilket som inträffar först.

Alla TSI-miljöer har en ytterligare inställning **lagringsgräns överskred beteende**. Den här inställningen styr beteendet för ingående och rensa när maxkapacitet för en miljö har uppnåtts. Det finns två beteenden att välja mellan:
- **Radera gamla data** (standard)  
- **Pausa ingress**

> [!NOTE]
> Som standard när du skapar en ny miljö, kvarhållning är konfigurerad för att **Radera gamla data**. Den här inställningen kan du ange vid behov efter Skapandetid med hjälp av Azure-portalen på den **konfigurera** sidan i TSI-miljö.

Information om hur du växlar kvarhållning beteenden finns [konfigurera kvarhållning i Time Series Insights](time-series-insights-how-to-configure-retention.md).

Jämför beteende för kvarhållning av data:

## <a name="purge-old-data"></a>Radera gamla data
- Detta är standardbeteendet för TSI-miljöer och utställningarna samma beteende TSI miljöer har en eftersom den lanserades som offentlig Preview.  
- Det här beteendet är att föredra när användare vill alltid se deras *senaste data* i sin TSI-miljö. 
- Det här beteendet *rensar* data en gång i miljön (kvarhållningstid, ändra storlek på eller count, beroende på vilket som inträffar först) har nått. Kvarhållning av säkerhetskopior är inställd på 30 dagar som standard. 
- De äldsta inmatade data tas bort först (FIFO-metoden).

### <a name="example-1"></a>Exempel 1:
Överväg en exempelmiljö med kvarhållning beteende **fortsätta ingångshändelser och radera gamla data**: I det här exemplet **datalagringstid** är inställd på 400 dagar. **Kapacitet** är inställd på S1-enhet som innehåller 30 GB total kapacitet.   Anta att inkommande data ackumuleras 500 MB per dag i genomsnitt. Den här miljön kan bara behålla 60 dagar med data som ges mängden inkommande data, eftersom maximala kapacitet har nåtts på 60 dagar. Inkommande data ackumuleras som: 500 MB för varje dag x 60 dagar = 30 GB. 

I det här exemplet 61st dag, miljön visar dina nyaste data, men det tar bort de äldsta data äldre än 60 dagar. Ingen rensning av databasen blir utrymme för den nya data som strömmas i, så att nya data kan fortsätta att undersökas. 

Om användare vill bevara data längre kan öka storleken på miljön genom att lägga till ytterligare enheter eller skicka mindre data.  

### <a name="example-2"></a>Exempel 2:
Överväg en miljö även är konfigurerat kvarhållning beteende **fortsätta ingångshändelser och radera gamla data**. I det här exemplet **datalagringstid** anges till ett lägre värde på 180 dagar. **Kapacitet** är inställd på S1-enhet som innehåller 30 GB total kapacitet. För att lagra data för fullständig 180 dagar, får inte den dagliga ingångshändelser överskrida 0.166 GB (166 MB) per dag.  

När den här miljön dagliga ingångshändelser överskrider 0.166 GB per dag, kan inte lagras data i 180 dagar, eftersom vissa data hämtar rensas. Överväg det här samma miljö under en upptagen tidsperiod. Anta att den miljön ingress rate kan öka till en genomsnittlig 0.189 GB per dag. I den upptagna tidsramen håller 158 dagars data bevaras (30GB/0.189 = 158,73 dagars kvarhållning). Nu är mindre än tid för kvarhållning av önskade data.

## <a name="pause-ingress"></a>Pausa ingress
- Det här beteendet är utformat för att säkerställa att data inte tas bort om de storlek och antal har nått innan deras kvarhållningsperiod.  
- Det här beteendet ger extra tid för användare att öka kapaciteten för sin miljö innan data tas bort på grund av brott mot lagringstid på
- Detta hjälper dig att skydda mot dataförlust, men skapar en möjlighet för förlusten av dina senaste data om ingående pausas utöver kvarhållningsperioden för din händelsekälla.
- Men när en miljö maximala kapacitet har nåtts pausas miljön inkommande data tills ytterligare åtgärder utförs: 
   - Du kan öka maximal kapacitet för den miljön. Mer information finns i [så här skalar du din Time Series Insights-miljö](time-series-insights-how-to-scale-your-environment.md) att lägga till fler skalningsenheter.
   - Kvarhållningsperioden för data har nåtts och data tas bort, så att miljön nedan sin maximala kapacitet.

### <a name="example-3"></a>Exempel 3:
Beakta en miljö med kvarhållning funktionen har konfigurerats att **pausa ingående**. I det här exemplet på **Datalagringsperiod** konfigureras till 60 dagar. **Kapacitet** är inställd på 3 enheter av S1. Anta att den här miljön har ingångshändelser på 2 GB data per dag. I den här miljön pausas ingående när den maximala kapaciteten har nåtts. Då visar miljön samma datamängd tills ingående återupptar eller tills ”Fortsätt ingående” är aktiverad (som skulle ta bort äldre data för att göra plats för nya data). 

Ingående återupptas när:
- Data flödar i den ordning som det togs emot av händelsekälla
- Händelserna indexeras baserat på deras tidsstämpel, såvida inte du har överskridit bevarandeprinciper på din händelsekälla. Mer information om konfigurationen för kvarhållning av händelse källa, [Event Hubs vanliga frågor och svar](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Du bör ställa in aviseringar för att informera om för att undvika att ingående paus. Dataförlust kan inträffa eftersom Standardkvarhållning är 1 dag för Azure händelsekällor. Därför när inkommande pausas förlorar sannolikt du de senaste data om inte ytterligare vidtas. Du måste öka kapaciteten, eller växla beteende för att **Radera gamla data** att undvika potentiella dataförlusten.

I den berörda Event Hubs, bör du justera den **meddelandelagring** egenskapen att minimera dataförlust när pausa ingående uppstår i Time Series Insights.

![Meddelandelagring för Event hub.](media/time-series-insights-contepts-retention/event-hub-retention.png)

Om inga egenskaper har konfigurerats på händelsekällan (timeStampPropertyName), TSI som standard är tidsstämpeln för ankomst till händelsehubben som x-axeln. Om timeStampPropertyName är konfigurerad för att vara något annat, efter miljön den konfigurerade timeStampPropertyName i datapaketet när händelser parsas. 

Om du behöver kan du utöka din miljö till hantera ytterligare kapacitet eller att öka lång kvarhållning, se [så här skalar du din Time Series Insights-miljö](time-series-insights-how-to-scale-your-environment.md) för mer information.  

## <a name="next-steps"></a>Nästa steg
Information om hur du växlar kvarhållning beteende finns [konfigurera kvarhållning i Time Series Insights](time-series-insights-how-to-configure-retention.md).
