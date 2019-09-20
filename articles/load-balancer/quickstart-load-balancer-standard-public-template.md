---
title: 'Snabbstart: Skapa en standard Load Balancer-Azure Resource Manager mall'
titlesuffix: Azure Load Balancer
description: Den här snabb starten visar hur du skapar en standard belastnings utjämning med hjälp av Azure Resource Manager-mallen.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Standard load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: ab55583d72297f2a1c72bac21e4414919f31b91b
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71161406"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Snabbstart: Skapa en standard Load Balancer för att belastningsutjämna virtuella datorer med Azure Resource Manager mall

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. I den här snabb starten lär du dig hur du distribuerar en Azure Resource Manager-mall som skapar en standard belastningsutjämnare för att belastningsutjämna virtuella datorer. Att använda Resource Manager-mallen tar färre steg jämfört med andra distributions metoder.

[Resource Manager-mall](../azure-resource-manager/template-deployment-overview.md) är en JavaScript Object Notation-fil (JSON) som definierar infrastrukturen och konfigurationen för ditt projekt. Mallen använder deklarativ syntax, som låter dig ange vad du vill distribuera utan att du behöver skriva sekvensen med programmerings kommandon för att skapa den. Om du vill veta mer om hur du utvecklar Resource Manager-mallar läser du [Resource Manager-dokumentationen](/azure/azure-resource-manager/) och [mallen referens](/azure/templates/microsoft.network/loadbalancers).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-standard-load-balancer"></a>Skapa en standardlastbalanserare

Standard Load Balancer stöder endast en offentlig standard-IP-adress. När du skapar en standard belastningsutjämnare måste du också skapa en ny offentlig IP-adress som är konfigurerad som klient del för standard belastnings utjämningen.

Mallen som används i den här snabb starten är en [snabb starts mall](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json).

[!code-json[<Azure Resource Manager template create standard load balancer>](~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json)]

Flera Azure-resurser har definierats i mallen:

- **Microsoft. Network/belastningsutjämnare**
- **Microsoft. Network/publicIPAddresses**: för belastningsutjämnaren.
- **Microsoft.Network/networkSecurityGroups**
- **Microsoft.Network/virtualNetworks**
- **Microsoft. Compute/virutalMachines** (3 av dem)
- **Microsoft. Network/publicIPAddresses** (3 av dem): för var och en av de tre virtuella datorerna.
- **Microsoft. Network/networkInterfaces** (3 av dem)
- **Microsoft. Compute/virtualMachine/tillägg** (3 av dem): används för att konfigurera IIS och webb sidor

Du hittar fler mallar som är relaterade till Azure Load Balancer i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

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

Azure PowerShell används för att distribuera mallen. Förutom Azure PowerShell kan du också använda Azure Portal, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/resource-group-template-deploy-portal.md).

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
