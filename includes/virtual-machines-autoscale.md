Du kan enkelt [skala automatiskt](../articles/monitoring-and-diagnostics/insights-autoscale-best-practices.md) din [virtuella maskiner (VMs)](../articles/virtual-machines/windows/overview.md) när du använder [skalningsuppsättningar i virtuella](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) och [autoskalning funktion i Azure Övervakaren](../articles/monitoring-and-diagnostics/monitoring-overview-autoscale.md). Din virtuella dator måste vara medlemmar i en skaluppsättningen skalas automatiskt. Den här artikeln innehåller information som gör det möjligt för dig att bättre förstå hur du skalar dina virtuella datorer både vågrätt och lodrätt med automatisk och manuell metoder.

## <a name="horizontal-or-vertical-scaling"></a>Vågrät eller lodrät skalning

Funktionen Autoskala i Azure-Monitor endast skalas vågrätt, vilket ökar (”out”) eller minska antalet virtuella datorer (”i”). Teckenbredden är mer flexibelt i en situation med molnet som du kan köra potentiellt tusentals virtuella datorer för att hantera belastningen. Du skala horisontellt genom att automatiskt eller manuellt ändra kapacitet (eller instansantalet) för den skaluppsättning. 

Lodrät skalning behåller samma antal virtuella datorer, men gör de virtuella datorerna mer (”upp”) eller mindre (”nedåt”) kraftfulla. Power mäts i attribut, till exempel minne, processorhastighet och ledigt diskutrymme. Lodrät skalning är beroende av tillgängligheten för större maskinvara, vilket snabbt träffar en övre gräns och kan variera beroende på region. Lodrät skalning kräver också vanligtvis en VM att stoppa och starta om. Du skala lodrätt genom att ange en ny storlek i konfigurationen för de virtuella datorerna i skaluppsättning.

Använda runbooks i [Azure Automation](../articles/automation/automation-intro.md), kan du enkelt [skala virtuella datorer i en skaluppsättning](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md) uppåt eller nedåt.

## <a name="create-a-virtual-machine-scale-set"></a>Skapa en skaluppsättning för virtuell dator

Skaluppsättningar gör det enkelt att distribuera och hantera identiska virtuella datorer som en uppsättning. Du kan skapa Linux eller Windows skalningsuppsättningarna med hjälp av den [Azure-portalen](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), [Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md), eller [Azure CLI](../articles/virtual-machines/linux/tutorial-create-vmss.md). Du kan också skapa och hantera skalningsuppsättningar med SDK: er som [Python](/develop/python) eller [Node.js](/nodejs/azure), eller direkt med den [REST API: er](/rest/api/compute/virtualmachinescalesets). Automatisk skalning av virtuella datorer kan åstadkommas genom att använda mått och regler i uppsättningen skala.

## <a name="configure-autoscale-for-a-scale-set"></a>Konfigurera Autoskala för en skaluppsättning

Automatisk skalning innehåller antalet virtuella datorer för att hantera belastningen på ditt program. Det kan du lägga till virtuella datorer för att hantera belastningen ökar och spara pengar genom att ta bort virtuella datorer som placerad inaktiv. Du kan ange lägsta och högsta antal virtuella datorer ska köras baserat på en uppsättning regler. Med en minsta gör att körs programmet alltid även under ingen belastning. Med ett maximalt värde som begränsar din totalkostnaden möjliga varje timme.

Du kan aktivera Autoskala när du skapar skaluppsättningen med [Azure PowerShell](../articles/monitoring-and-diagnostics/insights-powershell-samples.md#create-and-manage-autoscale-settings) eller [Azure CLI](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings). Du kan också aktivera den när skaluppsättning har skapats. Du kan skapa en skalningsuppsättning, installera tillägget och konfigurera Autoskala med hjälp av en [Azure Resource Manager-mall](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md). Aktivera Autoskala från Azure-Monitor i Azure-portalen eller aktivera Autoskala från ange inställningar för skalan.

![Aktivera Autoskala](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>Mått

Funktionen Autoskala i Azure-Monitor kan du skala antalet virtuella datorer som körs eller inaktiv baserat på [mått](../articles/monitoring-and-diagnostics/insights-autoscale-common-metrics.md). Som standard ange VMs grundläggande värdnivå mått för disk-, nätverks- och CPU-användning. När du konfigurerar insamling av diagnostikdata med diagnostiska blir prestandaräknare för ytterligare gäst-OS tillgängliga för disk, CPU och minne.

![Mått villkor](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

Om programmet behöver skala baserat på mått som inte är tillgängliga via värden, så att de virtuella datorerna i skaluppsättning måste ha antingen den [Linux diagnostiska tillägget](../articles/virtual-machines/linux/diagnostic-extension.md) eller [Windows diagnostik tillägget](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)installerad. Om du skapar en skala som anges med Azure-portalen måste du också använda Azure PowerShell eller Azure CLI för att installera tillägget med diagnostik-konfigurationer som du behöver.
 
### <a name="rules"></a>Regler

[Regler](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) kombinera ett mått med en åtgärd som ska utföras. När villkor uppfylls, utlöses en eller flera automatiska åtgärder. Du kan till exempel ha en regel som definierats som ökar antalet virtuella datorer med 1 om den genomsnittliga processoranvändningen går över 85 procent.

![Automatiska åtgärder](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>Meddelanden

Du kan [definiera utlösare](../articles/monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md) så att specifika URL: er kallas eller e-postmeddelanden skickas utifrån de automatiska regler som du skapar. Webhooks kan du dirigera Azure aviseringsmeddelanden till andra system för efterbearbetning eller anpassade meddelanden.

## <a name="manually-scale-vms-in-a-scale-set"></a>Skala manuellt virtuella datorer i en skaluppsättning

### <a name="horizontal"></a>Horisontal

Du kan lägga till eller ta bort virtuella datorer genom att ändra kapacitet för skaluppsättning. I Azure-portalen kan du öka eller minska antalet virtuella datorer (visas som **instansen antal**) i skaluppsättningen med hjälp av skjutreglaget åsidosättning villkor på skärmen skalning åt vänster eller höger.

Med Azure PowerShell kan du behöver skala uppsättning objekt med [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss). Sedan ställer du in den **sku.capacity** för hur många virtuella datorer som du vill använda och uppdatera skalan egenskapsuppsättning med [uppdatering AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss). Med Azure CLI kan du ändra kapacitet med den **--ny kapacitet** parameter för den [az vmss skala](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) kommando.

### <a name="vertical"></a>Lodrät

Du kan ändra storleken på de virtuella datorerna i Azure-portalen på skärmen storlek för skaluppsättning manuellt. Du kan använda Azure PowerShell med Get-AzureRmVmss egenskapen avbildningen referens sku, och sedan använda [uppdatering AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) och [uppdatering AzureRmVmssInstance](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

## <a name="next-steps"></a>Nästa steg

- Mer information om skalningsuppsättningar i [beaktanden vid utformning av Skalningsuppsättningarna](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md).

