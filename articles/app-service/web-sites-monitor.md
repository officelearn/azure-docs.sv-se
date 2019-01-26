---
title: Övervaka appar – Azure Apptjänst | Microsoft Docs
description: Lär dig hur du övervakar appar i Azure App Service med hjälp av Azure portal.
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: a5d4d13d8e60cd7f273363a9bc385098e15cbb71
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913180"
---
# <a name="monitor-apps-in-azure-app-service"></a>Övervaka appar i Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) innehåller inbyggda övervakningsfunktioner för web apps, mobila serverdelar och API-appar i den [Azure-portalen](https://portal.azure.com).

I Azure-portalen kan du granska *kvoter* och *mått* granska App Service-planen för en app och konfigurera automatiskt *aviseringar* och *skalning* som baseras på mått.

## <a name="understand-quotas"></a>Förstå kvoter

Appar som finns i App Service är innebär vissa begränsningar för de resurser som de kan använda. Begränsningarna definieras av App Service-planen som är associerat med appen.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Om appen finns i en *kostnadsfri* eller *delad* plan, gränser för de resurser som använder appen definieras av kvoter.

Om appen finns i en *grundläggande*, *Standard*, eller *Premium* plan, gränser för de resurser som de kan använda ställs in med den *storlek* () Liten, medel, stor) och *antal instanser* (1, 2, 3 och så vidare) för App Service-planen.

Kvoter för kostnadsfria eller delade appar är:

| Kvot | Beskrivning |
| --- | --- |
| **CPU (kort)** | Hur mycket Processorkraft som tillåts för den här appen i en 5-minutersintervall. Den här kvoten återställer var femte minut. |
| **CPU (dag)** | Den totala mängden CPU som tillåts för den här appen under en dag. Den här kvoten återställer var 24: e timme vid midnatt UTC. |
| **Minne** | Den totala mängden minne som tillåts för den här appen. |
| **Bandwidth** | Den totala mängden utgående bandbredd som tillåts för den här appen under en dag. Den här kvoten återställer var 24: e timme vid midnatt UTC. |
| **Filesystem** | Den totala mängden lagring som är tillåtna. |

Den enda kvoten som gäller för appar som finns i *grundläggande*, *Standard*, och *Premium* planer är filsystem.

