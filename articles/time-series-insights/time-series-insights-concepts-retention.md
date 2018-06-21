---
title: Förstå datalagring i miljön Azure tid serien Insights | Microsoft Docs
description: Den här artikeln beskrivs två inställningar som styr datalagring i miljön Azure tid serien insikter.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: kfile
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: c8fad0d626fc0b9754f7f04c8000f48976eb766b
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293103"
---
# <a name="understand-data-retention-in-time-series-insights"></a>Förstå datalagring i tid serien insikter
Den här artikeln beskrivs två inställningar som påverkar datalagring i miljön tid serien insikter (TSD).

Varje TSD-miljö har en inställning som styr **datalagringstid**. Värdet ska vara mellan 1 och 400 dagar. Data tas bort baserat på miljön lagring kapacitet eller kvarhållande varaktigheten (1-400), beroende på vilket som inträffar först.

Varje TSD-miljö har en inställning för ytterligare **lagringsgräns överskred beteende**. Den här inställningen styr beteendet för inkommande trafik och rensa när maxkapaciteten för en miljö har uppnåtts. Det finns två beteenden kan välja mellan:
- **Radera gamla data** (standard)  
- **Pausa ingång**

> [!NOTE]
> Som standard när du skapar en ny miljö, kvarhållning är konfigurerad att **Radera gamla data**. Den här inställningen kan du ange vid behov efter skapelsetid med Azure-portalen på den **konfigurera** sidan TSD-miljön.

Mer information om hur du växlar kvarhållning beteenden [konfigurera bevaring i tid serien insikter](time-series-insights-how-to-configure-retention.md).

Jämför datakvarhållning beteendet:

## <a name="purge-old-data"></a>Radera gamla data
- Detta är standardbeteendet för TSD miljöer och bevisföremål i samma beteende TSD miljöer ställt eftersom den startas i till förhandsversion.  
- Det här problemet är att föredra när du vill se deras *senaste data* i sina miljöer TSD. 
- Det här beteendet *tar bort* data en gång i miljön gränser (tiden för datakvarhållning storlek eller antal, beroende på vilket som inträffar först). Kvarhållning anges till 30 dagar som standard. 
- Den äldsta infogade data tas bort först (FIFO metod).

### <a name="example-1"></a>Exempel 1:
Överväg att en exempel-miljö med kvarhållning beteende **fortsätta ingång och radera gamla data**: I det här exemplet **datalagringstid** är inställd på 400 dagar. **Kapacitet** är inställd på S1 enhet som innehåller 30 GB total kapacitet.   Anta inkommande data ackumuleras 500 MB varje dag i genomsnitt. Den här miljön kan endast behålla 60 dagar med data som ges av inkommande data, eftersom maximala kapacitet har uppnåtts 60 dagar. Inkommande data ackumuleras som: 500 MB varje dag x 60 dagar = 30 GB. 

I det här exemplet 61st dag miljön visar nyaste data, men det tar bort de äldsta data äldre än 60 dagar. Rensning gör plats för nya data strömning i, så att nya data kan fortsätta att undersökas. 

Om användaren vill bevara data längre kan öka storleken på miljön genom att lägga till ytterligare enheter eller skicka mindre data.  

### <a name="example-2"></a>Exempel 2:
Överväg att en miljö även är konfigurerat kvarhållning beteende **fortsätta ingång och radera gamla data**. I det här exemplet **datalagringstid** har angetts till ett lägre värde på 180 dagar. **Kapacitet** är inställd på S1 enhet som innehåller 30 GB total kapacitet. Dagliga ingång får inte överskrida 0.166 GB (166 MB) per dag för att lagra data för fullständig 180 dagar.  

När den här miljön dagliga ingång frekvensen överskrider 0.166 GB per dag, kan data inte lagras i 180 dagar, eftersom vissa data hämtar rensas. Överväg att den här samma miljö under en tidsperiod som upptagen. Anta att den miljön ingång hastighet kan öka till en genomsnittlig 0.189 GB per dag. I den upptagna tidsramen 158 dagars data bevaras (30GB/0.189 = 158.73 dagar för kvarhållning). Nu är mindre än tid för bevarande av önskade data.

## <a name="pause-ingress"></a>Pausa ingång
- Det här beteendet är utformat för att säkerställa att data inte tas bort om storlek och antal gränser uppnås innan deras kvarhållningsperioden.  
- Det här beteendet ger extra tid för användare att öka kapaciteten för deras miljö innan data tas bort på grund av brott mot för kvarhållningsperioden
- Detta skyddar mot förlust av data, men skapar en möjlighet för din senaste data går förlorade om ingång pausas utanför kvarhållningsperioden av din händelsekälla.
- Men när en miljö maximala kapacitet har nåtts pausas miljön data ingång tills ytterligare åtgärder: 
   - Du kan öka i miljön maximala kapacitet. Mer information finns i [så här skalar du tid serien insikter miljön](time-series-insights-how-to-scale-your-environment.md) att lägga till flera skalenheter.
   - Datalagringsperiod har nåtts och data tas bort, vilket gör miljön under sin maximala kapacitet.

### <a name="example-3"></a>Exempel 3:
Överväga en miljö med kvarhållning beteende som konfigurerats för att **pausa ingång**. I det här exemplet i **Datalagringsperiod** är konfigurerad till 60 dagar. **Kapacitet** är inställd på 3 enheter S1. Anta att den här miljön har ingång 2 GB data varje dag. I den här miljön pausas ingång när maximala kapacitet har uppnåtts. Då visas miljön samma datamängd tills ingång återupptar eller tills ”Fortsätt ingång” är aktiverat (vilket skulle ta bort äldre data för att göra plats för nya data). 

Ingång återupptas när:
- Dataflöden i den ordning som den har tagits emot av händelsekälla
- Händelserna som indexeras baserat på deras tidsstämpel om du har överskridit bevarandeprinciper för din händelsekälla. Mer information om händelsen källa kvarhållning configuration [Event Hubs vanliga frågor och svar](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> Du bör ange aviseringar för att ange meddelande för att undvika ingång paus. Dataförlust kan inträffa eftersom Standardkvarhållning är 1 dag för Azure händelsekällor. Därför när ingång pausas förlorar sannolikt du den senaste informationen om ytterligare åtgärd. Du måste öka kapaciteten eller växlar funktionssätt **Radera gamla data** att undvika dataförlust som är möjliga.

I Händelsehubbar påverkas bör du justera de **meddelandet kvarhållning** egenskapen att minimera dataförlust när paus ingång uppstår i tid serien insikter.

![Event hub meddelandet kvarhållning.](media/time-series-insights-contepts-retention/event-hub-retention.png)

Om inga egenskaper har konfigurerats på händelsekälla (timeStampPropertyName), standard TSD tidsstämpeln för ankomst till händelsehubben som x-axeln. Om timeStampPropertyName är konfigurerad som något annat efter miljön konfigurerade timeStampPropertyName i datapaketet när händelser parsas. 

Om du behöver utöka din miljö till anpassas ytterligare kapacitet eller att öka längden på kvarhållning, se [så här skalar du tid serien insikter miljön](time-series-insights-how-to-scale-your-environment.md) för mer information.  

## <a name="next-steps"></a>Nästa steg
Information om hur du växlar kvarhållning beteende, granska [konfigurera bevaring i tid serien insikter](time-series-insights-how-to-configure-retention.md).
