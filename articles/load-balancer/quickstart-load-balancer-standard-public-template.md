---
title: 'Snabbstart: Skapa en Standardbelastningsutjämnare – Azure Resource Manager-mall'
titlesuffix: Azure Load Balancer
description: Den här snabbstarten visar hur du skapar en Standardbelastningsutjämnare med hjälp av Azure Resource Manager-mallen.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer by using Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: a95bedc8b2b395f856512ec49bae630666fe36da
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272879"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Snabbstart: Skapa en Standardbelastningsutjämnare som belastningsutjämnar virtuella datorer med hjälp av Azure Resource Manager-mall

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. Den här snabbstarten visar hur du distribuerar en Azure Resource Manager-mall för att skapa en standardbelastningsutjämnare som belastningsutjämnar virtuella datorer (VM).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-standard-load-balancer"></a>Skapa en standardlastbalanserare

En Standard Load Balancer stöder endast offentliga IP-standardadresser. När du skapar en Standardbelastningsutjämnare, måste du också skapa en ny offentlig IP-adress som är konfigurerad som klientdelen för Standard Load Balancer. Det finns många metoder som kan användas för att skapa en standardbelastningsutjämnare. I den här snabbstarten använder du Azure PowerShell för att distribuera en [Resource Manager-mall](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json). Resource Manager-mallar är JSON-filer som definierar de resurser du behöver för att distribuera lösningen. Information om de koncept som är associerade med att distribuera och hantera dina Azure-lösningar finns i [dokumentation om Azure Resource Manager](/azure/azure-resource-manager/). Du hittar fler relaterade mall för Azure Load Balancer [Azure-Snabbstartsmallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

1. Välj **prova** från följande kodblock att öppna Azure Cloud shell och följ sedan anvisningarna för att logga in på Azure.

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
2. Välj **kopiera** från föregående kodblocket att kopiera PowerShell-skript.
3. Högerklicka på konsolfönstret shell och välj sedan **klistra in**.
4. Ange värden.

   Malldistributionen skapar tre tillgänglighetszoner.  Tillgänglighetszoner stöds bara i [vissa regioner](../availability-zones/az-overview.md). Använd någon av regionerna som stöds. Om du inte är säker på att ange **centralus**.

   Resursgruppens namn är projektnamnet på med **rg** sist. Du behöver resursgruppens namn i nästa avsnitt.

Det tar cirka 10 minuter för att distribuera mallen.

## <a name="test-the-load-balancer"></a>Testa belastningsutjämnaren

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **resursgrupper** i den vänstra rutan.
1. Välj den resursgrupp som du skapade i det sista avsnittet.  Standard resursgruppens namn är projektnamnet på med **rg** sist.
1. Välj belastningsutjämnaren.  Det finns bara en belastningsutjämnare. Standardnamnet är projektnamnet på med **-lb** sist.
1. Kopiera den offentliga IP-adressen (endast IP-adress delen) och klistra in den i adressfältet i webbläsaren. IIS-webbserverns standardsida visas i webbläsaren.

   ![IIS-webbserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Om du vill se hur belastningsutjämnaren distribuerar trafik över alla tre virtuella datorer, kan du framtvinga uppdatering av webbläsaren från klientdatorn.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, belastningsutjämnaren och alla relaterade resurser när de inte längre behövs. Om du vill göra det, Välj den resursgrupp som innehåller belastningsutjämnaren från Azure-portalen och välj sedan **ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten du skapade en Standardbelastningsutjämnare, anslutna virtuella datorer, konfigurerade trafikregel belastningsutjämnaren, hälsoavsökning och testade därefter belastningsutjämnaren. Om du vill läsa mer om Azure Load Balancer fortsätter du till självstudierna för Azure Load Balancer.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
