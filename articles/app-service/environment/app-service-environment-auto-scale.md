---
title: V1 autoskalning och Apptjänst-miljö
description: Autoskalning och Apptjänst-miljö
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
ms.openlocfilehash: 0feb6e4862f643c35a58c0321181bdda22b032e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23837136"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>V1 autoskalning och Apptjänst-miljö

> [!NOTE]
> Den här artikeln handlar om Apptjänstmiljö v1.  Det finns en nyare version av Apptjänst-miljön som är enklare att använda och körs på mer kraftfulla infrastruktur. Mer information om den nya versionen start med den [introduktion till Apptjänst-miljön](intro.md).
> 

Stöd för Azure Apptjänst-miljöer *autoskalning*. Du kan Autoskala enskilda arbetarpooler utifrån mått eller schema.

![Autoskala alternativ för en arbetspool.][intro]

Autoskalning optimerar användningen av dina resurser genom att automatiskt växande och minska storleken på en Apptjänst-miljö för att anpassa din budget och eller läsa in profilen.

## <a name="configure-worker-pool-autoscale"></a>Konfigurera worker poolen Autoskala
Du kan komma åt Autoskala funktioner från den **inställningar** fliken för worker-pool.

![Fliken Inställningar för worker-pool.][settings-scale]

Därifrån är gränssnittet ska vara ganska bekant eftersom den samma upplevelse som visas när du skalar en programtjänstplan. 

![Inställningar för manuell skala.][scale-manual]

Du kan också konfigurera en profil för Autoskala.

![Autoskala inställningar.][scale-profile]

Autoskalningsprofiler är användbara för att ange begränsningar för nivå. På så sätt kan du ha en konsekvent prestanda som uppstår genom att ange ett skalvärde som nedre gräns (1) och en förutsägbar utgifter fjärrskrivbordsanslutning genom att ange en övre gräns (2).

![Skala inställningarna i profilen.][scale-profile2]

När du definierar en profil kan du lägga till automatiska regler för att skala upp eller ned antalet instanser i arbetspool innanför kontrollens gränser som definierats av profilen. Autoskala regler baserat på mått.

![Skalningsregeln.][scale-rule]

 Alla arbetspool eller frontend mått kan användas för att definiera automatiska regler. De här måtten är samma mått som du kan övervaka i resursen bladet diagrammen eller Ställ in aviseringar för.

## <a name="autoscale-example"></a>Autoskala exempel
Autoskala för en Apptjänst-miljö kan bäst illustreras genom att gå via ett scenario.

Den här artikeln beskrivs alla nödvändiga överväganden när du ställer in autoskalning. Artikeln vägleder dig genom de samspel som när du räkna in autoskalning apptjänstmiljöer som finns i Apptjänst-miljö.

### <a name="scenario-introduction"></a>Scenariot introduktion
Frank är en sysadmin för ett företag som en del av arbetsbelastningarna som hanterar han har migrerats till en Apptjänst-miljö.

Apptjänst-miljö konfigureras för manuell skala på följande sätt:

* **Främre slutar:** 3
* **Arbetspool 1**: 10
* **Arbetspool 2**: 5
* **Arbetspool 3**: 5

Arbetspool 1 används för produktionsarbetsbelastningar medan arbetspool 2 och arbetspool 3 används för quality assurance (QA) och arbetsbelastningar för utveckling.

App Service-planer för QA och dev konfigureras för manuell skala. Produktion App Service-plan anges Autoskala hantera variationer i belastningen och trafik.

Frank är insatt i programmet. Han känner att användningsnivå för är mellan 9:00:00 och 18:00 eftersom detta är ett line-of-business (LOB)-program som de anställda använder medan de är på kontoret. Användning utelämnar efter när användare är klar för den dagen. Utanför användningsnivå finns fortfarande vissa eftersom användare kan komma åt appen från en fjärrdator med hjälp av sina mobila enheter eller hemdatorer. Produktion App Service-plan har redan konfigurerats för att kunna Autoskala baserat på CPU-användning med följande regler:

![Inställningar för LOB-app.][asp-scale]

