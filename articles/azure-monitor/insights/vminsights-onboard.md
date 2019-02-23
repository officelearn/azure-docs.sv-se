---
title: Distribuera Azure Monitor för virtuella datorer (förhandsversion) | Microsoft Docs
description: Den här artikeln beskriver hur du distribuerar och konfigurerar Azure Monitor för virtuella datorer så att du kan börja med att förstå hur det distribuerade programmet fungerar och vilka hälsoproblem har identifierats.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2019
ms.author: magoedte
ms.openlocfilehash: 874e8da66fb01df1b25d14bd8d86186f0f6b55bb
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733902"
---
# <a name="deploy-azure-monitor-for-vms-preview"></a>Distribuera Azure Monitor för virtuella datorer (förhandsversion)
Den här artikeln beskriver hur du ställer in Azure Monitor för virtuella datorer. Tjänsten övervakar operativsystemet hälsotillståndet för dina Azure-datorer (VM) och VM-skalningsuppsättningar och de virtuella datorerna i din miljö. Den här övervakning innefattar identifierings- och mappningen av programberoenden som kan finnas på dem. 

Du kan aktivera Azure Monitor för virtuella datorer med hjälp av något av följande metoder:

* Aktivera en Azure virtuell dator genom att välja **Insights (förhandsversion)** direkt från den virtuella datorn.
* Aktivera minst två virtuella Azure-datorer med hjälp av Azure Policy. Med den här metoden de nödvändiga beroendena för befintliga och nya virtuella datorer installeras och konfigureras korrekt. Icke-kompatibla virtuella datorer rapporteras så att du kan avgöra om du vill aktivera dem och hur du vill åtgärda dem.
* Aktivera två eller flera virtuella Azure-datorer eller virtuella datorn skalningsuppsättningar över en viss prenumeration eller resursgrupp med hjälp av PowerShell.

Mer information om varje metod finns senare i artikeln.

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar bör du kontrollera att du förstår informationen i följande avsnitt.

### <a name="log-analytics"></a>Log Analytics

Azure Monitor för virtuella datorer stöder en Log Analytics-arbetsyta i följande regioner:

- Västra centrala USA
- Östra USA
- Västra Europa
- Sydostasien<sup>1</sup>

<sup>1</sup> den här regionen för tillfället stöder inte funktionen Health i Azure Monitor för virtuella datorer.

>[!NOTE]
>Azure-datorer kan distribueras från alla regioner och är inte begränsad till regionerna som stöds för Log Analytics-arbetsytan.
>

Om du inte har en arbetsyta kan skapa du en med någon av följande metoder:
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Om du aktiverar övervakning för en virtuell Azure-dator i Azure-portalen, kan du skapa en arbetsyta under den här processen.

För att aktivera lösningen för scenariot i skala, konfigurera följande i din Log Analytics-arbetsyta:

* Installera ServiceMap och InfrastructureInsights lösningar. Du kan slutföra den här installationen med en Azure Resource Manager-mall som har angetts i den här artikeln.
* Konfigurera Log Analytics-arbetsytan för att samla in prestandaräknare.

Om du vill konfigurera din arbetsyta för scenariot i skala, finns i Konfigurera Log Analytics-arbetsyta för distribution av i skala.

### <a name="supported-operating-systems"></a>Operativsystem som stöds

I följande tabell visas de Windows- och Linux-operativsystem som stöds med Azure Monitor för virtuella datorer. En fullständig lista som beskriver den högre och den lägre Linux OS-versionen och kernel-versioner som stöds finns senare i det här avsnittet.

|Operativsystemversion |Prestanda |Kartor |Hälsa |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X |  |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 7, 6| X | X| X |
|Ubuntu 18.04, 16.04, 14.04 | X | X | X |
|CentOS Linux 7, 6 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Oracle Linux 7 | X<sup>1</sup> | | X |
|Oracle Linux 6 | X | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | | X |

