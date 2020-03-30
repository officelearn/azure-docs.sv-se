---
title: 'Snabbstart: Ny principtilldelning med Azure CLI'
description: I den här snabbstarten använder du Azure CLI för att skapa en Azure Policy-tilldelning för att identifiera icke-kompatibla resurser.
ms.date: 01/11/2020
ms.topic: quickstart
ms.openlocfilehash: 7f76191d97a936c745fc2b13b54011e787e0b5e6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75978325"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Snabbstart: Skapa en principtilldelning för att identifiera icke-kompatibla resurser med Azure CLI

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status.
Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar.

Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är _inkompatibla_ med principtilldelningen.

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. I den här guiden används Azure CLI för att skapa en principtilldelning och för att identifiera icke-kompatibla resurser i Azure-miljön.

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Den här snabbstarten kräver att du kör Azure CLI version 2.0.76 eller senare för att installera och använda CLI lokalt. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

- Registrera azure policy insights-resursleverantören med Azure CLI. När du registrerar resursprovidern säkerställer du att din prenumeration fungerar med den. Om du vill registrera en resursprovider måste du ha behörighet att utföra åtgärden att registrera resursprovidern. Den här åtgärden ingår i rollerna Deltagare och Ägare. Registrera resursprovidern genom att köra följande kommando:

  ```azurecli-interactive
  az provider register --namespace 'Microsoft.PolicyInsights'
  ```

  Läs mer om att registrera och visa resursprovidrar i [(Resursprovidrar och typer)](../../azure-resource-manager/management/resource-providers-and-types.md)

- Installera [ARMClient](https://github.com/projectkudu/ARMClient), om du inte redan gjort det. Det är ett verktyg som skickar HTTP-begäranden till Azure Resource Manager-baserade API:er.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten skapar du en principtilldelning och tilldelar definitionen **Granska virtuella datorer som inte använder hanterade diskar**. Den här principdefinitionen identifierar resurser som inte uppfyller villkoren i principdefinitionen.

Kör följande kommando för att skapa en ny principtilldelning:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

Föregående kommando använder följande information:

- **Namn** – det faktiska namnet på tilldelningen. I det här exemplet användes _audit-vm-manageddisks_.
- **Visningsnamn** – Visningsnamn för principtilldelningen. I det här fallet använder du _tilldelningen Granska virtuella datorer utan Managed Disks_.
- **Policy** – Principdefinitions-ID:t som du använder som bas för att skapa tilldelningen. I det här fallet är principdefinitionens ID _Granska virtuella datorer som inte använder hanterade diskar_. För att hämta principdefinitionens ID kör du det här kommandot: `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Omfång** – Ett omfång avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Det kan vara allt från en prenumeration till resursgrupper. Kom ihåg att ersätta &lt;omfång&gt; med namnet på din resursgrupp.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Om du vill visa de resurser som inte är kompatibla under den nya tilldelningen hämtar du principtilldelnings-ID:t genom att köra följande kommandon:

```azurecli-interactive
az policy assignment list --query "[?displayName=='Audit VMs without managed disks Assignment'].id"
```

Mer information om principtilldelnings-ID finns i [az principtilldelning](/cli/azure/policy/assignment).

Kör sedan följande kommando för att hämta resurs-ID:n för de icke-kompatibla resurserna som matas ut till en JSON-fil:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-09-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Ditt resultat liknar följande exempel:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
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

Om du vill ta bort den skapade tilldelningen använder du följande kommando:

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du vill ha mer information om tilldelning av principer för att validera att de nya resurserna är kompatibla fortsätter du till självstudien för att:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)