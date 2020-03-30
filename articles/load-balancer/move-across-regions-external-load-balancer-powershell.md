---
title: Flytta Azure extern belastningsutjämnare till en annan Azure-region med Azure PowerShell
description: Använd Azure Resource Manager-mallen för att flytta Azure extern belastningsutjämning från en Azure-region till en annan med Azure PowerShell.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a24eb4608e7630d5b613751fa2120361eccd7672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644825"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Flytta Azure extern belastningsutjämnare till en annan region med Azure PowerShell

Det finns olika scenarier där du vill flytta din befintliga externa belastningsutjämnare från en region till en annan. Du kanske till exempel vill skapa en extern belastningsutjämnare med samma konfiguration för testning. Du kanske också vill flytta en extern belastningsutjämnare till en annan region som en del av katastrofåterställningsplanering.

Externa belastningsutjämnare i Azure kan inte flyttas från en region till en annan. Du kan dock använda en Azure Resource Manager-mall för att exportera den befintliga konfigurationen och den offentliga IP-adressen för en extern belastningsutjämnare.  Du kan sedan arrangera resursen i en annan region genom att exportera belastningsutjämnaren och den offentliga IP-adressen till en mall, ändra parametrarna för att matcha målregionen och sedan distribuera mallarna till den nya regionen.  Mer information om Resurshanteraren och mallar finns i [Exportera resursgrupper till mallar](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Krav

- Kontrollera att Azures externa belastningsutjämnare finns i Azure-regionen som du vill flytta från.

- Azure externa belastningsutjämnare kan inte flyttas mellan regioner.  Du måste associera den nya belastningsutjämnaren med resurser i målregionen.

- Om du vill exportera en extern belastningsutjämnad konfiguration och distribuera en mall för att skapa en extern belastningsutjämnare i en annan region behöver du rollen Nätverksbidragsgivare eller högre.
   
- Identifiera källnätverkslayouten och alla resurser som du använder för tillfället. Den här layouten innehåller men är inte begränsad till belastningsutjämnare, nätverkssäkerhetsgrupper, offentliga IP-adresser och virtuella nätverk.

- Kontrollera att din Azure-prenumeration gör att du kan skapa externa belastningsutjämnare i målområdet som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrollera att prenumerationen har tillräckligt med resurser för att stödja tillägg av belastningsutjämnare för den här processen.  Se [Azure-prenumerations- och tjänstbegränsningar, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Förbereda och flytta
Följande steg visar hur du förbereder den externa belastningsutjämnaren för flytten med hjälp av en Resource Manager-mall och flyttar konfigurationen för extern belastningsutjämnare till målregionen med Azure PowerShell.  Som en del av den här processen måste den offentliga IP-konfigurationen för den externa belastningsutjämnaren inkluderas och måste jag göra först innan du flyttar den externa belastningsutjämnaren.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>Exportera den offentliga IP-mallen och distribuera från Azure PowerShell

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) och följ anvisningarna på skärmen:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. Hämta resurs-ID:t för den offentliga IP som du vill flytta till målregionen och placera det i en variabel med [Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportera käll-IP till en Json-fil till katalogen där du kör kommandot [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Filen som hämtas namnges efter den resursgrupp som resursen exporterades från.  Leta upp filen som exporterades från kommandot ** \<resource-group-name>.json** och öppna den i en valfri redigerare:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Om du vill redigera parametern för det offentliga IP-namnet ändrar du **egenskapsstandardvärde** för källens offentliga IP-namn till namnet på din offentliga IP-adress, och ser till att namnet är inom citationstecken:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Om du vill redigera målområdet där den **location** offentliga IP-adressen ska flyttas ändrar du platsegenskapen under resurser:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Om du vill hämta regionplatskoder kan du använda Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) genom att köra följande kommando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Du kan också ändra andra parametrar i mallen om du vill och är valfria beroende på dina behov:

    * **Sku** - Du kan ändra sku för den offentliga IP i konfigurationen från standard till grundläggande eller grundläggande till standard genom att ändra **sku** > **namn** egenskap i ** \<resurs-grupp-namn>.json** fil:

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Mer information om skillnaderna mellan grundläggande och standardiserade offentliga IPS finns i [Skapa, ändra eller ta bort en offentlig IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Offentlig IP-allokeringsmetod** och **inaktiv timeout** - Du kan ändra båda dessa alternativ i mallen genom att ändra egenskapen **publicIPAllocationMethod** från **dynamisk** till **statisk** eller **statisk** till **dynamisk**. Tidsgränsen för inaktiv kan ändras genom att ändra egenskapen **idleTimeoutInMinutes** till önskad mängd.  Standard är **4:**

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Mer information om allokeringsmetoderna och tidsgränsen för inaktiva tidsutgång finns i [Skapa, ändra eller ta bort en offentlig IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


9. Spara ** \<filen resursgrupp-namn>.json.**

10. Skapa en resursgrupp i målregionen för den offentliga mål-IP-adressen som ska distribueras med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0).
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Distribuera den redigerade ** \<resursgruppnamnet>.json-filen** till resursgruppen som skapades i föregående steg med [New-AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Om du vill verifiera att resurserna skapades i målregionen använder du [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) och [Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportera den externa belastningsutjämnaren och distribuera från Azure PowerShell

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) och följ anvisningarna på skärmen:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Hämta resurs-ID:t för den externa belastningsutjämnaren som du vill flytta till målregionen och placera det i en variabel med [Get-AzLoadBalancer:](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportera källans externa belastningsutjämnare till en .json-fil till katalogen där du kör kommandot [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. Filen som hämtas namnges efter den resursgrupp som resursen exporterades från.  Leta upp filen som exporterades från kommandot ** \<resource-group-name>.json** och öppna den i en valfri redigerare:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Om du vill redigera parametern för namnet på den externa belastningsutjämnaren ändrar du **egenskapsstandardvärdevärdet** för källans externa belastningsutjämnare till namnet på målutomvänd belastningsutjämnaren, se till att namnet är inom citationstecken:

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  Om du vill redigera värdet för den offentliga mål-IP-adressen som flyttades ovan måste du först hämta resurs-ID:et och sedan kopiera och klistra in det ** \<i filen resursgruppnamn>.json.**  Om du vill hämta ID:et använder du [Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Skriv variabeln och tryck enter för att visa resurs-ID.  Markera ID-sökvägen och kopiera den till Urklipp:

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  I filen ** \<resource-group-name>.json** klistrar du in **resurs-ID:n** från variabeln i stället för **standardvärde** i den andra parametern för det offentliga IP-externa ID:et, se till att du omsluter sökvägen inom citationstecken:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  Om du har konfigurerat utgående NAT och utgående regler för belastningsutjämnaren finns en tredje post i den här filen för det externa ID:et för den utgående offentliga IP-adressen.  Upprepa stegen ovan i **målregionen** för att hämta ID:n för den utgående offentliga iP:n och klistra in posten i ** \<filen>.json för resursgrupp och namn:**

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. Om du vill redigera målområdet där den externa belastningsutjämnarens konfiguration ska flyttas ändrar du platsegenskapen **location** **resources** ** \<** under resurser i filen>.json för resursgruppsnamn:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
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
    
    * **Sku** - Du kan ändra sku för den externa belastningsutjämningen i konfigurationen från standard till grundläggande eller grundläggande till standard genom att ändra **egenskapen sku** > **name** i ** \<filen>.json för resursgruppsnamn:**

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
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

    * **Utgående regler** – Du kan lägga till eller ta bort utgående regler i konfigurationen genom att redigera egenskapen **outboundRules** i ** \<filen>.json för resursgruppsnamn:**

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Mer information om utgående regler finns i [utgående regler för belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)

13. Spara ** \<filen resursgrupp-namn>.json.**
    
10. Skapa eller skapa en resursgrupp i målregionen för att rikta extern belastningsutjämnare som ska distribueras med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). Den befintliga resursgruppen ovanifrån kan också återanvändas som en del av den här processen:
    
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

Efter distributionen, om du vill starta om eller ignorera den offentliga IP-adressen och belastningsutjämnaren i målet, tar du bort resursgruppen som skapades i målet och den flyttade offentliga IP- och belastningsutjämnaren tas bort.  Om du vill ta bort resursgruppen använder du [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flytten av NSG tar du bort käll-NSG- eller resursgruppen, använder [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) eller [Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0) och [Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en Azure-nätverkssäkerhetsgrupp från en region till en annan och rensade källresurserna.  Mer information om hur du flyttar resurser mellan regioner och haveriberedskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Flytta virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
