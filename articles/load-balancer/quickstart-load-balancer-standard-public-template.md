---
title: 'Snabbstart: Skapa en standardbelastningsutjämnare – Azure Resource Manager-mall'
titlesuffix: Azure Load Balancer
description: Den här snabbstarten visar hur du skapar en standardbelastningsutjämnare med hjälp av Azure Resource Manager-mallen.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 00dbb29cac30f2a3bbecf71727c79617e24af3e0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441306"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-an-azure-resource-manager-template"></a>Snabbstart: Skapa en standardbelastningsutjämnare som belastningsutjämnar virtuella datorer med hjälp av en Azure Resource Manager-mall

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. Den här snabbstarten visar hur du distribuerar en Azure Resource Manager-mall som skapar en standardbelastningsutjämnare som belastningsutjämnar virtuella datorer.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-standard-load-balancer"></a>Skapa en standardbelastningsutjämnare

Standardbelastningsutjämnare stöder endast en offentlig IP-adress. När du skapar en standardbelastningsutjämnare, måste du också skapa en ny offentlig IP-adress som är konfigurerad som klientdelen för belastningsutjämnaren på Standard.

Du kan använda många metoder för att skapa en standardbelastningsutjämnare. I den här snabbstarten använder du Azure PowerShell för att distribuera en [Resource Manager-mall](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json). Resource Manager-mallar är JSON-filer som definierar de resurser du behöver för att distribuera lösningen.

Information om de koncept som är associerade med att distribuera och hantera dina Azure-lösningar finns i [dokumentation om Azure Resource Manager](/azure/azure-resource-manager/). Du hittar fler mallar som är relaterade till Azure Load Balancer [Azure-Snabbstartsmallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

1. Välj **prova** från följande kodblock att öppna Azure Cloud Shell och följ sedan anvisningarna för att logga in på Azure.

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

1. Välj **kopiera** från föregående kodblocket att kopiera PowerShell-skript.

1. Högerklicka på konsolfönstret shell och välj sedan **klistra in**.

1. Ange värden.

   Malldistributionen skapar tre tillgänglighetszoner. Tillgänglighetszoner stöds bara i [vissa regioner](../availability-zones/az-overview.md). Använd någon av regionerna som stöds. Om du inte är säker på att ange **centralus**.

   Resursgruppens namn är projektnamnet på med **rg** sist. Du behöver resursgruppens namn i nästa avsnitt.

Det tar cirka 10 minuter för att distribuera mallen.

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **resursgrupper** i den vänstra rutan.

1. Välj den resursgrupp som du skapade i föregående avsnitt. Standard resursgruppens namn är projektnamnet på med **rg** sist.

1. Välj belastningsutjämnaren. Standardnamnet är projektnamnet på med **-lb** sist.

1. Kopiera enbart IP-adress delen av den offentliga IP-adressen och klistra in den i adressfältet i webbläsaren. Webbläsaren visar standardsidan för webbservern Internet Information Services (IIS).

   ![IIS-webbserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Om du vill se hur belastningsutjämnaren distribuerar trafik över alla tre virtuella datorer, kan du tvinga en uppdatering av webbläsaren från klientdatorn.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dem, ta bort resursgruppen, belastningsutjämnaren och alla relaterade resurser. Om du vill göra det, går du till Azure-portalen väljer du den resursgrupp som innehåller belastningsutjämnaren och välj sedan **ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade en standardbelastningsutjämnare, anslutna virtuella datorer, konfigurerat regeln för belastningsutjämning trafik, gjorde en hälsoavsökning och testade därefter belastningsutjämnaren.

Om du vill veta mer kan gå vidare till självstudiekurserna för belastningsutjämnaren.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
 