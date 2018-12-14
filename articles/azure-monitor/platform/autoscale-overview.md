---
title: Översikt över automatisk skalning i virtuella datorer, molntjänster och Web Apps
description: Automatisk skalning i Microsoft Azure. Gäller för virtuella datorer, Virtual machine Scale sets, molntjänster och Web Apps.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: robb
ms.component: autoscale
ms.openlocfilehash: bf3105201f8eac8bebfe06320ab477cf0b7011e2
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53389026"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Översikt över automatisk skalning i Microsoft Azure Virtual Machines, Cloud Services och Web Apps
Den här artikeln beskriver vilka Microsoft Azure automatisk skalning är, dess fördelar, och hur du kommer igång med hjälp av den.  

Automatisk skalning i Azure Monitor gäller endast [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [molntjänster](https://azure.microsoft.com/services/cloud-services/), [App Service – Web Apps](https://azure.microsoft.com/services/app-service/web/), och [API Management-tjänster](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

> [!NOTE]
> Azure har två metoder för automatisk skalning. En äldre version av automatisk skalning gäller för virtuella datorer (tillgänglighetsuppsättningar). Den här funktionen har begränsad support och vi rekommenderar att du migrerar till VM-skalningsuppsättningar för stöd för snabbare och mer tillförlitlig autoskalning. En länk om hur du använder den äldre tekniken som ingår i den här artikeln.  
>
>

## <a name="what-is-autoscale"></a>Vad är automatisk skalning?
Automatisk skalning kan du har rätt mängd resurser som körs för att hantera belastningen på programmet. Det kan du lägga till resurser för att hantera ökad belastning och sparar du pengar genom att ta bort resurser som sitter inaktiv. Du kan ange ett lägsta och högsta antal instanser för att köra och lägga till eller ta bort virtuella datorer automatiskt baserat på en uppsättning regler. Med en minsta gör att körs ditt program alltid även utan belastning. Med en begränsar den totala möjliga timkostnad. Du skalar automatiskt mellan dessa två extremval med hjälp av regler som du skapar.

 ![Automatisk skalning beskrivs. Lägga till och ta bort virtuella datorer](./media/autoscale-overview/AutoscaleConcept.png)

När regelvillkor uppfylls utlöses en eller flera åtgärder för automatisk skalning. Du kan lägga till och ta bort virtuella datorer, eller utföra andra åtgärder. Följande konceptuellt diagram visar den här processen.  

 ![Flödesdiagram för automatisk skalning](./media/autoscale-overview/Autoscale_Overview_v4.png)

Följande förklaring gäller för de olika delarna i diagrammet ovan.   

## <a name="resource-metrics"></a>Resursmått
Resurser genererar mått, de här måtten bearbetas senare av regler. Mått komma via olika metoder.
VM-skalningsuppsättningar använder dessa data från Azure-diagnostik agenter telemetri för webbappar och molntjänster som kommer direkt från Azure-infrastrukturen. Vissa vanliga statistiken omfattar processoranvändning, minnesanvändning, antalet trådar, kölängden och diskanvändning. En lista över vilka telemetridata som du kan använda finns i [vanliga mått för autoskalning](../../azure-monitor/platform/autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Anpassade mått
Du kan även använda dina egna anpassade mått som kan avger dina program. Om du har konfigurerat din programmen för att skicka mått till Application Insights kan du använda dessa mått för att fatta beslut på om du vill skala eller inte.

## <a name="time"></a>Tid
Schema-baserade regler baserat på UTC. Du måste ange tidszonen korrekt när du konfigurerar dina regler.  

## <a name="rules"></a>Regler
Diagrammet visar endast en regel för automatisk skalning, men du kan ha många av dem. Du kan skapa komplexa överlappande regler som behövs för din situation.  Typer av regel  

* **Måttbaserad** – till exempel göra den här åtgärden när processoranvändningen är över 50%.
* **Tidsbaserade** – till exempel utlösaren en webhook var 8: 00 på lördagar i en viss tidszon.

Mått-baserade regler mäta programinläsning och lägga till eller ta bort virtuella datorer utifrån belastningen. Schema-baserade regler kan du skala när du ser klockslag i inläsningen och vill skala innan en möjlig belastningen ökning eller minskning inträffar.  

## <a name="actions-and-automation"></a>Åtgärder och automation
Regler kan utlösa en eller flera typer av åtgärder.

* **Skala** -skala virtuella datorer in eller ut
* **E-post** – skicka e-post till prenumerationsadministratörer, medadministratörer och/eller ytterligare e-postadress du specificerar
* **Automatisera via webhooks** -anropa webhooks, som kan utlösa flera komplexa åtgärder i eller utanför Azure. Du kan starta en Azure Automation-runbook, Azure Function eller Azure Logic App i Azure. Exempel-URL från tredje part utanför Azure innehåller tjänster som Slack och Twilio.

## <a name="autoscale-settings"></a>Autoskalningsinställningar
Automatisk skalning använder följande termer och struktur.

- En **autoskalningsinställning** läses av motorn för automatisk skalning att avgöra om du vill skala upp eller ned. Den innehåller en eller flera profiler, information om målresurs och Meddelandeinställningar.

    - En **autoskalningsprofilen** är en kombination av a:

        - **kapacitet inställningen**, vilket anger lägsta och högsta och standardvärdena för antalet instanser.
        - **uppsättning regler**, som innehåller en utlösare (tid eller mått) och en skalningsåtgärd (upp eller ned).
        - **upprepning**, vilket betyder att när automatisk skalning bör vidta den här profilen.

        Du kan ha flera profiler som gör att du kan ta hand om olika överlappande krav. Du kan ha olika autoskalningsprofiler för olika tidpunkter på dagen eller dagar i veckan, till exempel.

    - En **meddelandeinställningen** definierar vilka meddelanden som ska utföras när en händelse med automatisk skalning sker utifrån uppfyller kriterierna i någon av de autoskalningsinställning profilerna. Automatisk skalning kan meddela en eller flera e-postadresser eller göra anrop till en eller flera webhook.


![Inställningen för automatisk skalning i Azure, profilen och regelstruktur](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

En fullständig lista över konfigurerbara fält och beskrivningar finns i den [REST-API för automatisk skalning](https://msdn.microsoft.com/library/dn931928.aspx).

Kodexempel, se

* [Avancerad automatisk skalning med Resource Manager-mallar för VM Scale Sets](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md)  
* [REST-API för automatisk skalning](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Vågrät eller lodrät skalning
Automatisk skalning endast skalas vågrätt, vilket är en ökning (”ut”) eller minska (”in”) i antalet Virtuella datorinstanser.  Vågrätt är mer flexibel i en situation med molnet eftersom den låter dig köra potentiellt tusentals virtuella datorer för att hantera belastningen.

Däremot är vertikal skalning olika. Det ser till att samma antal virtuella datorer, men det gör att de virtuella datorerna mer (”dig”) eller mindre (”av”) kraftfulla. Power mäts i minne, processorhastighet, diskutrymme osv.  Vertikal skalning har flera begränsningar. Det är beroende av tillgängligheten för större maskinvara, vilket snabbt når en övre gräns och kan variera beroende på region. Lodrät skalning kräver också vanligtvis en virtuell dator för att stoppa och starta om.

Mer information finns i [lodrätt skalning av Azure-dator med Azure Automation](../../virtual-machines/linux/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="methods-of-access"></a>Åtkomstmetoder
Du kan ställa in automatisk skalning via

* [Azure Portal](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md)
* [PowerShell](../../monitoring-and-diagnostics/insights-powershell-samples.md#create-and-manage-autoscale-settings)
* [Plattformsoberoende kommandoradsgränssnitt (CLI):](../../azure-monitor/platform/cli-samples.md#autoscale)
* [Azure Monitor REST-API](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Tjänster som stöds för autoskalning
| Tjänst | Schemat och dokument |
| --- | --- |
| Web Apps |[Skalning av Webbappar](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) |
| Cloud Services |[Skala en molntjänst](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtuella datorer: Klassisk |[Skala Tillgänglighetsuppsättningar för klassiska virtuella datorer](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Virtuella datorer: Windows-Skalningsuppsättningar |[Skala VM scale anger i Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Virtuella datorer: Linux-Skalningsuppsättningar |[Skala VM scale anger i Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Virtuella datorer: Windows-exempel |[Avancerad automatisk skalning med Resource Manager-mallar för VM Scale Sets](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md) |
| API Management-tjänst|[Skala automatiskt en Azure API Management-instans](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>Nästa steg
Mer information om automatisk skalning, Använd Autoscale Walkthroughs som angavs tidigare eller finns i följande resurser:

* [Azure Monitor autoscale vanliga mått](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Metodtips för automatisk skalning i Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Använda automatisk skalning åtgärder för att skicka e-post och webhook varningsmeddelanden](../../azure-monitor/platform/autoscale-webhook-email.md)
* [REST-API för automatisk skalning](https://msdn.microsoft.com/library/dn931953.aspx)
* [Felsökning VM Scale Sets automatisk skalning](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
