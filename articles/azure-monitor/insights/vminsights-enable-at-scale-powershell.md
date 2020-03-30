---
title: Aktivera Azure Monitor för virtuella datorer med PowerShell eller mallar
description: I den här artikeln beskrivs hur du aktiverar Azure Monitor för virtuella datorer för en eller flera Azure-virtuella datorer eller skalningsuppsättningar för virtuella datorer eller virtuella datorer med hjälp av Azure PowerShell- eller Azure Resource Manager-mallar.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: 75d5203e7c475a44b6a00dbf9286f43114b7b54f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480852"
---
# <a name="enable-azure-monitor-for-vms-using-azure-powershell-or-resource-manager-templates"></a>Aktivera Azure Monitor för virtuella datorer med Azure PowerShell- eller Resource Manager-mallar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

I den här artikeln beskrivs hur du aktiverar Azure Monitor för virtuella datorer för virtuella Azure-datorer eller skalningsuppsättningar för virtuella datorer med hjälp av Azure PowerShell- eller Azure Resource Manager-mallar. I slutet av den här processen har du framgångsrikt börjat övervaka alla dina virtuella datorer och lära dig om någon upplever prestanda eller tillgänglighet problem.

## <a name="set-up-a-log-analytics-workspace"></a>Konfigurera en log analytics-arbetsyta

Om du inte har en Log Analytics-arbetsyta måste du skapa en. Granska de metoder som [föreslås](vminsights-enable-overview.md#log-analytics) i avsnittet Förutsättningar innan du fortsätter med stegen för att konfigurera den. Sedan kan du slutföra distributionen av Azure Monitor för virtuella datorer med hjälp av mallmetoden Azure Resource Manager.

### <a name="install-the-vminsights-solution"></a>Installera VMInsights-lösningen

Den här metoden innehåller en JSON-mall som anger konfigurationen för att aktivera lösningskomponenterna på logganalysarbetsytan.

Om du inte vet hur du distribuerar resurser med hjälp av en mall läser du:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du vill använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. Om du vill `az --version`identifiera din version kör du . Information om hur du installerar eller uppgraderar Azure CLI finns [i Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Spara den här filen som *installsolutionsforvminsights.json* i en lokal mapp.

1. Samla in värdena för *WorkspaceName,* *ResourceGroupName*och *WorkspaceLocation*. Värdet för *WorkspaceName* är namnet på log analytics-arbetsytan. Värdet för *WorkspaceLocation* är den region som arbetsytan har definierats i.

1. Nu är det dags att distribuera den här mallen.

    * Använd följande PowerShell-kommandon i mappen som innehåller mallen:

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

        ```output
        provisioningState       : Succeeded
        ```

    * Så här kör du följande kommando med hjälp av Azure CLI:

        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

        ```output
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>Aktivera med Azure Resource Manager-mallar

Vi har skapat exempel på Azure Resource Manager-mallar för introduktion av virtuella datorer och skaluppsättningar för virtuella datorer. Dessa mallar innehåller scenarier som du kan använda för att aktivera övervakning på en befintlig resurs och skapa en ny resurs som har övervakning aktiverat.

>[!NOTE]
>Mallen måste distribueras i samma resursgrupp som resursen som ska tas ombord.

Om du inte vet hur du distribuerar resurser med hjälp av en mall läser du:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du vill använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. Om du vill `az --version`identifiera din version kör du . Information om hur du installerar eller uppgraderar Azure CLI finns [i Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="download-templates"></a>Ladda ned mallar

Azure Resource Manager-mallarna finns i en arkivfil (.zip) som du kan [hämta](https://aka.ms/VmInsightsARMTemplates) från vår GitHub-repo. Innehållet i filen innehåller mappar som representerar varje distributionsscenario med en mall och parameterfil. Innan du kör dem ändrar du parameterfilen och anger de värden som krävs. Ändra inte mallfilen om du inte behöver anpassa den för att stödja dina specifika krav. När du har ändrat parameterfilen kan du distribuera den med hjälp av följande metoder som beskrivs senare i den här artikeln.

Hämtningsfilen innehåller följande mallar för olika scenarier:

- **Befintlig mall för befintligt programmall** möjliggör Azure Monitor för virtuella datorer om den virtuella datorn redan finns.
- **NewVmOnboarding-mall** skapar en virtuell dator och gör det möjligt för Azure Monitor för virtuella datorer att övervaka den.
- **BefintligmallEnSOnboarding** aktiverar Azure Monitor för virtuella datorer om skalningsuppsättningen för den virtuella datorn redan finns.
- **NewVmssOnboarding-mall** skapar skaluppsättningar för virtuella datorer och gör det möjligt för Azure Monitor för virtuella datorer att övervaka dem.
- **ConfigureWorkspace-mallen** konfigurerar din Log Analytics-arbetsyta så att den stöder Azure Monitor för virtuella datorer genom att aktivera lösningar och insamling av prestandaräknare för Linux- och Windows-operativsystem.

>[!NOTE]
>Om skalningsuppsättningar för virtuella datorer redan fanns och uppgraderingsprincipen är inställd på **Manuell**aktiveras inte Azure Monitor för virtuella datorer för instanser som standard efter att ha kört mallen **ExistingVmssOnboarding** Azure Resource Manager. Du måste uppgradera instanserna manuellt.

### <a name="deploy-by-using-azure-powershell"></a>Distribuera med hjälp av Azure PowerShell

Följande steg möjliggör övervakning med hjälp av Azure PowerShell.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

```output
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>Distribuera med hjälp av Azure CLI

Följande steg möjliggör övervakning med hjälp av Azure CLI.

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

Utdata liknar följande:

```output
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>Aktivera med PowerShell

Om du vill aktivera Azure Monitor för virtuella datorer för flera virtuella datorer eller skaluppsättningar för virtuella datorer använder du [PowerShell-skriptet Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0). Den är tillgänglig från Azure PowerShell Gallery. Detta skript itererar genom:

- Varje virtuell dator och skala för virtuella datorer som anges i din prenumeration.
- Den scoped-resursgrupp som anges av *ResourceGroup*.
- En skalauppsättning för en virtuell dator eller virtuell dator som anges av *namn*.

För varje vm- eller virtuell datorskalauppsättning verifierar skriptet om vm-tillägget redan är installerat. Om vm-tillägget är installerat försöker skriptet installera om det. Om vm-tillägget inte är installerat installerar skriptet VM-tilläggen Log Analytics och Dependency Agent.

Kontrollera att du använder Azure PowerShell-modulen Az version 1.0.0 eller senare med `Enable-AzureRM` kompatibilitetsalias aktiverade. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

Om du vill hämta en lista över skriptets `Get-Help`argumentinformation och exempelanvändning kör du .

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

I följande exempel visas hur du använder PowerShell-kommandona i mappen för att aktivera Azure Monitor för virtuella datorer och förstå den förväntade utdata:

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

Nu när övervakning är aktiverat för dina virtuella datorer är den här informationen tillgänglig för analys med Azure Monitor för virtuella datorer.

- Information om hur du visar identifierade programberoenden finns i [Visa Azure Monitor för virtuella datorer Karta](vminsights-maps.md).

- Information om hur du identifierar flaskhalsar och övergripande användning med den virtuella datorns prestanda finns i [Visa Azure VM-prestanda](vminsights-performance.md).
