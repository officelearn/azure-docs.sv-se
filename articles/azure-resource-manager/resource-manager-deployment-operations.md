---
title: Distributionsåtgärder med Azure Resource Manager | Microsoft Docs
description: Beskriver hur du visar Azure Resource Manager distributionsåtgärder med portalen, PowerShell, Azure CLI och REST API.
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: tomfitz
ms.openlocfilehash: 9320e3089e02e1ca6b6bcce0287946baaf0558d9
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452045"
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Visa distributionsåtgärder med Azure Resource Manager

Du kan visa åtgärderna för en distribution via Azure portal. Du kanske är mest intresserad visa åtgärderna när du har fått ett fel under distributionen så att den här artikeln handlar om hur du visar åtgärder som har misslyckats. Portalen tillhandahåller ett gränssnitt som gör det möjligt att enkelt hitta felen och fastställa eventuella korrigeringar.

Du kan felsöka distributionen genom att titta på granskningsloggarna eller distributionsåtgärder. Den här artikeln visar båda metoderna. Om du vill ha hjälp med att lösa specifika distributionsfel finns i [lösa vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portalen
Visa distributionsåtgärder, använda följande steg:

1. Se status för den senaste distributionen för resursgruppen som ingår i distributionen. Du kan välja denna status för att få mer information.
   
    ![Distributionsstatus](./media/resource-manager-deployment-operations/deployment-status.png)
2. Du kan se de senaste distributionshistoriken. Välj den distribution som misslyckades.
   
    ![Distributionsstatus](./media/resource-manager-deployment-operations/select-deployment.png)
3. Välj länken för att se en beskrivning av varför distributionen misslyckades. I bilden nedan är DNS-posten inte unikt.  
   
    ![Visa misslyckad distribution](./media/resource-manager-deployment-operations/view-error.png)
   
    Det här felmeddelandet ska vara räcker att påbörja felsökning. Men om du vill ha mer information om vilka uppgifter har slutförts kan du visa åtgärderna som du ser i följande steg.
4. Du kan visa alla distributionsåtgärder. Välj alla åtgärder som du vill ha mer information.
   
    ![Visa åtgärder](./media/resource-manager-deployment-operations/view-operations.png)
   
    Du ser i det här fallet att lagringskontot, virtuellt nätverk och tillgänglighetsuppsättning har skapats. Det gick inte att den offentliga IP-adressen och andra resurser som inte har försökts.
5. Du kan visa händelser för distributionen genom att välja **händelser**.
   
    ![Visa händelser](./media/resource-manager-deployment-operations/view-events.png)
6. Du ser alla händelser för distributionen och väljer något mer information. Observera Korrelations-ID: N. Det här värdet kan vara användbart när du arbetar med teknisk support att felsöka en distribution.
   
    ![Visa händelser](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. Hämta övergripande status för en distribution med den **Get-AzureRmResourceGroupDeployment** kommando. 

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   Eller du kan filtrera resultatet för dessa distributioner som har misslyckats.

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
1. Hämta Korrelations-ID med:

  ```powershell
  (Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
  ```

1. Varje distributionen omfattar flera åtgärder. Varje åtgärd representerar ett steg i distributionsprocessen. För att identifiera vad som gick fel med en distribution, måste vanligtvis du se information om distributionsåtgärder. Du kan se statusen för åtgärder med **Get-AzureRmResourceGroupDeploymentOperation**.

  ```powershell 
  Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    Som returnerar flera åtgärder med var och en i följande format:

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

1. Om du vill ha mer information om misslyckade åtgärder, hämta egenskaperna för åtgärder med **misslyckades** tillstånd.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    Som returnerar alla misslyckade åtgärder med var och en i följande format:

  ```powershell
  provisioningOperation : Create
  provisioningState     : Failed
  timestamp             : 2016-06-14T21:54:55.1468068Z
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
1. Om du vill ha statusmeddelande för en viss misslyckad åtgärd, använder du följande kommando:

  ```powershell
  ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    Som returnerar:

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```
1. Varje distributionsåtgärd i Azure inkluderar innehåll för begäran och svar. Innehållet i begäran är vad du skickas till Azure under distributionen (till exempel skapa en virtuell dator, OS-disk och andra resurser). Svarsinnehållet är vad Azure skickas tillbaka från din begäran om distribution. Under distributionen kan du använda **DeploymentDebugLogLevel** parametern för att ange att begäran/svar eller bevaras i loggen. 

  Du får den här informationen från loggen och spara den lokalt genom att använda följande PowerShell-kommandon:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
  ```

## <a name="azure-cli"></a>Azure CLI

1. Hämta övergripande status för en distribution med den **azure group deployment show** kommando.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment
  ```
  
1. En av de returnerade värdena är den **correlationId**. Det här värdet används för att spåra relaterade händelser och kan vara användbart när du arbetar med teknisk support att felsöka en distribution.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
  ```

1. Visa åtgärderna för en distribution, använda:

  ```azurecli
  az group deployment operation list -g ExampleGroup -n ExampleDeployment
  ```

## <a name="rest"></a>REST

1. Få information om en distribution med den [få information om en malldistributionen](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get) igen.

  ```http
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
        "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
        "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
      }  
    }
  }
  ```

2. Få information om distributioner med [visa alla distributionsåtgärder för mallen](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List). 

  ```http
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