| **Autoskala profil – veckodagar – App Service-plan** | **Autoskala profil – helger – App Service-plan** |
| --- | --- |
| **Namn:** veckodag profil |**Namn:** helg profil |
| **Skala av:** schema-och prestanda |**Skala av:** schema-och prestanda |
| **Profil:** veckodagar |**Profil:** lördag |
| **Typ:** upprepning |**Typ:** upprepning |
| **Målområde:** 5 till 20 instanser |**Målområde:** 3 till 10 instanser |
| **Dagar:** måndag, tisdag, onsdag, torsdag, fredag |**Dagar:** lördag, söndag |
| **Starttid:** 9:00:00 |**Starttid:** 9:00:00 |
| **Tidszon:** UTC-08 |**Tidszon:** UTC-08 |
|  | |
| **Autoskala regeln (skala upp)** |**Autoskala regeln (skala upp)** |
| **Resurs:** produktion (Apptjänst-miljö) |**Resurs:** produktion (Apptjänst-miljö) |
| **Mått:** CPU i % |**Mått:** CPU i % |
| **Åtgärden:** större än 60% |**Åtgärden:** större än 80% |
| **Varaktighet:** 5 minuter |**Varaktighet:** 10 minuter |
| **Tid aggregering:** Genomsnittlig |**Tid aggregering:** Genomsnittlig |
| **Åtgärd:** öka antalet av 2 |**Åtgärd:** öka antalet med 1 |
| **Kall ned (minuter):** 15 |**Kall ned (minuter):** 20 |
|  | |
| **Autoskala regeln (skala ned)** |**Autoskala regeln (skala ned)** |
| **Resurs:** produktion (Apptjänst-miljö) |**Resurs:** produktion (Apptjänst-miljö) |
| **Mått:** CPU i % |**Mått:** CPU i % |
| **Åtgärden:** mindre än 30% |**Åtgärden:** mindre än 20% |
| **Varaktighet:** 10 minuter |**Varaktighet:** 15 minuter |
| **Tid aggregering:** Genomsnittlig |**Tid aggregering:** Genomsnittlig |
| **Åtgärd:** minska antalet med 1 |**Åtgärd:** minska antalet med 1 |
| **Kall ned (minuter):** 20 |**Kall ned (minuter):** 10 |

### <a name="app-service-plan-inflation-rate"></a>App Service-plan inflationen hastighet
Programtjänstplaner som har konfigurerats för att kunna Autoskala gör det på en högsta överföringshastighet per timme. Detta värde kan beräknas baserat på de värden som anges på Autoskala regeln.

Förstå och beräkna den *App Service-plan inflationen hastighet* är viktigt för Apptjänst-miljö Autoskala eftersom skala ändringar i en arbetspool inte omedelbar.

App Service-plan inflationen satsen beräknas enligt följande:

![App Service-plan inflationen beräkning.][ASP-Inflation]

Baserat på Autoskala – skala upp regel för produktion programtjänstplanen veckodag profil:

![App Service-plan inflationen hastighet för veckodagar baserat på Autoskala – skala upp regeln.][Equation1]

När det gäller Autoskala – skala upp regel för lördag profilen för produktion App Service-plan löser formeln till:

![App Service-plan inflationen hastighet för helger baserat på Autoskala – skala upp regeln.][Equation2]

Det här värdet kan också beräknas för skala ned åtgärder.

Baserat på Autoskala – skala ned regel för profilen veckodag för produktion App Service-plan detta skulle se ut så här:

![App Service-plan inflationen hastighet för veckodagar baserat på Autoskala – skala ned regeln.][Equation3]

När det gäller Autoskala – skala ned regel för lördag profilen för produktion App Service-plan löser formeln till:  

![App Service-plan inflationen hastighet för helger baserat på Autoskala – skala ned regeln.][Equation4]

Produktion App Service-plan kan växa på högst åtta instanser per timme under vecka och fyra instanser per timme när det är helgen. Det kan släppa instanser på högst fyra instanser per timme under vecka och sex instanser per timme under helger.

Om flera App Service-planer finns i en arbetspool, måste du beräkna den *totala inflationen hastigheten* som summan av inflationen hastighet för alla programtjänstplaner som värd i den poolen, worker.

