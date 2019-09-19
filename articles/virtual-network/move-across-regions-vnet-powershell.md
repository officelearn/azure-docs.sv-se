---
title: Flytta Azure-Virtual Network till en annan Azure-region med Azure PowerShell
description: Använd Azure Resource Manager mall för att flytta Azure-Virtual Network från en Azure-region till en annan med hjälp av Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: fe4c3fe131eb763ef8875cced91ab3ae22abca08
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077664"
---
# <a name="move-azure-virtual-network-to-another-region-using-azure-powershell"></a>Flytta Azure-Virtual Network till en annan region med hjälp av Azure PowerShell

Det finns olika scenarier där du vill flytta dina befintliga virtuella Azure-nätverk (virtuella nätverk) från en region till en annan. Du kanske till exempel vill skapa ett virtuellt nätverk med samma konfiguration för testning och tillgänglighet för ditt befintliga virtuella nätverk. Du kanske också vill flytta ett virtuellt produktions nätverk till en annan region som en del av Disaster Recovery-planeringen.

Du kan använda en Azure Resource Manager-mall för att slutföra flyttningen av det virtuella nätverket till en annan region. Du gör detta genom att exportera det virtuella nätverket till en mall, ändra parametrarna för att matcha mål regionen och sedan distribuera mallen till den nya regionen.  Mer information om Resource Manager och mallar finns i [Exportera resurs grupper till mallar](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Förutsättningar

- Se till att Azure-Virtual Network finns i den Azure-region som du vill flytta från.

- Om du vill exportera ett virtuellt nätverk och distribuera en mall för att skapa ett virtuellt nätverk i en annan region behöver du rollen som nätverks deltagare eller högre.

- Peering för virtuella nätverk återskapas inte och kommer att Miss klar om de fortfarande finns i mallen.  Du måste ta bort alla virtuella nätverks-peer-datorer innan du exporterar mallen och sedan återupprätta peer-datorerna när det virtuella nätverket har flyttats.
    
- Identifiera käll nätverks layouten och alla resurser som du använder just nu. Den här layouten omfattar men är inte begränsad till belastningsutjämnare, nätverks säkerhets grupper (NSG: er) och offentliga IP-adresser.

- Kontrol lera att din Azure-prenumeration låter dig skapa virtuella nätverk i mål regionen som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrol lera att din prenumeration har tillräckligt med resurser för att kunna lägga till virtuella nätverk för den här processen.  Se [begränsningar, kvoter och begränsningar för Azure-prenumerationen och tjänsten](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Förbered och flytta
Följande steg visar hur du förbereder det virtuella nätverket för flytt med hjälp av en Resource Manager-mall och flyttar det virtuella nätverket till mål regionen med hjälp av Azure PowerShell-kommandon.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-and-deploy-the-target-virtual-network-with-powershell"></a>Exportera det virtuella nätverket och distribuera det virtuella mål nätverket med PowerShell

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) och följ anvisningarna på skärmen:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Hämta resurs-ID för det virtuella nätverk som du vill flytta till mål regionen och placera det i en variabel med [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportera det virtuella käll nätverket till en. JSON-fil till den katalog där du kör kommandot [export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0):
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Den hämtade filen får namnet efter resurs gruppen som resursen exporterades från.  Leta upp filen som exporterades från kommandot  **\<resurs grupp-namn >. JSON** och öppna den i valfritt redigerings program:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Om du vill redigera parametern för det virtuella nätverks namnet ändrar du egenskapen **DefaultValue** för det virtuella käll nätverks namnet till namnet på det virtuella mål nätverket, se till att namnet är i citat tecken:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Om du vill redigera mål regionen där det virtuella nätverket ska flyttas ändrar du egenskapen **location** under resurser:

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
  
7. Om du vill hämta regions koderna för regionen kan du använda Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) genom att köra följande kommando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  Du kan också ändra andra parametrar i  **\<resurs grupps namnet >. JSON-** fil om du väljer, och är valfria beroende på dina krav:

    * **Adress utrymme** – det virtuella nätverkets > adress utrymme kan ändras innan du sparar genom att ändra  **\< avsnittet Resources addressSpace och ändra egenskapen addressPrefixes i resurs grupp-namn >. JSON-** fil:

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

    * **Undernät** – under nätets namn och under nätets adress utrymme kan ändras eller läggas till genom att ändra  **\<avsnittet undernät i resurs grupps namnet >. JSON-** fil. Namnet på under nätet kan ändras genom att ändra egenskapen **Name** . Adress utrymmet för under nätet kan ändras genom att ändra egenskapen **addressPrefix** i  **\<resurs grupps namnet >. JSON-** fil:

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

         I **resurs grupps namnet >. JSON-fil, för att ändra adressprefixet, måste det redige ras på två platser, avsnittet som anges ovan och avsnittet typ nedan. \<**  Ändra egenskapen **addressPrefix** så att den matchar det som beskrivs ovan:

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

9.  Spara resurs  **gruppsnamnet>.JSON-fil.\<**

10. Skapa en resurs grupp i mål regionen som det virtuella mål-VNET ska distribueras med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Distribuera det redigerade  **\<resurs grupps namnet >. JSON-** filen till resurs gruppen som skapades i föregående steg med [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0):

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Om du vill kontrol lera att resurserna har skapats i mål regionen använder du [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) och [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Kasta bort 

Om du vill börja om eller ta bort det virtuella nätverket i målet efter distributionen tar du bort resurs gruppen som skapades i målet och det flyttade virtuella nätverket tas bort.  Om du vill ta bort resurs gruppen använder du [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flyttningen av det virtuella nätverket tar du bort det virtuella käll nätverket eller resurs gruppen, använder [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) eller [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du ett Azure-Virtual Network från en region till en annan och rensade käll resurserna.  Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Flytta virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
