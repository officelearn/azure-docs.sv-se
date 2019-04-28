---
title: Starta datorer med hjälp av Automation-runbooks i Azure DevTest Labs | Microsoft Docs
description: Lär dig mer om att starta virtuella datorer i ett labb i Azure DevTest Labs med hjälp av Azure Automation-runbooks.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: 8d3885ba25e479316f97ecbb0681a1680650fc09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61083629"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Starta virtuella datorer i ett labb i ordning med hjälp av Azure Automation-runbooks
Den [autostart](devtest-lab-set-lab-policy.md#set-autostart) funktion i DevTest Labs kan du konfigurera virtuella datorer att starta automatiskt vid en viss tidpunkt. Den här funktionen stöder dock inte datorer att starta i en viss ordning. Det finns flera scenarier där den här typen av automation är användbart.  Ett scenario är där en Jumpbox VM i ett laboratorium måste startas först, innan andra virtuella datorer, som Jumpbox används som åtkomstpunkt till andra virtuella datorer.  Den här artikeln visar hur du konfigurerar ett Azure Automation-konto med en PowerShell-runbook som kör ett skript. Skriptet använder taggar på virtuella datorer i labbet att styra startordningen utan att ändra skriptet.

## <a name="setup"></a>Konfiguration
I det här exemplet, virtuella datorer i labbet måste ha taggen **StartupOrder** lagts till med lämpligt värde (0,1,2, osv.). Ange en dator som inte behöver startas som -1.

## <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto
Skapa ett Azure Automation-konto genom att följa instruktionerna i [i den här artikeln](../automation/automation-create-standalone-account.md). Välj den **kör som-konton** när du skapar kontot. När automation-kontot har skapats kan du öppna den **moduler** och välj **uppdatera Azure-moduler** på menyraden. Standardmoduler är flera versioner gamla och utan uppdatering skriptet inte kanske fungerar.

## <a name="add-a-runbook"></a>Lägg till en runbook
Nu väljer du om du vill lägga till en runbook i automation-kontot, **Runbooks** på den vänstra menyn. Välj **Lägg till en runbook** i menyn och följ anvisningarna för att [skapa en PowerShell-runbook](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>PowerShell-skript
Följande skript tar för prenumerationsnamnet labbnamn som parametrar. Flödet av skriptet är att hämta alla virtuella datorer i labbet och sedan tolka tagginformation för att skapa en lista över de Virtuella datorerna och deras startordningen. Skriptet går igenom de virtuella datorerna i ordning och börjar de virtuella datorerna. Om det finns flera virtuella datorer i en specifik ordningsnummer, startas de asynkront med PowerShell-jobb. För dessa virtuella datorer som inte har en tagg, set startup värdet ska vara sist (10), kommer de att startas sist som standard.  Om labbet inte vill att den virtuella datorn till att autostarta, anger du Taggvärdet till 11 och kommer att ignoreras.

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
Ha det här skriptet kör varje dag, [skapa ett schema](../automation/shared-resources/schedules.md#creating-a-schedule) i automation-kontot. När schemat skapas [länkar den till runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

I en storskalig situation där det finns flera prenumerationer med flera labs, lagra parameterinformationen i en fil för olika labb och skicka filen till skriptet i stället för enskilda parametrarna. Skriptet måste ändras, men körningen core skulle vara samma. Det här exemplet använder Azure Automation för att köra PowerShell-skriptet, finns men det andra alternativ som att använda en aktivitet i en pipeline för version och utgåva.

## <a name="next-steps"></a>Nästa steg
Finns i följande artikel om du vill veta mer om Azure Automation: [En introduktion till Azure Automation](../automation/automation-intro.md).