<sup>1</sup> the prestanda funktion i Azure Monitor för virtuella datorer är tillgängliga från Azure Monitor. Det är inte tillgängligt när du har åtkomst till den direkt från den vänstra rutan i Azure VM.

>[!NOTE]
>Följande information gäller för att ge stöd för Linux-operativsystem:
> - Endast standardversioner och SMP Linux-kernelversioner stöds.
> - Avvikande kernel versioner, t.ex. (Physical Address tillägget) och Xen, inte stöds för alla Linux-distribution. Till exempel ett system med release-sträng med *2.6.16.21-0.8-xen* stöds inte.
> - Anpassade kernlar, inklusive omkompileringar av kernlar som är standard, stöds inte.
> - CentOSPlus kernel stöds inte.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operativsystemversion | Kernel-version |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Operativsystemversion | Kernel-version |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Operativsystemversion | Kernel-version |
|:--|:--|
| Ubuntu 18.04 | Kernel 4.15. * |
| Ubuntu 16.04.3 | Kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 6 med Unbreakable Enterprise Kernel
| Operativsystemversion | Kernel-version
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 5 med Unbreakable Enterprise Kernel

| Operativsystemversion | Kernel-version
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Operativsystemversion | Kernel-version
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency-agent
I Azure Monitor för virtuella datorer kartan funktionen hämtar data från Microsoft Dependency agenten. Beroendeagenten är beroende av Log Analytics-agenten ansluter till Log Analytics. Därför måste systemet ha Log Analytics-agenten installeras och konfigureras med beroendeagenten.

Oavsett om du aktiverar Azure Monitor för virtuella datorer för en virtuell Azure-dator eller du använda metoden i skala distributionen kan behöva du använda agent-tillägget för Azure VM beroende för att installera agenten som en del av upplevelsen.

I en hybridmiljö kan du hämta och installera beroendeagenten på något av två sätt: Manuellt eller genom att använda en automatisk distributionsmetod för virtuella datorer som finns utanför Azure.

I följande tabell beskrivs de anslutna källor som har stöd för funktionen kartan i en hybridmiljö.

