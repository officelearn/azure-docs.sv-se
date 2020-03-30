---
title: Automatisk skalning v1
description: Automatisk skalning och apptjänstmiljö v1. Det här dokumentet tillhandahålls endast för kunder som använder den äldre v1 ASE.
author: btardif
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4f071c0d09fc2fa97eeea45bd82228b7eb8434a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687286"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Automatisk skalning och apptjänstmiljö v1

> [!NOTE]
> Den här artikeln handlar om App Service Environment v1.  Det finns en nyare version av App Service Environment som är enklare att använda och körs på mer kraftfull infrastruktur. Om du vill veta mer om den nya versionen börjar med [introduktionen till App Service Environment](intro.md).
> 

Azure App Service-miljöer stöder *automatisk skalning*. Du kan automatiskt skala enskilda arbetarpooler baserat på mått eller schema.

![Alternativ för automatisk skalning för en arbetspool.][intro]

Automatisk skalning optimerar resursanvändningen genom att automatiskt öka och krympa en App Service-miljö så att den passar din budget och eller din läsprofil.

## <a name="configure-worker-pool-autoscale"></a>Konfigurera automatisk skalning av arbetspoolen
Du kan komma åt funktionen för automatisk skalning på fliken **Inställningar** i arbetarpoolen.

![Fliken Inställningar i arbetarpoolen.][settings-scale]

Därifrån bör gränssnittet vara ganska bekant eftersom det är samma upplevelse som du ser när du skalar en App Service-plan. 

![Inställningar för manuell skala.][scale-manual]

Du kan också konfigurera en profil för automatisk skalning.

![Inställningar för automatisk skalning.][scale-profile]

Profiler för automatisk skalning är användbara för att ange gränser för din skala. På så sätt kan du ha en konsekvent prestandaupplevelse genom att ange ett lägre bound scale-värde (1) och ett förutsägbart utgiftstak genom att ange en övre gräns (2).

![Skalningsinställningar i profil.][scale-profile2]

När du har definierat en profil kan du lägga till regler för automatisk skalning för att skala upp eller ned antalet instanser i arbetarpoolen inom de gränser som definieras av profilen. Regler för automatisk skalning baseras på mått.

![Skalningsregel.][scale-rule]

 Alla arbetarpooler eller frontend-mått kan användas för att definiera regler för automatisk skalning. Dessa mått är samma mått som du kan övervaka i resursbladsdiagrammen eller ställa in aviseringar för.

## <a name="autoscale-example"></a>Exempel på automatisk skalning
Automatisk skalning av en App Service-miljö kan bäst illustreras genom att gå igenom ett scenario.

I den här artikeln beskrivs alla nödvändiga överväganden när du konfigurerar automatisk skalning. Artikeln går igenom de interaktioner som spelar in när du räknar in automatisk skalning av App Service-miljöer som finns i App Service Environment.

### <a name="scenario-introduction"></a>Introduktion till scenario
Frank är ett sysadmin för ett företag som har migrerat en del av arbetsbelastningarna som de hanterar till en App Service-miljö.

App service-miljön är konfigurerad för manuell skala enligt följande:

* **Främre ände:** 3
* **Arbetarpool 1:** 10
* **Arbetarpool 2:** 5
* **Arbetarpool 3:** 5

Arbetspool 1 används för produktionsarbetsbelastningar, medan arbetspool 2 och arbetspool 3 används för kvalitetssäkring (QA) och utvecklingsarbetsbelastningar.

Appserviceplanerna för QA och dev är konfigurerade för manuell skala. Produktionsapptjänstplanen är inställd på automatisk skalning för att hantera variationer i belastning och trafik.

Frank är väl förtrogen med ansökan. De vet att rusningstid för belastning är mellan 09:00 och 18:00 eftersom detta är ett affärsprogram (LOB) som anställda använder när de är på kontoret. Användningen sjunker efter det när användarna är klara för den dagen. Utanför rusningstid finns det fortfarande en viss belastning eftersom användarna kan komma åt appen på distans med hjälp av sina mobila enheter eller hemdatorer. Produktionsapptjänstplanen är redan konfigurerad för automatisk skalning baserat på CPU-användning med följande regler:

![Specifika inställningar för LOB-appen.][asp-scale]

