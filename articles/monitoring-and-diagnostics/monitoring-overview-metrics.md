---
title: Översikt över mått i Microsoft Azure | Microsoft Docs
description: Översikt över mått och deras användning i Microsoft Azure
author: anirudhcavale
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 405ec51c-0946-4ec9-b535-60f65c4a5bd1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: ancav
ms.openlocfilehash: 88020b27403f06ab8f1c0a1c45b798db02f517f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Översikt över mått i Microsoft Azure
Den här artikeln beskriver vilka mått som finns i Microsoft Azure sina fördelar och hur du börjar använda dem.  

## <a name="what-are-metrics"></a>Vad är mått?
Azure-Monitor kan du använda telemetri för att få insyn i prestanda och hälsotillståndet för dina arbetsbelastningar i Azure. Viktigaste är Azure telemetridata mätvärdena (kallas även prestandaräknare) sänds av mest Azure-resurser. Azure-Monitor finns flera sätt att konfigurera och använda de här måtten för övervakning och felsökning.

## <a name="what-can-you-do-with-metrics"></a>Vad kan du göra med mått?
Mått är en del av telemetri och gör att du kan göra följande:

* **Spåra prestanda** för din resurs (till exempel en VM, webbplats eller logik app) genom att rita upp dess mått i en portal diagram och fästa diagrammet på en instrumentpanel.
* **Få ett meddelande om ett problem** som påverkar prestandan för din resurs när en måttet överskrider ett visst tröskelvärde.
* **Konfigurera automatiska åtgärder**, till exempel autoskalning en resurs eller startar en runbook när en måttet överskrider ett visst tröskelvärde.
* **Utföra avancerade analyser** eller rapportering på prestanda eller användning trender för din resurs.
* **Arkivera** prestanda eller hälsa historiken för din resurs **för efterlevnad och granskning** syften.

## <a name="what-are-the-characteristics-of-metrics"></a>Vad är egenskaper hos mått?
Mått har följande egenskaper:

* Alla mätvärden har **minut frekvens** (om inget annat anges i definitionen för ett mått). Du får ett värde varje minut från din resurs så att du nära realtid insyn i tillstånd och hälsotillståndet för din resurs.
* Mått är **tillgängliga omedelbart**. Du behöver inte välja eller konfigurera ytterligare diagnostik.
* Du kan komma åt **93 dagar tidigare** för varje mått. Du kan snabbt se senaste och en månadsvis trender i prestanda eller hälsotillståndet för din resurs.
* Vissa mått kan ha namn-värdepar attribut kallas **dimensioner**. Dessa kan du ytterligare segmentera och utforska ett mått på ett bättre sätt.

Du kan också:

* Konfigurera ett mått **avisering regel som skickar ett meddelande eller tar automatisk åtgärd** om måttet överskrider tröskelvärdet som du har angett. Autoskala är en automatisk specialåtgärd som gör att du kan skala upp din resurs för att uppfylla inkommande begäranden eller läses in på webbplatsen eller datorresurser. Du kan konfigurera en regel för automatiska inställningen att skala in eller ut baserat på ett mått som passerar ett tröskelvärde.

* **Väg** alla mått Application Insights eller logganalys (OMS) för att aktivera omedelbara analytics, Sök och anpassade aviseringar på mått data från dina resurser. Du kan också strömma mått till en Händelsehubb, så att du kan sedan dirigera dem till Azure Stream Analytics eller anpassade appar för analys i nära realtid. Du ställa in Event Hub strömning med diagnostikinställningar.

* **Arkivera mått till lagring** längre bevarande eller använda dem för offline-rapportering. Du kan vidarebefordra dina till Azure Blob storage när du konfigurerar inställningar för diagnostik för din resurs.

* Enkelt identifiera, komma åt, och **visa alla** via Azure portal när du väljer en resurs och rita mått i ett diagram.

* **Använda** mätvärden via den nya Azure övervakaren REST API: er.

* **Frågan** mått med hjälp av PowerShell-cmdlets eller plattformsoberoende REST API.

  ![Routning av mätvärden i Azure-Monitor](./media/monitoring-overview-metrics/Metrics_Overview_v4.png)

## <a name="access-metrics-via-the-portal"></a>Åtkomst till mätvärden via portalen
Följande är en snabb genomgång av hur du skapar ett mått diagram med hjälp av Azure portal.

### <a name="to-view-metrics-after-creating-a-resource"></a>Visa mått när du har skapat en resurs
1. Öppna Azure Portal.
2. Skapa en Azure App Service-webbplats.
3. När du skapar en webbplats, går du till den **översikt** bladet på webbplatsen.
4. Du kan visa nya mått som en **övervakning** panelen. Du kan redigera panelen och välja fler mått.

   ![Mått på en resurs i Azure-Monitor](./media/monitoring-overview-metrics/MetricsOverview1.png)

### <a name="to-access-all-metrics-in-a-single-place"></a>Åtkomst till alla mått i en enda plats
1. Öppna Azure Portal.
2. Navigera till den nya **övervakaren** fliken och sedan väljer du den **mått** alternativet under.
3. Välj din prenumeration, resursgrupp och namnet på resursen från den nedrullningsbara listan.
4. Visa listan över tillgängliga mått. Välj det mått som du är intresserad av och rita den sedan.
5. Du kan fästa den på instrumentpanelen genom att klicka på PIN-koden i det övre högra hörnet.

   ![Åtkomst till alla mått i en enda plats i Azure-Monitor](./media/monitoring-overview-metrics/MetricsOverview2.png)

