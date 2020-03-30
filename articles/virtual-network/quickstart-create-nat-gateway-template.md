---
title: 'Snabbstart: Skapa en NAT-gateway - Resource Manager-mall'
titleSuffix: Azure Virtual Network NAT
description: Den här snabbstarten visar hur du skapar en NAT-gateway med hjälp av Azure Resource Manager-mallen.
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
ms.openlocfilehash: 6b3dee698e63ed2b0a44f05b593b8840d1cd23ee
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80066407"
---
# <a name="quickstart-create-a-nat-gateway---resource-manager-template"></a>Snabbstart: Skapa en NAT-gateway - Resource Manager-mall

Kom igång med NAT för virtuellt nätverk med hjälp av en Azure Resource Manager-mall.  Den här mallen distribuerar ett virtuellt nätverk, en VIRTUELL NAT-gatewayresurs och Ubuntu virtuell dator. Den virtuella Ubuntu-datorn distribueras till ett undernät som är associerat med NAT-gatewayresursen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-a-nat-gateway-and-supporting-resources"></a>Skapa en NAT-gateway och stödresurser

Den här mallen är konfigurerad för att skapa en 

* Virtuellt nätverk 
* NAT-gatewayresurs
* Ubuntu virtuell maskin

Ubuntu-datorn distribueras till ett undernät som är associerat med NAT-gatewayresursen.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json" range="1-335" highlight="256-282":::

Nio Azure-resurser definieras i mallen:

**Microsoft.Network**

* **[Microsoft.Network/natGateways](https://docs.microsoft.com/azure/templates/microsoft.network/natgateways)**: Skapar en NAT-gatewayresurs.

* **[Microsoft.Network/networkSecurityGroups](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)**: Skapar en nätverkssäkerhetsgrupp.

    * **[Microsoft.Network/networkSecurityGroups/securityRules](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups/securityrules)**: Skapar en säkerhetsregel.

* **[Microsoft.Network/publicIPAdresser :](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)** Skapar en offentlig IP-adress.

* **[Microsoft.Network/publicIPPrefixes](https://docs.microsoft.com/azure/templates/microsoft.network/publicipprefixes)**: Skapar ett offentligt IP-prefix.

* **[Microsoft.Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)**: Skapar ett virtuellt nätverk.

    * **[Microsoft.Network/virtualNetworks/undernät](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets)**: Skapar ett virtuellt nätverksundernät.

* **[Microsoft.Network/networkinterfaces](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)**: Skapar ett nätverksgränssnitt.

**Microsoft.Compute**

* **[Microsoft.Compute/virtualMachines](https://docs.microsoft.com/azure/templates/Microsoft.Compute/virtualMachines)**: Skapar en virtuell dator.

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

**Azure-portal**

[![Distribuera till Azure](./media/quick-create-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Resursgrupper** i den vänstra rutan.

3. Markera den resursgrupp som du skapade i föregående avsnitt. Standardnamn för resursgrupp är **myResourceGroupNAT**

4. Kontrollera att följande resurser har skapats i resursgruppen:

    ![NAT-resursgrupp för virtuellt nätverk](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Rensa resurser

**Azure CLI**

När det inte längre behövs kan du använda kommandot [az-gruppborttagning](/cli/azure/group#az-group-delete) för att ta bort resursgruppen och alla resurser som finns i.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

När det inte längre behövs kan du använda kommandot [Ta bort AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) för att ta bort resursgruppen och alla resurser som finns i.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure-portal**

När det inte längre behövs tar du bort resursgruppen, NAT-gatewayen och alla relaterade resurser. Markera resursgruppen **myResourceGroupNAT** som innehåller NAT-gatewayen och välj sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en:

* NAT-gatewayresurs
* Virtuellt nätverk
* Ubuntu virtuell maskin

Den virtuella datorn distribueras till ett virtuellt nätverksundernät som är associerat med NAT-gatewayen. 

Om du vill veta mer om NAT och Azure Resource Manager fortsätter du till artiklarna nedan.

* Läs en [översikt över NAT för virtuellt nätverk](nat-overview.md)
* Läs om [NAT Gateway-resursen](nat-gateway-resource.md)
* Läs mer om [Azure Resource Manager](../azure-resource-manager/management/overview.md)
