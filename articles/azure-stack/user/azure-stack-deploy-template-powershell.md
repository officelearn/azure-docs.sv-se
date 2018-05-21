---
title: Distribuera mallar med PowerShell i Azure-stacken | Microsoft Docs
description: Distribuera en mall till Azure-stacken med hjälp av PowerShell.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 4af82deef029120aa2699e7c69c501ae61a1e8bd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Distribuera en mall till Azure-stacken med hjälp av PowerShell

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan använda PowerShell för att distribuera Azure Resource Manager-mallar till Azure-stacken. Den här artikeln visar hur du använder PowerShell för att distribuera en mall.

## <a name="run-azurerm-powershell-cmdlets"></a>Kör AzureRM PowerShell-cmdlets

Det här exemplet använder AzureRM PowerShell-cmdlets och en mall som lagras på GitHub. Mallen skapar en virtuell dator i Windows Server 2012 R2 Datacenter.

>[!NOTE]
>Innan du försöker det här exemplet, se till att du har [konfigurerats PowerShell](azure-stack-powershell-configure-user.md) för en användare i Azure-stacken.

1. Gå till <http://aka.ms/AzureStackGitHub> och Sök efter den **101-enkel-windows-vm** mall. Spara mallen till den här platsen: C:\\mallar\\azuredeploy-101-enkel-windows-vm.json.
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
   >Varje gång du kör det här skriptet kan öka värdet på parametern ”$myNum” för att förhindra att skrivs över din distribution.

4. Öppna Företagsportalen, Välj Azure-stacken **Bläddra**, och välj sedan **virtuella datorer** att hitta den nya virtuella datorn (*myDeployment001*).

## <a name="next-steps"></a>Nästa steg

[Distribuera mallar med Visual Studio](azure-stack-deploy-template-visual-studio.md)
