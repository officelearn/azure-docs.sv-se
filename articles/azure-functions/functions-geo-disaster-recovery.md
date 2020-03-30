---
title: Azure Functions geo-disaster recovery och hög tillgänglighet
description: Så här använder du geografiska regioner för redundans och överstvikning i Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 481a716bd6ced5c304da41c70fdcfc687b76661d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080230"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions geo-disaster recovery Azure Functions geo-disaster recovery Azure Functions geo-disaster recovery Azure Functions

När hela Azure-regioner eller datacenter upplever driftstopp är det viktigt för beräkning att fortsätta bearbetningen i en annan region.  I den här artikeln beskrivs några av de strategier som du kan använda för att distribuera funktioner för att möjliggöra haveriberedskap.

## <a name="basic-concepts"></a>Grundläggande begrepp

Azure Functions körs i en viss region.  Om du vill få högre tillgänglighet kan du distribuera samma funktioner till flera regioner.  När du är i flera regioner kan du ha dina funktioner som körs i det *aktiva/aktiva* mönstret eller *aktivt/passivt* mönster.  

* Aktiv/aktiv. Båda regionerna är aktiva och tar emot händelser (duplicera eller roterande). Aktiv/aktiv rekommenderas för HTTPS-funktioner i kombination med Azure Front Door.
* Aktiv/passiv. En region är aktiv och tar emot händelser, medan en sekundär är inaktiv.  När redundans krävs aktiveras den sekundära regionen och tar över bearbetningen.  Detta rekommenderas för icke-HTTP-funktioner som Service Bus och Event Hubs.

Läs om hur du [kör appar i flera regioner](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) för mer information om distributioner med flera regioner.

## <a name="activeactive-for-https-functions"></a>Aktiv/aktiv för HTTPS-funktioner

För att uppnå aktiva/aktiva distributioner av funktioner krävs en komponent som kan samordna händelserna mellan båda regionerna.  För HTTPS-funktioner utförs den här samordningen med [Azure Front Door](../frontdoor/front-door-overview.md).  Azure Front Door kan dirigera och round-robin HTTPS-begäranden mellan flera regionala funktioner.  Det kontrollerar också regelbundet hälsan för varje slutpunkt.  Om en regional funktion slutar svara på hälsokontroller tar Azure Front Door den ur rotation och vidarebefordrar endast trafik till felfria funktioner.  

![Arkitektur för Azure Ytterdörr och funktion](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Aktiv/aktiv för icke-HTTPS-funktioner

Du kan fortfarande uppnå aktiva/aktiva distributioner för icke-HTTPS-funktioner.  Du måste dock överväga hur de två regionerna kommer att interagera eller samordna med varandra.  Om du distribuerade samma funktionsapp i två regioner, som var och en utlöser i samma servicebusskö, skulle de fungera som konkurrerande konsumenter i kön.  Även om detta innebär att varje meddelande bara bearbetas av en av instanserna, betyder det också att det fortfarande finns en enda felpunkt på den enda servicebussen.  Om du distribuerar två servicebussköer (en i en primär region, en i en sekundär region) och de två funktionsapparna pekade på regionkön, kommer utmaningen nu i hur kömeddelandena fördelas mellan de två regionerna.  Det innebär ofta att varje utgivare försöker publicera ett meddelande i *båda* regionerna, och varje meddelande bearbetas av båda aktiva funktionsappar.  Även om detta skapar ett aktivt/aktivt mönster, skapar det andra utmaningar kring dubblering av beräkning och när eller hur data konsolideras.  Av dessa skäl rekommenderas att icke-HTTPS-utlösare använder det aktiva/passiva mönstret.

## <a name="activepassive-for-non-https-functions"></a>Aktiv/passiv för icke-HTTPS-funktioner

Aktiv/passiv ger ett sätt för endast en enda funktion att bearbeta varje meddelande, men ger en mekanism för att växla över till en sekundär region i händelse av en katastrof.  Azure Functions fungerar tillsammans med [Azure Service Bus geo-recovery](../service-bus-messaging/service-bus-geo-dr.md) och [Azure Event Hubs geo-recovery](../event-hubs/event-hubs-geo-dr.md).

Om du använder Azure Event Hubs-utlösare som exempel skulle det aktiva/passiva mönstret omfatta följande:

* Azure Event Hub distribueras till både en primär och sekundär region.
* Geo-katastrof aktiverad för att para ihop den primära och sekundära eventhubben.  Detta skapar också ett "alias" som du kan använda för att ansluta till händelsehubbar och växla från primära till sekundära utan att ändra anslutningsinformationen.
* Funktionsappar som distribueras till både en primär och sekundär region.
* Funktionsapparna utlöser *direct* på direktkopplingssträngen (icke-alias) för respektive händelsehubb. 
* Utgivare till händelsehubben ska publiceras i aliasanslutningssträngen. 

![Aktiv-passiv exempelarkitektur](media/functions-geo-dr/active-passive.png)

Före redundans dirigeras utgivare som skickar till det delade aliaset till den primära händelsehubben.  Den primära funktionsappen lyssnar uteslutande på den primära händelsehubben.  Den sekundära funktionsappen är passiv och inaktiv.  Så snart redundansen har initierats dirigeras nu utgivare som skickar till det delade aliaset till den sekundära händelsehubben.  Den sekundära funktionsappen blir nu aktiv och startar utlösa automatiskt.  Effektiv redundans till en sekundär region kan drivas helt från händelsehubben, där funktionerna bara aktiveras när respektive händelsenav är aktivt.

Läs mer om information och överväganden för redundans med [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) och [händelsehubbar](../event-hubs/event-hubs-geo-dr.md).

## <a name="next-steps"></a>Nästa steg

* [Skapa Azure Ytterdörr](../frontdoor/quickstart-create-front-door.md)
* [Redundansöverväganden för händelsehubbar](../event-hubs/event-hubs-geo-dr.md#considerations)
