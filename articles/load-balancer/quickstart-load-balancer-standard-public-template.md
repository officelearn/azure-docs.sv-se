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
ms.date: 05/30/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 0e47560ca43b23f4779da701f3e6f11f53a6b1ce
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480396"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Snabbstart: Skapa en Standardbelastningsutjämnare som belastningsutjämnar virtuella datorer med hjälp av Azure Resource Manager-mall

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. Du kan distribuera en Azure Resource Manager-mall för att skapa en belastningsutjämnare som belastningsutjämnar virtuella datorer (VM). Den här snabbstartsguiden visar hur du lastbalanserar virtuella datorer med en Standard Load Balancer.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-standard-load-balancer"></a>Skapa en standardlastbalanserare

I det här avsnittet skapar du en Standardbelastningsutjämnare som hjälper till att belastningsutjämna virtuella datorer. En Standard Load Balancer stöder endast offentliga IP-standardadresser. När du skapar en Standard Load Balancer, och även måste skapa en ny offentlig IP-standardadress som är konfigurerad som klientdelen (med namnet *LoadBalancerFrontend* som standard) för Standard Load Balancer. Det finns många metoder som kan användas för att skapa en standardbelastningsutjämnare. I den här snabbstarten använder du Azure PowerShell för att distribuera en [Resource Manager-mall](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json). Resource Manager-mallar är JSON-filer som definierar de resurser du behöver för att distribuera lösningen. Information om de koncept som är associerade med att distribuera och hantera dina Azure-lösningar finns i [dokumentation om Azure Resource Manager](/azure/azure-resource-manager/). Du hittar fler relaterade mall för Azure Load Balancer [Azure-Snabbstartsmallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

Om du vill distribuera mallen, Välj **prova** att öppna Azure Cloud shell och klistra in följande PowerShell-skript i fönstret shell. Om du vill klistra in koden, högerklicka på shell-fönstret och välj sedan **klistra in**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
$adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword
```

Observera att resursgruppens namn är projektnamnet på med **rg** sist. Du behöver resursgruppens namn i nästa avsnitt.  Det tar några minuter att skapa resurser.

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