| Ansluten källa | Stöds | Beskrivning |
|:--|:--|:--|
| Windows-agenter | Ja | Förutom den [Log Analytics-agenten för Windows](../../azure-monitor/platform/log-analytics-agent.md), Windows-agenter kräver Microsoft Dependency agenten. En fullständig lista över operativsystemversioner, se [operativsystem som stöds](#supported-operating-systems). |
| Linux-agenter | Ja | Förutom den [Log Analytics-agenten för Linux](../../azure-monitor/platform/log-analytics-agent.md), kräver Microsoft Dependency agenten för Linux-agenter. En fullständig lista över operativsystemversioner, se [operativsystem som stöds](#supported-operating-systems). |
| System Center Operations Manager-hanteringsgrupp | Nej | |

Beroendeagenten kan hämtas från följande platser:

| Fil | Operativsystem | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll
För att aktivera och få åtkomst till funktionerna i Azure Monitor för virtuella datorer, måste tilldelas följande roller för åtkomst:

- Om du vill aktivera lösningen måste du ha den *Log Analytics contributor* roll.

- Om du vill visa prestanda, hälsotillstånd och mappa data, måste du ha den *övervakning läsare* rollen för den virtuella Azure-datorn. Log Analytics-arbetsytan måste konfigureras för Azure Monitor för virtuella datorer.

Läs mer om hur du styr åtkomst till en Log Analytics-arbetsyta, [hantera arbetsytor](../../azure-monitor/platform/manage-access.md).

## <a name="enable-monitoring-in-the-azure-portal"></a>Aktivera övervakning i Azure portal
Om du vill aktivera övervakning av Azure-VM i Azure-portalen, gör du följande:

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **virtuella datorer**.

1. Välj en virtuell dator i listan.

1. På sidan virtuell dator i den **övervakning** väljer **Insights (förhandsversion)**.

1. På den **Insights (förhandsversion)** väljer **Prova nu**.

    ![Aktivera Azure Monitor för virtuella datorer för en virtuell dator](./media/vminsights-onboard/enable-vminsights-vm-portal-01.png)
1. På den **Azure Monitor insikter Onboarding** om du har en befintlig Log Analytics-arbetsyta i samma prenumeration, markerar du den i den nedrullningsbara listan.  
    Listan förväljer standardarbetsytan och plats som den virtuella datorn har distribuerats till i prenumerationen. 

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervakningsdata från den virtuella datorn följer du anvisningarna i [skapa en Log Analytics-arbetsyta](../../azure-monitor/learn/quick-create-workspace.md) i någon av regionerna som stöds ovan.

När du har aktiverat övervakning, kan det ta ungefär 10 minuter innan du kan visa hälsomått för den virtuella datorn.

![Aktivera Azure Monitor för virtuella datorer övervakning distributionsbearbetning](./media/vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="deploy-at-scale"></a>Distribuera i stor skala
I det här avsnittet distribuerar du Azure Monitor för virtuella datorer i stor skala med hjälp av Azure Policy eller Azure PowerShell.

Innan du distribuerar dina virtuella datorer, förkonfigurera Log Analytics-arbetsytan genom att göra följande:

1. Om du inte redan har en arbetsyta, skapa en som stöder Azure Monitor för virtuella datorer.  
    Innan du fortsätter Se [hantera arbetsytor](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json) att förstå överväganden kostnader, hantering och kompatibilitet.

1. Skapa en ny arbetsyta om det inte redan finns som kan användas för att stödja Azure Monitor för virtuella datorer. Granska [hantera arbetsytor](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json) innan du skapar en ny arbetsyta för att förstå överväganden kostnader, hantering och kompatibilitet innan du fortsätter.

1. Aktivera prestandaräknare på arbetsytan för samlingen på Linux- och Windows-datorer.

1. Installera och aktivera ServiceMap och InfrastructureInsights lösningen i din arbetsyta.

### <a name="set-up-a-log-analytics-workspace"></a>Konfigurera en Log Analytics-arbetsyta
Om du inte har en Log Analytics-arbetsyta kan du skapa en genom att granska de metoder som föreslås i den [”krav”](#log-analytics) avsnittet.

#### <a name="enable-performance-counters"></a>Aktivera prestandaräknare
Om Log Analytics-arbetsytan som refereras av lösningen inte redan har konfigurerats för att samla in prestandaräknare som krävs av lösningen, måste du aktivera dem. Du kan göra det på något av två sätt:
* Manuellt, enligt beskrivningen i [Windows och Linux prestanda datakällor i Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Genom att hämta och köra ett PowerShell.skript som är tillgänglig från [Azure PowerShell-galleriet](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Installera ServiceMap och InfrastructureInsights lösningar
Den här metoden innehåller en JSON-mall som anger konfigurationen för att aktivera komponenterna för lösningen i Log Analytics-arbetsytan.

Om du inte är bekant med att distribuera resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. För att identifiera din version, kör `az --version`. Om du behöver installera eller uppgradera Azure CLI kan du läsa [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Spara filen som *installsolutionsforvminsights.json* till en lokal mapp.

1. Samla in värden för *WorkspaceName*, *ResourceGroupName*, och *WorkspaceLocation*. Värdet för *WorkspaceName* är namnet på Log Analytics-arbetsytan. Värdet för *WorkspaceLocation* är den region som arbetsytan är definierad i.

1. Nu är det dags att distribuera den här mallen.
 
    * Använd följande PowerShell-kommandon i den mapp som innehåller mallen:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        Konfigurationsändringen kan ta några minuter att slutföra. När den är klar visas ett meddelande som liknar följande och som innehåller resultatet:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Så här kör du följande kommando med hjälp av Azure-CLI:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

        ```azurecli
        provisioningState       : Succeeded

### Enable by using Azure Policy
To enable Azure Monitor for VMs at scale in a way that helps ensure consistent compliance and the automatic enabling of the newly provisioned VMs, we recommend [Azure Policy](../../governance/policy/overview.md). These policies:

* Deploy the Log Analytics agent and the Dependency agent.
* Report on compliance results.
* Remediate for non-compliant VMs.

To enable Azure Monitor for VMs by using Azure Policy in your tenant:

- Assign the initiative to a scope: management group, subscription, or resource group
- Review and remediate compliance results

For more information about assigning Azure Policy, see [Azure Policy overview](../../governance/policy/overview.md#policy-assignment) and review the [overview of management groups](../../governance/management-groups/index.md) before you continue.

The policy definitions are listed in the following table:

|Name |Description |Type |
|-----|------------|-----|
|[Preview]: Enable Azure Monitor for VMs |Enable Azure Monitor for the Virtual Machines (VMs) in the specified scope (management group, subscription, or resource group). Takes Log Analytics workspace as a parameter. |Initiative |
|[Preview]: Audit Dependency Agent Deployment – VM Image (OS) unlisted |Reports VMs as non-compliant if the VM Image (OS) isn't defined in the list and the agent isn't installed. |Policy |
|[Preview]: Audit Log Analytics Agent Deployment – VM Image (OS) unlisted |Reports VMs as non-compliant if the VM Image (OS) isn't defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Dependency Agent for Linux VMs |Deploy Dependency Agent for Linux VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Dependency Agent for Windows VMs |Deploy Dependency Agent for Windows VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Log Analytics Agent for Linux VMs |Deploy Log Analytics Agent for Linux VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |
|[Preview]: Deploy Log Analytics Agent for Windows VMs |Deploy Log Analytics Agent for Windows VMs if the VM Image (OS) is defined in the list and the agent isn't installed. |Policy |

Standalone policy (not included with the initiative) is described here:

|Name |Description |Type |
|-----|------------|-----|
|[Preview]: Audit Log Analytics Workspace for VM - Report Mismatch |Report VMs as non-compliant if they aren't logging to the Log Analytics workspace specified in the policy/initiative assignment. |Policy |

#### Assign the Azure Monitor initiative
With this initial release, you can create the policy assignment only in the Azure portal. To understand how to complete these steps, see [Create a policy assignment from the Azure portal](../../governance/policy/assign-policy-portal.md).

1. To launch the Azure Policy service in the Azure portal, select **All services**, and then search for and select **Policy**.

1. In the left pane of the Azure Policy page, select **Assignments**.  
    An assignment is a policy that has been assigned to take place within a specific scope.
    
1. At the top of the **Policy - Assignments** page, select **Assign Initiative**.

1. On the **Assign Initiative** page, select the **Scope** by clicking the ellipsis (...), and select a management group or subscription.  
    In our example, a scope limits the policy assignment to a grouping of virtual machines for enforcement.
    
1. At the bottom of the **Scope** page, save your changes by selecting **Select**.

1. (Optional) To remove one or more resources from the scope, select **Exclusions**.

1. Select the **Initiative definition** ellipsis (...) to display the list of available definitions, select **[Preview] Enable Azure Monitor for VMs**, and then select **Select**.  
    The **Assignment name** box is automatically populated with the initiative name you selected, but you can change it. You can also add an optional description. The **Assigned by** box is automatically populated based on who is logged in, and this value is optional.
    
1. In the **Log Analytics workspace** drop-down list for the supported region, select a workspace.

    >[!NOTE]
    >If the workspace is beyond the scope of the assignment, grant *Log Analytics Contributor* permissions to the policy assignment's Principal ID. If you don't do this, you might see a deployment failure such as: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ... `
    >To grant access, review [how to manually configure the managed identity](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
    >  
    The **Managed Identity** check box is selected, because the initiative being assigned includes a policy with the *deployIfNotExists* effect.
    
1. In the **Manage Identity location** drop-down list, select the appropriate region.

1. Select **Assign**.

#### Review and remediate the compliance results

You can learn how to review compliance results by reading [identify non-compliance results](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). In the left pane, select **Compliance**, and then locate the **[Preview] Enable Azure Monitor for VMs** initiative for VMs that aren't compliant according to the assignment you created.

![Policy compliance for Azure VMs](./media/vminsights-onboard/policy-view-compliance-01.png)

Based on the results of the policies included with the initiative, VMs are reported as non-compliant in the following scenarios:

* Log Analytics or the Dependency agent isn't deployed.  
    This scenario is typical for a scope with existing VMs. To mitigate it, deploy the required agents by [creating remediation tasks](../../governance/policy/how-to/remediate-resources.md) on a non-compliant policy.  
    - [Preview]: Deploy Dependency Agent for Linux VMs
    - [Preview]: Deploy Dependency Agent for Windows VMs
    - [Preview]: Deploy Log Analytics Agent for Linux VMs
    - [Preview]: Deploy Log Analytics Agent for Windows VMs

* VM Image (OS) isn't identified in the policy definition.  
    The criteria of the deployment policy include only VMs that are deployed from well-known Azure VM images. Check the documentation to see whether the VM OS is supported. If it isn't supported, duplicate the deployment policy and update or modify it to make the image compliant.  
    - [Preview]: Audit Dependency Agent Deployment – VM Image (OS) unlisted
    - [Preview]: Audit Log Analytics Agent Deployment – VM Image (OS) unlisted

* VMs aren't logging in to the specified Log Analytics workspace.  
    It's possible that some VMs in the initiative scope are logging in to a Log Analytics workspace other than the one that's specified in the policy assignment. This policy is a tool to identify which VMs are reporting to a non-compliant workspace.  
    - [Preview]: Audit Log Analytics Workspace for VM - Report Mismatch

### Enable with PowerShell
To enable Azure Monitor for VMs for multiple VMs or virtual machine scale sets, you can use the PowerShell script [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), available from the Azure PowerShell Gallery. This script iterates through every virtual machine and virtual machine scale set in your subscription, in the scoped resource group that's specified by *ResourceGroup*, or to a single VM or virtual machine scale set that's specified by *Name*. For each VM or virtual machine scale set, the script verifies whether the VM extension is already installed. If the VM extension is not installed, the script tries to reinstall it. If the VM extension is installed, the script installs the Log Analytics and Dependency agent VM extensions.

This script requires Azure PowerShell module version 5.7.0 or later. Run `Get-Module -ListAvailable AzureRM` to find the version. If you need to upgrade, see [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). If you're running PowerShell locally, you also need to run `Connect-AzureRmAccount` to create a connection with Azure.

To get a list of the script's argument details and example usage, run `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VMs and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VMs/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


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

    Install for all VMs in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to reinstall (move to a new workspace)
```

I följande exempel visas med hjälp av PowerShell-kommandon i mappen att aktivera Azure Monitor för virtuella datorer och förstå utdata som förväntas:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or VM ScaleSets matching criteria specified

VMs or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on above 2 VMs or VM Scale Sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example to update workspace

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

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-a-hybrid-environment"></a>Aktivera för en hybridmiljö
Det här avsnittet beskrivs hur du distribuerar virtuella datorer eller fysiska datorer som finns i ditt datacenter eller i andra miljöer i molnet för övervakning av Azure Monitor för virtuella datorer.

I Azure Monitor för virtuella datorer kartan beroendeagenten överföra inte några data själva och det kräver inte ändringar i brandväggar eller portar. Kartdata överförs alltid genom Log Analytics-agenten i Azure Monitor-tjänsten, antingen direkt eller via den [OMS-gatewayen](../../azure-monitor/platform/gateway.md) om din IT-säkerhetsprinciper inte tillåter att datorer i nätverket för att ansluta till internet.

Granska kraven och distributionsmetoder för den [Log Analytics Linux och Windows-agenten](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Stegen kan sammanfattas på följande sätt:

1. Installera Log Analytics-agenten för Windows eller Linux.

1. Ladda ned och installera Azure Monitor för virtuella datorer kartan beroendeagenten för [Windows](https://aka.ms/dependencyagentwindows) eller [Linux](https://aka.ms/dependencyagentlinux).

1. Aktivera insamling av prestandaräknare.

1. Distribuera Azure Monitor för virtuella datorer.

### <a name="install-the-dependency-agent-on-windows"></a>Installera beroendeagenten på Windows
Du kan installera beroendeagenten manuellt på Windows-datorer genom att köra `InstallDependencyAgent-Windows.exe`. Om du kör den här körbara filen utan några alternativ, startar en installationsguide som du kan följa för att installera agenten interaktivt.

>[!NOTE]
>*Administratören* behörighet krävs för att installera eller avinstallera agenten.

Följande tabell visar de parametrar som stöds av installationsprogrammet för agenten från kommandoraden.

| Parameter | Beskrivning |
|:--|:--|
| /? | Returnerar en lista över kommandoradsalternativ. |
| / S | Utför en tyst installation utan interaktion från användaren. |

Till exempel för att köra installationsprogrammet med det `/?` parametern, Fyll **InstallDependencyAgent Windows.exe /?**.

Filer för Windows beroendeagenten installeras i *C:\Program Files\Microsoft Beroendeagenten* som standard. Om det inte går att starta när installationen är klar beroendeagenten, loggarna för Detaljerad felinformation. Loggkatalogen är *%Programfiles%\Microsoft beroende Agent\logs*.

### <a name="install-the-dependency-agent-on-linux"></a>Installera beroendeagenten på Linux
Beroende-agenten är installerad på Linux-servrar från *InstallDependencyAgent Linux64.bin*, ett kommandoskript med en självextraherande binärfilen. Du kan köra filen med hjälp av `sh` eller Lägg till körbehörighet till själva filen.

>[!NOTE]
> Du måste ha rotbehörighet för att kunna installera eller konfigurera agenten.
>

| Parameter | Beskrivning |
|:--|:--|
| -hjälp | Hämta en lista med kommandoradsalternativ. |
| -s | Utför en tyst installation utan någon användarprompter. |
| – Kontrollera | Kontrollera behörigheter och operativsystemet, men inte installera agenten. |

Till exempel för att köra installationsprogrammet med det `-help` parametern, Fyll **InstallDependencyAgent Linux64.bin-hjälpa**.

Installera beroendeagenten för Linux som rot genom att köra följande kommando, `sh InstallDependencyAgent-Linux64.bin`.

Om det inte går att starta beroendeagenten, kontrollera loggarna för Detaljerad felinformation. På Linux-agenter till loggkatalogen är */var/opt/microsoft/dependency-agent/log*.

Filer för beroendeagenten placeras i följande kataloger:

| Filer | Plats |
|:--|:--|
| Kärnfiler | /opt/microsoft/dependency-agent |
| Loggfiler | /var/opt/microsoft/dependency-agent/log |
| Konfigurationsfiler | /etc/opt/microsoft/dependency-agent/config |
| Körbara tjänstfiler | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binära lagringsfiler | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Aktivera prestandaräknare
Om Log Analytics-arbetsytan som refereras av lösningen inte är konfigurerad för att samla in prestandaräknare som krävs av lösningen, behöver så att de. Du kan göra det på något av två sätt:
* Manuellt, enligt beskrivningen i [Windows och Linux prestanda datakällor i Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Genom att hämta och köra ett PowerShell.skript som är tillgänglig från [Azure PowerShell-galleriet](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="deploy-azure-monitor-for-vms"></a>Distribuera Azure Monitor för virtuella datorer
Den här metoden innehåller en JSON-mall som anger konfigurationen för att aktivera komponenterna för lösningen i Log Analytics-arbetsytan.

Om du inte är bekant med att distribuera resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. För att identifiera din version, kör `az --version`. Om du behöver installera eller uppgradera Azure CLI kan du läsa [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

#### <a name="create-and-execute-a-template"></a>Skapa och köra en mall

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
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Spara filen som *installsolutionsforvminsights.json* till en lokal mapp.

1. Redigera värdena för *WorkspaceName*, *ResourceGroupName*, och *WorkspaceLocation*. Värdet för *WorkspaceName* är fullständiga resurs-ID för Log Analytics-arbetsytan, som innehåller namnet på arbetsytan. Värdet för *WorkspaceLocation* är den region som arbetsytan är definierad i.

1. Är du redo att distribuera den här mallen med hjälp av följande PowerShell-kommando:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Konfigurationsändringen kan ta några minuter att slutföra. När den är klar visas ett meddelande som liknar följande och som innehåller resultatet:

    ```powershell
    provisioningState       : Succeeded
    ```
När du har aktiverat övervakning, kan det ta ungefär 10 minuter innan du kan visa hälsotillstånd och mått för hybrid-dator.

## <a name="performance-counters-enabled"></a>Prestandaräknare som aktiverats
Azure Monitor för virtuella datorer konfigurerar Log Analytics-arbetsytan för att samla in prestandaräknare som används av lösningen. I följande tabell visas de objekt och räknare som konfigurerats av lösningen som samlas in var 60: e sekund.

### <a name="windows-performance-counters"></a>Windows-prestandaräknare

|Objektnamn |Namn på räknare |
|------------|-------------|
|Logisk disk |Ledigt utrymme i procent |
|Logisk disk |Genomsn. S/diskläsning |
|Logisk disk |Genomsn. S/disköverföring |
|Logisk disk |Genomsn. S/diskskrivning |
|Logisk disk |Disk byte/sek |
|Logisk disk |Disk – lästa byte/sek |
|Logisk disk |Diskläsningar/sek |
|Logisk disk |Disköverföringar/sek |
|Logisk disk |Disk – skrivna byte/sek |
|Logisk disk |Diskskrivningar/sek |
|Logisk disk |Ledigt utrymme i MB |
|Minne |Tillgängliga megabyte |
|Nätverkskort |Mottagna byte/sek |
|Nätverkskort |Skickade byte/sek |
|Processor |Tid i procent för processor |

### <a name="linux-performance-counters"></a>Prestandaräknare för Linux

|Objektnamn |Namn på räknare |
|------------|-------------|
|Logisk Disk |Använt utrymme i procent |
|Logisk Disk |Disk – lästa byte/sek |
|Logisk Disk |Diskläsningar/sek |
|Logisk Disk |Disköverföringar/sek |
|Logisk Disk |Disk – skrivna byte/sek |
|Logisk Disk |Diskskrivningar/sek |
|Logisk Disk |Ledigt utrymme i MB |
|Logisk Disk |Logisk Disk byte/sek |
|Minne |Tillgängligt minne i megabyte |
|Nätverk |Totalt antal byte mottaget |
|Nätverk |Totalt antal överförda byte |
|Processor |Tid i procent för processor |

## <a name="diagnostic-and-usage-data"></a>Diagnostik- och användningsdata
Microsoft samlar automatiskt in användnings- och data via din användning av Azure Monitor-tjänsten. Microsoft använder dessa data för att tillhandahålla och förbättra kvaliteten, säkerheten och integriteten för tjänsten. Data från funktionen kartan innehåller information om konfigurationen av din programvara, till exempel operativsystem och version, IP-adress, DNS-namn och namn på arbetsstation för att tillhandahålla korrekta och effektiva funktioner för felsökning. Microsoft samlar inte in namn, adresser eller annan kontaktinformation.

Mer information om insamling och användning finns i den [sekretesspolicyn för Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]
## <a name="next-steps"></a>Nästa steg

Nu när övervakning är aktiverad för den virtuella datorn bör är den här informationen tillgänglig för analys med Azure Monitor för virtuella datorer. Läs hur du använder funktionen hälsotillstånd i [visa Azure Monitor för virtuella datorer Health](vminsights-health.md). Identifierade programberoenden finns [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md).
