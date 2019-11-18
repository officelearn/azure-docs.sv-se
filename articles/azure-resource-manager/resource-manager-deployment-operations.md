---
title: Distributionshistorik
description: Beskriver hur du visar Azure Resource Manager distributions åtgärder med portalen, PowerShell, Azure CLI och REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: d8daf7191bb22f7c7057f6ef6b220a18868872cc
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149565"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Visa distributions historik med Azure Resource Manager

Med Azure Resource Manager kan du Visa distributions historiken och granska vissa åtgärder i tidigare distributioner. Du kan se de resurser som har distribuerats och få information om eventuella fel.

Information om hur du löser specifika distributions fel finns i [lösa vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portal

För att få information om en distribution från distributions historiken.

1. Välj den resurs grupp som du vill undersöka.

1. Välj länken under **distributioner**.

   ![Välj distributions historik](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Välj en av distributionerna från distributions historiken.

   ![Välj distribution](./media/resource-manager-deployment-operations/select-details.png)

1. En sammanfattning av distributionen visas, inklusive en lista över de resurser som har distribuerats.

    ![Distributions Sammanfattning](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. Om du vill visa mallen som används för distributionen väljer du **mall**. Du kan hämta mallen för att återanvända den.

    ![Visa mall](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Om distributionen misslyckades visas ett fel meddelande. Välj fel meddelandet för mer information.

    ![Visa misslyckad distribution](./media/resource-manager-deployment-operations/show-error.png)

1. Det detaljerade fel meddelandet visas.

    ![Visa fel information](./media/resource-manager-deployment-operations/show-details.png)

1. Korrelations-ID: t används för att spåra relaterade händelser och kan vara till hjälp när du arbetar med teknisk support för att felsöka en distribution.

    ![Hämta korrelations-ID](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. Om du vill veta mer om det steg som misslyckades väljer du **Åtgärds information**.

    ![Välj distributions åtgärder](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. Du ser informationen för det steget i distributionen.

    ![Visa åtgärds information](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använd kommandot **Get-AzResourceGroupDeployment** för att få en distributions övergripande status.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Eller så kan du filtrera resultaten för endast de distributioner som har misslyckats.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

Korrelations-ID: t används för att spåra relaterade händelser och kan vara till hjälp när du arbetar med teknisk support för att felsöka en distribution. Använd följande för att hämta korrelations-ID:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Varje distribution innehåller flera åtgärder. Varje åtgärd representerar ett steg i distributions processen. För att upptäcka vad som gick fel med en distribution, behöver du vanligt vis se information om distributions åtgärderna. Du kan se status för åtgärderna med **Get-AzResourceGroupDeploymentOperation**.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

Som returnerar flera åtgärder med var och en i följande format:

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

Hämta mer information om misslyckade åtgärder genom att hämta egenskaperna för åtgärder med **felaktigt** tillstånd.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

Vilket returnerar alla misslyckade åtgärder med var och en i följande format:

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

Notera serviceRequestId och trackingId för åtgärden. ServiceRequestId kan vara till hjälp när du arbetar med teknisk support för att felsöka en distribution. Du använder trackingId i nästa steg för att fokusera på en viss åtgärd.

Använd följande kommando för att hämta status meddelandet för en viss misslyckad åtgärd:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Returnerar:

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Varje distributions åtgärd i Azure innehåller innehåll för begäran och svar. Under distributionen kan du använda **DeploymentDebugLogLevel** -parametern för att ange att begäran och/eller svar loggas.

Du får den informationen från loggen och sparar den lokalt med hjälp av följande PowerShell-kommandon:

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Azure CLI

Om du vill hämta den övergripande statusen för en distribution använder du kommandot **Azure Group Deployment show** .

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
Korrelations-ID: t används för att spåra relaterade händelser och kan vara till hjälp när du arbetar med teknisk support för att felsöka en distribution.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

Om du vill se åtgärder för en distribution använder du:

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

Om du vill ha information om en distribution använder du [Hämta information om en mall distributions](https://docs.microsoft.com/rest/api/resources/deployments) åtgärd.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

I svaret noterar du särskilt **provisioningState**-, **correlationId**-och **Error** -elementen. **CorrelationId** används för att spåra relaterade händelser och kan vara till hjälp när du arbetar med teknisk support för att felsöka en distribution.

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

Om du vill ha information om distributioner använder du [lista alla mallar distributions åtgärder](https://docs.microsoft.com/rest/api/resources/deployments). 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
Svaret innehåller information om begäran och/eller svar baserat på vad du angav i egenskapen **debugSetting** under distributionen.

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>Nästa steg
* Information om hur du löser specifika distributions fel finns i [lösa vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Information om hur du använder aktivitets loggarna för att övervaka andra typer av åtgärder finns i [Visa aktivitets loggar för att hantera Azure-resurser](resource-group-audit.md).
* Om du vill verifiera distributionen innan du kör den, se [distribuera en resurs grupp med Azure Resource Manager mall](resource-group-template-deploy.md).

