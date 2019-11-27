---
title: Distributions historik
description: Beskriver hur du visar Azure Resource Manager distributions åtgärder med portalen, PowerShell, Azure CLI och REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 895704e5c4cb8acc60067809bdd7e7baa6f05142
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538907"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Visa distributions historik med Azure Resource Manager

Med Azure Resource Manager kan du Visa distributions historiken och granska vissa åtgärder i tidigare distributioner. Du kan se de resurser som har distribuerats och få information om eventuella fel.

Information om hur du löser specifika distributions fel finns i [lösa vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Hämta distributioner och korrelations-ID

Du kan visa information om en distribution via Azure Portal, PowerShell, Azure CLI eller REST API. Varje distribution har ett korrelations-ID som används för att spåra relaterade händelser. Det kan vara användbart när du arbetar med teknisk support för att felsöka en distribution.

# <a name="portaltabazure-portal"></a>[Portalen](#tab/azure-portal)

1. Välj den resurs grupp som du vill undersöka.

1. Välj länken under **distributioner**.

   ![Välj distributions historik](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Välj en av distributionerna från distributions historiken.

   ![Välj distribution](./media/resource-manager-deployment-operations/select-details.png)

1. En sammanfattning av distributionen visas, inklusive korrelations-ID: t. 

    ![Distributions Sammanfattning](./media/resource-manager-deployment-operations/show-correlation-id.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill visa en lista över alla distributioner för en resurs grupp använder du kommandot [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Om du vill hämta en speciell distribution från en resurs grupp lägger du till parametern **DeploymentName** .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Använd följande för att hämta korrelations-ID:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill visa en lista över distributionen av en resurs grupp använder du [AZ Group Deployment List](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-list).

```azurecli-interactive
az group deployment list --resource-group ExampleGroup
```

Om du vill hämta en enskild distribution använder du [AZ Group Deployment show](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-show).

```azurecli-interactive
az group deployment show --resource-group ExampleGroup --name ExampleDeployment
```
  
Använd följande för att hämta korrelations-ID:

```azurecli-interactive
az group deployment show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="httptabhttp"></a>[HTTP](#tab/http)

Om du vill visa en lista över distributioner för en resurs grupp använder du följande åtgärd. För det senaste API-versions numret som ska användas i begäran, se [distributioner – lista efter resurs grupp](/rest/api/resources/deployments/listbyresourcegroup). 

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

För att få en speciell distribution. Använd följande åtgärd. Det senaste API-versions numret som ska användas i begäran finns i [distributioner-get](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Svaret innehåller korrelations-ID: t.

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

## <a name="get-deployment-operations-and-error-message"></a>Hämta distributions åtgärder och fel meddelande

Varje distribution kan innehålla flera åtgärder. Visa distributions åtgärder om du vill ha mer information om en distribution. När en distribution Miss lyckas innehåller distributions åtgärderna ett fel meddelande.

# <a name="portaltabazure-portal"></a>[Portalen](#tab/azure-portal)

1. I sammanfattningen för en distribution väljer du **Åtgärds information**.

    ![Välj distributions åtgärder](./media/resource-manager-deployment-operations/get-operation-details.png)

1. Du ser informationen för det steget i distributionen. När ett fel uppstår innehåller informationen fel meddelandet.

    ![Visa åtgärds information](./media/resource-manager-deployment-operations/see-operation-details.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill visa distributions åtgärderna för distribution till en resurs grupp använder du kommandot [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation) .

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Om du vill visa misslyckade åtgärder filter åtgärder med **felaktigt** tillstånd.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Använd följande kommando för att hämta status meddelandet för misslyckade åtgärder:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill visa distributions åtgärderna för distribution till en resurs grupp använder du kommandot [AZ Group Deployment operation List](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-group-deployment-operation-list) .

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeployment
```

Om du vill visa misslyckade åtgärder filter åtgärder med **felaktigt** tillstånd.

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Använd följande kommando för att hämta status meddelandet för misslyckade åtgärder:

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="httptabhttp"></a>[HTTP](#tab/http)

Använd följande åtgärd för att hämta distributions åtgärder. Det senaste API-versions numret som ska användas i begäran finns i [distributions åtgärder-lista](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

Svaret innehåller ett fel meddelande.

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

* Information om hur du löser specifika distributions fel finns i [lösa vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Information om hur du använder aktivitets loggarna för att övervaka andra typer av åtgärder finns i [Visa aktivitets loggar för att hantera Azure-resurser](resource-group-audit.md).
* Om du vill verifiera distributionen innan du kör den, se [distribuera en resurs grupp med Azure Resource Manager mall](resource-group-template-deploy.md).