> [!NOTE]
> Du kan komma åt värdnivå mått från virtuella datorer (Azure Resource Manager-baserat) och virtuella datorn anger utan några ytterligare inställningar för diagnostik. Dessa nya värdnivå mått är tillgängliga för Windows och Linux-instanser. De här måtten är ska inte förväxlas med det att du har åtkomst till när du aktiverar Azure-diagnostik på virtuella datorer eller virtuella datorn anger gäst-OS-nivå. Mer information om hur du konfigurerar diagnostik finns [vad är Microsoft Azure-diagnostik](../azure-diagnostics.md).
>
>

Övervakare för Azure har också en ny mått diagram upplevelse som är tillgängliga i förhandsversionen. Det här upplevelsen kan användare överlägg mått från flera resurser i ett diagram. Användare kan också rita segment, och filtrera flerdimensionella mått med mätvärdet nytt diagram upplevelse. Mer information [Klicka här](https://aka.ms/azuremonitor/new-metrics-charts)

## <a name="access-metrics-via-the-rest-api"></a>Åtkomst till mätvärden via REST API
Azure mått kan nås via API: er för Azure-Monitor. Det finns två API: er som hjälper dig identifiera och komma åt mått:

* Använd den [mått för Azure-Monitor definitioner REST API](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) att komma åt listan över mått och dimensioner som är tillgängliga för en tjänst.
* Använd den [Azure övervakaren mått REST API](https://docs.microsoft.com/rest/api/monitor/metrics) segmentera, filtrera och komma åt data faktiska mått.

> [!NOTE]
> Den här artikeln beskriver mätvärden via den [nya API: et för mått](https://docs.microsoft.com/rest/api/monitor/) för Azure-resurser. API-versionen för den nya måttdefinitioner och mått API: er är 2018-01-01. Äldre måttdefinitioner och mått kan nås med API-version 2014-04-01.
>
>

En mer detaljerad genomgång med hjälp av Azure övervakaren REST-API: er finns [REST-API för Azure-Monitor genomgången](monitoring-rest-api-walkthrough.md).

## <a name="export-metrics"></a>Exportera mått
Går du till den **diagnostikinställningarna** bladet under den **övervakaren** fliken och visa exportalternativ för mått. Du kan välja mått (och diagnostikloggar) ska vidarebefordras till Blob storage till Händelsehubbar i Azure eller till OMS för användningsområden som har nämnts tidigare i den här artikeln.

 ![Exportalternativ för mätvärden i Azure-Monitor](./media/monitoring-overview-metrics/MetricsOverview3.png)

Du kan konfigurera detta via Resource Manager-mallar [PowerShell](insights-powershell-samples.md), [Azure CLI](insights-cli-samples.md), eller [REST API: er](https://msdn.microsoft.com/library/dn931943.aspx).

## <a name="take-action-on-metrics"></a>Utför en åtgärd på mått
Du kan konfigurera Varningsregler eller automatiska inställningar för att ta emot meddelanden eller utför automatiserade åtgärder på mätvärden.

### <a name="configure-alert-rules"></a>Konfigurera Varningsregler
Du kan konfigurera Varningsregler på mått. Dessa Varningsregler kan kontrollera om ett mått har passerat ett visst tröskelvärde. Det finns två mått aviseringar funktionerna i Azure-Monitor.

Mått aviseringar: de kan sedan meddela dig via e-post eller eller en webhook som kan användas för att köra alla anpassade skript. Du kan också använda webhooken för att konfigurera integration produkten från tredje part.

 ![Mått och Varningsregler i Azure-Monitor](./media/monitoring-overview-metrics/MetricsOverview4.png)

Nära realtid aviseringar (förhandsversion): dessa har möjlighet att övervaka flera mått och tröskelvärden för en resurs och meddela dig via en [grupp](/monitoring-action-groups.md). Lär dig mer om [nära realtid mått aviseringar här](https://aka.ms/azuremonitor/near-real-time-alerts).


### <a name="autoscale-your-azure-resources"></a>Autoskala din Azure resurser
Vissa Azure-resurser stöd för skalning in eller ut av flera instanser för att hantera dina arbetsbelastningar. Autoskala gäller för App Service (webbprogram), skalningsuppsättningar i virtuella datorer och klassiska Azure-molntjänster. Du kan konfigurera automatiska regler för att skala in eller ut när ett mått som påverkar din arbetsbelastning överskrider ett tröskelvärde som du anger. Mer information finns i [översikt över autoskalning](monitoring-overview-autoscale.md).

 ![Mått och Autoskala i Azure-Monitor](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="learn-about-supported-services-and-metrics"></a>Lär dig mer om tjänster som stöds och mått
Du kan visa en detaljerad lista över alla tjänster som stöds och deras mått på [Azure-Monitor mått--stöds mått per resurstypen](monitoring-supported-metrics.md).

## <a name="next-steps"></a>Nästa steg
Se länkarna i den här artikeln. Dessutom lär dig mer om:  

* [Vanliga mätvärden för autoskalning](insights-autoscale-common-metrics.md)
* [Så här skapar du Varningsregler](insights-alerts-portal.md)
* [Analysera loggar från Azure storage med logganalys](../log-analytics/log-analytics-azure-storage.md)
