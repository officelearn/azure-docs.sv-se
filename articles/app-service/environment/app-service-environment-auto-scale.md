---
title: Automatisk skalning och App Service Environment v1 - Azure
description: Automatisk skalning och App Service-miljö
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 29a639142395c43fea06c1d6d18909b3c9f33b86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769504"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Automatisk skalning och App Service Environment v1

> [!NOTE]
> Den här artikeln handlar om App Service Environment v1.  Det finns en nyare version av App Service Environment som är enklare att använda och körs på kraftfullare infrastruktur. Mer information om den nya versionen början med den [introduktion till App Service Environment](intro.md).
> 

Stöd för Azure App Service-miljöer *autoskalning*. Du kan skala automatiskt individuella arbetarpooler baserat på mått eller schemaläggning.

![Alternativ för automatisk skalning för en worker-pool.][intro]

Automatisk skalning optimerar resursanvändningen genom att automatiskt växande och minska storleken på en App Service-miljö för att de passar din budget och eller läsa in profilen.

## <a name="configure-worker-pool-autoscale"></a>Konfigurera worker pool autoscale
Du kan komma åt funktionerna för automatisk skalning från den **inställningar** fliken arbetarpoolen.

![Inställningsfliken i arbetarpoolen.][settings-scale]

Därifrån kan är gränssnittet ska vara ganska välbekanta eftersom den samma upplevelse som du ser när du skalar en App Service plan. 

![Inställningar för manuell skala.][scale-manual]

Du kan också konfigurera en autoskalningsprofil.

![Inställningarna för automatisk skalning.][scale-profile]

Profiler för automatisk skalning är användbara för att ange begränsningar för din skalningsuppsättning. På så sätt kan du ha en konsekvent prestanda som uppstår genom att ange ett undre gränsvärde skala värde (1) och ett tak för förutsägbara kostnader genom att ange en övre gräns (2).

![Skala inställningarna i profilen.][scale-profile2]

När du har definierat en profil kan du lägga till regler för automatisk skalning att skala upp eller ned antalet instanser i arbetarpoolen inom det giltiga intervallet som definierats av profilen. Regler för automatisk skalning baserat på mått.

![Skalningsregeln.][scale-rule]

 Alla arbetarpooler eller mått i klientdelen kan användas för att definiera regler för automatisk skalning. Dessa mått är de samma mått som du kan övervaka i resursen bladet diagram eller ställa in aviseringar för.

## <a name="autoscale-example"></a>Exempel för automatisk skalning
Automatisk skalning av App Service environment kan bäst illustreras genom att gå igenom ett scenario.

Den här artikeln beskrivs alla nödvändiga överväganden när du ställer in automatisk skalning. Artikeln vägleder dig genom de interaktioner som har betydelse vid du räkna in automatisk skalning App Service-miljöer som finns i App Service Environment.

### <a name="scenario-introduction"></a>Scenario-introduktion
Frank är en sysadmin för ett företag som har en del av de arbetsbelastningar som hanterar han till en App Service-miljö.

App Service-miljö är konfigurerad att manuell skala enligt följande:

* **Klientdelar:** 3
* **Arbetarpool 1**: 10
* **Arbetarpool 2**: 5
* **Arbetarpool 3**: 5

Arbetarpool 1 används för produktionsarbetsbelastningar medan arbetarpool 2 och arbetarpoolen 3 används för utvecklingsarbetsbelastningar och kvalitetssäkring (kvalitetskontroll).

App Service-planer för QA och utveckling är konfigurerade att manuell skala. App Service-plan för produktion anges som automatisk skalning utan variationer i nätverksbelastningen och.

Frank är insatt i programmet. Han känner att tiderna med högsta användningsnivå för är mellan 9:00:00 och 18:00, eftersom detta är en line-of-business (LOB) program som anställda använder när de är på kontoret. Användningen sjunker efter det när användare är klar för den dagen. Utanför högbelastningstid finns fortfarande vissa eftersom användarna kommer åt appen via en fjärranslutning med hjälp av sina mobila enheter eller hemdatorer. Produktion App Service-plan har redan konfigurerats för automatisk skalning baserat på CPU-användning med följande regler:

![Inställningar för LOB-app.][asp-scale]

