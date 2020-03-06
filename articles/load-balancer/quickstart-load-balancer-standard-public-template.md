---
title: 'Snabb start: skapa en Load Balancer-Azure-mall'
titleSuffix: Azure Load Balancer
description: Den här snabb starten visar hur du skapar en belastningsutjämnare med hjälp av Azure Resource Manager mall.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 1f681a9a8d8547589873cff055d9f59973c27355
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78295793"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Snabb start: skapa en Load Balancer för att belastningsutjämna virtuella datorer med Azure Resource Manager-mall

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. I den här snabb starten lär du dig hur du distribuerar en Azure Resource Manager-mall som skapar en standard belastningsutjämnare för att belastningsutjämna virtuella datorer. Att använda Resource Manager-mallen tar färre steg jämfört med andra distributions metoder.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-load-balancer"></a>Skapa en Load Balancer

Load Balancer och offentliga IP-SKU: er måste matcha. När du skapar en Standard Load Balancer måste du också skapa en ny standard-offentlig IP-adress som är konfigurerad som klient del för standard belastnings utjämningen. Använd [den här mallen](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/)om du vill skapa en grundläggande Load Balancer. Microsoft rekommenderar att du använder standard-SKU för produktions arbets belastningar.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json" range="1-150" highlight="58-122":::

Flera Azure-resurser har definierats i mallen:

- [**Microsoft. Network/belastningsutjämnare**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): för belastningsutjämnaren och för var och en av de tre virtuella datorerna.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3 av dem)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3 av dem)
- [**Microsoft. Compute/virtualMachine/Extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3 av dem): används för att konfigurera IIS och webb sidor

Du hittar fler mallar som är relaterade till Azure Load Balancer i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Distribuera mallen

1. Välj **prova** från följande kodblock för att öppna Azure Cloud Shell och följ sedan anvisningarna för att logga in på Azure.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   Vänta tills du ser prompten från-konsolen.

1. Kopiera PowerShell-skriptet genom att välja **Kopiera** från föregående kodblock.

1. Högerklicka på fönstret Shell-konsol och välj sedan **Klistra in**.

1. Ange värdena.

   Vid mal Lav drift skapas tre tillgänglighets zoner. Tillgänglighets zoner stöds bara i [vissa regioner](../availability-zones/az-overview.md). Använd en av de regioner som stöds. Om du inte är säker anger du **centrala**.

   Resurs gruppens namn är projekt namnet med **RG** tillagt. Du behöver resurs grupps namnet i nästa avsnitt.

Det tar cirka 10 minuter att distribuera mallen. När det är slutfört ser utdata ut ungefär så här:

![Azure Standard Load Balancer Resource Manager-mall PowerShell-distribution av utdata](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Azure PowerShell används för att distribuera mallen. Förutom Azure PowerShell kan du också använda Azure Portal, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-portal.md).

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **resurs grupper** i det vänstra fönstret.

1. Välj den resurs grupp som du skapade i föregående avsnitt. Standard resurs grupps namnet är projekt namnet med **RG** tillagt.

1. Markera belastningsutjämnaren. Dess standard namn är projekt namnet med **-lb-** tillägg.

1. Kopiera endast IP-delen av den offentliga IP-adressen och klistra in den i adress fältet i webbläsaren.

   ![Azure standard Load Balancer Resource Manager-mall offentlig IP](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    Webbläsaren visar standard sidan för webb servern för Internet Information Services (IIS).

   ![IIS-webbserver](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Om du vill se belastningsutjämnaren distribuerar trafik över alla tre virtuella datorer kan du framtvinga en uppdatering av webbläsaren från klient datorn.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dem tar du bort resurs gruppen, belastningsutjämnaren och alla relaterade resurser. Om du vill göra det går du till Azure Portal, väljer den resurs grupp som innehåller belastningsutjämnaren och väljer sedan **ta bort resurs grupp**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat en standard belastningsutjämnare, anslutna virtuella datorer till den, konfigurerat trafik regeln för belastnings utjämning, gjorde en hälso avsökning och testade belastnings utjämning.

Om du vill veta mer kan du fortsätta till självstudierna för Load Balancer.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
