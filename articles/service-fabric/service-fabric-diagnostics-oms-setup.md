---
title: Azure Service Fabric – Konfigurera övervakning med Azure Monitor-loggar | Microsoft Docs
description: Lär dig hur du ställer in Azure Monitor-loggar för att visualisera och analysera händelser att övervaka dina Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: 3523a2df413740f644151c548e403c39c9be1f03
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670514"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Konfigurera Azure Monitor-loggar för ett kluster

Azure Monitor-loggar är vår rekommendation att övervaka klustret händelser. Du kan ställa in Log Analytics-arbetsytan via Azure Resource Manager, PowerShell eller Azure Marketplace. Om du behåller en uppdaterad Resource Manager-mall för distributionen för framtida användning kan du använda samma mall för att ställa in miljön för Azure Monitor-loggar. Distribution via Marketplace är enklare om du redan har ett kluster som distribueras med aktiverat Diagnostikfunktionen. Om du inte har åtkomst på prenumerationsnivå i kontot som du distribuerar till, distribuera med hjälp av PowerShell eller Resource Manager-mallen.

> [!NOTE]
> Du måste ha diagnostik som är aktiverade för att visa händelser för kluster- eller plattform-nivå för att konfigurera Azure Monitor-loggar att övervaka ditt kluster. Referera till [hur du ställer in diagnostik i Windows-kluster](service-fabric-diagnostics-event-aggregation-wad.md) och [hur du ställer in diagnostik i Linux-kluster](service-fabric-diagnostics-oms-syslog.md) mer

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Distribuera en Log Analytics-arbetsyta med hjälp av Azure Marketplace

Om du vill lägga till en Log Analytics-arbetsyta när du har distribuerat ett kluster, gå till Azure Marketplace i portalen och letar efter **Service Fabric-analys**. Det här är en anpassad lösning för Service Fabric-distributioner som har data som är specifika för Service Fabric. I den här processen skapas både lösningen (instrumentpanelen för att visa insikter) och arbetsytan (aggregering av underliggande klustret).

1. Välj **New** på den vänstra navigeringsmenyn. 

2. Sök efter **Service Fabric-analys**. Välj den resurs som visas.

3. Välj **Skapa**.

    ![Service Fabric-analys i Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. I fönstret Skapa Service Fabric-analys, Välj **Välj en arbetsyta** för den **OMS-arbetsyta** fält, och sedan **skapa en ny arbetsyta**. Fyll i posterna som krävs. Det enda kravet är att prenumerationen för Service Fabric-klustret och arbetsytan är samma. När posterna har validerats börjar distribuera din arbetsyta. Distributionen tar bara några minuter.

5. När du är klar väljer **skapa** igen längst ned i fönstret Skapa Service Fabric-analys. Se till att den nya arbetsytan visas den under **OMS-arbetsyta**. Den här åtgärden lägger till lösningen till arbetsytan som du skapade.

Om du använder Windows, fortsätter du med följande steg för att ansluta Azure Monitor-loggar till lagringskontot där dina klusterhändelser lagras. 

>[!NOTE]
>Service Fabric-analys-lösningen har endast stöd för Windows-kluster. Linux-kluster finns i vår artikel på [hur du ställer in Azure Monitor-loggar för Linux-kluster](service-fabric-diagnostics-oms-syslog.md).  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Ansluta Log Analytics-arbetsytan till ditt kluster 

1. Arbetsytan måste vara anslutna till diagnostikdata som kommer från ditt kluster. Gå till resursgruppen där du skapade Service Fabric-analys-lösningen. Välj **ServiceFabric\<nameOfWorkspace\>**  och gå till dess översiktssidan. Därifrån kan du ändra inställningar för lösning, inställningar för arbetsyta och få åtkomst till Log Analytics-arbetsytan.

2. På den vänstra menyn under **datakällor för arbetsyta**väljer **lagringskontologgar**.

3. På den **lagringskontologgar** väljer **Lägg till** överst för att lägga till ditt kluster loggar i arbetsytan.

4. Välj **lagringskonto** att lägga till lämpligt konto som skapats i klustret. Om du använde standardnamnet för lagringskontot är **sfdg\<resourceGroupName\>**. Du kan också bekräfta detta med Azure Resource Manager-mallen som används för att distribuera ditt kluster genom att markera det värde som används för **applicationDiagnosticsStorageAccountName**. Om namnet inte visas, rulla nedåt och välj **Läs in fler**. Välj namnet på lagringskontot.

5. Ange datatyp. Ange den till **Service Fabric-händelser**.

6. Kontrollera att källan är automatiskt inställd på **WADServiceFabric\*EventTable**.

7. Välj **OK** att ansluta din arbetsyta till ditt kluster loggar.

    ![Lägg till lagringskontologgar i Azure Monitor-loggar](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Kontot visas nu som en del av ditt storage-konto loggar i din arbetsyta-datakällor.

Du har lagt till Service Fabric-analys-lösningen i en Log Analytics-arbetsyta som nu är korrekt ansluten till ditt kluster-plattformen och application log-tabell. Du kan lägga till ytterligare källor till arbetsytan på samma sätt.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Distribuera Azure Monitor-loggar med Azure Resource Manager

När du distribuerar ett kluster med hjälp av Resource Manager-mall mallen skapar en ny Log Analytics-arbetsyta, lägger till Service Fabric-lösningen till arbetsytan och konfigurerar den för att läsa data från tabellerna lämpligt.

Du kan använda och ändra [den här exempelmallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) som uppfyller dina krav. Den här mallen gör följande

* Skapar ett Service Fabric-kluster med 5 noder
* Skapar en Log Analytics-arbetsyta och Service Fabric-lösning
* Konfigurerar Log Analytics-agenten för att samla in och skicka 2 exempel prestandaräknare till arbetsytan
* Konfigurerar WAD för att samla in Service Fabric och skickar dem till Azure storage-tabeller (WADServiceFabric * EventTable)
* Konfigurerar Log Analytics-arbetsytan för att läsa händelserna från dessa tabeller


Du kan distribuera mallen som en Resource Manager-uppgradering till klustret med hjälp av den `New-AzureRmResourceGroupDeployment` API i AzureRM PowerShell-modulen. Ett Exempelkommando skulle bli:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager identifierar att det här kommandot är en uppdatering till en befintlig resurs. Den bearbetar endast ändringar mellan den mall som driver den befintliga distributionen och den nya mall som tillhandahålls.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Distribuera Azure Monitor-loggar med Azure PowerShell

Du kan också distribuera log analytics-resursen via PowerShell genom att använda den `New-AzureRmOperationalInsightsWorkspace` kommando. Om du vill använda den här metoden gör att du har installerat [Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Använd det här skriptet för att skapa en ny Log Analytics-arbetsyta och lägga till Service Fabric-lösningen: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

När du är klar följer du anvisningarna i föregående avsnitt för att ansluta Azure Monitor-loggar till lämplig storage-kontot.

Du kan också lägga till andra lösningar eller göra andra ändringar Log Analytics-arbetsytan med hjälp av PowerShell. Mer information finns i [hantera Azure Monitor-loggar med hjälp av PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Nästa steg
* [Distribuera Log Analytics-agenten](service-fabric-diagnostics-oms-agent.md) till noderna kan samla in prestandaräknare och samla in loggar för dina behållare och docker-stats
* Bekanta dig med den [loggsökning och frågor](../log-analytics/log-analytics-log-searches.md) funktioner som erbjuds som en del av Azure Monitor-loggar
* [Använd View Designer för att skapa anpassade vyer i Azure Monitor-loggar](../azure-monitor/platform/view-designer.md)
