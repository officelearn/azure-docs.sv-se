---
title: Starta datorer med Automation-runbooks i Azure DevTest Labs
description: Lär dig hur du startar virtuella datorer i ett labb i Azure DevTest Labs med hjälp av Azure Automation-runbooks.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9bb97a73b7ca570ca122323e8e9c5a70c9348b15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166314"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Starta virtuella datorer i ett labb i ordning med hjälp av Azure Automation-runbooks
Med [funktionen för automatisk start i](devtest-lab-set-lab-policy.md#set-autostart) DevTest Labs kan du konfigurera virtuella datorer så att de startar automatiskt vid en viss tidpunkt. Den här funktionen stöder dock inte datorer för att starta i en viss ordning. Det finns flera scenarier där den här typen av automatisering skulle vara användbar.  Ett scenario är där en Virtuell Jumpbox-dator i ett labb måste startas först, innan de andra virtuella datorerna, eftersom Jumpbox används som åtkomstpunkt till de andra virtuella datorerna.  Den här artikeln visar hur du konfigurerar ett Azure Automation-konto med en PowerShell-runbook som kör ett skript. Skriptet använder taggar på virtuella datorer i labbet så att du kan styra startordningen utan att behöva ändra skriptet.

## <a name="setup"></a>Installation
I det här exemplet måste virtuella datorer i labbet ha taggen **StartupOrder** tillagd med rätt värde (0,1,2 osv.). Ange alla datorer som inte behöver startas som -1.

## <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto
Skapa ett Azure Automation-konto genom att följa instruktionerna i [den här artikeln](../automation/automation-create-standalone-account.md). Välj alternativet **Kör som konton** när du skapar kontot. När automatiseringskontot har **skapats** öppnar du sidan Moduler och väljer **Uppdatera Azure-moduler** i menyraden. Standardmodulerna är flera gamla versioner och utan uppdateringen kanske skriptet inte fungerar.

## <a name="add-a-runbook"></a>Lägga till en runbook
Om du vill lägga till en runbook i automationskontot väljer du **Runbooks** på den vänstra menyn. Välj **Lägg till en runbook** på menyn och följ instruktionerna för att skapa en [PowerShell-runbook](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>PowerShell-skript
Följande skript tar prenumerationsnamnet, labbnamnet som parametrar. Flödet av skriptet är att få alla virtuella datorer i labbet, och sedan tolka ut tagginformation för att skapa en lista över VM-namn och deras startordning. Skriptet går igenom de virtuella datorerna i ordning och startar de virtuella datorerna. Om det finns flera virtuella datorer i ett visst ordernummer startas de asynkront med PowerShell-jobb. För de virtuella datorer som inte har en tagg anger du startvärdet till det sista (10), de startas senast, som standard.  Om labbet inte vill att den virtuella datorn ska startas automatiskt anger du taggvärdet till 11 och ignoreras.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Skapa ett schema
Om du vill att skriptet körs dagligen [skapar du en schema](../automation/shared-resources/schedules.md#creating-a-schedule) i automatiseringskontot. När schemat har skapats [länkar du det till runbooken](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

I en storskalig situation där det finns flera prenumerationer med flera labb, lagra parameterinformationen i en fil för olika labb och skicka filen till skriptet i stället för de enskilda parametrarna. Skriptet skulle behöva ändras, men kärnkörningen skulle vara densamma. Medan det här exemplet använder Azure Automation för att köra PowerShell-skriptet, finns det andra alternativ som att använda en uppgift i en Build/Release-pipeline.

## <a name="next-steps"></a>Nästa steg
Se följande artikel om du vill veta mer om Azure Automation: [En introduktion till Azure Automation](../automation/automation-intro.md).
