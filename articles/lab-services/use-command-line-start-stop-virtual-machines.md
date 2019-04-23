---
title: Använd kommandoradsverktygen för att starta och stoppa virtuella datorer Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du använder kommandoradsverktyg för att starta och stoppa virtuella datorer i Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 51c45fdb0c96e84d3f37f485279aa805361f3818
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798937"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Använd kommandoradsverktygen för att starta och stoppa virtuella datorer i Azure DevTest Labs
Den här artikeln visar hur du använder Azure PowerShell eller Azure CLI för att starta eller stoppa virtuella datorer i ett labb i Azure DevTest Labs. Du kan skapa PowerShell/CLI-skript för att automatisera de här åtgärderna. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt
Azure DevTest Labs är ett sätt att skapa snabba, enkla och resurssnåla dev/test-miljöer. Det kan du hantera kostnader, snabbt etablera virtuella datorer och minimera spill.  Det finns inbyggda funktioner i Azure portal så att du kan konfigurera virtuella datorer i ett testlabb för att automatiskt starta och stoppa vid specifika tidpunkter. 

Men i vissa situationer kan du automatisera starta och stoppa virtuella datorer från PowerShell/CLI-skript. Det ger dig viss flexibilitet med starta och stoppa enskilda datorer när som helst i stället för vid specifika tidpunkter. Här följer några situationer i vilka körs dessa uppgifter med hjälp av skript skulle vara till hjälp.

- När du använder en 3-nivåprogram som en del av en testmiljö, måste nivåerna startas i en sekvens. 
- Stäng av en virtuell dator när en anpassade villkor uppfylls för att spara pengar. 
- Använda den som en aktivitet i ett CI/CD-arbetsflöde för att starta i början av flödet, använder de virtuella datorerna bygger datorer, testa datorer eller infrastruktur och sedan stoppa de virtuella datorerna när processen är klar. Ett exempel på detta är den anpassa avbildningen fabriken med Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell
Följande PowerShell-skript startar en virtuell dator i ett labb. [Anropa AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azurermps-6.13.0) är primärt fokus för det här skriptet. Den **ResourceId** parametern är fullständigt kvalificerade resurs-ID för den virtuella datorn i laboratoriet. Den **åtgärd** parametern är där den **starta** eller **stoppa** alternativ ställs in beroende på vad som behövs.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
if ($(Get-Module -Name AzureRM).Version.Major -eq 6) {
    $devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -Name $devTestLabName
} else {
    $devTestLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $devTestLabName
}

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure CLI
Den [Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) är ett annat sätt att automatisera startas och stoppas DevTest Labs virtuella datorer. Azure CLI kan vara [installerat](/cli/azure/install-azure-cli?view=azure-cli-latest) på olika operativsystem. Följande skript innehåller kommandon för att starta och stoppa en virtuell dator i ett labb. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Nästa steg
Se följande artikel för att använda Azure-portalen för att göra dessa åtgärder: [Starta om en virtuell dator](devtest-lab-restart-vm.md).
