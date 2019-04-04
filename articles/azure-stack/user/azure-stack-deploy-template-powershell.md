---
title: Distribuera mallar med PowerShell i Azure Stack | Microsoft Docs
description: Distribuera en mall i Azure Stack med hjälp av PowerShell.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: d71df99096e58b3ac7adc920b91891b9a50cd6f7
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58479589"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Distribuera en mall i Azure Stack med hjälp av PowerShell

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan använda PowerShell för att distribuera Azure Resource Manager-mallar i Azure Stack. Den här artikeln beskriver hur du använder PowerShell för att distribuera en mall.

## <a name="run-azurerm-powershell-cmdlets"></a>Kör AzureRM PowerShell-cmdletar

Det här exemplet används **AzureRM** PowerShell-cmdlets och en mall som lagras på GitHub. Mallen skapar en virtuell dator i Windows Server 2012 R2 Datacenter.

>[!NOTE]
>Innan du testar det här exemplet måste du se till att du har [konfigurerat PowerShell](azure-stack-powershell-configure-user.md) för en Azure Stack-användare.

1. Gå till [ https://aka.ms/AzureStackGitHub ](https://aka.ms/AzureStackGitHub) och hitta den **101-enkel-windows-vm** mall. Spara mallen i den här platsen: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Öppna en upphöjd PowerShell-kommandotolk.
3. Ersätt `username` och `password` i skriptet nedan med ditt användarnamn och lösenord och kör skriptet:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "local"
   
    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
        -NewStorageAccountName mystorage$myNum `
        -DnsNameForPublicIP mydns$myNum `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
        -VmName myVM$myNum `
        -WindowsOSVersion 2012-R2-Datacenter
    ```

    >[!IMPORTANT]
    >Varje gång du kör det här skriptet kan du öka värdet på den `$myNum` parameter för att förhindra att skriva över din distribution.

4. Öppna Azure Stack portal, Välj **Bläddra**, och välj sedan **virtuella datorer** att hitta din nya virtuella dator (**myDeployment001**).

## <a name="next-steps"></a>Nästa steg

- [Distribuera mallar med Visual Studio](azure-stack-deploy-template-visual-studio.md)
