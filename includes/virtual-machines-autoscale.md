---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 7bafc30bbf8661f7054e49c36af420507f37c989
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53326532"
---
Du kan enkelt [skala automatiskt](../articles/azure-monitor/platform/autoscale-best-practices.md) din [virtuella datorer (VM)](../articles/virtual-machines/windows/overview.md) när du använder [VM-skalningsuppsättningar](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) och [funktionen för automatisk skalning i Azure Övervaka](../articles/monitoring-and-diagnostics/monitoring-overview-autoscale.md). Dina virtuella datorer måste vara medlemmar i en skalningsuppsättning kan skalas automatiskt. Den här artikeln innehåller information som hjälper dig att bättre förstå hur du skalar dina virtuella datorer både lodrätt och vågrätt med hjälp av automatisk och manuell metoder.

## <a name="horizontal-or-vertical-scaling"></a>Vågrät eller lodrät skalning

Funktionen automatisk skalning i Azure Monitor bara skalas vågrätt, vilket är en ökning (”ut”) eller minska antalet virtuella datorer (”in”). Horisontell skalning är mer flexibel i en situation med molnet eftersom den låter dig köra potentiellt tusentals virtuella datorer för att hantera belastningen. Du skala horisontellt genom att automatiskt eller manuellt ändra kapaciteten (eller instansantalet) av skalningsuppsättningen. 

Vertikal skalning ser till att samma antal virtuella datorer, men gör de virtuella datorerna mer (”dig”) eller mindre (”av”) kraftfulla. Power mäts i attribut, till exempel minne, processorhastighet och ledigt diskutrymme. Vertikal skalning är beroende av tillgängligheten för större maskinvara, vilket snabbt når en övre gräns och kan variera beroende på region. Lodrät skalning kräver också vanligtvis en virtuell dator för att stoppa och starta om. Du skalar lodrätt genom att ange en ny storlek i konfigurationen av de virtuella datorerna i skalningsuppsättningen.

Med hjälp av runbooks i [Azure Automation](../articles/automation/automation-intro.md), du kan enkelt [skala virtuella datorer i en skalningsuppsättning](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md) upp eller ned.

## <a name="create-a-virtual-machine-scale-set"></a>Skapa en VM-skalningsuppsättning

Skalningsuppsättningar gör det enkelt att distribuera och hantera identiska virtuella datorer som en uppsättning. Du kan skapa Linux eller Windows scale sets med den [Azure-portalen](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), [Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md), eller [Azure CLI](../articles/virtual-machines/linux/tutorial-create-vmss.md). Du kan också skapa och hantera skalningsuppsättningar med SDK: er som [Python](https://azure.microsoft.com/develop/python/) eller [Node.js](/nodejs/azure), eller direkt med den [REST API: er](/rest/api/compute/virtualmachinescalesets). Automatisk skalning av virtuella datorer åstadkoms genom att använda mått och regler för skalningsuppsättningen.

## <a name="configure-autoscale-for-a-scale-set"></a>Konfigurera automatisk skalning för en skalningsuppsättning

Automatisk skalning innehåller rätt antal virtuella datorer för att hantera belastningen på programmet. Det kan du lägga till virtuella datorer för att hantera ökad belastning och spara pengar genom att ta bort virtuella datorer som sitter inaktiv. Du kan ange ett lägsta och högsta antal virtuella datorer som körs baserat på en uppsättning regler. Med en minsta gör att körs ditt program alltid även utan belastning. Med ett maxvärde som begränsar den totala möjliga timkostnad.

Du kan aktivera automatisk skalning när du skapar en skalningsuppsättning med [Azure PowerShell](../articles/monitoring-and-diagnostics/insights-powershell-samples.md#create-and-manage-autoscale-settings) eller [Azure CLI](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings). Du kan också aktivera det när skalningsuppsättningen skapas. Du kan skapa en skalningsuppsättning, installera tillägget och konfigurera automatisk skalning med hjälp av en [Azure Resource Manager-mall](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md). Aktivera autoskalning från Azure Monitor i Azure-portalen eller aktivera autoskalning från uppsättningen skalinställningar.

![Aktivera autoskalning](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>Mått

Funktionen automatisk skalning i Azure Monitor kan du skala antalet virtuella datorer som körs eller ned baserat på [mått](../articles/azure-monitor/platform/autoscale-common-metrics.md). Som standard erbjuder virtuella datorer värdnivå basmått för disk-, nätverks- och CPU-användning. När du konfigurerar insamling av diagnostikdata med hjälp av det diagnostiska tillägget blir prestandaräknare för ytterligare gäst-OS tillgängliga för disk, CPU och minne.

![Metrisk kriterier](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

Om programmet behöver för att skala baserat på mått som inte är tillgängliga via värden, så att de virtuella datorerna i skalningsuppsättningen måste ha antingen den [Linux-diagnostiktillägget](../articles/virtual-machines/linux/diagnostic-extension.md) eller [Windows diagnostiktillägget](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)installerad. Om du skapar en skalningsuppsättning med Azure-portalen, måste du också använda Azure PowerShell eller Azure CLI för att installera tillägget med diagnostik-konfiguration som du behöver.
 
### <a name="rules"></a>Regler

[Regler](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) kombinera ett mått med en åtgärd som ska utföras. När regelvillkor uppfylls utlöses en eller flera åtgärder för automatisk skalning. Du kan till exempel ha en regel som definierats som ökar antalet virtuella datorer med 1 om den genomsnittliga CPU-användningen mer än 85 procent.

![Åtgärder för automatisk skalning](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>Meddelanden

Du kan [konfigurera utlösare](../articles/azure-monitor/platform/autoscale-webhook-email.md) så att specifika URL: er anropas eller e-postmeddelanden skickas baserat på reglerna för automatisk skalning som du skapar. Webhooks kan du dirigera Azure aviseringsmeddelanden till andra system för efterbearbetning eller anpassade meddelanden.

## <a name="manually-scale-vms-in-a-scale-set"></a>Skala virtuella datorer i en skalningsuppsättning

### <a name="horizontal"></a>Horisontal

Du kan lägga till eller ta bort virtuella datorer genom att ändra kapaciteten för skalningsuppsättningen. I Azure-portalen kan du öka eller minska antalet virtuella datorer (visas som **antal instanser**) i skalningsuppsättningen med hjälp av skjutreglaget åsidosättning villkor på skärmen skalning åt vänster eller höger.

Med Azure PowerShell kan du behöver skala uppsättning objekt med [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss). Sedan ställer du in den **sku.capacity** egenskapen för hur många virtuella datorer som du vill och uppdatera skalningsuppsättning med [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss). Med Azure CLI kan du ändra kapaciteten med den **--ny kapacitet** parametern för den [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) kommando.

### <a name="vertical"></a>Lodrät

Du kan manuellt ändra storleken på de virtuella datorerna i Azure-portalen på skärmen storlek för skalningsuppsättningen. Du kan använda Azure PowerShell med Get-AzureRmVmss, ange sku-egenskapen bild referens och sedan använda [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) och [Update-AzureRmVmssInstance](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

## <a name="next-steps"></a>Nästa steg

- Mer information om skalningsuppsättningar i [designöverväganden för Skalningsuppsättningar](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md).

