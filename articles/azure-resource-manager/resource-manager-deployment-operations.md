---
title: "Distributionsåtgärder med Azure Resource Manager | Microsoft Docs"
description: "Beskriver hur du visar Azure Resource Manager distributionsåtgärder med portalen, PowerShell, Azure CLI och REST-API."
services: azure-resource-manager,virtual-machines
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 01/13/2017
ms.author: tomfitz
ms.openlocfilehash: fb6b3b357fd1f66184e480115a9c863ba31ac193
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Visa distributionsåtgärder med Azure Resource Manager


Du kan visa åtgärderna för en distribution via Azure-portalen. Du kanske är mest intresserad Visa åtgärder när du har tagit emot ett fel under distributionen så att den här artikeln fokuserar på Visa åtgärder som har misslyckats. Portalen tillhandahåller ett gränssnitt som gör att du lätt hitta felen och fastställa eventuella korrigeringar.

[!INCLUDE [resource-manager-troubleshoot-introduction](../../includes/resource-manager-troubleshoot-introduction.md)]

## <a name="portal"></a>Portalen
Distributionsåtgärder, Använd följande steg:

1. Se status för den senaste distributionen för resursgruppen ingår i distributionen. Du kan välja denna status för att få mer information.
   
    ![Distributionsstatus](./media/resource-manager-deployment-operations/deployment-status.png)
2. Du ser den senaste distributionshistoriken. Välj distributionen misslyckades.
   
    ![Distributionsstatus](./media/resource-manager-deployment-operations/select-deployment.png)
3. Klicka på länken om du vill se en beskrivning av varför distributionen misslyckades. DNS-posten är inte unikt i bilden nedan.  
   
    ![Visa misslyckad distribution](./media/resource-manager-deployment-operations/view-error.png)
   
    Det här felmeddelandet ska räcker att påbörja felsökningen. Om du vill ha mer information om vilka uppgifter har slutförts, du kan visa åtgärderna som visas i följande steg.
4. Du kan visa alla distributionsåtgärder i den **distribution** bladet. Välj för att se mer information.
   
    ![Visa operationer](./media/resource-manager-deployment-operations/view-operations.png)
   
    I så fall måste se du att lagringskonto, virtuella nätverk och tillgänglighetsuppsättning har skapats. Det gick inte att den offentliga IP-adressen och andra resurser har inte använts.
5. Du kan visa händelser för distributionen genom att välja **händelser**.
   
    ![Visa händelser](./media/resource-manager-deployment-operations/view-events.png)
6. Du se alla händelser för distributionen och välja något mer information. Observera för Korrelations-ID: N. Det här värdet kan vara användbart när du arbetar med teknisk support för att felsöka en distribution.
   
    ![Visa händelser](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. För att få övergripande status för en distribution kan använda den **Get-AzureRmResourceGroupDeployment** kommando. 

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   Eller så kan du filtrera resultaten för dessa distributioner som har misslyckats.

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
2. Varje distribution innehåller flera åtgärder. Varje åtgärd representerar ett steg i processen för distribution. För att identifiera vad som gick fel med en distribution, måste vanligtvis du se information om distributionsåtgärder. Du kan se status för åtgärder med **Get-AzureRmResourceGroupDeploymentOperation**.

  ```powershell 
  Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    Som returnerar flera åtgärder som i följande format:

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

3. Om du vill ha mer information om misslyckade åtgärder att hämta egenskaperna för åtgärder med **misslyckades** tillstånd.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    Som returnerar alla misslyckade åtgärder som i följande format:

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

    Observera serviceRequestId och trackingId för åtgärden. ServiceRequestId kan vara användbart när du arbetar med teknisk support för att felsöka en distribution. Du använder trackingId i nästa steg för att fokusera på en viss åtgärd.
4. För att få statusmeddelandet som en viss misslyckad åtgärd, använder du följande kommando:

  ```powershell
  ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    Som returnerar:

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```
4. Alla distributionsåtgärder i Azure inkluderar innehåll för förfrågan och svar. Frågeinnehållet är vad du skickat till Azure under distributionen (till exempel skapa en virtuell dator, OS-disken och andra resurser). Innehållet i svaret är Azure skickas tillbaka från din distributionsbegäran om. Under distributionen kan du använda **DeploymentDebugLogLevel** paramenter för att ange att begäran och/eller svar bevaras i loggen. 

  Du hämta informationen från loggen och spara den lokalt genom att använda följande PowerShell-kommandon:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
  ```

## <a name="azure-cli"></a>Azure CLI

1. Hämta övergripande status för en distribution med den **azure-grupp distribution visa** kommando.

  ```azurecli
  azure group deployment show --resource-group ExampleGroup --name ExampleDeployment --json
  ```
  
  En av de returnerade värdena är den **correlationId**. Det här värdet används för att spåra relaterade händelser och kan vara användbart när du arbetar med teknisk support för att felsöka en distribution.

  ```azurecli
  "properties": {
    "provisioningState": "Failed",
    "correlationId": "4002062a-a506-4b5e-aaba-4147036b771a",
  ```

2. Åtgärder för en distribution, Använd:

  ```azurecli
  azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json
  ```

## <a name="rest"></a>REST

1. Hämta information om en distribution med den [få information om en för malldistribution](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get) igen.

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
  ```

    Notera särskilt i svaret, den **provisioningState**, **correlationId**, och **fel** element. Den **correlationId** används för att spåra relaterade händelser och kan vara användbar när du arbetar med teknisk support för att felsöka en distribution.

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

2. Hämta information om distributionsåtgärder med den [visa alla distributionsåtgärder för mallen](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List) igen. 

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
  ```
   
    Svaret innehåller begäran och/eller svar information baserat på vad du har angett i den **debugSetting** egenskapen under distributionen.

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
* Hjälp med att lösa viss distributionsfel finns [Lös vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Läs om hur du använder aktivitetsloggarna för övervakning av andra typer av åtgärder i [visa aktivitetsloggar att hantera Azure-resurser](resource-group-audit.md).
* För att verifiera distributionen innan du kör den finns [distribuera en resursgrupp med Azure Resource Manager-mall](resource-group-template-deploy.md).