| **Profil för automatisk skalning – vardagar – App Service-plan** | **Profil för automatisk skalning – Helger – App Service-plan** |
| --- | --- |
| **Namn:** Profil för veckodag |**Namn:** Helgprofil |
| **Skala efter:** Schema- och prestandaregler |**Skala efter:** Schema- och prestandaregler |
| **Profil:** Vardagar |**Profil:** Helgen |
| **Typ:** Återkommande |**Typ:** Återkommande |
| **Målintervall:** 5 till 20 instanser |**Målintervall:** 3 till 10 instanser |
| **Dagar:** Måndag, Tisdag, Onsdag, Torsdag, Fredag |**Dagar:** Lördag, söndag |
| **Starttid:** 09:00 |**Starttid:** 09:00 |
| **Tidszon:** UTC-08 |**Tidszon:** UTC-08 |
|  | |
| **Regel för automatisk skalning (skala upp)** |**Regel för automatisk skalning (skala upp)** |
| **Resurs:** Produktion (App Service Environment) |**Resurs:** Produktion (App Service Environment) |
| **Mått:** CPU % |**Mått:** CPU % |
| **Drift:** Större än 60% |**Drift:** Större än 80% |
| **Längd:** 5 minuter |**Längd:** 10 minuter |
| **Tid aggregering:** Genomsnittliga |**Tid aggregering:** Genomsnittliga |
| **Åtgärd:** Öka antalet med 2 |**Åtgärd:** Öka antalet med 1 |
| **Kyl ner (minuter):** 15 |**Kyl ner (minuter):** 20 |
|  | |
| **Regel för automatisk skalning (skala ned)** |**Regel för automatisk skalning (skala ned)** |
| **Resurs:** Produktion (App Service Environment) |**Resurs:** Produktion (App Service Environment) |
| **Mått:** CPU % |**Mått:** CPU % |
| **Drift:** Mindre än 30% |**Drift:** Mindre än 20% |
| **Längd:** 10 minuter |**Längd:** 15 minuter |
| **Tid aggregering:** Genomsnittliga |**Tid aggregering:** Genomsnittliga |
| **Åtgärd:** Minska antalet med 1 |**Åtgärd:** Minska antalet med 1 |
| **Kyl ner (minuter):** 20 |**Kyl ner (minuter):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Inflationstakten i App Service-planen
App Service-planer som är konfigurerade för automatisk skalning gör det till en maximal hastighet per timme. Den här kursen kan beräknas baserat på de värden som anges på regeln om automatisk skalning.

Att förstå och beräkna *uppblåsningsgraden för App Service-planen* är viktigt för automatisk apptjänstmiljöskalning eftersom skalningsändringar i en arbetspool inte är omedelbara.

Inflationstakten i App Service-planen beräknas på följande sätt:

![Beräkning av inflationshastigheten för App Service-planen.][ASP-Inflation]

Baserat på regeln Automatisk skalning – Skala upp för veckodagsprofilen för produktionsapptjänstplanen:

![App Service plan inflationstakt för vardagar baserat på automatisk skala - Skala upp regeln.][Equation1]

När det gäller regeln Automatisk skalning – Skala upp för helgprofilen för produktionsapptjänstplanen, skulle formeln matcha till:

![App Service plan inflationstakt för helger baserat på automatisk skalning - Skala upp regeln.][Equation2]

Det här värdet kan också beräknas för nedskalningsåtgärder.

Baserat på regeln Automatisk skalning – Skala ned för veckodagsprofilen för produktionsapptjänstplanen, skulle detta se ut så här:

![Inflationstakten för apptjänstplanen för vardagar baserat på regeln Kalka automatiskt – skala ned.][Equation3]

När det gäller regeln Automatisk skalning – Skala ned för helgprofilen för produktionsapptjänstplanen, skulle formeln matcha till:  

![App Service plan inflationstakt för helger baserat på automatisk skalning - Skala ner regeln.][Equation4]

Produktionsappserviceplanen kan växa med högst åtta instanser/timmar under veckan och fyra instanser/timmar under helgen. Den kan släppa instanser med en maximal hastighet på fyra instanser/timmar under veckan och sex instanser/timme under helger.

Om flera App Service-planer finns i en arbetspool måste du beräkna den *totala inflationstakten* som summan av inflationstakten för alla App Service-planer som är värd i den arbetarpoolen.

