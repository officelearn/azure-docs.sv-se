---
title: Övervaka appar
description: Lär dig hur du övervakar appar i Azure App Service med hjälp av Azure-portalen. Förstå de kvoter och mått som rapporteras.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: d84340730391abd7dba4d13202503d37941c09b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500430"
---
# <a name="monitor-apps-in-azure-app-service"></a>Övervaka appar i Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) tillhandahåller inbyggda övervakningsfunktioner för webbappar, mobilappar och API-appar i [Azure-portalen](https://portal.azure.com).

I Azure-portalen kan du granska *kvoter* och *mått* för en app- och App Service-plan och ställa in *aviseringar* och *automatisk skalning av* regelbaserade mått.

## <a name="understand-quotas"></a>Förstå kvoter

Appar som finns i App Service omfattas av vissa begränsningar för de resurser de kan använda. Gränserna definieras av apptjänstplanen som är associerad med appen.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Om appen finns i en *kostnadsfri* eller *delad* plan definieras gränserna för de resurser som appen kan använda av kvoter.

Om appen finns i en *Basic-,* *Standard-* eller *Premium-plan* anges gränserna för de resurser som de kan använda efter *storleken* (Small, Medium, Large) och *instansantalet* (1, 2, 3 och så vidare) i App Service-planen.

Kvoter för kostnadsfria eller delade appar är:

| Kvot | Beskrivning |
| --- | --- |
| **CPU (kort)** | Mängden CPU som tillåts för den här appen i ett 5-minutersintervall. Den här kvoten återställs var femte minut. |
| **CPU (Dag)** | Den totala mängden CPU som tillåts för den här appen på en dag. Den här kvoten återställs var 24:e timme vid midnatt UTC. |
| **Minne** | Den totala mängden minne som tillåts för den här appen. |
| **Bandbredd** | Den totala mängden utgående bandbredd som tillåts för den här appen under en dag. Den här kvoten återställs var 24:e timme vid midnatt UTC. |
| **Filsystem** | Den totala mängden lagringsutrymme som tillåts. |

Den enda kvot som gäller för appar som finns i *Basic,* *Standard*och *Premium* är Filsystem.

