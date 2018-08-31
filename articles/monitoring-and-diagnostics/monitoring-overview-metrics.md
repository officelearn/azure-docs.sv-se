---
title: Översikt över mått i Microsoft Azure
description: Översikt över mått och deras användning i Microsoft Azure
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/05/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: d61ac48aa7c51bc4b215a7d56b1bbedfdc613f9f
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287564"
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Översikt över mått i Microsoft Azure
Den här artikeln beskriver vilka mått som finns i Microsoft Azure sina fördelar och hur du börjar använda dem.  

## <a name="what-are-metrics"></a>Vad är mått?
Azure Monitor kan du använda telemetri för att få insyn i prestanda och hälsa för arbetsbelastningar på Azure. Viktigaste är Azure telemetridata mått (kallas även prestandaräknare) skickas från de flesta Azure-resurser. Azure Monitor innehåller flera olika sätt att konfigurera och använda de här måtten för övervakning och felsökning.

## <a name="what-are-the-characteristics-of-metrics"></a>Vad är egenskaper för mått?
Mått har följande egenskaper:

* Alla mått har **en minut frekvens** (om inget annat anges i definitionen för ett mått). Du får ett måttvärde varje minut från din resurs, vilket ger dig en insyn i tillstånd och hälsa för din resurs realtid.
* Mått är **tillgängliga omedelbart**. Du behöver inte välja eller skapa ytterligare diagnostik.
* Du kan komma åt **93 dagars historik** för varje mått. Du kan snabbt leta efter de senaste och månatliga trenderna i prestanda eller hälsotillståndet för din resurs.
* Vissa mått kan ha namn-värdepar attribut kallas **dimensioner**. Dessa kan du ytterligare segmentera och utforska ett mått på ett mer meningsfulla sätt.

## <a name="what-can-you-do-with-metrics"></a>Vad kan du göra med mått?
Mått kan du utföra följande uppgifter:


- Konfigurera ett mått **avisering regel som skickar ett meddelande eller tar automatisk åtgärd** när måtten överskrider tröskelvärdet som du har angett. Åtgärder styrs via [åtgärdsgrupper](monitoring-action-groups.md). Exempel åtgärder omfattar e-post, telefon och SMS-meddelanden, anropa en webhook som startar en runbook och mycket mer. **Automatisk skalning** är en automatiserad specialåtgärd som gör det möjligt att skala du är en resurs upp och ned för att hantera belastningen men Håll kostnaderna lägre när inte under belastning. Du kan konfigurera en regel för inställning av automatisk skalning att skala in eller ut baserat på ett mått som korsar ett tröskelvärde.
- **Väg** alla mått till *Programinsikter* eller *Log Analytics* så att direkta analyser, sökning och anpassade varningar på mätvärden från dina resurser. Du kan också strömma mått för att en *Event Hub*, så att du kan sedan dirigera dem till Azure Stream Analytics eller anpassade appar för analys i nära realtid. Du ställer in Event Hub strömning med diagnostikinställningar.
- **Arkivera** prestanda eller hälsotillstånd historiken för dina resurser för efterlevnad, granskning eller rapportering offline.  Du kan dirigera dina mått till Azure Blob storage när du konfigurerar diagnostikinställningar för din resurs.
- Använd den **Azure-portalen** för att identifiera, komma åt och visa alla mått när du väljer en resurs och rita mått i ett diagram. Du kan spåra prestanda för din resurs (till exempel en virtuell dator, en webbplats eller en logikapp) genom att fästa diagrammet på instrumentpanelen.  
- **Utföra avancerade analyser** eller rapporter på prestanda och användning trender för din resurs.
- **Fråga** mått med hjälp av PowerShell-cmdlets eller plattformsoberoende REST API.
- **Använda** mätvärden via den nya Azure Monitor REST API: er.

  ![Routning av mått i Azure Monitor](./media/monitoring-overview-metrics/Metrics_Overview_v4.png)

## <a name="access-metrics-via-the-portal"></a>Mått för åtkomst via portalen
Följande är en snabb genomgång av hur du skapar ett måttdiagram med hjälp av Azure portal.

### <a name="to-view-metrics-after-creating-a-resource"></a>Visa mått när du har skapat en resurs
1. Öppna Azure Portal.
2. Skapa en Azure App Service-webbplats.
3. När du skapar en webbplats går du till den **översikt** bladet av webbplatsen.
4. Du kan visa nya mått som en **övervakning** panelen. Du kan redigera panelen och välja fler mått.

   ![Mått på en resurs i Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview1.png)

### <a name="to-access-all-metrics-in-a-single-place"></a>Åtkomst till alla mått i en enda plats
1. Öppna Azure Portal.
2. Gå till den nya **övervakaren** fliken och sedan väljer du den **mått** alternativet under den.
3. Välj din prenumeration, resursgrupp och namnet på resursen från den nedrullningsbara listan.
4. Visa listan över tillgängliga mått. Välj sedan de mått som du är intresserad av och rita den.
5. Du kan fästa den på instrumentpanelen genom att klicka på PIN-koden i det övre högra hörnet.

   ![Få åtkomst till alla mått på en enda plats i Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview2.png)

