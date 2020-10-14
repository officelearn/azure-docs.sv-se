---
title: 'Snabb start: skapa en intern belastningsutjämnare med hjälp av en mall'
description: Den här snabb starten visar hur du skapar en intern Azure Load Balancer med hjälp av en Azure Resource Manager mall (ARM-mall).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: allensu
ms.date: 09/14/2020
ms.openlocfilehash: aa68dad2f8f018a9f3f70f2f02fd5e989ccbad4e
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047768"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Snabb start: skapa en intern belastningsutjämnare för att belastningsutjämna virtuella datorer genom att använda en ARM-mall

I den här snabb starten beskrivs hur du använder en Azure Resource Manager mall (ARM-mall) för att skapa en intern Azure Load Balancer.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azures snabb starts mallar](https://azure.microsoft.com/resources/templates/201-2-vms-internal-load-balancer).

:::code language="json" source="~/quickstart-templates/201-2-vms-internal-load-balancer/azuredeploy.json":::

Flera Azure-resurser har definierats i mallen:

- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): virtuella dator lagrings konton för startdiagnostik.
- [**Microsoft. Compute/availabilitySets**](/azure/templates/microsoft.compute/availabilitySets): tillgänglighets uppsättning för virtuella datorer.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks): virtuellt nätverk för belastningsutjämnare och virtuella datorer.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkInterfaces): nätverks gränssnitt för virtuella datorer.
- [**Microsoft. Network/belastningsutjämnare**](/azure/templates/microsoft.network/loadBalancers): intern belastningsutjämnare.
- [**Microsoft. Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualMachines): Virtual Machines.

Du hittar fler mallar som är relaterade till Azure Load Balancer i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupLB"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **resurs grupper** i det vänstra fönstret.

3. Välj den resurs grupp som du skapade i föregående avsnitt. Standard resurs grupps namnet är **myResourceGroupLB**

4. Kontrol lera att följande resurser har skapats i resurs gruppen:

:::image type="content" source="media/quickstart-load-balancer-standard-internal-template/verify-creation.png" alt-text="Användar Azure Portal för att verifiera att resurser skapas." border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) för att ta bort resurs gruppen och alla resurser som ingår i.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>Nästa steg

En stegvis själv studie kurs som vägleder dig genom processen för att skapa en mall finns i:

> [!div class="nextstepaction"]
> [Självstudie: skapa och distribuera din första ARM-mall](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)