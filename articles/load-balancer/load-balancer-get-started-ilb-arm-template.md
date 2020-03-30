---
title: Skapa en intern belastningsutjämnare - Azure-mall
titleSuffix: Azure Load Balancer
description: Lär dig hur du skapar en intern lastbalanserare med hjälp av en mall i Resource Manager
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 0d7cc4d571ddeb0b57fd4f025b8cbf7b204f61e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456972"
---
# <a name="create-an-internal-load-balancer-using-a-template"></a>Skapa en intern lastbalanserare med hjälp av en mall

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [Powershell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Mall](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Distribuera mallen genom att klicka för att distribuera

Exempelmallen som är tillgänglig i den offentliga databasen använder en parameterfil som innehåller standardvärdena som används för att generera scenariot som beskrivs ovan. Om du vill distribuera den här mallen genom att klicka för att distribuera följer du [den här länken](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer), klickar på **Deploy to Azure** (Distribuera till Azure), ersätter standardparametervärdena om det behövs och följer anvisningarna på portalen.

## <a name="deploy-the-template-by-using-powershell"></a>Distribuera mallen med hjälp av PowerShell

Följ stegen nedan om du vill distribuera mallen som du hämtat med hjälp av PowerShell.

1. Om du aldrig har använt Azure PowerShell läser du [Hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/overview) och följer instruktionerna hela vägen till slutet för att logga in på Azure och välja din prenumeration.
2. Hämta parameterfilen till din lokala disk.
3. Redigera filen och spara den.
4. Kör cmdleten **New-AzResourceGroupDeployment** för att skapa en resursgrupp med mallen.

    ```azurepowershell-interactive
    New-AzResourceGroupDeployment -Name TestRG -Location westus `
        -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
        -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Distribuera mallen med hjälp av Azure CLI

Följ stegen nedan om du vill distribuera mallen med hjälp av Azure CLI.

1. Om du aldrig har använt Azure CLI läser du [Installera och konfigurera Azure CLI](../cli-install-nodejs.md) och följer instruktionerna fram till den punkt där du väljer ditt Azure-konto och din prenumeration.
2. Gå [https://shell.azure.com](https://shell.azure.com) till för att öppna Cloud Shell i din webbläsare. Kör kommandot **azure config mode** för att växla till Resource Manager-läge, som det visas nedan.

    ```console
    azure config mode arm
    ```

    Följande utdata förväntas från kommandot ovan:

        info:    New mode is arm

3. Öppna parameterfilen, markerar innehållet och spara det till en fil på din dator. I det här exemplet sparade vi parameterfilen till *parameters.json*.
4. Kör kommandot **azure group distribution create** för att distribuera den nya interna lastbalanseraren med hjälp av mall- och parameterfilerna som du hämtade och ändrade ovan. Listan som visas efter utdatan beskriver de parametrar som används.

    ```console
    azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json
    ```

## <a name="next-steps"></a>Nästa steg

[Konfigurera ett distributionsläge för lastbalanserare med hjälp av käll-IP-tilldelning](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en lastbalanserare](load-balancer-tcp-idle-timeout.md)

JSON-syntaxen och egenskaperna för en belastningsutjämnare i en mall finns i [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).