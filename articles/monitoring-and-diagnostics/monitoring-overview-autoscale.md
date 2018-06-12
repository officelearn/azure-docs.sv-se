---
title: Översikt över Autoskala i virtuella datorer och molntjänster Web Apps
description: Autoskala i Microsoft Azure. Gäller för virtuella datorer, virtuella skalningsuppsättningarna, Cloud Services och Web Apps.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/02/2016
ms.author: robb
ms.component: autoscale
ms.openlocfilehash: 4eeca81e08a0ecae9ba41ccdd2bf8a2f395f579c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264678"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Översikt över Autoskala i virtuella datorer i Microsoft Azure Cloud Services och Web Apps
Den här artikeln beskriver vilka Microsoft Azure-Autoskala är, dess fördelar, och hur du kommer igång med hjälp av den.  

Azure övervakaren Autoskala gäller enbart för [Skalningsuppsättningar i virtuella](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [molntjänster](https://azure.microsoft.com/services/cloud-services/), och [App Service - Webbappar](https://azure.microsoft.com/services/app-service/web/).

> [!NOTE]
> Azure har två Autoskala metoder. En äldre version av Autoskala gäller för virtuella datorer (tillgänglighetsuppsättningar). Den här funktionen har begränsad support och rekommenderar vi migrering till skalningsuppsättningar i virtuella datorer för snabbare och mer tillförlitlig Autoskala support. En länk på hur du använder äldre teknik ingår i den här artikeln.  
>
>

## <a name="what-is-autoscale"></a>Vad är Autoskala?
Autoskalningsfunktionen kan du ha rätt antal resurser som körs för att hantera belastningen på ditt program. Det kan du lägga till resurser för att hantera belastningen ökar och även spara pengar genom att ta bort resurser som placerad inaktiv. Du kan ange lägsta och högsta antal instanser som du vill köra och lägga till eller ta bort virtuella datorer automatiskt baserat på en uppsättning regler. Med en minsta gör att körs programmet alltid även under ingen belastning. Med en begränsar din totalkostnaden möjliga varje timme. Du skalar automatiskt mellan dessa två yttersta med regler som du skapar.

 ![Autoskala beskrivs. Lägga till och ta bort virtuella datorer](./media/monitoring-overview-autoscale/AutoscaleConcept.png)

När villkor uppfylls, utlöses en eller flera automatiska åtgärder. Du kan lägga till och ta bort virtuella datorer, eller utföra andra åtgärder. Följande konceptuellt diagram visar den här processen.  

 ![Flödesdiagram för Autoskala](./media/monitoring-overview-autoscale/Autoscale_Overview_v4.png)

Följande förklaring gäller för de olika delarna i föregående diagram.   

## <a name="resource-metrics"></a>Resurs-mått
Resurser genererar mått, de här måtten bearbetas senare av regler. Mått levereras via olika metoder.
Skaluppsättningar för den virtuella datorn använder telemetridata från Azure diagnostics agenter telemetri för webbprogram och molntjänster kommer direkt från Azure-infrastrukturen. Några vanliga statistiken omfattar processoranvändning, minnesanvändning, antalet trådar, kölängd och diskanvändning. En lista över vilka telemetridata som du kan använda finns [Autoskala vanliga mått](insights-autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Anpassade mått
Du kan också utnyttja dina egna anpassade mått som dina program kan avger. Om du har konfigurerat dina program att skicka mått till Application Insights kan du utnyttja dessa mått för att fatta beslut om att skala eller inte. 

## <a name="time"></a>Tid
Schema-baserade regler baserat på UTC. Du måste ange tidszonen korrekt när du konfigurerar dina regler.  

## <a name="rules"></a>Regler
Diagrammet visar endast en Autoskala regeln, men du kan ha många av dem. Du kan skapa komplexa överlappande regler som behövs för din situation.  Typer av regel  

* **Mått-baserade** – till exempel göra den här åtgärden när CPU-användning är över 50%.
* **Tidsbaserade** – till exempel utlösaren webhook var 8: 00 på lördag i en viss tidszon.

Mått-baserade regler mäta programinläsning och lägga till eller ta bort virtuella datorer baserat på att belastningen. Schema-baserade regler kan du skala när du se klockslag i din belastning och vill skala innan ett möjligt belastningen ökar eller minskar inträffar.  

## <a name="actions-and-automation"></a>Åtgärder och automatisering
Regler kan utlösa en eller flera typer av åtgärder.

* **Skala** -skala VMs in eller ut
* **E-post** – skicka e-post till prenumerationsadministratörer, medadministratörer eller en extra e-postadress som du anger
* **Automatisera via webhooks** -anropa webhooks, som kan utlösa flera komplexa åtgärder i eller utanför Azure. Du kan starta en Azure Automation-runbook, Azure-funktion eller Azure Logikappen i Azure. Exempel-URL från tredje part utanför Azure innehåller tjänster som Slack och Twilio.

## <a name="autoscale-settings"></a>Autoskala inställningar
Autoskala använder följande termer och struktur.

- En **autoskalningsinställning** läses av Autoskala-motorn att avgöra om du vill skala upp eller ned. Den innehåller en eller flera profiler, information om målresurs och inställningar för meddelanden.

    - En **Autoskala profil** är en kombination av a:

        - **kapacitet**, vilket anger lägsta och högsta och standardvärdena för antalet instanser.
        - **uppsättning regler**, som innehåller en utlösare (tid eller mått) och en skalningsåtgärd (uppåt eller nedåt).
        - **återkommande**, vilket anger att när Autoskala ska införas den här profilen.

        Du kan ha flera profiler, så att du kan ta hand om olika överlappande krav. Du kan ha olika autoskalningsprofiler för olika tidpunkter på dagen eller dagar i veckan, till exempel.

    - En **inställning för händelseavisering** definierar vilka meddelanden som ska utföras när Autoskala händelse inträffar baserat på uppfyller villkoren för någon av den autoskalningsinställning profiler. Autoskala kan meddela en eller flera e-postadresser eller göra anrop till en eller flera webhook.


![Azure autoskalningsinställning, profil och regelstruktur](./media/monitoring-overview-autoscale/AzureResourceManagerRuleStructure3.png)

En fullständig lista över konfigurerbara fält och beskrivningar som är tillgängliga i den [Autoskala REST API](https://msdn.microsoft.com/library/dn931928.aspx).

Kodexempel finns

* [Avancerade Autoskala konfigurationen med hjälp av Resource Manager-mallar för Skalningsuppsättningar](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [Autoskala REST API](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Vågrät eller lodrät skalning
Autoskala endast skalas vågrätt, vilket ökar (”out”) eller minska (”i”) i antal VM-instanser.  Vågrät är mer flexibelt i en situation med molnet som du kan köra potentiellt tusentals virtuella datorer för att hantera belastningen.

Däremot är lodrät skalning olika. Det behåller samma antal virtuella datorer, men det gör de virtuella datorerna mer (”upp”) eller mindre (”nedåt”) kraftfulla. Power mäts i minne, processorhastighet, diskutrymme osv.  Lodrät skalning har flera begränsningar. Det är beroende av tillgängligheten för större maskinvara, vilket snabbt träffar en övre gräns och kan variera beroende på region. Lodrät skalning kräver också vanligtvis en VM att stoppa och starta om.

Mer information finns i [lodrätt skala virtuella Azure-datorn med Azure Automation](../virtual-machines/linux/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="methods-of-access"></a>Åtkomstmetoder
Du kan ställa in autoskalning via

* [Azure Portal](insights-how-to-scale.md)
* [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
* [Plattformsoberoende kommandoradsgränssnittet (CLI)](insights-cli-samples.md#autoscale)
* [Azure-Monitor REST API](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Tjänster som stöds för Autoskala
| Tjänst | Schemat & Docs |
| --- | --- |
| Web Apps |[Skalning av webbprogram](insights-how-to-scale.md) |
| Cloud Services |[Autoskala en tjänst i molnet](../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtuella datorer: klassiska |[Skalning klassiska virtuella Tillgänglighetsuppsättningar](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Virtuella datorer: Skalningsuppsättningar Windows |[Skalning virtuella datorn anger i Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) |
| Virtuella datorer: Skalningsuppsättningarna Linux |[Skalning virtuella datorn anger i Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| Virtuella datorer: Windows-exempel |[Avancerade Autoskala konfigurationen med hjälp av Resource Manager-mallar för Skalningsuppsättningar](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## <a name="next-steps"></a>Nästa steg
Mer information om Autoskala Autoscale Walkthroughs som angavs tidigare eller finns i följande resurser:

* [Azure övervakaren Autoskala vanliga mått](insights-autoscale-common-metrics.md)
* [Metodtips för Azure-Monitor Autoskala](insights-autoscale-best-practices.md)
* [Använda automatiska åtgärder för att skicka e-post och webhook aviseringar](insights-autoscale-to-webhook-email.md)
* [Autoskala REST API](https://msdn.microsoft.com/library/dn931953.aspx)
* [Felsökning virtuella skala anger Autoskala](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
