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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 445628679a09a1884f63cdce446adec476af39af
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057003"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Distribuera en mall i Azure Stack med hjälp av PowerShell

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan använda PowerShell för att distribuera Azure Resource Manager-mallar i Azure Stack. Den här artikeln visar hur du använder PowerShell för att distribuera en mall.

## <a name="run-azurerm-powershell-cmdlets"></a>Kör AzureRM PowerShell-cmdletar

Det här exemplet använder AzureRM PowerShell-cmdlets och en mall som lagras på GitHub. Mallen skapar en virtuell dator i Windows Server 2012 R2 Datacenter.

>[!NOTE]
>Innan du testar det här exemplet måste du se till att du har [konfigurerat PowerShell](azure-stack-powershell-configure-user.md) för en Azure Stack-användare.

1. Gå till <http://aka.ms/AzureStackGitHub> och hitta den **101-enkel-windows-vm** mall. Spara mallen i den här platsen: C:\\mallar\\azuredeploy-101 – enkel-windows-vm.json.
2. Öppna en upphöjd PowerShell-kommandotolk.
3. Ersätt *användarnamn* och *lösenord* i skriptet nedan med ditt användarnamn och lösenord och kör skriptet.

   ```PowerShell
       # Set Deployment Variables
       $myNum = "001" #Modify this per deployment
       $RGName = "myRG$myNum"
       $myLocation = "local"
   
       # Create Resource Group for Template Deployment
       New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
       # Deploy Simple IaaS Template
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
   >Varje gång som du kör det här skriptet kan öka värdet på parametern ”$myNum” för att förhindra att skrivs över din distribution.

4. Öppna Azure Stack portal, Välj **Bläddra**, och välj sedan **virtuella datorer** att hitta din nya virtuella dator (*myDeployment001*).

## <a name="next-steps"></a>Nästa steg

[Distribuera mallar med Visual Studio](azure-stack-deploy-template-visual-studio.md)
