---
title: Distribuera om Azure Stack Development Kit (ASDK) | Microsoft Docs
description: I den här artikeln får du lära dig hur du installerar om ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: b52ac4ae2a02208e61aafebe883d33ed27309134
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56194350"
---
# <a name="redeploy-the-asdk"></a>Distribuera om ASDK
I den här artikeln får du lära dig hur du distribuera om Azure Stack Development Kit (ASDK) i en produktionsmiljö. Eftersom uppgraderar ASDK inte stöds, måste du distribuera helt om den att flytta till en nyare version. Du kan också distribuera om ASDK när som helst som du vill börja om från början.

> [!IMPORTANT]
> Uppgradera ASDK till en ny version stöds inte. Du måste distribuera om ASDK på värddatorn för development kit varje gång som du vill utvärdera en nyare version av Azure Stack.

## <a name="remove-azure-registration"></a>Ta bort Azure-registrering 
Om du tidigare har registrerat din ASDK-installation med Azure bör du ta bort registreringen resursen innan du omdistribuerar ASDK. Omregistrera ASDK om du vill aktivera tillgängligheten för objekt i marketplace när du distribuerar om ASDK. Om du inte redan har registrerat ASDK med din Azure-prenumeration kan du hoppa över det här avsnittet.

Ta bort resursen för registrering med den **Remove-AzsRegistration** cmdlet för att avregistrera Azure Stack. Använd sedan den **Remove-AzureRMResourceGroup** cmdlet för att ta bort Azure Stack-resursgruppen från Azure-prenumerationen:

1. Öppna en PowerShell-konsol som administratör på en dator som har åtkomst till privilegierad slutpunkt. För ASDK är som värddator för development kit.

2. Kör följande PowerShell-kommandon för att avregistrera ASDK installationen och ta bort den **azurestack** resursgruppen från Azure-prenumerationen:

  ```Powershell    
  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Provide Azure subscription admin credentials
  Add-AzureRmAccount

  # Provide ASDK admin credentials
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

  # Unregister Azure Stack
  Remove-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01

  # Remove the Azure Stack resource group
  Remove-AzureRmResourceGroup -Name azurestack -Force
  ```

3. Du uppmanas att logga in på både Azure-prenumerationen och den lokala ASDK installationen när skriptet körs.
4. När skriptet har slutförts bör du se meddelanden som liknar följande exempel:

    ` De-Activating Azure Stack (this may take up to 10 minutes to complete).` ` Your environment is now unable to syndicate items and is no longer reporting usage data.`
    ` Remove registration resource from Azure...`
    ` "Deleting the resource..." on target "/subscriptions/<subscription information>"`
    ` ********** End Log: Remove-AzsRegistration ********* `



Azure Stack nu ska har avregistreras från din Azure-prenumeration. Dessutom tas azurestack resursgruppen, som skapades när du registrerade ASDK med Azure kan också bort.

## <a name="deploy-the-asdk"></a>Distribuera ASDK
Om du vill distribuera om Azure Stack börja du om från början enligt beskrivningen nedan. Stegen är olika beroende på om huruvida du använder Azure Stack installer (asdk installer.ps1) skriptet för att installera ASDK.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Distribuera om ASDK med hjälp av installationsprogrammet för skript
1. Öppna en upphöjd PowerShell-konsol på ASDK-dator och gå till asdk installer.ps1 skriptet i den **AzureStack_Installer** directory finns på en annan enhet än systemenheten. Kör skriptet och klicka på **omstart**.

   ![Kör skriptet asdk installer.ps1](media/asdk-redeploy/1.png)

2. Välj det grundläggande operativsystemet (inte **Azure Stack**) och klicka på **nästa**.

   ![Starta om operativsystemet för värden](media/asdk-redeploy/2.png)

3. När det startar om i det grundläggande operativsystemet development kit värden, kan du logga in som lokal administratör. Leta upp och ta bort den **C:\CloudBuilder.vhdx** filen som användes som en del av den föregående distributionen. 

4. Upprepa samma steg som du har gjort till första [distribuera ASDK](asdk-install.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Distribuera om ASDK utan att använda installationsprogrammet
Om du inte använde asdk installer.ps1 skriptet för att installera ASDK, måste du manuellt konfigurera om värddatorn development kit innan du omdistribuerar ASDK.

1. Starta verktyget genom att köra **msconfig.exe** på ASDK-dator. På den **Start** , Välj dator värdoperativsystemet (inte Azure Stack) klickar du på **ange som standard**, och klicka sedan på **OK**. Klicka på **starta om** när du tillfrågas.

      ![Ange startkonfiguration](media/asdk-redeploy/4.png)

2. När det startar om i det grundläggande operativsystemet development kit värden, kan du logga in som lokal administratör på värddatorn för development kit. Leta upp och ta bort den **C:\CloudBuilder.vhdx** filen som användes som en del av den föregående distributionen. 

3. Upprepa samma steg som du har gjort till första [distribuera ASDK med hjälp av PowerShell](asdk-deploy-powershell.md).


## <a name="next-steps"></a>Nästa steg
[Publicera ASDK distributionsuppgifter](asdk-post-deploy.md)




