---
title: Flytta ett virtuellt Azure-nätverk till en annan Azure-region med hjälp av Azure PowerShell
description: Flytta ett virtuellt Azure-nätverk från en Azure-region till en annan med hjälp av en Resource Manager-mall och Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: dc316e5bbb88359ff8b1e8a4fc35a56541a577f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646718"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Flytta ett virtuellt Azure-nätverk till en annan region med hjälp av Azure PowerShell

Det finns olika scenarier för att flytta ett befintligt Virtuellt Azure-nätverk från en region till en annan. Du kanske till exempel vill skapa ett virtuellt nätverk med samma konfiguration för testning och tillgänglighet som det befintliga virtuella nätverket. Du kanske vill flytta ett virtuellt produktionsnätverk till en annan region som en del av planeringen för haveriberedskap.

Du kan använda en Azure Resource Manager-mall för att slutföra flytten av det virtuella nätverket till en annan region. Du gör detta genom att exportera det virtuella nätverket till en mall, ändra parametrarna för att matcha målregionen och sedan distribuera mallen till den nya regionen. Mer information om Resource Manager-mallar finns i [Exportera resursgrupper till mallar](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Krav

- Kontrollera att ditt virtuella nätverk finns i Azure-regionen som du vill flytta från.

- Om du vill exportera ett virtuellt nätverk och distribuera en mall för att skapa ett virtuellt nätverk i en annan region måste du ha rollen Nätverksbidragsgivare eller högre.

- Virtuella nätverks peerings skapas inte igen och de misslyckas om de fortfarande finns i mallen. Innan du exporterar mallen måste du ta bort alla virtuella nätverks peers. Du kan sedan återupprätta dem igen efter det virtuella nätverket flytta.
    
- Identifiera källnätverkslayouten och alla resurser som du använder för tillfället. Den här layouten innehåller men är inte begränsad till belastningsutjämnare, nätverkssäkerhetsgrupper (NSG) och offentliga IPs.

- Kontrollera att din Azure-prenumeration gör att du kan skapa virtuella nätverk i målregionen. Kontakta supporten för att aktivera den kvot som krävs.

- Kontrollera att prenumerationen har tillräckligt med resurser för att stödja tillägg av virtuella nätverk för den här processen. Läs mer i dokumentationen om [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Förbered för flytten
I det här avsnittet förbereder du det virtuella nätverket för flytten med hjälp av en Resource Manager-mall. Du flyttar sedan det virtuella nätverket till målregionen med hjälp av Azure PowerShell-kommandon.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Så här exporterar du det virtuella nätverket och distribuerar det virtuella målnätverket med PowerShell:

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) och följ sedan anvisningarna på skärmen:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Hämta resurs-ID:t för det virtuella nätverk som du vill flytta till målområdet och placera det sedan i en variabel med [get-azvirtualnetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Exportera det virtuella källnätverket till en .json-fil i katalogen där du kör kommandot [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. Den hämtade filen har samma namn som resursgruppen som resursen exporterades från. Leta reda på * \<filen>.json,* som du exporterade med kommandot och öppna den sedan i redigeraren:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Om du vill redigera parametern för **defaultValue** det virtuella nätverksnamnet ändrar du standardvärdegenskapen för källresursnätverkets namn till namnet på ditt virtuella målnätverk. Var noga med att bifoga namnet inom citationstecken.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Om du vill redigera målområdet där det **location** virtuella nätverket ska flyttas ändrar du platsegenskapen under resurser:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
1. Om du vill hämta regionplatskoder kan du använda Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) genom att köra följande kommando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. (Valfritt) Du kan också ändra andra parametrar i * \<filen resursgruppnamn>.json,* beroende på dina behov:

    * **Adressutrymme:** Innan du sparar filen kan du ändra adressutrymmet för det virtuella nätverket genom att ändra avsnittet **resurser** > **addressSpace** och ändra egenskapen **addressPrefixes:**

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },
        ```

    * **Undernät**: Du kan ändra eller lägga till undernätsnamnet och undernätsadressutrymmet genom att ändra avsnittet **undernät.** Du kan ändra namnet på undernätet **name** genom att ändra namnegenskapen. Och du kan ändra undernätsadressutrymmet genom att ändra **egenskapen addressPrefix:**

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        Om du vill ändra adressprefixet redigerar du filen på två ställen: i koden i föregående avsnitt och i **avsnittet typ** i följande kod. Ändra **egenskapen addressPrefix** i följande kod för att matcha **egenskapen addressPrefix** i koden i föregående avsnitt.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. Spara * \<filen resursgrupp-namn>.json.*

1. Skapa en resursgrupp i målregionen för det virtuella målnätverket som ska distribueras med hjälp av [New-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Distribuera den redigerade * \<resursgruppnamnet>.json-filen* till resursgruppen som du skapade i föregående steg med hjälp av [New-AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Om du vill kontrollera att resurserna har skapats i målregionen använder du [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) och [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Ta bort det virtuella nätverket eller resursgruppen 

När du har distribuerat det virtuella nätverket tar du bort eller tar bort resursgruppen som du skapade i målregionen om du vill starta eller ignorera det virtuella nätverket i målområdet och det flyttade virtuella nätverket tas bort. 

Om du vill ta bort resursgruppen använder du [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Rensa

Gör något av följande om du vill genomföra ändringarna och slutföra flytten av det virtuella nätverket:

* Ta bort resursgruppen med hjälp av [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Ta bort det virtuella källnätverket med hjälp av [Remove-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0)  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du ett virtuellt nätverk från en region till en annan med hjälp av PowerShell och rensade sedan upp de onödiga källresurserna. Mer information om hur du flyttar resurser mellan regioner och haveriberedskap i Azure finns i:

- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Flytta virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
