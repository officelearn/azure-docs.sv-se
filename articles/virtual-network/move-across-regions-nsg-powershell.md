---
title: Flytta Azure Network Security Group (NSG) till en annan Azure-region med Azure PowerShell
description: Använd Azure Resource Manager-mallen för att flytta Azure-nätverkssäkerhetsgrupp från en Azure-region till en annan med Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 0cbd8f61cb1b4cb8eae6b30625fb3039ff75adde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641521"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Flytta Azure Network Security Group (NSG) till en annan region med Azure PowerShell

Det finns olika scenarier där du vill flytta dina befintliga NSG:er från en region till en annan. Du kanske till exempel vill skapa en NSG med samma konfigurations- och säkerhetsregler för testning. Du kanske också vill flytta en NSG till en annan region som en del av katastrofåterställningsplanering.

Azure-säkerhetsgrupper kan inte flyttas från en region till en annan. Du kan dock använda en Azure Resource Manager-mall för att exportera de befintliga konfigurations- och säkerhetsreglerna för en NSG.  Du kan sedan iscensätta resursen i en annan region genom att exportera NSG till en mall, ändra parametrarna för att matcha målregionen och sedan distribuera mallen till den nya regionen.  Mer information om Resurshanteraren och mallar finns i [Exportera resursgrupper till mallar](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Krav

- Kontrollera att säkerhetsgruppen för Azure-nätverk finns i Azure-regionen som du vill flytta från.

- Azure-nätverkssäkerhetsgrupper kan inte flyttas mellan regioner.  Du måste associera den nya NSG till resurser i målregionen.

- Om du vill exportera en NSG-konfiguration och distribuera en mall för att skapa en NSG i en annan region behöver du rollen Nätverksbidragsgivare eller högre.
   
- Identifiera källnätverkslayouten och alla resurser som du använder för tillfället. Den här layouten innehåller men är inte begränsad till belastningsutjämnare, offentliga IP-adresser och virtuella nätverk.

- Kontrollera att din Azure-prenumeration gör att du kan skapa NSG:er i målregionen som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrollera att din prenumeration har tillräckligt med resurser för att stödja tillägg av NSG:er för den här processen.  Läs mer i [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Förbereda och flytta
Följande steg visar hur du förbereder nätverkssäkerhetsgruppen för konfigurations- och säkerhetsregelflytten med hjälp av en Resource Manager-mall och flyttar NSG-konfigurations- och säkerhetsreglerna till målregionen med Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportera mallen och distribuera från ett skript

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) och följ anvisningarna på skärmen:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Hämta resurs-ID:t för NSG som du vill flytta till målregionen och placera det i en variabel med [Get-AzNetworkSecurityGroup:](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportera käll-NSG till en .json-fil till katalogen där du kör kommandot [Export-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. Filen som hämtas namnges efter den resursgrupp som resursen exporterades från.  Leta upp filen som exporterades från kommandot ** \<resource-group-name>.json** och öppna den i en valfri redigerare:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Om du vill redigera parametern för NSG-namnet ändrar du **egenskapsstandardvärdevärdet** för käll-NSG-namnet till namnet på ditt mål NSG, se till att namnet är inom citationstecken:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. Om du vill redigera målområdet där NSG-konfigurationen **location** och säkerhetsreglerna ska flyttas ändrar du platsegenskapen under **resurser:**

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. Om du vill hämta regionplatskoder kan du använda Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) genom att köra följande kommando:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Du kan också ändra andra parametrar i ** \<resursgruppsnamnet>.json** om du vill och är valfria beroende på dina behov:

    * **Säkerhetsregler** - Du kan redigera vilka regler som distribueras till mål-NSG genom att lägga till eller ta bort regler i avsnittet **securityRules** i ** \<filen>.json för resursgruppsnamn:**

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        Om du vill slutföra tillägget eller borttagningen av reglerna i mål-NSG måste du också redigera de anpassade regeltyperna i slutet av ** \<filen>.json** i formatet nedan:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Spara ** \<filen resursgrupp-namn>.json.**

10. Skapa en resursgrupp i målregionen för mål-NSG som ska distribueras med [New-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Distribuera den redigerade ** \<resursgruppnamnet>.json-filen** till resursgruppen som skapades i föregående steg med [New-AzResourceGroupDeployment:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Om du vill verifiera att resurserna har skapats i målregionen använder du [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) och [Get-AzNetworkSecurityGroup:](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Ignorera 

Efter distributionen, om du vill börja om eller ignorera NSG i målet, ta bort resursgruppen som skapades i målet och den flyttade NSG tas bort.  Om du vill ta bort resursgruppen använder du [Remove-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flytten av NSG tar du bort käll-NSG- eller resursgruppen, använder [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) eller [Remove-AzNetworkSecurityGroup:](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0)

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en Azure-nätverkssäkerhetsgrupp från en region till en annan och rensade källresurserna.  Mer information om hur du flyttar resurser mellan regioner och haveriberedskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Flytta virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
