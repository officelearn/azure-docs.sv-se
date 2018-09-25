---
title: Publicera Azure Monitor för virtuella datorer | Microsoft Docs
description: Den här artikeln beskrivs hur du publicerar och konfigurera Azure Monitor för virtuella datorer så att du kan börja med att förstå hur det distribuerade programmet fungerar och vilka hälsoproblem har identifierats.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 2f0568064eed556429675ffb34c84d588ac670d5
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064364"
---
# <a name="how-to-onboard-the-azure-monitor-for-vms"></a>Hur att publicera Azure övervakar för virtuella datorer 
Den här artikeln beskriver hur du ställer in Azure Monitor för virtuella datorer för att övervaka hälsotillståndet för operativsystemet för virtuella datorer i Azure och identifiera och mappa programberoenden som kan vara baserad på dem.  

Aktivera Azure Monitor för virtuella datorer görs på något av följande metoder och information om hur du använder varje metod finns senare i artikeln.  

* En enda Azure-dator genom att välja **Insights (förhandsversion)** direkt från den virtuella datorn.
* Flera virtuella Azure-datorer med hjälp av Azure Policy för att se till att befintliga och nya virtuella datorer som utvärderas har de nödvändiga beroendena installeras och konfigureras korrekt.  Icke-kompatibla virtuella datorer rapporteras så att du kan avgöra baserat på vad inte är kompatibel, hur du vill åtgärda.  
* Flera virtuella Azure-datorer eller VM scale sets för över en viss prenumeration eller resursgrupp med hjälp av PowerShell.

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar bör du kontrollera att du har följande enligt beskrivningen i underordnade avsnitten nedan.

### <a name="log-analytics"></a>Log Analytics 

Log Analytics-arbetsytan i följande regioner stöds för närvarande:

  - Västra centrala USA  
  - Östra USA  
  - Västra Europa  
  - Sydostasien<sup>1</sup>  

<sup>1</sup> den här regionen stöder för närvarande inte funktionen Health i Azure Monitor för virtuella datorer   

Om du inte har en arbetsyta kan du skapa det via [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), via [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), eller i den [Azure-portalen](../log-analytics/log-analytics-quick-create-workspace.md).  

Du måste vara medlem i rollen som deltagare Log Analytics för att aktivera lösningen. Läs mer om hur du styr åtkomst till en Log Analytics-arbetsyta, [hantera arbetsytor](../log-analytics/log-analytics-manage-access.md).

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

### <a name="supported-operating-systems"></a>Operativsystem som stöds

I följande tabell visas de Windows- och Linux-operativsystem som stöds med Azure Monitor för virtuella datorer.  En fullständig lista med information om Linux-operativsystem högre och den lägre versionen och kernel-versioner som stöds finns senare i det här avsnittet.

|Operativsystemversion |Prestanda |Kartor |Hälsa |  
|-----------|------------|-----|-------|  
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |  
|Windows Server 2012 R2 | X | X | |  
|Windows Server 2012 | X | X | |  
|Windows Server 2008 R2 | X | X| |  
|RHEL 7, 6| X | X| X |  
|Ubuntu 18.04, 16.04, 14.04 | X | X | X |  
|Cent OS Linux 7, 6 | X | X | X |  
|SLES 12 | X | X | X |  
|Oracle Linux 7 | X<sup>1</sup> | | X |  
|Oracle Linux 6 | X | X | X |  
|Debian 9.4, 8 | X<sup>1</sup> | | X | 

<sup>1</sup> the prestanda funktion i Azure Monitor för virtuella datorer är endast tillgänglig från Azure Monitor, det är inte tillgängligt när du har åtkomst till den från den vänstra rutan i Azure VM direkt.  

>[!NOTE]
>Följande information gäller för att ge stöd för Linux-operativsystem:  
> - Endast standardversioner och SMP Linux-kernelversioner stöds.  
> - Avvikande kernelversioner, som exempelvis PAE och Xen, stöds inte för någon Linux-distribution. Till exempel stöds ett system med release-sträng med ”2.6.16.21-0.8-xen” inte.  
> - Anpassade kernelversioner inklusive omkompileringar av standardkernelversioner, stöds inte.  
> - CentOSPlus-kernel stöds inte.  


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operativsystemversion | Kernel-version |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

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

