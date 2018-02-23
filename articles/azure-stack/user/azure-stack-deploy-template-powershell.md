---
title: Distribuera mallar med PowerShell i Azure-stacken | Microsoft Docs
description: "Lär dig hur du distribuerar en virtuell dator med en Resource Manager-mall och PowerShell."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: d271b155d65a7dd95a92262da338cf3a272d140b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-in-azure-stack-using-powershell"></a>Distribuera mallar i Azure-stacken med hjälp av PowerShell

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Använd PowerShell för att distribuera Azure Resource Manager-mallar i Azure-stacken Development Kit.  Resource Manager-mallar distribuera och Tillhandahåll alla resurser i programmet i en enda, samordnad åtgärd.

## <a name="run-azurerm-powershell-cmdlets"></a>Kör AzureRM PowerShell-cmdlets
I det här exemplet kör du ett skript för att distribuera en virtuell dator till Azure-stacken Development Kit med hjälp av en Resource Manager-mall.  Innan du fortsätter bör du kontrollera att du har [konfigurerats PowerShell](azure-stack-powershell-configure-user.md)  

Den virtuella Hårddisken i exempel mallen är Windows Server-2012 R2-Datacenter.

1. Gå till <http://aka.ms/AzureStackGitHub>, söka efter den **101-enkel-windows-vm** mall, och spara den på följande plats: c:\\mallar\\ azuredeploy-101-enkel-windows-vm.json.
2. I PowerShell kör du följande distributionsskript. Ersätt *användarnamn* och *lösenord* med ditt användarnamn och lösenord. På efterföljande använder öka värdet för den *$myNum* parametern för att förhindra att skrivs över din distribution.
   
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
3. Öppna den Azure-stacken klickar du på **Bläddra**, klickar du på **virtuella datorer**, och leta efter den nya virtuella datorn (*myDeployment001*).


## <a name="next-steps"></a>Nästa steg
[Distribuera mallar med Visual Studio](azure-stack-deploy-template-visual-studio.md)

