---
title: Distributionshistoriken med Azure Resource Manager | Microsoft Docs
description: Beskriver hur du visar Azure Resource Manager distributionsåtgärder med portalen, PowerShell, Azure CLI och REST API.
tags: top-support-issue
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: 58d22e3fcae5c30e5d7dcc39b317afeef4a693ee
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606014"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Visa distributionshistoriken med Azure Resource Manager

Azure Resource Manager kan du visa distributionshistoriken och undersöka specifika åtgärder i tidigare distributioner. Du kan se de resurser som har distribuerats och få information om eventuella fel.

Om du vill ha hjälp med att lösa specifika distributionsfel finns i [lösa vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portal

Få information om en distribution från distributionshistoriken.

1. Välj den resursgrupp som du vill undersöka.

1. Klicka på länken under **distributioner**.

   ![Välj distributionshistoriken](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Välj en av distributionerna från distributionshistoriken.

   ![Välj distributionen](./media/resource-manager-deployment-operations/select-details.png)

1. En sammanfattning av distributionen visas, inklusive en lista över de resurser som har distribuerats.

    ![Distributionssammanfattning](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. Om du vill visa mallen som används för distributionen, Välj **mallen**. Du kan ladda ned mallen för att återanvända den.

    ![Visa mall](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Om distributionen misslyckades kan se du ett felmeddelande. Välj felmeddelande för mer information.

    ![Visa misslyckad distribution](./media/resource-manager-deployment-operations/show-error.png)

1. Det detaljerade felmeddelandet visas.

    ![Visa felinformationen](./media/resource-manager-deployment-operations/show-details.png)

1. Korrelations-ID används för att spåra relaterade händelser och kan vara användbart när du arbetar med teknisk support att felsöka en distribution.

    ![Hämta Korrelations-ID](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. Mer information om steget som misslyckades, Välj **åtgärdsinformation**.

    ![Välj distributionsåtgärder](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. Du ser information om det steget i distributionen.

    ![Visa information om åtgärd](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hämta övergripande status för en distribution med den **Get-AzResourceGroupDeployment** kommando.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Eller du kan filtrera resultatet för dessa distributioner som har misslyckats.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

Korrelations-ID används för att spåra relaterade händelser och kan vara användbart när du arbetar med teknisk support att felsöka en distribution. Hämta Korrelations-ID med:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Varje distributionen omfattar flera åtgärder. Varje åtgärd representerar ett steg i distributionsprocessen. För att identifiera vad som gick fel med en distribution, måste vanligtvis du se information om distributionsåtgärder. Du kan se statusen för åtgärder med **Get-AzResourceGroupDeploymentOperation**.

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

Om du vill ha mer information om misslyckade åtgärder, hämta egenskaperna för åtgärder med **misslyckades** tillstånd.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

Som returnerar alla misslyckade åtgärder med var och en i följande format:

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

Observera serviceRequestId och spårnings-ID för åtgärden. ServiceRequestId kan vara användbart när du arbetar med teknisk support att felsöka en distribution. Du använder trackingId i nästa steg för att fokusera på en viss åtgärd.

Om du vill ha statusmeddelande för en viss misslyckad åtgärd, använder du följande kommando:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Som returnerar:

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Varje distributionsåtgärd i Azure inkluderar innehåll för begäran och svar. Under distributionen kan du använda **DeploymentDebugLogLevel** parametern för att ange att begäran/svar eller loggas.

Du får den här informationen från loggen och spara den lokalt genom att använda följande PowerShell-kommandon:

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Azure CLI

Hämta övergripande status för en distribution med den **azure group deployment show** kommando.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
Korrelations-ID används för att spåra relaterade händelser och kan vara användbart när du arbetar med teknisk support att felsöka en distribution.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

Visa åtgärderna för en distribution, använda:

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

Om du vill ha information om en distribution kan använda den [få information om en malldistributionen](https://docs.microsoft.com/rest/api/resources/deployments) igen.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

I svaret, Observera särskilt den **provisioningState**, **correlationId**, och **fel** element. Den **correlationId** används för att spåra relaterade händelser och kan vara användbart när du arbetar med teknisk support att felsöka en distribution.

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

Hämta information om distribution av [visa alla distributionsåtgärder för mallen](https://docs.microsoft.com/rest/api/resources/deployments). 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
Svaret innehåller begäran/svar eller information baserat på vad du har angett i den **debugSetting** egenskapen under distributionen.

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
* Om du vill ha hjälp med att lösa specifika distributionsfel finns i [lösa vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Läs om hur du använder aktivitetsloggarna för övervakning av andra typer av åtgärder i [visa aktivitetsloggar för att hantera Azure-resurser](resource-group-audit.md).
* Om du vill verifiera distributionen innan du kör den, se [distribuera en resursgrupp med Azure Resource Manager-mall](resource-group-template-deploy.md).

