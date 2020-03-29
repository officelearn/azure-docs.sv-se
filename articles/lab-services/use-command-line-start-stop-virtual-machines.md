---
title: Använda kommandoradsverktyg för att starta och stoppa virtuella datorer Azure DevTest Labs
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: fd643559a09d5c75aad9be5f35c653994c8488cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169255"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Använda kommandoradsverktyg för att starta och stoppa virtuella Azure DevTest Labs-datorer
Den här artikeln visar hur du använder Azure PowerShell eller Azure CLI för att starta eller stoppa virtuella datorer i ett labb i Azure DevTest Labs. Du kan skapa PowerShell/CLI-skript för att automatisera dessa åtgärder. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt
Azure DevTest Labs är ett sätt att skapa snabba, enkla och lean dev/test-miljöer. Det gör att du kan hantera kostnader, snabbt etablera virtuella datorer och minimera avfall.  Det finns inbyggda funktioner i Azure-portalen som gör att du kan konfigurera virtuella datorer i ett labb för att automatiskt starta och stoppa vid specifika tidpunkter. 

I vissa fall kanske du vill automatisera start och stopp av virtuella datorer från PowerShell/CLI-skript. Det ger dig viss flexibilitet med att starta och stoppa enskilda maskiner när som helst istället för vid specifika tidpunkter. Här är några av de situationer där köra dessa uppgifter med hjälp av skript skulle vara till hjälp.

- När du använder ett program på tre nivåer som en del av en testmiljö måste nivåerna startas i en sekvens. 
- Inaktivera en virtuell dator när ett anpassat villkor uppfylls för att spara pengar. 
- Använd den som en uppgift i ett CI/CD-arbetsflöde för att starta i början av flödet, använd de virtuella datorerna som byggmaskiner, testdatorer eller infrastruktur och stoppa sedan de virtuella datorerna när processen är klar. Ett exempel på detta skulle vara den anpassade avbildningsfabriken med Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> Följande skript använder Azure PowerShell Az-modulen. 

Följande PowerShell-skript startar en virtuell dator i ett labb. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) är det primära fokuset för det här skriptet. **Parametern ResourceId** är fullständigt kvalificerat resurs-ID för den virtuella datorn i labbet. Parametern **Åtgärd** är där **start-** eller **stoppalternativen** ställs in beroende på vad som behövs.

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
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

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
[Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) är ett annat sätt att automatisera start och stopp av virtuella devtest labs-datorer. Azure CLI kan [installeras](/cli/azure/install-azure-cli?view=azure-cli-latest) på olika operativsystem. Följande skript ger dig kommandon för att starta och stoppa en virtuell dator i ett labb. 

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
Se följande artikel för att använda Azure-portalen för att utföra dessa åtgärder: [Starta om en virtuell dator](devtest-lab-restart-vm.md).
