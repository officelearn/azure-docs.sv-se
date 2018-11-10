---
title: Övervaka appar i Azure App Service | Microsoft Docs
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
ms.date: 11/28/2017
ms.author: byvinyal
ms.openlocfilehash: 9c58e5c64ea3689634d7afb4c5fef08c9b21798c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244380"
---
# <a name="how-to-monitor-apps-in-azure-app-service"></a>Så här: övervaka appar i Azure App Service
[App Service](https://go.microsoft.com/fwlink/?LinkId=529714) innehåller inbyggda övervakningsfunktioner i den [Azure-portalen](https://portal.azure.com).
Azure-portalen innehåller möjligheten att granska **kvoter** och **mått** för en app som App Service-planen, hur du konfigurerar **aviseringar** och även **skalning**  automatiskt baserat på de här måtten.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Förstå kvoter och mått
### <a name="quotas"></a>Kvoter
Program som körs i App Service är under vissa *gränser* för de resurser som de kan använda. Begränsningarna definieras av den **App Service-plan** som är associerade med appen.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Om programmet körs i en **kostnadsfri** eller **delad** planerar, och sedan gränser för de resurser som använder appen definieras av **kvoter**.

Om programmet körs i en **grundläggande**, **Standard** eller **Premium** planerar, och sedan gränser för de resurser som de kan använda ställs in med den **storlek**(Liten, medel, stor) och **antal instanser** (1, 2, 3,...) för den **App Service-plan**.

**Kvoter** för **kostnadsfri** eller **delad** appar är:

* **CPU(short)**
  * Mycket Processorkraft som tillåts för det här programmet i en 5-minutersintervall. Den här kvoten återställer var femte minut.
* **CPU(Day)**
  * Totalt antal mycket Processorkraft som tillåts för det här programmet i en dag. Den här kvoten återställer var 24: e timme vid midnatt UTC.
* **Minne**
  * Total mängd minne som tillåts för det här programmet.
* **Bandbredd**
  * Totala mängden utgående bandbredd som tillåts för det här programmet i en dag.
    Den här kvoten återställer var 24: e timme vid midnatt UTC.
* **Filsystem**
  * Totala mängden lagring som är tillåtna.

Den enda kvoten för appar i **grundläggande**, **Standard**, och **Premium** planer är **filsystem**.

Mer information om specifika kvoter, gränser och funktioner som är tillgängliga för olika App Service SKU: er finns här: [tjänstbegränsningar för Azure-prenumeration](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>Kvoter
Om ett program överskrider den **CPU (kort)**, **CPU (dag)**, eller **bandbredd** kvot sedan programmet stoppas tills kvoten återställs. Under denna tid som alla inkommande begäranden resultera i en **HTTP 403**.
![][http403]

Om programmet **minne** kvot överskrids och programmet startas.

Om den **filsystem** kvoten har överskridits och sedan någon skriva åtgärden misslyckas, vilket inkluderar alla skrivningar till loggarna.

Kvoter kan ökas eller bort från din app genom att uppgradera din App Service-plan.

### <a name="metrics"></a>Mått
**Mått** ger information om appen eller beteende för App Service-plan.

För en **programmet**, tillgängliga mått är:

* **Genomsnittlig svarstid**
  * Genomsnittlig tid att hantera begäranden i ms-appen.
* **Genomsnittligt arbetsminne**
  * Genomsnittlig mängd minne i MIB som används av appen.
* **CPU-tid**
  * Hur mycket Processorkraft i sekunder som används av appen. Läs mer om det här måttet: [vs CPU CPU-tid i procent](#cpu-time-vs-cpu-percentage)
* **Data i**
  * Mängden inkommande bandbredd som används av appen i MIB.
* **Data ut**
  * Mängden utgående bandbredd som används av appen i MIB.
* **HTTP 2xx**
  * Antal begäranden, vilket resulterar i en HTTP-statuskod: > = 200 men < 300.
* **HTTP 3xx**
  * Antal begäranden, vilket resulterar i en HTTP-statuskod: > = 300 men < 400.
* **HTTP 401**
  * Antal begäranden, vilket resulterar i HTTP 401-statuskod.
* **HTTP 403**
  * Antal begäranden, vilket resulterar i HTTP 403-statuskod.
* **HTTP 404**
  * Antal begäranden, vilket resulterar i HTTP 404-statuskod.
* **HTTP 406 Ogiltigt format**
  * Antal begäranden, vilket resulterar i 406 Ogiltigt format för HTTP-statuskod.
* **HTTP 4xx**
  * Antal begäranden, vilket resulterar i en HTTP-statuskod: > = 400 men < 500.
* **HTTP-serverfel**
  * Antal begäranden, vilket resulterar i en HTTP-statuskod: > = 500 men < 600.
* **Arbetsminne**
  * Mängden minne som används av appen i MIB.
* **Begäranden**
  * Totalt antal begäranden oavsett deras resulterande HTTP-statuskod.

För en **App Service-plan**, tillgängliga mått är:

> [!NOTE]
> App Service-plan mått är bara tillgängliga för planer som **grundläggande**, **Standard**, och **Premium** nivåer.
> 
> 

* **CPU-procent**
  * Den genomsnittliga CPU som används i alla instanser av planen.
* **Minnesprocent**
  * Det genomsnittliga minne som används i alla instanser av planen.
* **Data i**
  * Den genomsnittliga inkommande bandbredd som används i alla instanser av planen.
* **Data ut**
  * Medelvärdet utgående bandbredd som används i alla instanser av planen.
* **Diskkölängd**
  * Det genomsnittliga antalet både läsa och skriva begäranden som har ställts i kö på lagring. En hög diskkölängd är en indikation på ett program som kan långsammare på grund av hög diskens i/o.
* **HTTP-Kölängd**
  * Det genomsnittliga antalet HTTP-begäranden som hade direkt i kön innan uppfylls. En hög eller ökande HTTP-Kölängd är ett symtom på en plan vid hög belastning.

### <a name="cpu-time-vs-cpu-percentage"></a>Vs CPU CPU-tid i procent
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Det finns två mått som återspeglar CPU-användning. **CPU-tid** och **CPU-procent**

**CPU-tid** är användbart för appar finns i **kostnadsfri** eller **delad** planer eftersom en av sina kvoter har definierats i CPU-minuter som används av appen.

**CPU-procent** är användbart för appar finns i **grundläggande**, **standard**, och **premium** planer eftersom de kan skalas upp. CPU-procent är en bra indikation på den totala användningen för alla instanser.

## <a name="metrics-granularity-and-retention-policy"></a>Mått granularitet och bevarandeprincip
Mått för ett program och app service-plan är inloggad och sammanställs av tjänsten med följande Precision och lagringsprinciper för:

* **Minut** kornighet mått finns kvar **30 timmar**
* **Timme** kornighet mått finns kvar **30 dagar**
* **Dag** kornighet mått finns kvar **30 dagar**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Övervakning av kvoter och mått i Azure-portalen.
Du kan granska status för de olika **kvoter** och **mått** påverkar ett program i den [Azure-portalen](https://portal.azure.com).

![][quotas]
**Kvoter** finns under Inställningar >**kvoter**. UX-Gränssnittet kan du granska: (1) namn på kvoter, (2) intervallet för återställning, (3) den aktuella gränsen och (4) aktuellt värde.

![][metrics]
**Mått** kan nås direkt från resurssidan. Du kan också anpassa diagrammet efter: (1) **klickar du på** på den och väljer (2) **redigera diagram**.
Härifrån kan du ändra (3) **tidsintervall**, (4) **diagramtyp**, 5 **mått** ska visas.  

Du kan lära dig mer om mätvärden här: [övervakar tjänstmått](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Aviseringar och automatisk skalning
Mått för en App eller App Service-plan kan vara kopplat till aviseringar. Mer information om det finns [Varningsaviseringar](../monitoring-and-diagnostics/insights-alerts-portal.md).

App Service-appar som finns i basic, standard eller premium stöd för App Service-planer **Autoskala**. Automatisk skalning kan du konfigurera regler som övervakar mått för App Service-plan. Regler kan öka eller minska instansantalet med ytterligare resurser efter behov. Regler kan också hjälpa dig att spara pengar när programmet etableras över. Du kan läsa mer om automatisk skalning här: [hur du skalar](../monitoring-and-diagnostics/insights-how-to-scale.md) och här [bästa praxis för automatisk skalning i Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](https://azure.microsoft.com/try/app-service/). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inga kreditkort krävs. Inga åtaganden.
> 
> 

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