> [!NOTE]
> Du kan komma åt värdnivå mått från virtuella datorer (Azure Resource Manager-baserade) och VM scale sets för utan ytterligare diagnostiska inställningar. Dessa nya mått på värdnivå är tillgängliga för Windows och Linux-instanser. De här måtten är ska inte förväxlas med de mått som gäst-OS-nivå är att du har åtkomst till när du stänger på Azure-diagnostik på virtuella datorer eller VM scale sets. Läs mer om att konfigurera diagnostik i [vad är Microsoft Azure Diagnostics](../azure-diagnostics.md).
>
>

Azure Monitor har också en nya mått som diagram upplevelse som är tillgänglig som förhandsversion. Den här upplevelsen kan användare företagsdataskydd mått från flera resurser på ett diagram. Användare kan också rita segment, och filtrera flerdimensionella mått med den här nya mått som diagram upplevelse. Mer [Klicka här](https://aka.ms/azuremonitor/new-metrics-charts)

## <a name="access-metrics-via-the-rest-api"></a>Mått för åtkomst via REST API
Azure-mått kan nås via API: er för Azure Monitor. Det finns två API: er som hjälper dig att upptäcka och komma åt mått:

* Använd den [REST API för Azure Monitor måttdefinitioner](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) att komma åt listan över mått och dimensioner, som är tillgängliga för en tjänst.
* Använd den [Azure Monitor Metrics REST API](https://docs.microsoft.com/rest/api/monitor/metrics) att segmentera, filtrera och komma åt faktiska mätvärden.

> [!NOTE]
> Den här artikeln beskriver mätvärden via den [nya API: et för mått](https://docs.microsoft.com/rest/api/monitor/) för Azure-resurser. API-versionen för den nya definitioner av mätvärden och mått API: er är 2018-01-01. Definitioner av mätvärden för äldre och mått kan nås med API-version 2014-04-01.
>
>

En mer detaljerad genomgång med hjälp av Azure Monitor REST-API: er finns i [Azure Monitor REST API-genomgång](monitoring-rest-api-walkthrough.md).

## <a name="export-metrics"></a>Exportera mått
Går du till den **diagnostikinställningar** bladet under den **övervakaren** fliken och visa exportalternativ för mått. Du kan välja mått och diagnostikloggar som dirigeras till Blob storage till Azure Event Hubs eller till Log Analytics för användningsfall som har nämnts tidigare i den här artikeln.

 ![Exportalternativ för mått i Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview3.png)

Du kan konfigurera detta via Resource Manager-mallar, [PowerShell](insights-powershell-samples.md), [Azure CLI](insights-cli-samples.md), eller [REST API: er](https://msdn.microsoft.com/library/dn931943.aspx).

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
>

## <a name="take-action-on-metrics"></a>Vidta åtgärder för mått
Du kan konfigurera Varningsregler eller inställningarna för automatisk skalning för att ta emot meddelanden eller vidta automatiska åtgärder på måttdata.

### <a name="configure-alert-rules"></a>Konfigurera Varningsregler
Du kan konfigurera Varningsregler för mått. Dessa Varningsregler kan kontrollera om ett mått har passerat ett visst tröskelvärde. Det finns två metriska varningar funktioner som erbjuds av Azure Monitor.

Måttaviseringar: de kan sedan meddelar dig via e-post eller utlöses en webhook som kan användas för att köra eventuella anpassade skript. Du kan också använda webhooken för att konfigurera produkten från tredje part-integration.

 ![Mått och aviseringsregler i Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview4.png)

Nyare måttaviseringar möjlighet att övervaka flera mått och tröskelvärden för en resurs och meddelar dig via en [åtgärdsgrupp](monitoring-action-groups.md). Läs mer om [nyare aviseringar här](https://aka.ms/azuremonitor/near-real-time-alerts).


### <a name="autoscale-your-azure-resources"></a>Autoskala din Azure-resurser
Vissa Azure-resurser stöd för att skala in eller ut över flera instanser för att hantera dina arbetsbelastningar. Automatisk skalning gäller för App Service (Webbappar), VM-skalningsuppsättningar och klassiska Azure-molntjänster. Du kan konfigurera regler för automatisk skalning att skala in eller ut när ett mått som påverkar din arbetsbelastning överskrider ett tröskelvärde som du anger. Mer information finns i [översikt över automatisk skalning](monitoring-overview-autoscale.md).

 ![Mått och automatisk skalning i Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="learn-about-supported-services-and-metrics"></a>Läs mer om tjänster som stöds och mått
Du kan visa en detaljerad lista över alla tjänster som stöds och deras mått [Azure Monitor-mått – mått som stöds per resurstyp](monitoring-supported-metrics.md).

## <a name="next-steps"></a>Nästa steg
Se länkarna i den här artikeln. Dessutom kan du läsa om:  

* [Vanliga mått för autoskalning](insights-autoscale-common-metrics.md)
* [Så här skapar du Varningsregler](insights-alerts-portal.md)
* [Analysera loggar från Azure storage med Log Analytics](../log-analytics/log-analytics-azure-storage.md)
