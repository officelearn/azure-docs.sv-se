---
title: Distributionshistorik
description: Beskriver hur du visar Azure Resource Manager-distributionsåtgärder med portalen, PowerShell, Azure CLI och REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: b0f196f86bed05094b04bfc20c7cef2248a91c65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460304"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Visa distributionshistorik med Azure Resource Manager

Med Azure Resource Manager kan du visa din distributionshistorik och undersöka specifika åtgärder i tidigare distributioner. Du kan se de resurser som har distribuerats och få information om eventuella fel.

Mer information om hur du löser vissa distributionsfel finns i [Lösa vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Hämta distributioner och korrelations-ID

Du kan visa information om en distribution via Azure-portalen, PowerShell, Azure CLI eller REST API. Varje distribution har ett korrelations-ID som används för att spåra relaterade händelser. Det kan vara användbart när du arbetar med teknisk support för att felsöka en distribution.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Markera den resursgrupp som du vill undersöka.

1. Välj länken under **Distributioner**.

   ![Välj distributionshistorik](./media/deployment-history/select-deployment-history.png)

1. Välj en av distributionerna från distributionshistoriken.

   ![Välj distribution](./media/deployment-history/select-details.png)

1. En sammanfattning av distributionen visas, inklusive korrelations-ID.

    ![Distributionssammanfattning](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Om du vill visa en lista över alla distributioner för en resursgrupp använder du kommandot [Get-AzResourceGroupDeployment.](/powershell/module/az.resources/Get-AzResourceGroupDeployment)

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Om du vill hämta en viss distribution från en resursgrupp lägger du till parametern **DeploymentName.**

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

För att få korrelations-ID:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill visa distributionen för en resursgrupp använder du [listan över az-distributionsgrupper](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Om du vill få en viss distribution använder du [az-distributionsgruppen show](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-show).

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

För att få korrelations-ID:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Om du vill visa distributioner för en resursgrupp använder du följande åtgärd. Det senaste API-versionsnumret som ska användas i begäran finns i [Distributioner - Lista efter resursgrupp](/rest/api/resources/deployments/listbyresourcegroup).

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

För att få en specifik distribution. använda följande åtgärd. Det senaste API-versionsnumret som ska användas i begäran finns i [Distributioner - Hämta](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Svaret innehåller korrelations-ID.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Hämta distributionsåtgärder och felmeddelanden

Varje distribution kan innehålla flera åtgärder. Om du vill se mer information om en distribution kan du visa distributionsåtgärderna. När en distribution misslyckas innehåller distributionsåtgärderna ett felmeddelande.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj **Åtgärdsinformation**i sammanfattningen för en distribution .

    ![Välj distributionsåtgärder](./media/deployment-history/get-operation-details.png)

1. Du ser information om det steget i distributionen. När ett fel uppstår innehåller informationen felmeddelandet.

    ![Visa åtgärdsinformation](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Om du vill visa distributionsåtgärderna för distribution till en resursgrupp använder du kommandot [Get-AzResourceGroupDeploymentOperation.](/powershell/module/az.resources/get-azdeploymentoperation)

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Om du vill visa misslyckade åtgärder filtrerar du åtgärder med **tillståndet Misslyckades.**

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Om du vill hämta statusmeddelandet för misslyckade åtgärder använder du följande kommando:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill visa distributionsåtgärderna för distribution till en resursgrupp använder du kommandot [för driftslist för az deployment-grupp.](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-deployment-group-operation-list)

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeployment
```

Om du vill visa misslyckade åtgärder filtrerar du åtgärder med **tillståndet Misslyckades.**

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Om du vill hämta statusmeddelandet för misslyckade åtgärder använder du följande kommando:

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Använd följande åtgärd för att hämta distributionsåtgärder. Det senaste API-versionsnumret som ska användas i begäran finns i [Deployment Operations - List](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

Svaret innehåller ett felmeddelande.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du löser vissa distributionsfel finns i [Lösa vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager](common-deployment-errors.md).
* Mer information om hur du använder aktivitetsloggarna för att övervaka andra typer av åtgärder finns i [Visa aktivitetsloggar för att hantera Azure-resurser](../management/view-activity-logs.md).
* Information om hur du validerar distributionen innan du kör den finns i [Distribuera en resursgrupp med Azure Resource Manager-mallen](deploy-powershell.md).