Mer information om specifika kvoter, gränser och funktioner som är tillgängliga för olika App Service SKU: er finns i [Azure-prenumerationstjänsten](../azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Kvoter

Om en app överskrider den *CPU (kort)*, *CPU (dag)*, eller *bandbredd* kvoten appen stoppas tills kvoten återställs. Alla inkommande begäranden resultera i ett HTTP 403-fel under denna tid.

![403 felmeddelande][http403]

Appen startas om appen minneskvoten har överskridits.

Någon skriva åtgärden misslyckas om filsystemet kvoten har överskridits. Skriva åtgärden fel omfattar alla skrivningar till loggarna.

Du kan öka eller ta bort kvoter från din app genom att uppgradera din App Service-plan.

## <a name="understand-metrics"></a>Förstå mått

Mått ger information om appen eller App Service-planens beteende.

För en app är tillgängliga mått:

| Mått | Beskrivning |
| --- | --- |
| **Genomsnittlig svarstid** | Genomsnittlig tid för app som hanterar begäranden, i millisekunder. |
| **Genomsnittligt arbetsminne** | Genomsnittlig mängden minne som används av appen, i megabyte (MiB). |
| **Anslutningar** | Antalet bundna sockets finns i sandbox-miljön (w3wp.exe och dess underordnade processer).  En bunden socket skapas genom att anropa bind()/connect() API: er och finns kvar tills nämnda socket stängs med CloseHandle()/closesocket(). |
| **CPU-tid** | Hur mycket Processorkraft som används av appen, i sekunder. Läs mer om det här måttet [vs CPU CPU-tid i procent](#cpu-time-vs-cpu-percentage). |
| **Aktuellt antal sammansättningar** | Aktuellt antal sammansättningar läses in i alla AppDomains i det här programmet. |
| **Data i** | Mängden inkommande bandbredd som används av appen i MiB. |
| **Data Out** | Mängden utgående bandbredd som används av appen i MiB. |
| **Generation 0-skräpinsamlingar** | Hur många gånger som generation 0-objekt har samlats in sedan början av app-processen. Högre generation generationer inkluderar alla lägre generation generationer.|
| **Generation 1-skräpinsamlingar** | Hur många gånger som generation 1-objekt har samlats in sedan början av app-processen. Högre generation generationer inkluderar alla lägre generation generationer.|
| **Generation 2-skräpinsamlingar** | Hur många gånger som generation 2-objekt har samlats in sedan början av app-processen.|
| **Antal referenser** | Det totala antalet handtag som för tillfället är öppna av app-process.|
| **Http 2xx** | Totalt antal begäranden, vilket resulterar i minst en HTTP-statuskod 200 men < 300. |
| **Http 3xx** | Totalt antal begäranden, vilket resulterar i en HTTP-statuskod minst 300 men < 400. |
| **Http 401** | Totalt antal begäranden, vilket resulterar i HTTP 401-statuskod. |
| **Http 403** | Totalt antal begäranden, vilket resulterar i HTTP 403-statuskod. |
| **Http 404** | Totalt antal begäranden, vilket resulterar i HTTP 404-statuskod. |
| **Http 406** | Totalt antal begäranden, vilket resulterar i 406 Ogiltigt format för HTTP-statuskod. |
| **HTTP 4xx** | Totalt antal begäranden, vilket resulterar i en HTTP-statuskod minst 400 men < 500. |
| **HTTP-serverfel** | Totalt antal begäranden, vilket resulterar i en HTTP-statuskod minst 500 men < 600. |
| **I/O övrigt byte Per sekund** | Den hastighet som app-processen skickar byte till i/o-åtgärder som inte inkluderar data, till exempel kontrollåtgärder.|
| **I/O övriga åtgärder Per sekund** | Den hastighet med vilken processen app utfärdar i/o-åtgärder som varken är läs- eller skrivåtgärder.|
| **I/O lästa byte Per sekund** | Den hastighet med vilken app-processen läser byte från i/o-åtgärder.|
| **I/o-läsningsåtgärder Per sekund** | Den hastighet med vilken app-process begär Läs i/o-åtgärder.|
| **I/O skrivna byte Per sekund** | Den hastighet med vilken app processen skriver byte till i/o-åtgärder.|
| **I/o-skrivåtgärder Per sekund** | Den hastighet med vilken processen app utfärdar i/o-skrivåtgärder.|
| **Arbetsminne** | Den aktuella mängden minne som används av appen i MiB. |
| **Privata byte** | Privata byte är processens aktuella storlek i byte av minne som har allokerats till app-processen som inte kan delas med andra processer.|
| **Begäranden** | Det totala antalet begäranden oavsett deras resulterande HTTP-statuskod. |
| **Förfrågningar i programkö** | Antal begäranden i programbegärandekön.|
| **Antal tråd** | Antal trådar som är aktiva i app-processen.|
| **Totalt antal Appdomäner** | Det aktuella antalet AppDomains läses in i det här programmet.|
| **Totalt antal Appdomäner som tas bort från minnet** | Det totala antalet AppDomains tas bort från minnet sedan programmet.|


För en App Service-plan är tillgängliga mått:

> [!NOTE]
> App Service-plan mått är bara tillgängliga för planer som *grundläggande*, *Standard*, och *Premium* nivåer.
> 

| Mått | Beskrivning |
| --- | --- |
| **CPU-procent** | Den genomsnittliga CPU som används i alla instanser av planen. |
| **Minnesprocent** | Det genomsnittliga minne som används i alla instanser av planen. |
| **Data i** | Den genomsnittliga inkommande bandbredd som används i alla instanser av planen. |
| **Data Out** | Medelvärdet utgående bandbredd som används i alla instanser av planen. |
| **Disk Queue Length** | Det genomsnittliga antalet både läsa och skriva begäranden som har ställts i kö på lagring. En hög diskkölängd är en indikation på en app som kan långsammare på grund av hög diskens i/o. |
| **Http Queue Length** | Det genomsnittliga antalet HTTP-begäranden som hade direkt i kön innan uppfylls. En hög eller ökande HTTP-Kölängd är ett symtom på en plan vid hög belastning. |

### <a name="cpu-time-vs-cpu-percentage"></a>Vs CPU CPU-tid i procent
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Det finns två mått som återspeglar CPU-användning:

**CPU-tid**: Användbart för appar finns på den kostnadsfria eller delade planer, eftersom en av sina kvoter har definierats i CPU-minuter som används av appen.

**CPU-procent**: Användbart för appar i Basic, Standard och Premium-planer, eftersom de kan skalas upp. CPU-procent är en bra indikation på den totala användningen för alla instanser.

## <a name="metrics-granularity-and-retention-policy"></a>Princip för mått granularitet och kvarhållning
Mått för en app och app service-plan är inloggad och aggregeras av tjänsten, med följande Precision och lagringsprinciper för:

* **Minut** kornighet mått finns kvar 30 timmar.
* **Timme** kornighet mått ska bevaras i 30 dagar.
* **Dag** kornighet mått ska bevaras i 30 dagar.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Övervakning av kvoter och mått i Azure portal
För att granska statusen för de olika kvoter och mått som påverkar en app, gå till den [Azure-portalen](https://portal.azure.com).

![Kvoter diagram i Azure portal][quotas]

Om du vill söka efter kvoter, Välj **inställningar** > **kvoter**. I diagrammet, kan du granska: 
1. Kvotnamn.
1. Dess intervallet för återställning.
1. Den aktuella gränsen.
1. Dess aktuella värde.

![Måttdiagram i Azure-portalen][metrics] du kan komma åt mått direkt från den **Resource** sidan. Så här anpassar diagrammet: 
1. Välj diagrammet.
1. Välj **redigera diagram**.
1. Redigera den **tidsintervall**.
1. Redigera den **diagramtyp**.
1. Redigera de mått som du vill visa.  

Läs mer om mätvärden i [övervakar tjänstmått](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Aviseringar och automatisk skalning
Mått för en app eller en App Service plan kan vara kopplat till aviseringar. Mer information om varningsaviseringar finns [här](../monitoring-and-diagnostics/insights-alerts-portal.md).

App Service-appar som finns i Basic, Standard eller Premium-App Service-planer stöd för automatisk skalning. Med automatisk skalning kan konfigurera du regler som övervakar mått för App Service-plan. Regler kan öka eller minska instansantalet, vilket kan ge ytterligare resurser efter behov. Regler kan också hjälpa dig att spara pengar när appen konfigureras felaktigt.

Läs mer om automatisk skalning, [så här skalar du](../monitoring-and-diagnostics/insights-how-to-scale.md) och [bästa praxis för automatisk skalning i Azure Monitor](../azure-monitor/platform/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png