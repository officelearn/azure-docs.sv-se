---
title: 'Snabbstart: Skapa en belastningsutjämnare - Azure-mall'
titleSuffix: Azure Load Balancer
description: Den här snabbstarten visar hur du skapar en belastningsutjämnare med hjälp av Azure Resource Manager-mallen.
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
ms.openlocfilehash: 175c5a36c873d16d50d5192a489133a01018e335
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474599"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Snabbstart: Skapa en belastningsutjämnare för att läsa in fördelnings virtuella datorer med hjälp av Azure Resource Manager-mallen

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. Den här snabbstarten visar hur du distribuerar en Azure Resource Manager-mall som skapar en standardbelastningsutjämnare för att läsa in virtuella datorer. Med hjälp av Resource Manager-mallen tar färre steg att jämföra med andra distributionsmetoder.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-a-load-balancer"></a>Skapa en lastbalanserare

Belastningsutjämnare och offentliga IP-SKU:er måste matcha. När du skapar en standardbelastningsutjämningsapparat måste du också skapa en ny offentlig standard-IP-adress som har konfigurerats som klientdel för standardbelastningsutjämnaren. Om du vill skapa en grundläggande belastningsutjämnare använder du [den här mallen](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/). Microsoft rekommenderar att du använder Standard SKU för produktionsarbetsbelastningar.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart Templates](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json" range="1-324" highlight="58-122":::

Flera Azure-resurser har definierats i mallen:

- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Network/publicIPAdresser:**](/azure/templates/microsoft.network/publicipaddresses)för belastningsutjämnaren och för var och en av de tre virtuella datorerna.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3 av dem)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3 av dem)
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3 av dem): används för att konfigurera IIS och webbsidorna

Mer information om fler mallar som är relaterade till Azure Load Balancer finns i [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Distribuera mallen

1. Välj **Prova det** från följande kodblock för att öppna Azure Cloud Shell och följ sedan instruktionerna för att logga in på Azure.

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

   Vänta tills du ser uppmaningen från konsolen.

1. Välj **Kopiera** från föregående kodblock om du vill kopiera PowerShell-skriptet.

1. Högerklicka på skalkonsolfönstret och välj sedan **Klistra in**.

1. Ange värdena.

   Malldistributionen skapar tre tillgänglighetszoner. Tillgänglighetszoner stöds endast i [vissa regioner](../availability-zones/az-overview.md). Använd en av de regioner som stöds. Om du är osäker, ange **centralus**.

   Resursgruppsnamnet är projektnamnet med **rg** bifogat. Du behöver resursgruppsnamnet i nästa avsnitt.

Det tar cirka 10 minuter att distribuera mallen. När den är klar liknar utdata:

![Azure Standard Load Balancer Resource Manager-mall PowerShell-distributionsutdata](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Azure PowerShell används för att distribuera mallen. Förutom Azure PowerShell kan du även använda Azure-portalen, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar](../azure-resource-manager/templates/deploy-portal.md).

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Resursgrupper** i den vänstra rutan.

1. Markera den resursgrupp som du skapade i föregående avsnitt. Standardresursgruppnamnet är projektnamnet med **rg** bifogat.

1. Välj belastningsutjämnaren. Standardnamnet är projektnamnet med **-lb** bifogat.

1. Kopiera endast IP-adressdelen av den offentliga IP-adressen och klistra sedan in den i webbläsarens adressfält.

   ![Azure standard belastningsutjämnare Resource Manager mall offentlig IP](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    I webbläsaren visas standardsidan för webbservern För Internet Information Services (IIS).

   ![IIS-webbserver](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Om du vill se belastningsutjämnaren distribuerar trafik mellan alla tre virtuella datorer kan du tvinga fram en uppdatering av din webbläsare från klientdatorn.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dem tar du bort resursgruppen, belastningsutjämnaren och alla relaterade resurser. Det gör du genom att gå till Azure-portalen, välja den resursgrupp som innehåller belastningsutjämnaren och välj sedan **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en standardbelastningsutjämnare, kopplade virtuella datorer till den, konfigurerade trafikregeln för belastningsutjämnare, gjorde en hälsoavsökning och testade sedan belastningsutjämnaren.

Om du vill veta mer fortsätter du till självstudierna för belastningsutjämnaren.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