![Beräkning av total inflationshastighet för flera App Service-planer som finns i en arbetspool.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Använd inflationstakten i apptjänstplanen för att definiera regler för automatisk skalning av arbetspooler
Arbetarpooler som är värdar för App Service-planer som är konfigurerade för automatisk skalning måste tilldelas en buffert med kapacitet. Bufferten gör det möjligt för autoskalningsåtgärderna att växa och krympa App Service-planen efter behov. Den minsta bufferten skulle vara den beräknade totala app serviceplanen inflationstakt.

Eftersom det tar lite tid att tillämpa apptjänstmiljöskalningsåtgärder bör alla ändringar ta hänsyn till ytterligare efterfrågeändringar som kan inträffa medan en skalningsåtgärd pågår. För att hantera den här svarstiden rekommenderar vi att du använder den beräknade totala appserviceplanens inflationstakt som det minsta antalet instanser som läggs till för varje automatisk skalning.

Med den här informationen kan Frank definiera följande profil och regler för automatisk skalning:

![Profilerregler för automatisk skalning för LOB-exempel.][Worker-Pool-Scale]

| **Profil för automatisk skalning – Vardagar** | **Profil för automatisk skalning – Helger** |
| --- | --- |
| **Namn:** Profil för veckodag |**Namn:** Helgprofil |
| **Skala efter:** Schema- och prestandaregler |**Skala efter:** Schema- och prestandaregler |
| **Profil:** Vardagar |**Profil:** Helgen |
| **Typ:** Återkommande |**Typ:** Återkommande |
| **Målintervall:** 13 till 25 instanser |**Målintervall:** 6 till 15 instanser |
| **Dagar:** Måndag, Tisdag, Onsdag, Torsdag, Fredag |**Dagar:** Lördag, söndag |
| **Starttid:** 07:00 |**Starttid:** 09:00 |
| **Tidszon:** UTC-08 |**Tidszon:** UTC-08 |
|  | |
| **Regel för automatisk skalning (skala upp)** |**Regel för automatisk skalning (skala upp)** |
| **Resurs:** Arbetspool 1 |**Resurs:** Arbetspool 1 |
| **Mått:** ArbetareTillgängligt |**Mått:** ArbetareTillgängligt |
| **Drift:** Färre än 8 |**Drift:** Mindre än 3 |
| **Längd:** 20 minuter |**Längd:** 30 minuter |
| **Tid aggregering:** Genomsnittliga |**Tid aggregering:** Genomsnittliga |
| **Åtgärd:** Öka antalet med 8 |**Åtgärd:** Öka antalet med 3 |
| **Svalka (minuter):** 180 |**Svalka (minuter):** 180 |
|  | |
| **Regel för automatisk skalning (skala ned)** |**Regel för automatisk skalning (skala ned)** |
| **Resurs:** Arbetspool 1 |**Resurs:** Arbetspool 1 |
| **Mått:** ArbetareTillgängligt |**Mått:** ArbetareTillgängligt |
| **Drift:** Större än 8 |**Drift:** Större än 3 |
| **Längd:** 20 minuter |**Längd:** 15 minuter |
| **Tid aggregering:** Genomsnittliga |**Tid aggregering:** Genomsnittliga |
| **Åtgärd:** Minskning räkna med 2 |**Åtgärd:** Minskning räkna med 3 |
| **Kyl ner (minuter):** 120 |**Kyl ner (minuter):** 120 |

Målintervallet som definieras i profilen beräknas av de minsta instanser som definierats i profilen för apptjänstplanen + bufferten.

Det maximala intervallet skulle vara summan av alla maximala intervall för alla App Service-planer som finns i arbetarpoolen.

Ökningsantalet för uppskalningsreglerna bör anges till minst 1X appserviceplanens inflationstakt för uppskalning.

Minskningsantalet kan justeras till något mellan 1/2X eller 1X appserviceplanens inflationstakt för nedskalning.

### <a name="autoscale-for-front-end-pool"></a>Automatisk skalning för frontend-pool
Regler för automatisk skalning av klientsidan är enklare än för arbetspooler. I första hand bör du  
Se till att måttets varaktighet och timers anser att skalningsåtgärder på en App Service-plan inte är omedelbara.

I det här scenariot vet Frank att felprocenten ökar efter att klientändarna når 80 % CPU-användning och ställer in regeln för automatisk skalning för att öka instanserna enligt följande:

![Inställningar för automatisk skalning för frontend-pool.][Front-End-Scale]

| **Profil för automatisk skalning – Främre ände** |
| --- |
| **Namn:** Automatisk skalning – Främre ände |
| **Skala efter:** Schema- och prestandaregler |
| **Profil:** Vardagliga |
| **Typ:** Återkommande |
| **Målintervall:** 3 till 10 instanser |
| **Dagar:** Vardagliga |
| **Starttid:** 09:00 |
| **Tidszon:** UTC-08 |
|  |
| **Regel för automatisk skalning (skala upp)** |
| **Resurs:** Front-end pool |
| **Mått:** CPU % |
| **Drift:** Större än 60% |
| **Längd:** 20 minuter |
| **Tid aggregering:** Genomsnittliga |
| **Åtgärd:** Öka antalet med 3 |
| **Kyl ner (minuter):** 120 |
|  |
| **Regel för automatisk skalning (skala ned)** |
| **Resurs:** Arbetspool 1 |
| **Mått:** CPU % |
| **Drift:** Mindre än 30% |
| **Längd:** 20 minuter |
| **Tid aggregering:** Genomsnittliga |
| **Åtgärd:** Minskning räkna med 3 |
| **Kyl ner (minuter):** 120 |

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
