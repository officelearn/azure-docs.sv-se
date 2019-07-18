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
ms.date: 06/19/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: e1a04cad7fe5c9c95397ffad2faa80c7d657d0f8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273802"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-an-azure-resource-manager-template"></a>Snabbstart: Skapa en standard Load Balancer för att belastningsutjämna virtuella datorer med hjälp av en Azure Resource Manager mall

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. I den här snabb starten lär du dig hur du distribuerar en Azure Resource Manager-mall som skapar en standard belastningsutjämnare för att belastningsutjämna virtuella datorer.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-standard-load-balancer"></a>Skapa en standardbelastningsutjämnare

Standard Load Balancer stöder endast en offentlig standard-IP-adress. När du skapar en standard belastningsutjämnare måste du också skapa en ny offentlig IP-adress som är konfigurerad som klient del för standard belastnings utjämningen.

Du kan använda många metoder för att skapa en standard-belastningsutjämnare. I den här snabb starten använder du Azure PowerShell för att distribuera en [Resource Manager-mall](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json). Resource Manager-mallar är JSON-filer som definierar de resurser du behöver för att distribuera lösningen.

Information om de begrepp som är kopplade till att distribuera och hantera dina Azure-lösningar finns i [Azure Resource Manager-dokumentationen](/azure/azure-resource-manager/). Du hittar fler mallar som är relaterade till Azure Load Balancer i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

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

Det tar cirka 10 minuter att distribuera mallen.

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **resurs grupper** i det vänstra fönstret.

1. Välj den resurs grupp som du skapade i föregående avsnitt. Standard resurs grupps namnet är projekt namnet med **RG** tillagt.

1. Markera belastningsutjämnaren. Dess standard namn är projekt namnet med **-lb-** tillägg.

1. Kopiera endast IP-delen av den offentliga IP-adressen och klistra in den i adress fältet i webbläsaren. Webbläsaren visar standard sidan för webb servern för Internet Information Services (IIS).

   ![IIS-webbserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Om du vill se belastningsutjämnaren distribuerar trafik över alla tre virtuella datorer kan du framtvinga en uppdatering av webbläsaren från klient datorn.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dem tar du bort resurs gruppen, belastningsutjämnaren och alla relaterade resurser. Om du vill göra det går du till Azure Portal, väljer den resurs grupp som innehåller belastningsutjämnaren och väljer sedan **ta bort resurs grupp**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat en standard belastningsutjämnare, anslutna virtuella datorer till den, konfigurerat trafik regeln för belastnings utjämning, gjorde en hälso avsökning och testade belastnings utjämning.

Om du vill veta mer kan du fortsätta till självstudierna för Load Balancer.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
 