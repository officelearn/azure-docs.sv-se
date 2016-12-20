---
title: "Skapa en Internetuppkopplad belastningsutjämnare i Resource Manager med hjälp av en mall | Microsoft Docs"
description: "Lär dig hur du skapar en Internetuppkopplad belastningsutjämnare i Resource Manager med hjälp av en mall"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
ms.assetid: b24f4729-4559-4458-8527-71009d242647
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: c40545833da86426d3e71955b8eb8627db3c1e4b
ms.openlocfilehash: 47a057a5ff7fc893a2f82d762db499ec10a8db8e

---

# <a name="creating-an-internet-facing-load-balancer-using-a-template"></a>Skapa en Internetuppkopplad belastningsutjämnare med hjälp av en mall

> [!div class="op_single_selector"]
> * [Portal](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Mall](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver Resource Manager-distributionsmodellen. Du kan också läsa om [hur du skapar en Internetuppkopplad belastningsutjämnare med hjälp av den klassiska distributionsmodellen](load-balancer-get-started-internet-classic-portal.md)

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Distribuera mallen genom att klicka för att distribuera

Exempelmallen som är tillgänglig i den offentliga databasen använder en parameterfil som innehåller standardvärdena som används för att generera scenariot som beskrivs ovan. Om du vill distribuera den här mallen genom att klicka för att distribuera följer du [den här länken](http://go.microsoft.com/fwlink/?LinkId=544801), klickar på **Deploy to Azure** (Distribuera till Azure), ersätter standardparametervärdena om det behövs och följer anvisningarna på portalen.

## <a name="deploy-the-template-by-using-powershell"></a>Distribuera mallen med hjälp av PowerShell

Följ stegen nedan om du vill distribuera mallen som du hämtat med hjälp av PowerShell.

1. Om du aldrig använt Azure PowerShell tidigare, se [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs) och följ instruktionerna till slutet för att logga in på Azure och välja din prenumeration.
2. Kör cmdleten **New-AzureRmResourceGroupDeployment** för att skapa en resursgrupp med hjälp av mallen.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
        -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
        -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Distribuera mallen med hjälp av Azure CLI

Följ stegen nedan om du vill distribuera mallen med hjälp av Azure CLI.

1. Om du aldrig har använt Azure CLI, se [installera och konfigurera Azure CLI](../xplat-cli-install.md) och följ instruktionerna upp till den punkt där du väljer Azure-konto och prenumeration.
2. Kör kommandot **azure config mode** för att växla till Resource Manager-läge, som det visas nedan.

    ```azurecli
    azure config mode arm
    ```

    Följande utdata förväntas från kommandot ovan:

        info:    New mode is arm

3. Navigera till [snabbstartsmallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules) i webbläsaren, kopiera innehållet i JSON-filen och klistra in det i en ny fil på din dator. I det här scenariot kopierar du värdena nedan till en fil med namnet **c:\lb\azuredeploy.parameters.json**.
4. Kör cmdleten **azure group deployment create** för att distribuera den nya belastningsutjämnaren med hjälp av mall- och parameterfilerna som du hämtade och ändrade ovan. Listan som visas efter alla utdata förklarar parametrarna som använts.

    ```azurecli
    azure group create --name TestRG --location westus --template-file 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' --parameters-file 'c:\lb\azuredeploy.parameters.json'
    ```

## <a name="next-steps"></a>Nästa steg

[Komma igång med att konfigurera en intern belastningsutjämnare](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurera ett distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Dec16_HO1-->


