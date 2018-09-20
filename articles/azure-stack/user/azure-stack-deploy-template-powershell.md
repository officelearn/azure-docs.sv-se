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
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 4b254f9a4446a1b0ff400e0d63effe68fc4f82b4
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363674"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Distribuera en mall i Azure Stack med hjälp av PowerShell

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan använda PowerShell för att distribuera Azure Resource Manager-mallar i Azure Stack. Den här artikeln beskriver hur du använder PowerShell för att distribuera en mall.

## <a name="run-azurerm-powershell-cmdlets"></a>Kör AzureRM PowerShell-cmdletar

Det här exemplet använder AzureRM PowerShell-cmdlets och en mall som lagras på GitHub. Mallen skapar en virtuell dator i Windows Server 2012 R2 Datacenter.

>[!NOTE]
>Innan du testar det här exemplet måste du se till att du har [konfigurerat PowerShell](azure-stack-powershell-configure-user.md) för en Azure Stack-användare.

1. Gå till [ http://aka.ms/AzureStackGitHub ](http://aka.ms/AzureStackGitHub) och hitta den **101-enkel-windows-vm** mall. Spara mallen i den här platsen: C:\\mallar\\azuredeploy-101 – enkel-windows-vm.json.
2. Öppna en upphöjd PowerShell-kommandotolk.
3. Ersätt *användarnamn* och *lösenord* i skriptet nedan med ditt användarnamn och lösenord och kör skriptet.

   ```PowerShell
   # Set deployment variables
   $myNum = "001" #Modify this per deployment
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

[Distribuera mallar med Visual Studio](azure-stack-deploy-template-visual-studio.md)