![Beräkning av totala inflationen hastigheten för flera App Service-planer finns i en arbetspool.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Använda App Service-plan inflationen hastighet för att definiera worker poolen Autoskala regler
Worker pooler som är värdar för App Service-planer som har konfigurerats för att kunna Autoskala behöver att allokera en buffert av kapacitet. Bufferten kan åtgärderna Autoskala att växa eller krympa programtjänstplanen efter behov. Minsta bufferten är beräknade App Service planera inflationen summan.

Eftersom skalningsåtgärder för Apptjänst-miljö ta lite tid att tillämpa, bör ändringar konto för ytterligare begäran ändringar som kan inträffa när en skalningsåtgärd pågår. För att tillgodose det här svarstid, rekommenderar vi att du använder den beräknade App Service planera inflationen frekvens som det minsta antalet instanser som har lagts till för varje Autoskala-åtgärd.

Med den här informationen kan du definiera följande Autoskala profil och regler Frank:

![Autoskala profil regler för LOB-exempel.][Worker-Pool-Scale]

| **Autoskala profil – veckodagar** | **Autoskala profil – söndag** |
| --- | --- |
| **Namn:** veckodag profil |**Namn:** helg profil |
| **Skala av:** schema-och prestanda |**Skala av:** schema-och prestanda |
| **Profil:** veckodagar |**Profil:** lördag |
| **Typ:** upprepning |**Typ:** upprepning |
| **Målområde:** 13 25 instanser |**Målområde:** 6-15 instanser |
| **Dagar:** måndag, tisdag, onsdag, torsdag, fredag |**Dagar:** lördag, söndag |
| **Starttid:** 7:00:00 |**Starttid:** 9:00:00 |
| **Tidszon:** UTC-08 |**Tidszon:** UTC-08 |
|  | |
| **Autoskala regeln (skala upp)** |**Autoskala regeln (skala upp)** |
| **Resurs:** arbetspool 1 |**Resurs:** arbetspool 1 |
| **Mått:** WorkersAvailable |**Mått:** WorkersAvailable |
| **Åtgärden:** mindre än 8 |**Åtgärden:** färre än 3 |
| **Varaktighet:** 20 minuter |**Varaktighet:** 30 minuter |
| **Tid aggregering:** Genomsnittlig |**Tid aggregering:** Genomsnittlig |
| **Åtgärd:** öka antalet med 8 |**Åtgärd:** öka antalet med 3 |
| **Kall ned (minuter):** 180 |**Kall ned (minuter):** 180 |
|  | |
| **Autoskala regeln (skala ned)** |**Autoskala regeln (skala ned)** |
| **Resurs:** arbetspool 1 |**Resurs:** arbetspool 1 |
| **Mått:** WorkersAvailable |**Mått:** WorkersAvailable |
| **Åtgärden:** större än 8 |**Åtgärden:** större än 3 |
| **Varaktighet:** 20 minuter |**Varaktighet:** 15 minuter |
| **Tid aggregering:** Genomsnittlig |**Tid aggregering:** Genomsnittlig |
| **Åtgärd:** minska antalet med 2 |**Åtgärd:** minska antalet med 3 |
| **Kall ned (minuter):** 120 |**Kall ned (minuter):** 120 |

Mål-intervallet som definierats i profilen beräknas genom att de minsta instanser som anges i profilen för App Service-plan + buffert.

Maximalt intervall är summan av alla maximalt intervall för alla App Service-planer finns i poolen worker.

Öka antalet för skalan regler ska konfigureras med minst 1 X-App Service-Plan inflationen hastighet för skalan.

Minska antalet kan justeras till något mellan 1/2 X eller 1 X-App Service Plan inflationen hastighet för att skala ned.

### <a name="autoscale-for-front-end-pool"></a>Autoskala för frontend-pool
Regler för frontend Autoskala är enklare än för arbetarpooler. Främst bör du  
Kontrollera att beakta varaktighet för mätning och cooldown timers skalningsåtgärder på en apptjänstplan inte är omedelbar.

I det här scenariot vet Frank att Felfrekvensen ökar när frontwebbservrarna når 80% CPU-användning och anger Autoskala regeln för att öka instanser på följande sätt:

![Autoskala inställningar för frontend-pool.][Front-End-Scale]

| **Autoskala profil – framför slutar** |
| --- |
| **Namn:** Autoskala – framför slutar |
| **Skala av:** schema-och prestanda |
| **Profil:** varje dag |
| **Typ:** upprepning |
| **Målområde:** 3 till 10 instanser |
| **Dagar:** varje dag |
| **Starttid:** 9:00:00 |
| **Tidszon:** UTC-08 |
|  |
| **Autoskala regeln (skala upp)** |
| **Resurs:** frontend-pool |
| **Mått:** CPU i % |
| **Åtgärden:** större än 60% |
| **Varaktighet:** 20 minuter |
| **Tid aggregering:** Genomsnittlig |
| **Åtgärd:** öka antalet med 3 |
| **Kall ned (minuter):** 120 |
|  |
| **Autoskala regeln (skala ned)** |
| **Resurs:** arbetspool 1 |
| **Mått:** CPU i % |
| **Åtgärden:** mindre än 30% |
| **Varaktighet:** 20 minuter |
| **Tid aggregering:** Genomsnittlig |
| **Åtgärd:** minska antalet med 3 |
| **Kall ned (minuter):** 120 |

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