| **Profilen för automatisk skalning – vardagar – App Service-plan** | **Profilen för automatisk skalning – helger – App Service-plan** |
| --- | --- |
| **Namn:** Weekday-profil |**Namn:** Helgen profil |
| **Skala genom att:** Schema och Prestandaregler |**Skala genom att:** Schema och Prestandaregler |
| **Profil:** Veckodagar |**Profil:** Helgen |
| **Typ:** Upprepning |**Typ:** Upprepning |
| **Målområde:** 5 till 20 instanser |**Målområde:** 3-10 instanser |
| **Dagar:** Måndag, tisdag, onsdag, torsdag, fredag |**Dagar:** Lördag, söndag |
| **Starttid:** 9:00:00 |**Starttid:** 9:00:00 |
| **Tidszon:** UTC-08 |**Tidszon:** UTC-08 |
|  | |
| **Regeln för automatisk skalning (skala upp)** |**Regeln för automatisk skalning (skala upp)** |
| **resursen:** Produktion (App Service Environment) |**resursen:** Produktion (App Service Environment) |
| **Mått:** Processor i procent |**Mått:** Processor i procent |
| **Åtgärd:** Större än 60% |**Åtgärd:** Större än 80% |
| **Varaktighet:** 5 minuter |**Varaktighet:** 10 minuter |
| **Tidsmängd:** Medel |**Tidsmängd:** Medel |
| **Åtgärd:** Öka antal med 2 |**Åtgärd:** Öka antal med 1 |
| **Väntetid (minuter):** 15 |**Väntetid (minuter):** 20 |
|  | |
| **Regeln för automatisk skalning (skala ned)** |**Regeln för automatisk skalning (skala ned)** |
| **resursen:** Produktion (App Service Environment) |**resursen:** Produktion (App Service Environment) |
| **Mått:** Processor i procent |**Mått:** Processor i procent |
| **Åtgärd:** Mindre än 30% |**Åtgärd:** Mindre än 20% |
| **Varaktighet:** 10 minuter |**Varaktighet:** 15 minuter |
| **Tidsmängd:** Medel |**Tidsmängd:** Medel |
| **Åtgärd:** Minska antal med 1 |**Åtgärd:** Minska antal med 1 |
| **Väntetid (minuter):** 20 |**Väntetid (minuter):** 10 |

### <a name="app-service-plan-inflation-rate"></a>App Service-plan inflationen hastighet
App Service-planer som har konfigurerats för automatisk skalning gör det på en högsta överföringshastighet per timme. Priset kan beräknas baserat på de värden som anges i regeln för automatisk skalning.

Förstå och beräkna den *App Service-plan inflationen rate* är viktigt för App Service environment autoskalning eftersom ändringar till en worker-pool inte är omedelbar.

App Service-plan inflationen priset beräknas enligt följande:

![App Service-plan inflationen beräkning.][ASP-Inflation]

Baserat på autoskalning – skala upp regel för profilen veckodag för produktion App Service-planen:

![App Service-plan inflationen hastighet för veckodagar baserat på autoskalning – skala upp regeln.][Equation1]

När det gäller autoskalning – skala upp regel för helgen profilen för produktion App Service-planen skulle formeln matcha med:

![App Service-plan inflationen hastighet för helger baserat på autoskalning – skala upp regeln.][Equation2]

Det här värdet kan också beräknas för skala ned åtgärder.

Baserat på autoskalning – skala ned regel för profilen veckodag för produktion App Service-planen detta skulle se ut så här:

![App Service-plan inflationen hastighet för veckodagar baserat på autoskalning – skala ned regeln.][Equation3]

När det gäller autoskalning – skala ned regel för helgen profilen för produktion App Service-planen skulle formeln matcha med:  

![App Service-plan inflationen hastighet för helger baserat på autoskalning – skala ned regeln.][Equation4]

App Service-plan för produktion kan växa på högst åtta instanser per timme under veckan och fyra instanser per timme när det är helgen. Det kan släppa instanser på högst fyra instanser per timme under veckan och sex instanser per timme under helger.

Om flera App Service-planer finns i en arbetspool, måste du beräkna den *totala inflationen antalet* som summan av inflationen avgiften för alla App Service-planer som värd för i den arbetarpoolen.

