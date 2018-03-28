---
title: Använda Azure CLI för att skapa en principtilldelning som identifierar icke-kompatibla resurser i Azure-miljön | Microsoft Docs
description: Använda PowerShell för att skapa en Azure Policy-tilldelning som identifierar icke-kompatibla resurser.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: f56f00aabbef2cfa86264d3e962af9a9c0bafa98
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Skapa en principtilldelning för att identifiera icke-kompatibla resurser i Azure-miljön med Azure CLI

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status. Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar.

Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är *inkompatibla* med principtilldelningen.

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. I den här guiden används Azure CLI för att skapa en principtilldelning och för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

För den här snabbstarten måste du köra Azure CLI version 2.0.4 eller senare för att installera och använda CLI:t lokalt. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).



## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten skapar du en principtilldelning och tilldelar definitionen Granska virtuella datorer utan Managed Disks. Den här principdefinitionen ska identifiera resurser som inte uppfyller villkoren i principdefinitionen.

Kör följande kommando för att skapa en ny principtilldelning:

```
az policy assignment create --name 'Audit Virtual Machines without Managed Disks Assignment' --scope '<scope>' --policy '<policy definition ID>' --sku 'standard'
```

Föregående kommando använder följande information:

- **Namn** – Visningsnamn för principtilldelningen. I det här fallet använder du *tilldelningen Granska virtuella datorer utan Managed Disks*.
- **Policy** – Principdefinitions-ID:t som du använder som bas för att skapa tilldelningen. I det här fallet är principdefinitionen *Granska virtuella datorer utan Managed Disks*. För att hämta principdefinitionens ID kör du det här kommandot: `az policy definition show --name 'Audit Virtual Machines without Managed Disks Assignment'`
- **Omfång** – Ett omfång avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Det kan vara allt från en prenumeration till resursgrupper. Kom ihåg att ersätta &lt;omfång&gt; med namnet på din resursgrupp.
- **Sku** – Det här kommandot skapar en principtilldelning med standardnivån. Med standardnivån kan du få skalenlig hantering, utvärdering av efterlevnad och reparation. Standardnivån är kostnadsfri för närvarande. I framtiden kommer standardnivån att vara avgiftsbelagd. När prissättningsändringen sker meddelas det, och mer information finns i [priserna för Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy).


## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Om du vill visa de resurser som inte är kompatibla under den nya tilldelningen hämtar du principtilldelnings-ID:t genom att köra följande kommandon:

```
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```
$policyAssignment.PolicyAssignmentId
```

Mer information om principtilldelnings-ID finns i [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Kör sedan följande kommando för att hämta resurs-ID:n för de icke-kompatibla resurserna som matas ut till en JSON-fil:

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Ditt resultat liknar följande exempel:

```
{
"@odata.context":"https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
"@odata.count": 3,
"value": [
{
    "@odata.id": null,
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
    },
    {
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
         },
{
      "@odata.id": null,
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
      "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
         }

]
}

```

Resultatet är jämförbart med det du vanligtvis skulle se under **Icke-kompatibla resurser** i Azure-portalens vy.

## <a name="clean-up-resources"></a>Rensa resurser

De andra guiderna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta arbeta med senare självstudier ska du inte rensa upp resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta tar du bort tilldelningen som du skapade genom att köra följande kommando:

```azurecli
az policy assignment delete –name Audit Virtual Machines without Managed Disks Assignment --scope /subscriptions/ <subscriptionID> / <resourceGroupName>
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du vill lära dig mer om att tilldela principer och se till att **framtida** resurser som skapas är kompatibla kan du gå vidare till självstudien för:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./create-manage-policy.md)
