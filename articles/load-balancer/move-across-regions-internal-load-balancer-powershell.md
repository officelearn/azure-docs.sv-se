---
title: Flytta Azures interna belastningsutjämnare till en annan Azure-region med Azure PowerShell
description: Använd Azure Resource Manager-mall för att flytta Azure intern belastningsutjämning från en Azure-region till en annan med Azure PowerShell
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f8e431124155fe23853fe61e985fe4db522c3f77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644281"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Flytta Azures interna belastningsutjämnare till en annan region med PowerShell

Det finns olika scenarier där du vill flytta din befintliga interna belastningsutjämnare från en region till en annan. Du kanske till exempel vill skapa en intern belastningsutjämnare med samma konfiguration för testning. Du kanske också vill flytta en intern belastningsutjämnare till en annan region som en del av katastrofåterställningsplanering.

Azure interna belastningsutjämnare kan inte flyttas från en region till en annan. Du kan dock använda en Azure Resource Manager-mall för att exportera den befintliga konfigurationen och det virtuella nätverket för en intern belastningsutjämnare.  Du kan sedan arrangera resursen i en annan region genom att exportera belastningsutjämnaren och det virtuella nätverket till en mall, ändra parametrarna för att matcha målregionen och sedan distribuera mallarna till den nya regionen.  Mer information om Resurshanteraren och mallar finns i [Exportera resursgrupper till mallar](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Krav

- Kontrollera att Azures interna belastningsutjämnare finns i Azure-regionen som du vill flytta från.

- Azure interna belastningsutjämnare kan inte flyttas mellan regioner.  Du måste associera den nya belastningsutjämnaren med resurser i målregionen.

- Om du vill exportera en intern belastningsutjämnad konfiguration och distribuera en mall för att skapa en intern belastningsutjämnare i en annan region behöver du rollen Nätverksbidragsgivare eller högre.
   
- Identifiera källnätverkslayouten och alla resurser som du använder för tillfället. Den här layouten innehåller men är inte begränsad till belastningsutjämnare, nätverkssäkerhetsgrupper, virtuella datorer och virtuella nätverk.

- Kontrollera att din Azure-prenumeration gör att du kan skapa interna belastningsutjämnare i målområdet som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrollera att prenumerationen har tillräckligt med resurser för att stödja tillägg av belastningsutjämnare för den här processen.  Se [Azure-prenumerations- och tjänstbegränsningar, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Förbereda och flytta
Följande steg visar hur du förbereder den interna belastningsutjämnaren för flytten med hjälp av en Resource Manager-mall och flyttar den interna belastningsutjämnarens konfiguration till målregionen med Azure PowerShell.  Som en del av den här processen måste den virtuella nätverkskonfigurationen för den interna belastningsutjämnaren inkluderas och måste göras först innan den interna belastningsutjämnaren flyttas.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>Exportera den virtuella nätverksmallen och distribuera från Azure PowerShell

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) och följ anvisningarna på skärmen:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Hämta resurs-ID:t för det virtuella nätverk som du vill flytta till målregionen och placera det i en variabel med [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportera det virtuella källnätverket till en .json-fil till katalogen där du kör kommandot [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Filen som hämtas namnges efter den resursgrupp som resursen exporterades från.  Leta upp filen som exporterades från kommandot ** \<resource-group-name>.json** och öppna den i en valfri redigerare:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Om du vill redigera parametern för det virtuella nätverksnamnet ändrar **du egenskapsstandardvärdevärdet** för källens virtuella nätverksnamn till namnet på ditt virtuella målnätverk, se till att namnet är inom citationstecken:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Om du vill redigera målområdet där VNET **location** ska flyttas ändrar du platsegenskapen under resurser:

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
  
7. Om du vill hämta regionplatskoder kan du använda Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) genom att köra följande kommando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  Du kan också ändra andra parametrar i ** \<filen resursgrupp-namn>.json** om du vill och är valfria beroende på dina behov:

    * **Adressutrymme** - Adressutrymmet för det virtuella nätverket kan ändras innan du sparar genom att ändra avsnittet **resurser** > **addressSpace** och ändra egenskapen **addressPrefixes** i ** \<filen>.json för resursgruppsnamn:**

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

    * **Undernät** - Undernätsnamnet och undernätsadressutrymmet kan ändras eller läggas till genom att ändra **undernätsavsnittet** i ** \<filen>.json för resursgruppsnamn.** Namnet på undernätet kan ändras genom **name** att ändra namnegenskapen. Undernätsadressutrymmet kan ändras genom att ändra egenskapen **addressPrefix** i ** \<filen>.json för resursgruppsnamn:**

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

         Om du vill ändra adressprefixet måste ** \<>** det redigeras på två ställen, avsnittet ovan och **avsnittet** nedan för att ändra adressprefixet.  Ändra **egenskapen addressPrefix** så att den matchar den ovan:

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

9.  Spara ** \<filen resursgrupp-namn>.json.**

10. Skapa en resursgrupp i målregionen för mål-VNET som ska distribueras med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Distribuera den redigerade ** \<resursgruppnamnet>.json-filen** till resursgruppen som skapades i föregående steg med [New-AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. Om du vill verifiera att resurserna har skapats i målregionen använder du [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) och [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportera den interna belastningsutjämnaren och distribuera från Azure PowerShell

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) och följ anvisningarna på skärmen:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Hämta resurs-ID:t för den interna belastningsutjämnaren som du vill flytta till målregionen och placera det i en variabel med [Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportera källans interna belastningsutjämnad konfiguration till en .json-fil till katalogen där du kör kommandot [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. Filen som hämtas namnges efter den resursgrupp som resursen exporterades från.  Leta upp filen som exporterades från kommandot ** \<resource-group-name>.json** och öppna den i en valfri redigerare:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Om du vill redigera parametern för det interna belastningsutjämnarnamnet ändrar du **egenskapsstandardvärdevärdet** för källans interna belastningsutjämnad namn till namnet på ditt interna målbelastningsutjämnar, se till att namnet är inom citationstecken:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. Om du vill redigera värdet för det virtuella målnätverk som flyttades ovan måste du ** \<** först hämta resurs-ID:et och sedan kopiera och klistra in det i filen>.json för resursgruppsnamn.  För att hämta ID: [Använd Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Skriv variabeln och tryck enter för att visa resurs-ID.  Markera ID-sökvägen och kopiera den till Urklipp:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  I filen ** \<resource-group-name>.json** klistrar du in **resurs-ID:n** från variabeln i stället för **standardvärde** i den andra parametern för målvirtos nätverks-ID, och se till att du omsluter sökvägen inom citationstecken:
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. Om du vill redigera målområdet där den interna belastningsutjämna konfigurationen ska flyttas ändrar du platsegenskapen under **resurser** i **location** ** \<filen>.json** för resursgrupp och namn:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. Om du vill hämta regionplatskoder kan du använda Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) genom att köra följande kommando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Du kan också ändra andra parametrar i mallen om du vill och är valfria beroende på dina behov:
    
    * **Sku** - Du kan ändra sku för den interna belastningsutjämningen i konfigurationen från standard till grundläggande eller grundläggande till standard genom att ändra **egenskapen sku** > **name** i ** \<resursgruppnamnet>.json-filen:**

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Mer information om skillnaderna mellan grundläggande belastningsutjämnare och standardstandard sku finns i [översikt över Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Belastningsutjämningsregler** - Du kan lägga till eller ta bort belastningsutjämningsregler i konfigurationen genom att lägga till eller ta bort poster i avsnittet **loadBalancingRules** i ** \<filen>.json för resursgruppsnamn:**

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Mer information om belastningsutjämningsregler finns i [Vad är Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Avsökningar** - Du kan lägga till eller ta bort en avsökning för belastningsutjämnaren i konfigurationen genom att lägga till eller ta bort poster i avsnittet **avsökningar** i ** \<filen>.json för resursgruppsnamn:**

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Mer information om hälsoavsökningar för Azure Load Balancer finns i [belastningsutjämnare hälsoavsökningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Inkommande NAT-regler** - Du kan lägga till eller ta bort inkommande NAT-regler för belastningsutjämnaren genom att lägga till eller ta bort poster i avsnittet **inboundNatRules i** ** \<filen>.json för resursgruppsnamn:**

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Om du vill slutföra tillägget eller borttagningen av en inkommande **type** NAT-regel måste regeln finnas eller tas bort som en typegenskap i slutet av ** \<filen resursgruppnamn>.json:**

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Mer information om inkommande NAT-regler finns i [Vad är Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
    
13. Spara ** \<filen resursgrupp-namn>.json.**
    
10. Skapa eller skapa en resursgrupp i målregionen för att rikta intern belastningsutjämnare ska distribueras med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Den befintliga resursgruppen ovanifrån kan också återanvändas som en del av den här processen:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Distribuera den redigerade ** \<resursgruppnamnet>.json-filen** till resursgruppen som skapades i föregående steg med [New-AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Om du vill verifiera att resurserna har skapats i målregionen använder du [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) och [Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Ignorera 

Om du vill starta om eller ignorera det virtuella nätverket och belastningsutjämnaren i målet efter distributionen tar du bort resursgruppen som skapades i målet och den flyttade virtuella nätverks- och belastningsutjämnaren tas bort.  Om du vill ta bort resursgruppen använder du [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flytten av NSG tar du bort käll-NSG- eller resursgruppen, använder [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) eller [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) och [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en intern Azure-belastningsutjämnare från en region till en annan och rensade källresurserna.  Mer information om hur du flyttar resurser mellan regioner och haveriberedskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Flytta virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
