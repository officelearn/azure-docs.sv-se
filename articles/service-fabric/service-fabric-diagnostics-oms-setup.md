---
title: Konfigurera övervakning med Azure Monitor-loggar
description: Lär dig hur du konfigurerar Azure Monitor-loggar för att visualisera och analysera händelser för att övervaka dina Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: cf0fab9942dcbb7ee09e554f2c9ba8738f208009
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609935"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Konfigurera Azure Monitor-loggar för ett kluster

Azure Monitor-loggar är vår rekommendation att övervaka klusternivåhändelser. Du kan konfigurera Log Analytics-arbetsytan via Azure Resource Manager, PowerShell eller Azure Marketplace. Om du underhåller en uppdaterad Resource Manager-mall för distributionen för framtida bruk använder du samma mall för att konfigurera din Azure Monitor-loggmiljö. Distribution via Marketplace är enklare om du redan har ett kluster som distribueras med diagnostik aktiverat. Om du inte har åtkomst på prenumerationsnivå i det konto som du distribuerar till distribuerar du med hjälp av PowerShell eller Resource Manager-mallen.

> [!NOTE]
> Om du vill konfigurera Azure Monitor-loggar för att övervaka ditt kluster måste du ha diagnostik aktiverat för att visa händelser på klusternivå eller plattformsnivå. Mer information om [hur du konfigurerar diagnostik i Windows-kluster](service-fabric-diagnostics-event-aggregation-wad.md) och [hur du konfigurerar diagnostik i Linux-kluster](service-fabric-diagnostics-oms-syslog.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Distribuera en Log Analytics-arbetsyta med hjälp av Azure Marketplace

Om du vill lägga till en Log Analytics-arbetsyta när du har distribuerat ett kluster går du till Azure Marketplace i portalen och letar efter **Service Fabric Analytics**. Det här är en anpassad lösning för distributioner av Service Fabric som har data som är specifika för Service Fabric. I den här processen skapar du både lösningen (instrumentpanelen för att visa insikter) och arbetsytan (aggregering av underliggande klusterdata).

1. Välj **Ny** på menyn för vänster navigering. 

2. Sök efter **Service Fabric Analytics**. Markera den resurs som visas.

3. Välj **Skapa**.

    ![Analys av serviceinfrastruktur på Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. I fönstret Skapa Service Fabric Analytics väljer du **Välj en arbetsyta** för **OMS-arbetsytan** och skapar sedan **en ny arbetsyta**. Fyll i de obligatoriska posterna. Det enda kravet här är att prenumerationen för Service Fabric-klustret och arbetsytan är densamma. När dina poster har validerats börjar arbetsytan distribueras. Distributionen tar bara några minuter.

5. När du är klar väljer du **Skapa** igen längst ned i fönstret Skapa Service Fabric Analytics. Kontrollera att den nya arbetsytan visas under **OMS Workspace**. Den här åtgärden lägger till lösningen på arbetsytan som du skapade.

Om du använder Windows fortsätter du med följande steg för att ansluta Azure Monitor-loggar till lagringskontot där klusterhändelserna lagras. 

>[!NOTE]
>Service Fabric Analytics-lösningen stöds endast för Windows-kluster. För Linux-kluster kan du läsa vår artikel om [hur du konfigurerar Azure Monitor-loggar för Linux-kluster](service-fabric-diagnostics-oms-syslog.md).  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Ansluta log analytics-arbetsytan till klustret 

1. Arbetsytan måste vara ansluten till diagnostikdata som kommer från klustret. Gå till resursgruppen där du skapade Service Fabric Analytics-lösningen. Välj **ServiceFabric-namnOfWorkspace\<\> ** och gå till översiktssidan. Därifrån kan du ändra lösningsinställningar, arbetsyteinställningar och komma åt Log Analytics-arbetsytan.

2. Välj **Lagringskonton loggar**under Datakällor på menyn Arbetsyta på menyn **För arbetsyter**.

3. På sidan **Lagringskontologgar** väljer du **Lägg till** högst upp för att lägga till klustrets loggar på arbetsytan.

4. Välj **Lagringskonto** om du vill lägga till rätt konto som skapats i klustret. Om du använde standardnamnet är **lagringskontot\<\>sfdg resourceGroupName**. Du kan också bekräfta detta med Azure Resource Manager-mallen som används för att distribuera klustret genom att kontrollera värdet som används för **applicationDiagnosticsStorageAccountName**. Om namnet inte visas rullar du nedåt och väljer **Läs in mer**. Välj namnet på lagringskontot.

5. Ange datatypen. Ställ in den **på Service Fabric Events**.

6. Kontrollera att källan automatiskt är inställd på **WADServiceFabric\*EventTable**.

7. Välj **OK** om du vill ansluta arbetsytan till klustrets loggar.

    ![Lägga till lagringskontologgar i Azure Monitor-loggar](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Kontot visas nu som en del av ditt lagringskontologgar i arbetsytans datakällor.

Du har lagt till Service Fabric Analytics-lösningen på en Log Analytics-arbetsyta som nu är korrekt ansluten till klustrets plattforms- och programloggtabell. Du kan lägga till ytterligare källor på arbetsytan på samma sätt.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Distribuera Azure Monitor-loggar med Azure Resource Manager

När du distribuerar ett kluster med hjälp av en Resource Manager-mall skapar mallen en ny Log Analytics-arbetsyta, lägger till Service Fabric-lösningen på arbetsytan och konfigurerar den för att läsa data från lämpliga lagringstabeller.

Du kan använda och ändra [den här exempelmallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) för att uppfylla dina krav. Den här mallen gör följande

* Skapar ett 5-nodservicetyg-kluster
* Skapar en log analytics-lösning för arbetsyta och serviceinfrastruktur
* Konfigurerar Log Analytics-agenten för att samla in och skicka två exempelprestandaräknare till arbetsytan
* Konfigurerar WAD för att samla in Service Fabric och skickar dem till Azure-lagringstabeller (WADServiceFabric*EventTable)
* Konfigurerar arbetsytan Log Analytics för att läsa händelserna från dessa tabeller


Du kan distribuera mallen som en Resource Manager-uppgradering till klustret med hjälp av `New-AzResourceGroupDeployment` API:et i Azure PowerShell-modulen. Ett exempel kommando skulle vara:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager upptäcker att det här kommandot är en uppdatering till en befintlig resurs. Ändringarna mellan mallen som driver den befintliga distributionen och den nya mallen som tillhandahålls bearbetas endast.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Distribuera Azure Monitor-loggar med Azure PowerShell

Du kan också distribuera logganalysresursen `New-AzOperationalInsightsWorkspace` via PowerShell med kommandot. Om du vill använda den här metoden kontrollerar du att du har installerat [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Använd det här skriptet för att skapa en ny Log Analytics-arbetsyta och lägga till Service Fabric-lösningen i det: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

När du är klar följer du stegen i föregående avsnitt för att ansluta Azure Monitor-loggar till lämpligt lagringskonto.

Du kan också lägga till andra lösningar eller göra andra ändringar på logganalysarbetsytan med hjälp av PowerShell. Mer information finns i [Hantera Azure Monitor-loggar med PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Nästa steg
* [Distribuera Log Analytics-agenten](service-fabric-diagnostics-oms-agent.md) på dina noder för att samla in prestandaräknare och samla in dockerstatistik och loggar för dina behållare
* Bekanta dig med [loggsöknings- och frågefunktionerna](../log-analytics/log-analytics-log-searches.md) som erbjuds som en del av Azure Monitor-loggar
* [Använda Vydesignern för att skapa anpassade vyer i Azure Monitor-loggar](../azure-monitor/platform/view-designer.md)
