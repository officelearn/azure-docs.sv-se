---
title: Registrera ASDK med Azure | Microsoft Docs
description: "Beskriver hur du registrerar Azure stacken med Azure för att aktivera marketplace syndikering och användningsrapportering."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 9e2dbc71f6424b87945e346a42c86d4cde7f740e
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="register-azure-stack-with-azure"></a>Registrera Azure stacken med Azure
Du kan registrera installationen av Azure Stack Development Kit (ASDK) med Azure att hämta marketplace-objekt från Azure och Ställ in commerce rapporterar tillbaka till Microsoft. Registrering rekommenderas eftersom du kan testa viktiga Azure Stack-funktioner som marketplace syndikering och användningsrapportering. När du har registrerat Azure Stack rapporterat användning till Azure handel. Du kan se den under den prenumeration som du använde för registrering. Dock debiteras inte ASDK användare för användning rapporterar.


## <a name="register-azure-stack-with-azure"></a>Registrera Azure stacken med Azure 
Följ dessa steg för att registrera ASDK med Azure.

> [!NOTE]
> De här stegen måste köras från en dator som har åtkomst till Privilegierade slutpunkten. För ASDK är som värddator för development kit. 

Innan du använder de här stegen för att registrera ASDK med Azure, se till att du har installerat Azure Stack PowerShell och hämta Azure Stack-verktyg som beskrivs i den [efter distributionskonfiguration](asdk-post-deploy.md) artikel. 

1. Öppna ett PowerShell-konsolen som administratör.  

2. Kör följande PowerShell-kommandon att registrera din ASDK installationen med Azure (du måste logga in på både Azure-prenumerationen och den lokala ASDK installationen):

    ```PowerShell
    # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
    Set-AzsRegistration `
        -CloudAdminCredential $CloudAdminCred `
        -PrivilegedEndpoint AzS-ERCS01 `
        -BillingModel Development

3. When the script completes, you should see this message: **Your environment is now registered and activated using the provided parameters.**

    ![](media/asdk-register/1.PNG) 

## Verify the registration was successful
Follow these steps to verify that the ASDK registration with Azure was successful.

1. Sign in to the [Azure Stack administration portal](https://adminportal.local.azurestack.external).

2. Click **Marketplace Management** > **Add from Azure**.

    ![](media/asdk-register/2.PNG) 

3. If you see a list of items available from Azure, your activation was successful.

    ![](media/asdk-register/3.PNG) 

## Next steps
[Add an Azure Stack marketplace item](asdk-marketplace-item.md)