Mer information om specifika kvoter, begränsningar och funktioner som är tillgängliga för de olika SKU:erna för App-tjänst finns i [Azure Subscription-tjänstgränser](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Tillämpning av kvoter

Om en app överskrider *CPU(kort)*, *CPU (Dag)* eller *bandbreddskvot* stoppas appen tills kvoten återställs. Under den här tiden resulterar alla inkommande begäranden i ett HTTP 403-fel.

![403 felmeddelande][http403]

Om appen Minneskvot överskrids startas appen om.

Om filsystemkvoten överskrids misslyckas alla skrivåtgärder. Skrivåtgärdsfel inkluderar alla skrivningar till loggar.

Du kan öka eller ta bort kvoter från appen genom att uppgradera apptjänstplanen.

## <a name="understand-metrics"></a>Förstå mått

> [!NOTE]
> **Filsystemanvändning** är ett nytt mått som distribueras globalt, inga data förväntas om du inte har vitlistats för privat förhandsgranskning.
> 

> [!IMPORTANT]
> **Genomsnittlig svarstid** kommer att inaktuell för att undvika förväxling med måttaggregeringar. Använd **svarstid** som ersättning.

Mått ger information om appen eller apptjänstplanens beteende.

För en app är de tillgängliga måtten:

| Mått | Beskrivning |
| --- | --- |
| **Svarstid** | Den tid det tar för appen att betjäna förfrågningar, på några sekunder. |
| **Genomsnittlig svarstid (föråldrad)** | Den genomsnittliga tid det tar för appen att visa begäranden, på några sekunder. |
| **Genomsnittligt arbetsminne** | Den genomsnittliga mängden minne som används av appen, i megabyte (MiB). |
| **Anslutningar** | Antalet bundna sockets som finns i sandlådan (w3wp.exe och dess underordnade processer).  En bunden socket skapas genom att anropa bind()/connect() API:er och förblir tills nämnda socket är stängd med CloseHandle()/closesocket(). |
| **CPU-tid** | Mängden cpu som förbrukas av appen, på några sekunder. Mer information om det här måttet finns i [CPU-tid jämfört med CPU-procent](#cpu-time-vs-cpu-percentage). |
| **Aktuella sammansättningar** | Det aktuella antalet sammansättningar som har lästs in i alla AppDomäner i det här programmet. |
| **Data i** | Mängden inkommande bandbredd som förbrukas av appen i MiB. |
| **Data ut** | Mängden utgående bandbredd som förbrukas av appen i MiB. |
| **Användning av filsystem** | Procentandel av filsystemkvoten som förbrukas av appen. |
| **Gen 0 Sophämtningar** | Antalet gånger generation 0-objekten är skräp som samlats in sedan appprocessen startade. Högre generationens GCs inkluderar alla lägre generationens GCs.|
| **Gen 1 Sophämtningar** | Antalet gånger generation 1-objekten är skräp som samlats in sedan appprocessen startade. Högre generationens GCs inkluderar alla lägre generationens GCs.|
| **Gen 2 Sophämtningar** | Antalet gånger generation 2 objekt är skräp samlas in sedan början av appprocessen.|
| **Antal referenser** | Det totala antalet handtag som för närvarande är öppna av appprocessen.|
| **Http 2xx (på andra sätt)** | Antalet begäranden som resulterar i en HTTP-statuskod ≥ 200 men < 300. |
| **Http 3xx (på)** | Antalet begäranden som resulterar i en HTTP-statuskod ≥ 300 men < 400. |
| **Http 401** | Antalet begäranden som resulterar i HTTP 401-statuskod. |
| **Http 403** | Antalet begäranden som resulterar i HTTP 403-statuskod. |
| **Http 404** | Antalet begäranden som resulterar i HTTP 404-statuskod. |
| **Http 406** | Antalet begäranden som resulterar i HTTP 406-statuskod. |
| **Http 4xx (på)** | Antalet begäranden som resulterar i en HTTP-statuskod ≥ 400 men < 500. |
| **Http-serverfel** | Antalet begäranden som resulterar i en HTTP-statuskod ≥ 500 men < 600. |
| **IO Andra byte per sekund** | Den hastighet med vilken appprocessen utfärdar byte till I/O-åtgärder som inte omfattar data, till exempel kontrollåtgärder.|
| **IO Övriga transaktioner per sekund** | Den hastighet med vilken appprocessen utfärdar I/O-åtgärder som inte läses eller skrivåtgärder.|
| **IO läs byte per sekund** | Den hastighet med vilken appprocessen läser byte från I/O-åtgärder.|
| **IO läsa operationer per sekund** | Den hastighet med vilken appprocessen utfärdar läs-I/O-åtgärder.|
| **IO skriv byte per sekund** | Den hastighet med vilken appprocessen skriver byte till I/O-åtgärder.|
| **IO-skrivåtgärder per sekund** | Den hastighet med vilken appprocessen utfärdar skriv-I/O-åtgärder.|
| **Arbetsminne** | Den aktuella mängden minne som används av appen i MiB. |
| **Privata byte** | Privata byte är den aktuella storleken, i byte, av minne som appprocessen har allokerat som inte kan delas med andra processer.|
| **Begäranden** | Det totala antalet begäranden oavsett deras resulterande HTTP-statuskod. |
| **Begäranden i programkö** | Antalet begäranden i kön för programbegäran.|
| **Antal trådar** | Antalet trådar som för närvarande är aktiva i appprocessen.|
| **Totalt antal appdomäner** | Det aktuella antalet AppDomains som läses in i det här programmet.|
| **Totalt antal appdomäner har tagits bort** | Det totala antalet AppDomains lossas sedan starten av programmet.|


För en App Service-plan är de tillgängliga måtten:

> [!NOTE]
> Mätvärden för App Service-abonnemang är endast tillgängliga för abonnemang på nivåerna *Basic,* *Standard*och *Premium.*
> 

| Mått | Beskrivning |
| --- | --- |
| **CPU-procent** | Den genomsnittliga processorn som används i alla instanser av planen. |
| **Minnesprocent** | Det genomsnittliga minne som används i alla instanser av planen. |
| **Data i** | Den genomsnittliga inkommande bandbredd som används i alla instanser av planen. |
| **Data ut** | Den genomsnittliga utgående bandbredd som används i alla instanser av planen. |
| **Längd på diskkö** | Genomsnittligt antal både läs- och skrivbegäranden som köades vid lagring. En hög diskkölängd är en indikation på en app som kan vara långsammare på grund av överdriven disk-I/O. |
| **Http kö längd** | Genomsnittligt antal HTTP-begäranden som måste sitta i kön innan de uppfylldes. En hög eller ökande HTTP-kölängd är ett symptom på en plan under tung belastning. |

### <a name="cpu-time-vs-cpu-percentage"></a>CPU-tid vs CPU-procent
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Det finns två mått som återspeglar CPU-användning:

**CPU-tid:** Användbart för appar som finns i kostnadsfria eller delade abonnemang, eftersom en av deras kvoter definieras i CPU-minuter som används av appen.

**CPU-procent:** Användbart för appar som finns i Basic-, Standard- och Premium-abonnemang, eftersom de kan skalas ut. CPU-procent är en bra indikation på den totala användningen i alla instanser.

## <a name="metrics-granularity-and-retention-policy"></a>Måttgranularitet och bevarandeprincip
Mått för en app- och apptjänstplan loggas och aggregeras av tjänsten, med följande granulariteter och bevarandeprinciper:

* **Minute** Minutgranularitetsmått sparas i 30 timmar.
* **Hour** Timgranularitetsmått sparas i 30 dagar.
* **Dag** granularitet mått hålls i 30 dagar.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Övervaka kvoter och mått i Azure-portalen
Om du vill granska statusen för de olika kvoter och mått som påverkar en app går du till [Azure-portalen](https://portal.azure.com).

![Kvotdiagram i Azure-portalen][quotas]

Om du vill söka efter kvoter väljer du **Inställningar** > **kvoter**. I diagrammet kan du granska: 
1. Kvotnamnet.
1. Dess återställningsintervall.
1. Dess nuvarande gräns.
1. Dess aktuella värde.

![Måttdiagram i Azure-portalen][metrics] Du kan komma åt mått direkt från sidan **resursöversikt.** Här ser du diagram som representerar några av appmåtten.

Genom att klicka på något av dessa diagram kommer du till måttvyn där du kan skapa anpassade diagram, fråga olika mått och mycket mer. 

Mer information om mått finns i [Övervaka servicemått](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Varningar och automatisk skalning
Mått för en app eller en App Service-plan kan anslutas till aviseringar. Mer information om varningsaviseringar finns [här](../monitoring-and-diagnostics/insights-alerts-portal.md).

App Service-appar som finns i Grundläggande eller högre App Service-abonnemang stöder automatisk skalning. Med automatisk skalning kan du konfigurera regler som övervakar måtten för App Service-planen. Regler kan öka eller minska antalet instanser, vilket kan ge ytterligare resurser efter behov. Regler kan också hjälpa dig att spara pengar när appen är överetablering.

Mer information om automatisk skalning finns i [Så här skalar](../monitoring-and-diagnostics/insights-how-to-scale.md) och [metodtips för automatisk skalning av Azure Monitor](../azure-monitor/platform/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png