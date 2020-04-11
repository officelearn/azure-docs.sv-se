---
title: Automatisk skalning i virtuella datorer, molntjänster och webbappar
description: Automatisk skalning i Microsoft Azure. Gäller virtuella datorer, skalningsuppsättningar för virtuella datorer, molntjänster och webbappar.
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: eeb8b301bf087efa164a7864cdce3a04952f45ed
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114146"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Översikt över autoskalning i Microsoft Azure Virtual Machines, Cloud Services och Web Apps
I den här artikeln beskrivs vad automatisk skalning av Microsoft Azure är, dess fördelar och hur du kommer igång med den.  

Automatisk skalning av Azure Monitor gäller endast för [skalningsuppsättningar för virtuella](https://azure.microsoft.com/services/virtual-machine-scale-sets/)datorer, [Molntjänster](https://azure.microsoft.com/services/cloud-services/), [App Service – Webbappar](https://azure.microsoft.com/services/app-service/web/)och [API Management-tjänster](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

> [!NOTE]
> Azure har två metoder för automatisk skalning. En äldre version av automatisk skalning gäller för virtuella datorer (tillgänglighetsuppsättningar). Den här funktionen har begränsat stöd och vi rekommenderar att du migrerar till skalningsuppsättningar för virtuella datorer för snabbare och mer tillförlitligt stöd för automatisk skalning. En länk om hur du använder den äldre tekniken ingår i den här artikeln.  
>
>

## <a name="what-is-autoscale"></a>Vad är automatisk skalning?
Med automatisk skalning kan du ha rätt mängd resurser som körs för att hantera belastningen på ditt program. Det gör att du kan lägga till resurser för att hantera ökningar i belastning och även spara pengar genom att ta bort resurser som sitter inaktiva. Du anger ett minsta och högsta antal instanser som ska köras och lägga till eller ta bort virtuella datorer automatiskt baserat på en uppsättning regler. Att ha ett minimum ser till att ditt program alltid körs även under ingen belastning. Att ha ett maximum begränsar din totala möjliga timkostnad. Du skalar automatiskt mellan dessa två ytterligheter med hjälp av regler som du skapar.

 ![Automatisk skalning förklaras. Lägga till och ta bort virtuella datorer](./media/autoscale-overview/AutoscaleConcept.png)

När regelvillkoren är uppfyllda utlöses en eller flera åtgärder för automatisk skalning. Du kan lägga till och ta bort virtuella datorer eller utföra andra åtgärder. Följande konceptuella diagram visar den här processen.  

 ![Flödesdiagram för automatisk skalning](./media/autoscale-overview/Autoscale_Overview_v4.png)

Följande förklaring gäller för bitarna i föregående diagram.   

## <a name="resource-metrics"></a>Resursmått
Resurser släpper ut mått, dessa mått bearbetas senare av regler. Statistiken kommer via olika metoder.
Skalningsuppsättningar för virtuella datorer använder telemetridata från Azure-diagnostikagenter, medan telemetri för webbappar och molntjänster kommer direkt från Azure Infrastructure. En del vanlig statistik är CPU-användning, minnesanvändning, trådantal, kölängd och diskanvändning. En lista över vilka telemetridata du kan använda finns i [Automatisk skalning av gemensamma mått](../../azure-monitor/platform/autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Anpassade mått
Du kan också utnyttja dina egna anpassade mått som dina program kan släppa ut. Om du har konfigurerat dina program för att skicka mått till Application Insights kan du utnyttja dessa mått för att fatta beslut om du vill skala eller inte.

## <a name="time"></a>Tid
Schemabaserade regler baseras på UTC. Du måste ställa in tidszonen på rätt sätt när du ställer in reglerna.  

## <a name="rules"></a>Regler
Diagrammet visar bara en regel för automatisk skalning, men du kan ha många av dem. Du kan skapa komplexa överlappande regler efter behov för din situation.  Regeltyper inkluderar  

* **Måttbaserad** - Gör till exempel den här åtgärden när CPU-användningen är över 50%.
* **Tidsbaserad** - Till exempel utlösa en webhook varje 08:00 på lördag i en viss tidszon.

Måttbaserade regler mäter programinläsning och lägger till eller tar bort virtuella datorer baserat på den inläsningen. Schemabaserade regler gör att du kan skala när du ser tidsmönster i din last och vill skala innan en eventuell belastning ökar eller minskar inträffar.  

## <a name="actions-and-automation"></a>Åtgärder och automatisering
Regler kan utlösa en eller flera typer av åtgärder.

* **Skala** - Skala virtuella datorer in eller ut
* **E-post** - Skicka e-post till prenumerationsadministratörer, medadministratörer och/eller ytterligare e-postadress som du anger
* **Automatisera via webhooks** - Ring webhooks, som kan utlösa flera komplexa åtgärder inom eller utanför Azure. I Azure kan du starta en Azure Automation-runbook, Azure-funktion eller Azure Logic App. Exempel på tredjeparts-URL utanför Azure inkluderar tjänster som Slack och Twilio.

## <a name="autoscale-settings"></a>Autoskalningsinställningar
Automatisk skalning använder följande terminologi och struktur.

- En **automatisk skalningsinställning** läss av motorn för automatisk skalning för att avgöra om du vill skala upp eller ned. Den innehåller en eller flera profiler, information om målresursen och meddelandeinställningar.

  - En **profil för automatisk skalning** är en kombination av en:

    - **kapacitetsinställning**, som anger lägsta, högsta och standardvärden för antal instanser.
    - **uppsättning regler**, som var och en innehåller en utlösare (tid eller mått) och en skalningsåtgärd (upp eller ned).
    - **återkommande**, vilket anger när automatisk skalning ska sätta den här profilen i kraft.

      Du kan ha flera profiler, vilket gör att du kan ta hand om olika överlappande krav. Du kan till exempel ha olika profiler för automatisk skalning för olika tider på dygnet eller veckodagar.

  - En **meddelandeinställning** definierar vilka meddelanden som ska inträffa när en händelse för automatisk skalning inträffar baserat på att uppfylla villkoren för en av profilerna för automatisk skalning. Automatisk skalning kan meddela en eller flera e-postadresser eller ringa samtal till en eller flera webhooks.


![Azure-inställning, profil och regelstruktur för automatisk skalning](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

Den fullständiga listan över konfigurerbara fält och beskrivningar finns i [REST API för automatisk skalning](https://msdn.microsoft.com/library/dn931928.aspx).

För kodexempel finns i

* [Avancerad konfiguration för automatisk skalning med Resource Manager-mallar för vm-skalningsuppsättningar](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md)  
* [REST-API för automatisk skalning](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Vågrät kontra lodrät skalning
Automatisk skalning skalas bara vågrätt, vilket är en ökning ("ut") eller minskning ("in") i antalet VM-instanser.  Vågrätt är mer flexibelt i en molnsituation eftersom det gör att du kan köra potentiellt tusentals virtuella datorer för att hantera belastning.

Däremot är vertikal skalning annorlunda. Det håller samma antal virtuella datorer, men gör de virtuella datorerna mer ("upp") eller mindre ("ner") kraftfull. Ström mäts i minne, CPU-hastighet, diskutrymme, etc.  Vertikal skalning har fler begränsningar. Den är beroende av tillgängligheten för större maskinvara, som snabbt når en övre gräns och kan variera mellan olika regioner. Vertikal skalning kräver också vanligtvis en virtuell dator för att stoppa och starta om.


## <a name="methods-of-access"></a>Metoder för tillgång
Du kan ställa in automatisk skalning via

* [Azure Portal](../../azure-monitor/platform/autoscale-get-started.md)
* [PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)
* [Plattformsoberoende kommandoradsgränssnitt (CLI):](../../azure-monitor/platform/cli-samples.md#autoscale)
* [REST-API:et för Azure Monitor](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Tjänster som stöds för automatisk skalning
| Tjänst | Schema & dokument |
| --- | --- |
| Web Apps |[Skala webbappar](../../azure-monitor/platform/autoscale-get-started.md) |
| Cloud Services |[Skala en molntjänst automatiskt](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtuella datorer: Klassisk |[Skala klassiska tillgänglighetsuppsättningar för virtuella datorer](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Virtuella datorer: Windows-skalningsuppsättningar |[Skalning av skalning av skalning av virtuella datorer i Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Virtuella datorer: Linux-skalningsuppsättningar |[Skalning av skalning av skalning av skalning av skalning av skalning av skalning av skalning av skalning](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Virtuella datorer: Windows-exempel |[Avancerad konfiguration för automatisk skalning med Resource Manager-mallar för vm-skalningsuppsättningar](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md) |
| API-hanteringstjänst|[Skala en Azure API Management-instans automatiskt](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om automatisk skalning använder du genomdroughsna För automatisk skalning som tidigare angetts eller på följande resurser:

* [Azure Monitor automatisk skalning vanliga mått](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Metodtips för autoskalning i Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Använda åtgärder för automatisk skalning för att skicka aviseringar om e-post och webhook](../../azure-monitor/platform/autoscale-webhook-email.md)
* [REST-API för automatisk skalning](https://msdn.microsoft.com/library/dn931953.aspx)
* [Felsökning av automatisk skalning av virtuella datorer](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

