---
title: Flytta Azure Public IP till en annan Azure-region med Azure PowerShell
description: Använd Azure Resource Manager-mallen för att flytta Azure Public IP från en Azure-region till en annan med Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: c55b6011381d385fed7c7b8175ff02ec9be66fdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641595"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>Flytta Azure Public IP till en annan region med Azure PowerShell

Det finns olika scenarier där du vill flytta dina befintliga Offentliga Azure-IPs från en region till en annan. Du kanske till exempel vill skapa en offentlig IP med samma konfiguration och sku för testning. Du kanske också vill flytta en offentlig IP till en annan region som en del av katastrofåterställningsplanering.

Offentliga Azure-IPs är regionspecifika och kan inte flyttas från en region till en annan. Du kan dock använda en Azure Resource Manager-mall för att exportera den befintliga konfigurationen av en offentlig IP.  Du kan sedan arrangera resursen i en annan region genom att exportera den offentliga IP-adressen till en mall, ändra parametrarna för att matcha målregionen och sedan distribuera mallen till den nya regionen.  Mer information om Resurshanteraren och mallar finns i [Exportera resursgrupper till mallar](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Krav

- Kontrollera att Azure Public IP finns i Azure-regionen som du vill flytta från.

- Offentliga Azure-IPs kan inte flyttas mellan regioner.  Du måste associera den nya offentliga ip till resurser i målregionen.

- Om du vill exportera en offentlig IP-konfiguration och distribuera en mall för att skapa en offentlig IP-adress i en annan region behöver du rollen Nätverksbidragsgivare eller högre.
   
- Identifiera källnätverkslayouten och alla resurser som du använder för tillfället. Den här layouten innehåller men är inte begränsad till belastningsutjämnare, nätverkssäkerhetsgrupper (NSG) och virtuella nätverk.

- Kontrollera att din Azure-prenumeration gör att du kan skapa offentliga IP-adresser i målområdet som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrollera att prenumerationen har tillräckligt med resurser för att stödja tillägg av offentliga IPs för den här processen.  Läs mer i [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Förbereda och flytta
Följande steg visar hur du förbereder den offentliga IP-adressen för konfigurationsflytten med hjälp av en Resource Manager-mall och flyttar den offentliga IP-konfigurationen till målregionen med Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportera mallen och distribuera från ett skript

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) och följ anvisningarna på skärmen:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Hämta resurs-ID:t för den offentliga IP som du vill flytta till målregionen och placera det i en variabel med [Get-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportera det virtuella källnätverket till en .json-fil till katalogen där du kör kommandot [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Filen som hämtas namnges efter den resursgrupp som resursen exporterades från.  Leta upp filen som exporterades från kommandot ** \<resource-group-name>.json** och öppna den i en valfri redigerare:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
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
## <a name="discard"></a>Ignorera 

Efter distributionen, om du vill börja om eller ignorera den offentliga ip i målet, ta bort resursgruppen som skapades i målet och den flyttade offentliga IP kommer att tas bort.  Om du vill ta bort resursgruppen använder du [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flytten av det virtuella nätverket tar du bort det virtuella källnätverket eller resursgruppen, använder [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) eller [Remove-AzPublicIPAddress:](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en Azure Public IP från en region till en annan och rensade källresurserna.  Mer information om hur du flyttar resurser mellan regioner och haveriberedskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Flytta virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
