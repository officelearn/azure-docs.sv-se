---
title: Aktivera Azure Monitor for VMs (klassisk) med PowerShell eller mallar
description: I den här artikeln beskrivs hur du aktiverar Azure Monitor for VMs för en eller flera virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer med Azure PowerShell eller Azure Resource Manager mallar.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: e28a5dce4dda677ef4e5eb0ed08c42ec1f03c308
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395801"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>Aktivera Azure Monitor for VMs (för hands version) med hjälp av Azure PowerShell-eller Resource Manager-mallar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

I den här artikeln förklaras hur du aktiverar Azure Monitor for VMs (för hands version) för virtuella Azure-datorer eller skalnings uppsättningar för virtuella datorer genom att använda Azure PowerShell eller Azure Resource Manager mallar. I slutet av den här processen kommer du att ha börjat övervaka alla dina virtuella datorer och lära dig om det finns problem med prestanda eller tillgänglighet.

## <a name="set-up-a-log-analytics-workspace"></a>Konfigurera en Log Analytics-arbetsyta

Om du inte har en Log Analytics arbets yta måste du skapa en. Granska de metoder som föreslås i avsnittet [förutsättningar](vminsights-enable-overview.md#log-analytics) innan du fortsätter med stegen för att konfigurera den. Sedan kan du slutföra distributionen av Azure Monitor for VMs med hjälp av metoden Azure Resource Manager mall.

### <a name="enable-performance-counters"></a>Aktivera prestandaräknare

Om Log Analytics-arbetsytan som refereras av lösningen inte redan har konfigurerats för att samla in prestandaräknare som krävs av lösningen, måste du aktivera dem. Det kan du göra på något av två sätt:
* Manuellt, enligt beskrivningen i [prestanda data källor för Windows och Linux i Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Genom att hämta och köra ett PowerShell-skript som är tillgängligt från [Azure PowerShell galleriet](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="install-the-servicemap-solution"></a>Installera ServiceMap-lösningen

Den här metoden innehåller en JSON-mall som anger konfigurationen för att aktivera komponenterna för lösningen i Log Analytics-arbetsytan.

Om du inte vet hur du distribuerar resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du vill använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. Du kan identifiera din version genom att köra `az --version`. Information om hur du installerar eller uppgraderar Azure CLI finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Spara filen som *installsolutionsforvminsights. JSON* i en lokal mapp.

1. Avbilda värdena för *WorkspaceName*, *ResourceGroupName*och *WorkspaceLocation*. Värdet för *WorkspaceName* är namnet på din Log Analytics-arbetsyta. Värdet för *WorkspaceLocation* är den region som arbets ytan definieras i.

1. Nu är det dags att distribuera den här mallen.

    * Använd följande PowerShell-kommandon i den mapp som innehåller mallen:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Konfigurations ändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

        ```output
        provisioningState       : Succeeded
        ```

    * Så här kör du följande kommando med hjälp av Azure-CLI:

        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Konfigurations ändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

        ```output
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Aktivera med Azure Resource Manager mallar

Vi har skapat exempel Azure Resource Manager mallar för att publicera dina virtuella datorer och skalnings uppsättningar för virtuella datorer. Dessa mallar innehåller scenarier som du kan använda för att aktivera övervakning av en befintlig resurs och skapa en ny resurs som har övervakning aktive rad.

>[!NOTE]
>Mallen måste distribueras i samma resurs grupp som den resurs som ska tas ombord.

Om du inte vet hur du distribuerar resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du vill använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. Du kan identifiera din version genom att köra `az --version`. Information om hur du installerar eller uppgraderar Azure CLI finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Hämta mallar

Azure Resource Manager mallar finns i en arkivfil (. zip) som du kan [Ladda ned](https://aka.ms/VmInsightsARMTemplates) från vår GitHub-lagrings platsen. Innehållet i filen innehåller mappar som representerar varje distributions scenario med en mall och parameter fil. Innan du kör dem ändrar du parameter filen och anger de värden som krävs. Ändra inte mallfilen om du inte behöver anpassa den så att den stöder dina specifika krav. När du har ändrat parameter filen kan du distribuera den med hjälp av följande metoder som beskrivs längre fram i den här artikeln.

Nedladdnings filen innehåller följande mallar för olika scenarier:

- **ExistingVmOnboarding** -mallen aktiverar Azure Monitor for VMS om den virtuella datorn redan finns.
- **NewVmOnboarding** -mallen skapar en virtuell dator och aktiverar Azure Monitor for VMS för att övervaka den.
- **ExistingVmssOnboarding** -mallen aktiverar Azure Monitor for VMS om den virtuella datorns skal uppsättning redan finns.
- **NewVmssOnboarding** -mallen skapar skalnings uppsättningar för virtuella datorer och aktiverar Azure Monitor for VMS för att övervaka dem.
- **ConfigureWorkspace** -mallen konfigurerar Log Analytics arbets ytan till att stödja Azure Monitor for VMS genom att aktivera lösningar och samling av prestanda räknare för Linux och Windows-operativsystem.

>[!NOTE]
>Om den virtuella datorns skalnings uppsättningar redan finns och uppgraderings principen är inställd på **manuell**, aktive ras Azure Monitor for VMS inte för instanser som standard när du har kört **ExistingVmssOnboarding** Azure Resource Manager-mallen. Du måste uppgradera instanserna manuellt.

### <a name="deploy-by-using-azure-powershell"></a>Distribuera med hjälp av Azure PowerShell

Följande steg aktiverar övervakning med hjälp av Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Konfigurations ändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

```output
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Distribuera med hjälp av Azure CLI

Följande steg aktiverar övervakning med hjälp av Azure CLI.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

Resultatet ser ut ungefär så här:

```output
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Aktivera med PowerShell

Om du vill aktivera Azure Monitor for VMs för flera virtuella datorer eller skalnings uppsättningar för virtuella datorer använder du PowerShell-skriptet [install-VMInsights. ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0). Den är tillgänglig från Azure PowerShell galleriet. Det här skriptet upprepas genom:

- Varje skalnings uppsättning för virtuella datorer och virtuella datorer i din prenumeration.
- Den omfångs resurs grupp som anges av *ResourceGroup*.
- En enda virtuell dator eller en virtuell dators skalnings uppsättning som anges med *namn*.

Skriptet verifierar för varje virtuell dator eller VM-skalningsuppsättning om VM-tillägget har redan installerats. Om VM-tillägget är installerat försöker skriptet installera det igen. Om VM-tillägget inte är installerat installerar skriptet Log Analytics-och beroende agentens VM-tillägg.

Kontrol lera att du använder Azure PowerShell module AZ version 1.0.0 eller senare med `Enable-AzureRM` kompatibilitets Ali Aset aktive rad. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

Om du vill hämta en lista över skriptets argument information och exempel på användning, kör `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

I följande exempel visas med hjälp av PowerShell-kommandon i mappen att aktivera Azure Monitor för virtuella datorer och förstå utdata som förväntas:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>Nästa steg

Nu när övervakning är aktiverat för dina virtuella datorer är den här informationen tillgänglig för analys med Azure Monitor for VMs.

- Information om hur du visar identifierade program beroenden finns i [visa Azure Monitor for VMS karta](vminsights-maps.md).

- Information om hur du identifierar Flask halsar och övergripande användning med den virtuella datorns prestanda finns i [Visa prestanda för virtuella Azure-datorer](vminsights-performance.md).
