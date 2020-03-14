---
title: 'Snabb start: skapa en NAT-gateway – Resource Manager-mall'
titleSuffix: Azure Virtual Network NAT
description: Den här snabb starten visar hur du skapar en NAT-gateway med hjälp av Azure Resource Manager-mallen.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway by using an Azure Resource Manager template so that I can provide outbound connectivity for my virtual machines.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2020
ms.author: allensu
ms.custom: subject-armqs
ms.openlocfilehash: 051b1bd22b2e04f92c02b63a416160d824d1be10
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79216991"
---
# <a name="quickstart-create-a-nat-gateway---resource-manager-template"></a>Snabb start: skapa en NAT-gateway – Resource Manager-mall

Kom igång med Virtual Network NAT genom att använda en Azure Resource Manager-mall.  Den här mallen distribuerar ett virtuellt nätverk, en NAT-gateway och en virtuell Ubuntu-dator. Den virtuella Ubuntu-datorn distribueras till ett undernät som är associerat med NAT-gatewayen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-nat-gateway-and-supporting-resources"></a>Skapa en NAT-gateway och stöd resurser

Den här mallen har kon figurer ATS för att skapa en 

* Virtuellt nätverk 
* NAT-gateway-resurs
* Ubuntu virtuell dator

Den virtuella Ubuntu-datorn distribueras till ett undernät som är associerat med NAT gateway-resursen.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts mallar](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json" range="1-335" highlight="256-282":::

Nio Azure-resurser definieras i mallen:

**Microsoft. Network**

* **[Microsoft. Network/natGateways](https://docs.microsoft.com/azure/templates/microsoft.network/natgateways)** : skapar en NAT-gateway-resurs.

* **[Microsoft. Network/networkSecurityGroups](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)** : skapar en nätverks säkerhets grupp.

    * **[Microsoft. Network/networkSecurityGroups/securityRules](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups/securityrules)** : skapar en säkerhets regel.

* **[Microsoft. Network/publicIPAddresses](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)** : skapar en offentlig IP-adress.

* **[Microsoft. Network/publicIPPrefixes](https://docs.microsoft.com/azure/templates/microsoft.network/publicipprefixes)** : skapar ett offentligt IP-prefix.

* **[Microsoft. Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)** : skapar ett virtuellt nätverk.

    * **[Microsoft. Network/virtualNetworks/subnets](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets)** : skapar ett undernät för virtuellt nätverk.

* **[Microsoft. Network/NetworkInterfaces](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)** : skapar ett nätverks gränssnitt.

**Microsoft. Compute**

* **[Microsoft. Compute/virtualMachines](https://docs.microsoft.com/azure/templates/Microsoft.Compute/virtualMachines)** : skapar en virtuell dator.

### <a name="deploy-the-template"></a>Distribuera mallen

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupNAT"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az group deployment create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. westcentralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

$resourceGroupName = "myResourceGroupNAT"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Azure Portal**

[![Distribuera till Azure](./media/quick-create-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **resurs grupper** i det vänstra fönstret.

3. Välj den resurs grupp som du skapade i föregående avsnitt. Standard resurs grupps namnet är **myResourceGroupNAT**

4. Kontrol lera att följande resurser har skapats i resurs gruppen:

    ![Virtual Network NAT-resurs grupp](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Rensa resurser

**Azure CLI**

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) för att ta bort resurs gruppen och alla resurser som ingår i.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

När de inte längre behövs kan du använda kommandot [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) för att ta bort resurs gruppen och alla resurser som ingår i.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure Portal**

Ta bort resurs gruppen, NAT-gatewayen och alla relaterade resurser när de inte längre behövs. Välj den resurs grupp **myResourceGroupNAT** som innehåller NAT-gatewayen och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en:

* NAT-gateway-resurs
* Virtuellt nätverk
* Ubuntu virtuell dator

Den virtuella datorn distribueras till ett virtuellt nätverks under nät som är associerat med NAT-gatewayen. 

Om du vill veta mer om Virtual Network NAT och Azure Resource Manager fortsätter du till artiklarna nedan.

* Läs en [Översikt över Virtual Network NAT](nat-overview.md)
* Läs om [NAT-gatewayens resurs](nat-gateway-resource.md)
* Läs mer om [Azure Resource Manager](../azure-resource-manager/management/overview.md)