### <a name="hybrid-environment-connected-sources"></a>Hybridmiljö anslutna källor
Azure Monitor för virtuella datorer kartan hämtar data från Microsoft Dependency agenten. Beroendeagenten är beroende av Log Analytics-agenten ansluter till Log Analytics. Det innebär att ett system måste ha Log Analytics-agenten installeras och konfigureras med beroendeagenten.  I följande tabell beskrivs de anslutna källor som har stöd för funktionen kartan i en hybridmiljö.

| Ansluten källa | Stöds | Beskrivning |
|:--|:--|:--|
| Windows-agenter | Ja | Förutom den [Log Analytics-agenten för Windows](../log-analytics/log-analytics-concept-hybrid.md), Windows-agenter kräver Microsoft Dependency agenten. Se [Operativsystem som stöds](#supported-operating-systems) för en fullständig lista med operativsystemversioner. |
| Linux-agenter | Ja | Förutom den [Log Analytics-agenten för Linux](../log-analytics/log-analytics-concept-hybrid.md), kräver Microsoft Dependency agenten för Linux-agenter. Se [Operativsystem som stöds](#supported-operating-systems) för en fullständig lista med operativsystemversioner. |
| System Center Operations Manager-hanteringsgrupp | Nej | |  

På Windows, Microsoft Monitoring Agent (MMA) används av både System Center Operations Manager och Log Analytics att samla in och skicka övervakningsdata. System Center Operations Manager och Log Analytics tillhandahåller olika out-nyckelfärdig versioner av agenten. Båda dessa versioner kan rapportera till System Center Operations Manager, till Log Analytics eller till båda.  

På Linux, Log Analytics-agenten för Linux samlar och skickar övervakning av data till Log Analytics.   

Om din Windows- eller Linux-datorer inte kan ansluta direkt till tjänsten, måste du konfigurera Log Analytics-agenten för att ansluta till Log Analytics med hjälp av OMS-gatewayen. Mer information om hur du distribuerar och konfigurerar OMS-gatewayen finns [ansluter datorer utan Internetåtkomst med OMS-gatewayen](../log-analytics/log-analytics-oms-gateway.md).  

Beroendeagenten kan hämtas från följande plats.

| Fil | Operativsystem | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.1 | 55030ABF553693D8B5112569FB2F97D7C54B66E9990014FC8CC43EFB70DE56C6 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.1 | 43C75EF0D34471A0CBCE5E396FFEEF4329C9B5517266108FA5D6131A353D29FE |

## <a name="diagnostic-and-usage-data"></a>Diagnostik- och användningsdata
Microsoft samlar automatiskt in användnings- och data via din användning av Azure Monitor-tjänsten. Microsoft använder dessa data för att tillhandahålla och förbättra kvaliteten, säkerheten och integriteten för tjänsten. Data från funktionen kartan innehåller information om konfigurationen av din programvara, till exempel operativsystem och version, IP-adress, DNS-namn och namn på arbetsstation för att tillhandahålla korrekta och effektiva funktioner för felsökning. Microsoft samlar inte in namn, adresser eller annan kontaktinformation.

Mer information om insamling och användning finns i den [sekretesspolicyn för Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="performance-counters-enabled"></a>Prestandaräknare som aktiverats
Azure Monitor för virtuella datorer konfigureras en Log Analytics-arbetsyta för att samla in prestandaräknare som används av lösningen.  I följande tabell visas de objekt och räknare som konfigurerats av lösningen som samlas in var 60: e sekund.

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

## <a name="enable-from-the-azure-portal"></a>Aktivera från Azure portal
Om du vill aktivera övervakning av Azure-VM i Azure-portalen, gör du följande:

1. I Azure-portalen väljer du **virtuella datorer**. 
2. Välj en virtuell dator i listan. 
3. På sidan virtuell dator i den **övervakning** väljer **Insights (förhandsversion)**.
4. På den **Insights (förhandsversion)** väljer **Prova nu**.

    ![Aktivera Azure Monitor för virtuella datorer för en virtuell dator](./media/monitoring-vminsights-onboard/enable-vminsights-vm-portal-01.png)

5. På den **Azure Monitor insikter Onboarding** om du har en befintlig Log Analytics-arbetsyta i samma prenumeration, markerar du den i den nedrullningsbara listan.  Listan förväljer standardarbetsytan och plats som den virtuella datorn har distribuerats till i prenumerationen. 

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervakningsdata från den virtuella datorn följer du anvisningarna i [skapa en Log Analytics-arbetsyta](../log-analytics/log-analytics-quick-create-workspace.md) i någon av regionerna som stöds ovan.   

När du har aktiverat övervakning, kan det ta ungefär 10 minuter innan du kan visa hälsomått för den virtuella datorn. 

![Aktivera Azure Monitor för virtuella datorer övervakning distributionsbearbetning](./media/monitoring-vminsights-onboard/onboard-vminsights-vm-portal-status.png)

## <a name="enable-using-azure-policy"></a>Aktivera med hjälp av Azure Policy
Att aktivera lösningen för flera virtuella Azure-datorer som säkerställer konsekvent efterlevnad och automatisk aktivering för nya virtuella datorer som etablerats [Azure Policy](../azure-policy/azure-policy-introduction.md) rekommenderas.  Med hjälp av Azure Policy enligt de principer som angetts ger följande fördelar för nya virtuella datorer:

* Aktivera Azure Monitor för virtuella datorer för varje virtuell dator i definierade omfattning
* Distribuera Log Analytics-agenten 
* Distribuera Beroendeagenten för att identifiera programberoenden och visas på kartan
* Granska om din Azure VM OS-avbildning i en fördefinierad lista i principdefinitionen  
* Granska om arbetsytan än den angivna loggar in Azure-VM
* Rapport om kompatibilitetsresultat 
* Stöd för åtgärder för inkompatibla virtuella datorer

Om du vill aktivera den för din klient, kräver den här processen:

- Konfigurera en Log Analytics-arbetsyta med hjälp av anvisningarna här
- Importera initiativdefinition till din klient (på Hanteringsgruppen eller prenumerationen nivå)
- Tilldela principen till önskad omfattning
- Granska kompatibilitetsresultaten

### <a name="add-the-policies-and-initiative-to-your-subscription"></a>Lägga till principer och initiativ i prenumerationen
Om du vill använda principerna som du kan använda ett angivet PowerShell-skript – [Lägg till VMInsightsPolicy.ps1](https://www.powershellgallery.com/packages/Add-VMInsightsPolicy/1.2) tillgängliga från Azure PowerShell-galleriet för att slutföra den här uppgiften. Skriptet lägger till principer och ett initiativ till din prenumeration.  Utför följande steg för att konfigurera Azure Policy i din prenumeration. 

1. Ladda ned PowerShell-skriptet till ditt lokala filsystem.

2. Använd följande PowerShell-kommando i mappen för att lägga till principer. Skriptet har stöd för följande valfria parametrar: 

    ```powershell
    -UseLocalPolicies [<SwitchParameter>]
      <Optional> Load the policies from a local folder instead of https://raw.githubusercontent.com/dougbrad/OnBoardVMInsights/Policy/Policy/

    -SubscriptionId <String>
      <Optional> SubscriptionId to add the Policies/Initiatives to
    -ManagementGroupId <String>
      <Optional> Management Group Id to add the Policies/Initiatives to

    -Approve [<SwitchParameter>]
      <Optional> Gives the approval to add the Policies/Initiatives without any prompt
    ```  

    >[!NOTE]
    >Obs: Om du planerar att tilldela initiativ/principen till flera prenumerationer, definitionerna måste vara lagrad i hanteringsgruppen som innehåller de prenumerationer som du tilldelar principen till. Därför måste du använda parametern - ManagementGroupID.
    >
   
    Exempel utan parametrar:  `.\Add-VMInsightsPolicy.ps1`

### <a name="create-a-policy-assignment"></a>Skapa en principtilldelning
När du har kört den `Add-VMInsightsPolicy.ps1` PowerShell-skript, följande initiativ och principer har lagts till:

* **Distribuera Log Analytics-agenten för virtuella Windows-datorer – förhandsversion**
* **Distribuera Log Analytics-agenten för Linux-datorer – förhandsversion**
* **Distribuera Beroendeagenten för virtuella Windows-datorer – förhandsversion**
* **Distribuera Beroendeagenten för virtuella Linux-datorer – förhandsversion**
* **Granska distribution - VM Image (OS) olistade - förhandsversionen av Log Analytics-agenten**
* **Granska beroende Agent Deployment - VM Image (OS) olistade - Preview**

Följande initiativ parameter har lagts till:

- **Logga Analytice arbetsyta** (du måste ange resurs-ID för arbetsytan kopplar en tilldelning med PowerShell eller CLI)

    För virtuella datorer finns som icke-kompatibla från granskningsprinciperna **virtuella datorer inte i OS omfång...**  villkor princip för programdistribution bara innehåller virtuella datorer som distribueras från välkända Azure VM-avbildningar. I dokumentationen om VM-operativsystem som stöds eller inte.  Om den inte behöver duplicera princip för programdistribution och uppdatera/ändra det så att bilden i omfånget.

Följande valfria fristående princip har lagts till:

- **Virtuell dator har konfigurerats för Felmatchade Log Analytics-arbetsyta – förhandsversion**

    Detta kan användas för att identifiera virtuella datorer som redan har konfigurerats med den [Log Analytics VM-tillägget](../virtual-machines/extensions/oms-windows.md), men har konfigurerats med en annan arbetsyta än avsett (vilket indikeras med principtilldelningen). Detta tar en parameter för WorkspaceID.

Du kan bara skapa principtilldelningen från Azure-portalen med den här första versionen. Information om hur du utför de här stegen finns i [skapa en principtilldelning från Azure portal](../azure-policy/assign-policy-definition.md).

## <a name="enable-with-powershell"></a>Aktivera med PowerShell
Om du vill aktivera Azure Monitor för virtuella datorer för flera virtuella datorer eller VM-skalningsuppsättningar, kan du använda ett angivet PowerShell-skript – [installera VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0) tillgängliga från Azure PowerShell-galleriet för att slutföra den här uppgiften.  Det här skriptet ska gå igenom varje virtuell dator och VM-skalningsuppsättning i din prenumeration, i den begränsade resursgruppen som anges av *ResourceGroup*, eller till en enda virtuell dator eller skala som anges av *namn*.  För varje virtuell dator eller virtuell dator kontrollerar skriptet-skalningsuppsättning om VM-tillägget har installerats, och om inte försöker installera det på nytt.  I annat fall fortsätter den att installera Log Analytics och beroende Agent VM-tillägg.   

Det här skriptet kräver Azure PowerShell-Modulversion 5.7.0-installationsprogram eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

Om du vill ha hjälp med att skriptet kan du köra `Get-Help` att hämta en lista över argument information och exempel på användning.   

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and Dependency Agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VM's and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VM's/VM Scale Sets that will apply to and let you confirm to continue.
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
        If using PolicyAssignmentName parameter, subscription that VM's are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong to

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VM's to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VM's/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Suported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
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

    Install for all VM's in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to ReInstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to ReInstall (move to a new workspace)
```

I följande exempel visas med hjälp av PowerShell-kommandon i mappen att aktivera Azure Monitor för virtuella datorer och förstå utdata som förväntas:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VM's or VM ScaleSets matching criteria specified

VM's or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency Agent extensions on above 2 VM's or VM Scale Sets.
VM's in a non-running state will be skipped.
Extension will not be re-installed if already installed. Use -ReInstall if desired, for example to update workspace

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

## <a name="enable-for-hybrid-environment"></a>Aktivera för hybridmiljö
Det här avsnittet beskrivs hur du publicera virtuella datorer eller fysiska datorer som värd ditt datacenter eller andra molnmiljö för övervakning av Azure Monitor för virtuella datorer.  

I Azure Monitor för virtuella datorer kartan beroendeagenten överföra inte några data själva och det kräver inte ändringar i brandväggar eller portar. Data på kartan överförs alltid genom Log Analytics-agenten i Azure Monitor-tjänsten, antingen direkt eller via den [OMS-gatewayen](../log-analytics/log-analytics-oms-gateway.md) om din IT-säkerhetsprinciper inte tillåter att datorer i nätverket att ansluta till Internet.

Granska kraven och distributionsmetoder för den [Log Analytics Linux och Windows-agenten](../log-analytics/log-analytics-concept-hybrid.md).

Sammanfattande steg:

1. Installera Log Analytics-agenten för Windows eller Linux
2. Installera Azure Monitor för virtuella datorer kartan beroendeagent
3. Aktivera insamling av prestandaräknare
4. Publicera Azure Monitor för virtuella datorer

### <a name="install-the-dependency-agent-on-windows"></a>Installera beroendeagenten på Windows 
Beroendeagenten kan installeras manuellt på Windows-datorer genom att köra `InstallDependencyAgent-Windows.exe`. Om du kör den här körbara filen utan några alternativ, startar en installationsguide som du kan följa för att installera interaktivt.  

>[!NOTE]
>Administratörsbehörighet krävs för att installera eller avinstallera agenten.

Följande tabell visar de specifika parametrarna som stöds av installationsprogrammet för agenten från kommandoraden.  

| Parameter | Beskrivning |
|:--|:--|
| /? | Returnerar en lista över kommandoradsalternativ. |
| / S | Utföra en tyst installation utan interaktion från användaren. |

Till exempel för att köra installationsprogrammet med det `/?` parametern, Fyll `InstallDependencyAgent-Windows.exe /?`

Filer för Windows beroendeagenten installeras i `C:\Program Files\Microsoft Dependency Agent` som standard.  Om det inte går att starta när installationen är klar beroendeagenten, loggarna för Detaljerad felinformation. Loggkatalogen är `%Programfiles%\Microsoft Dependency Agent\logs`. 

### <a name="install-the-dependency-agent-on-linux"></a>Installera beroendeagenten på Linux
Beroende-agenten är installerad på Linux-servrar från `InstallDependencyAgent-Linux64.bin`, ett kommandoskript med en självextraherande binärfilen. Du kan köra filen med hjälp av `sh` eller Lägg till körbehörighet till själva filen.

>[!NOTE]
> Du måste ha rotbehörighet för att kunna installera eller konfigurera agenten.
> 

| Parameter | Beskrivning |
|:--|:--|
| -hjälp | Hämta en lista med kommandoradsalternativ. |
| -s | Utför en tyst installation utan någon användarprompter. |
| – Kontrollera | Kontrollera behörigheter och operativsystemet, men installera inte agenten. |

Till exempel för att köra installationsprogrammet med det `-help` parametern, Fyll `InstallDependencyAgent-Linux64.bin -help`.

Installera beroendeagenten för Linux som rot genom att köra följande kommando `sh InstallDependencyAgent-Linux64.bin`
    
Om det inte går att starta beroendeagenten, kontrollera loggarna för Detaljerad felinformation. På Linux-agenter till loggkatalogen är `/var/opt/microsoft/dependency-agent/log`.

Filer för beroendeagenten placeras i följande kataloger:

| Filer | Plats |
|:--|:--|
| Kärnfiler | /opt/microsoft/dependency-agent |
| Loggfiler | /var/opt/microsoft/dependency-agent/log |
| Konfigurationsfiler | /etc/opt/microsoft/dependency-agent/config |
| Körbara tjänstfiler | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binära lagringsfiler | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Aktivera prestandaräknare
Om Log Analytics-arbetsytan som refereras av lösningen inte är konfigurerat för att samla in prestandaräknare som krävs av lösningen redan kan måste de vara aktiverat. Detta kan åstadkommas manuellt enligt [här](../log-analytics/log-analytics-data-sources-performance-counters.md), eller genom att hämta och köra ett PowerShell-skript som är tillgängliga från [Azure Powershell-galleriet](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).
 
### <a name="onboard-azure-monitor-for-vms"></a>Publicera Azure Monitor för virtuella datorer
Den här metoden innehåller en JSON-mall som anger konfigurationen för att aktivera lösningskomponenter till Log Analytics-arbetsytan.  

Om du inte är bekant med begreppet att distribuera resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 

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
    ```

2. Spara filen som **installsolutionsforvminsights.json** till en lokal mapp.
3. Redigera värdena för **WorkspaceName**, **ResourceGroupName**, och **WorkspaceLocation**.  Värdet för **WorkspaceName** är den är fullständiga resurs-ID för Log Analytics-arbetsytan, som innehåller namnet på arbetsytan och värdet för **WorkspaceLocation** är den region som arbetsytan har definierats i.
4. Du är redo att distribuera den här mallen med hjälp av följande PowerShell-kommando:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

    ```powershell
    provisioningState       : Succeeded
    ```
När du har aktiverat övervakning, kan det ta ungefär 10 minuter innan du kan visa hälsotillstånd och mått för hybrid-dator. 

## <a name="next-steps"></a>Nästa steg

Den här informationen är tillgänglig för analys med Azure Monitor för virtuella datorer med övervakning aktiverad för den virtuella datorn.  Läs hur du använder funktionen hälsotillstånd i [visa Azure Monitor för virtuella datorer Health](monitoring-vminsights-health.md), eller om du vill visa identifierade programberoenden, se [visa Azure Monitor för virtuella datorer kartan](monitoring-vminsights-maps.md).  