---
title: "Skapa en offentlig belastningsutjämnare – Azure-mall | Microsoft Docs"
description: "Lär dig hur du skapar en offentlig belastningsutjämnare i Resource Manager med hjälp av en mall"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: b24f4729-4559-4458-8527-71009d242647
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 21ff5a48d29117ba1d6195f308f00180b1f1edbf
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="creating-a-public-load-balancer-using-a-template"></a>Skapa en offentlig belastningsutjämnare med hjälp av en mall

> [!div class="op_single_selector"]
> * [Portal](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Mall](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Distribuera mallen genom att klicka för att distribuera

Exempelmallen som är tillgänglig i den offentliga databasen använder en parameterfil som innehåller standardvärdena som används för att generera scenariot som beskrivs ovan. Om du vill distribuera den här mallen genom att klicka för att distribuera följer du [den här länken](http://go.microsoft.com/fwlink/?LinkId=544801), klickar på **Deploy to Azure** (Distribuera till Azure), ersätter standardparametervärdena om det behövs och följer anvisningarna på portalen.

## <a name="deploy-the-template-by-using-powershell"></a>Distribuera mallen med hjälp av PowerShell

Följ stegen nedan om du vill distribuera mallen som du hämtat med hjälp av PowerShell.

1. Om du aldrig använt Azure PowerShell tidigare, se [Installera och konfigurera Azure PowerShell](/powershell/azure/overview) och följ instruktionerna till slutet för att logga in på Azure och välja din prenumeration.
2. Kör cmdleten **New-AzureRmResourceGroupDeployment** för att skapa en resursgrupp med hjälp av mallen.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
        -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
        -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Distribuera mallen med hjälp av Azure CLI

Följ stegen nedan om du vill distribuera mallen med hjälp av Azure CLI.

1. Om du aldrig har använt Azure CLI, se [installera och konfigurera Azure CLI](../cli-install-nodejs.md) och följ instruktionerna upp till den punkt där du väljer Azure-konto och prenumeration.
2. Kör kommandot **azure config mode** för att växla till Resource Manager-läge, som det visas nedan.

    ```azurecli
    azure config mode arm
    ```

    Här är den utdata som förväntas för det ovanstående kommandot:

        info:    New mode is arm

3. Navigera till [snabbstartsmallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules) i webbläsaren, kopiera innehållet i JSON-filen och klistra in det i en ny fil på din dator. I det här scenariot kopierar du värdena nedan till en fil med namnet **c:\lb\azuredeploy.parameters.json**.
4. Kör cmdleten **azure group deployment create** för att distribuera den nya belastningsutjämnaren med hjälp av mall- och parameterfilerna som du hämtade och ändrade ovan. Listan som visas efter utdatan beskriver de parametrar som används.

    ```azurecli
    azure group create --name TestRG --location westus --template-file 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' --parameters-file 'c:\lb\azuredeploy.parameters.json'
    ```

## <a name="next-steps"></a>Nästa steg

[Komma igång med att konfigurera en intern belastningsutjämnare](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurera ett distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)