![Total beräkning av inflationen för flera App Service-planer finns i en arbetspool.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Använda App Service-plan inflationen frekvensen för att definiera regler för automatisk skalning av worker-pool
Worker-pooler som är värdar för App Service-planer som har konfigurerats för automatisk skalning måste tilldelas en buffert med kapacitet. Bufferten möjliggör åtgärder för automatisk skalning att växa eller krympa App Service-planen efter behov. Minsta bufferten är beräknade App Service Plan inflationen summan.

Eftersom App Service environment skalningsåtgärder ta lite tid att tillämpa, ska ändringar svara och begäran ändringar kan göras som kan inträffa när en skalningsåtgärd pågår. För att hantera den här fördröjningen, rekommenderar vi att du använder den beräknade App Service Plan inflationen frekvens som det minsta antalet instanser som har lagts till för varje åtgärd för automatisk skalning.

Med den här informationen kan du definiera följande profilen för automatisk skalning och regler Frank:

![Regler för automatisk skalning profil för LOB-exempel.][Worker-Pool-Scale]

| **Profilen för automatisk skalning – veckodagar** | **Profilen för automatisk skalning – helger** |
| --- | --- |
| **Namn:** Weekday-profil |**Namn:** Helgen profil |
| **Skala genom att:** Schema och Prestandaregler |**Skala genom att:** Schema och Prestandaregler |
| **Profil:** Veckodagar |**Profil:** Helgen |
| **Typ:** Upprepning |**Typ:** Upprepning |
| **Målområde:** 13 till 25 instanser |**Målområde:** 6-15-instanser |
| **Dagar:** Måndag, tisdag, onsdag, torsdag, fredag |**Dagar:** Lördag, söndag |
| **Starttid:** 7:00:00 |**Starttid:** 9:00:00 |
| **Tidszon:** UTC-08 |**Tidszon:** UTC-08 |
|  | |
| **Regeln för automatisk skalning (skala upp)** |**Regeln för automatisk skalning (skala upp)** |
| **resursen:** Arbetarpool 1 |**resursen:** Arbetarpool 1 |
| **Mått:** WorkersAvailable |**Mått:** WorkersAvailable |
| **Åtgärd:** Mindre än 8 |**Åtgärd:** Mindre än 3 |
| **Varaktighet:** 20 minuter |**Varaktighet:** 30 minuter |
| **Tidsmängd:** Medel |**Tidsmängd:** Medel |
| **Åtgärd:** Öka antal med 8 |**Åtgärd:** Öka antal med 3 |
| **Väntetid (minuter):** 180 |**Väntetid (minuter):** 180 |
|  | |
| **Regeln för automatisk skalning (skala ned)** |**Regeln för automatisk skalning (skala ned)** |
| **resursen:** Arbetarpool 1 |**resursen:** Arbetarpool 1 |
| **Mått:** WorkersAvailable |**Mått:** WorkersAvailable |
| **Åtgärd:** Större än 8 |**Åtgärd:** Är större än 3 |
| **Varaktighet:** 20 minuter |**Varaktighet:** 15 minuter |
| **Tidsmängd:** Medel |**Tidsmängd:** Medel |
| **Åtgärd:** Minska antal med 2 |**Åtgärd:** Minska antal med 3 |
| **Väntetid (minuter):** 120 |**Väntetid (minuter):** 120 |

Målområde som definierats i profilen beräknas genom att de minsta instanser som anges i profilen för App Service-plan + buffert.

Maximalt intervall är summan av de största intervall för alla App Service-planer finns i arbetarpoolen.

Öka antalet för skalan in regler sättas till minst 1 X-App Service Plan inflationen frekvensen för att skala.

Minska antal kan justeras till något mellan 1/2 X eller 1 X-App Service Plan inflationen frekvensen för att skala ned.

### <a name="autoscale-for-front-end-pool"></a>Automatisk skalning för adresspool på klientsidan
Regler för klientdelen autoskalning är enklare än för arbetarpooler. Främst bör du  
Se till att varaktigheten för mätning och nedkylningstiden timers överväger skalningsåtgärder i en App Service-plan inte är omedelbar.

Det här scenariot vet Frank att Felfrekvensen ökar när klientdelar når 80% CPU-användning och anger regeln för automatisk skalning att öka instanser enligt följande:

![Inställningarna för automatisk skalning för adresspool på klientsidan.][Front-End-Scale]

| **Profilen för automatisk skalning – klientdel** |
| --- |
| **Namn:** Automatisk skalning – klientdel |
| **Skala genom att:** Schema och Prestandaregler |
| **Profil:** varje dag |
| **Typ:** Upprepning |
| **Målområde:** 3-10 instanser |
| **Dagar:** varje dag |
| **Starttid:** 9:00:00 |
| **Tidszon:** UTC-08 |
|  |
| **Regeln för automatisk skalning (skala upp)** |
| **resursen:** Adresspool på klientsidan |
| **Mått:** Processor i procent |
| **Åtgärd:** Större än 60% |
| **Varaktighet:** 20 minuter |
| **Tidsmängd:** Medel |
| **Åtgärd:** Öka antal med 3 |
| **Väntetid (minuter):** 120 |
|  |
| **Regeln för automatisk skalning (skala ned)** |
| **resursen:** Arbetarpool 1 |
| **Mått:** Processor i procent |
| **Åtgärd:** Mindre än 30% |
| **Varaktighet:** 20 minuter |
| **Tidsmängd:** Medel |
| **Åtgärd:** Minska antal med 3 |
| **Väntetid (minuter):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
