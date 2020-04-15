---
title: Förstå datalagring i din miljö - Azure Time Series Insight | Microsoft-dokument
description: I den här artikeln beskrivs två inställningar som styr datalagring i azure time series insights-miljön.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: fd34595d5ea942602efc920904ff326fc203c088
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380694"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Förstå datalagring i Azure Time Series Insights

I den här artikeln beskrivs två primära inställningar som påverkar datalagring i azure time series insights-miljön.

## <a name="video"></a>Video

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Följande video sammanfattar Tidsseriestatistik datalagring och hur man planerar för det.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Var och en av dina Azure Time Series Insights-miljöer har en inställning som styr **datalagringstiden**. Värdet sträcker sig från 1 till 400 dagar. Data raderas baserat på miljöns lagringskapacitet eller lagringstid, beroende på vilket som inträffar först.

Dessutom har din Azure Time Series Insights-miljö en **lagringsgräns som överskridits** beteendeinställningen. Den styr inträngning och rensa beteende när den maximala kapaciteten för en miljö nås. Det finns två beteenden att välja mellan när du konfigurerar det:

- **Rensa gamla data** (standard)  
- **Pausa inträngning**

> [!NOTE]
> När du skapar en ny miljö är lagringen som standard konfigurerad för **att rensa gamla data**. Den här inställningen kan växlas efter behov efter skapandetid med hjälp av Azure-portalen, på sidan **Konfigurera** i time series insights-miljön.
> * Information om hur du konfigurerar bevarandeprinciper finns [i Konfigurera kvarhållning i Time Series Insights](time-series-insights-how-to-configure-retention.md).

Båda principerna för datalagring beskrivs mer i detalj nedan.

## <a name="purge-old-data"></a>Rensa gamla data

- **Rensa gamla data** är standardinställningen för Azure Time Series Insights-miljöer.  
- **Rensa gamla data** är att föredra när användare alltid vill ha sina *senaste data* i sin Time Series Insights-miljö.
- Rensning **av gamla datavärden** *rensar* data när miljöns gränser (kvarhållningstid, storlek eller antal, beroende på vilket som inträffar först) har uppnåtts. Kvarhållning är inställd på 30 dagar som standard.
- De äldsta inmatade data rensas först (metoden "First In First Out").

### <a name="example-one"></a>Exempel ett

Tänk dig en exempelmiljö med lagringsbeteende **Fortsätt ingående och rensa gamla data:**

**Datalagringstiden** är inställd på 400 dagar. **Kapaciteten** är inställd på S1-enhet, som innehåller 30 GB total kapacitet. Låt oss anta inkommande data ackumuleras till 500 MB varje dag i genomsnitt. Den här miljön kan bara lagra data till ett värde av 60 dagar med tanke på mängden inkommande data, eftersom den maximala kapaciteten uppnås vid 60 dagar. Inkommande data ackumuleras som: 500 MB varje dag x 60 dagar = 30 GB.

På den 61:a dagen visar miljön de färskaste data, men rensar de äldsta data som är äldre än 60 dagar. Rensningen ger utrymme för nya data som strömmar in, så att nya data kan fortsätta att utforskas. Om användaren vill behålla data längre kan de öka miljöns storlek genom att lägga till ytterligare enheter eller skicka mindre data.  

### <a name="example-two"></a>Exempel två

Överväg en miljö också konfigurerat lagringsbeteende **Fortsätt ingående och rensa gamla data**. I det här exemplet anges **datalagringstiden** till ett lägre värde på 180 dagar. **Kapaciteten** är inställd på S1-enhet, som innehåller 30 GB total kapacitet. För att lagra data för hela 180 dagar får den dagliga inträngningen inte överstiga 0,166 GB (166 MB) per dag.  

När den här miljöns dagliga inträngningshastighet överstiger 0,166 GB per dag kan data inte lagras i 180 dagar, eftersom vissa data rensas. Tänk på samma miljö under en upptagen tidsram. Anta att miljöns inträngningshastighet kan öka till i genomsnitt 0,189 GB per dag. Under den upptagna tidsramen lagras cirka 158 dagars data (30 GB/0,189 = 158,73 dagars kvarhållning). Den här tiden är mindre än den önskade tidsramen för datalagring.

## <a name="pause-ingress"></a>Pausa inträngning

- **Inställningen Paus ingress** är utformad för att säkerställa att data inte rensas om storleks- och räknegränserna nås före kvarhållningsperioden.  
- **Pausinträngning** ger ytterligare tid för användarna att öka kapaciteten i sin miljö innan data rensas på grund av brott mot kvarhållningsperioden.
- Det hjälper till att skydda dig från dataförlust, men kan skapa en möjlighet för förlust av dina senaste data om inträngning pausas efter lagringsperioden för din händelsekälla.
- När en miljö har uppnåtts så pausar miljön dock datainträngning tills följande ytterligare åtgärder inträffar:

   - Du ökar miljöns maximala kapacitet för att lägga till fler skalenheter enligt beskrivningen i [Hur du skalar din Time Series Insights-miljö](time-series-insights-how-to-scale-your-environment.md).
   - Lagringsperioden har uppnåtts och data rensas, vilket gör att miljön ligger under dess maximala kapacitet.

### <a name="example-three"></a>Exempel tre

Överväg en miljö med kvarhållningsbeteende konfigurerat för att **pausa inträngning**. I det här exemplet är **datakvarhållningsperioden** konfigurerad till 60 dagar. **Kapaciteten** är inställd på tre (3) enheter av S1. Anta att den här miljön har inträngning på 2 GB-data varje dag. I den här miljön pausas inträngningen när den maximala kapaciteten har uppnåtts.

På den tiden visar miljön samma datauppsättning tills ingående återupptas eller tills **fortsätta inträngning** är aktiverat (vilket skulle rensa äldre data för att göra plats för nya data).

När ingående återupptas:

- Dataflöden i den ordning som den togs emot av händelsekällan
- Händelserna indexeras baserat på deras tidsstämpel, såvida du inte har överskridit bevarandeprinciper på din händelsekälla. Om du vill ha mer information om konfiguration av loggkällans [kvarhållning, vanliga frågor om händelsehubbar](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Du bör ställa in aviseringar för att meddela meddelande för att undvika att inträngning pausas. Dataförlust är möjlig eftersom standardlagring är 1 dag för Azure-händelsekällor. När ingående har pausats förlorar du förmodligen de senaste data om inte ytterligare åtgärder vidtas. Du måste öka kapaciteten eller växla beteende till **Rensa gamla data** för att undvika dataförlustpotentialen.

I de påverkade händelsehubbar bör du överväga att justera egenskapen **Meddelandekvarhållning** för att minimera dataförlust när pausinträngning inträffar i Time Series Insights.

[![Bevarande av händelsehubbmeddelande.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Om inga egenskaper har konfigurerats på händelsekällan (`timeStampPropertyName`) standardinställningar för Tidsseriestatistik till tidsstämpeln för ankomst vid händelsenav som X-axeln. Om `timeStampPropertyName` är konfigurerad för att vara något annat `timeStampPropertyName` söker miljön efter den konfigurerade i datapaketet när händelser tolkas.

Läs [om Hur du skalar time series insights-miljön](time-series-insights-how-to-scale-your-environment.md) för att skala din miljö för att rymma ytterligare kapacitet eller öka lagringslängden.

## <a name="next-steps"></a>Nästa steg

- Information om hur du konfigurerar eller ändrar inställningar för datalagring finns [i Konfigurera kvarhållning i Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Lär dig mer om [förmildrande svarstid i Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).
