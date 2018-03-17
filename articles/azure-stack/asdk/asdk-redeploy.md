---
title: Distribuera Azure Stack Development Kit (ASDK) | Microsoft Docs
description: "Lär dig hur du installerar om ASDK i den här självstudiekursen."
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
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 579414f79da29a443ddaf8e167bf3889a647f33d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-redeploy-the-asdk"></a>Självstudier: Distribuera om ASDK
I kursen får lära du att distribuera om Azure Stack Development Kit (ASDK) i en produktionsmiljö. Eftersom ASDK inte finns stöd för att uppgradera, måste du omdistribuera helt flytta det till en nyare version. Du kan också distribuera ASDK när som helst som du vill börja om från början.

> [!IMPORTANT]
> Uppgradera ASDK till en ny version stöds inte. Du måste distribuera ASDK på värddatorn development kit varje gång som du vill utvärdera en nyare version av Azure-stacken.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Ta bort registreringen för Azure 
> * Distribuera om ASDK

## <a name="remove-azure-registration"></a>Ta bort registreringen för Azure 
Om du redan har registrerat din ASDK installation med Azure bör du ta bort resursen registrering innan du omdistribuerar ASDK. Registrera ASDK om du vill aktivera marketplace syndikering när du distribuerar ASDK. Om du inte redan har registrerat ASDK med din Azure-prenumeration kan du hoppa över det här avsnittet.

Ta bort resursen registrering med den **ta bort AzsRegistration** för att avregistrera Azure stacken. Använd sedan den **ta bort AzureRMRsourceGroup** för att ta bort resursgruppen Azure Stack från din Azure-prenumeration:

1. Öppna ett PowerShell-konsolen som administratör på en dator som har åtkomst till Privilegierade slutpunkten. För ASDK är som värddator för development kit.

2. Kör följande PowerShell-kommandon för att avregistrera ASDK installationen och ta bort den **azurestack** resursgrupp från din Azure-prenumeration:

  ```Powershell    
  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Provide Azure subscription admin credentials
  Login-AzureRmAccount

  # Provide ASDK admin credentials
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

  # Unregister Azure Stack
  Remove-AzsRegistration `
      -CloudAdminCredential $YourCloudAdminCredential `
      -PrivilegedEndpoint AzS-ERCS01

  # Remove the Azure Stack resource group
  Remove-AzureRmResourceGroup -Name azurestack -Force
  ```

3. Du uppmanas att logga in på både Azure-prenumerationen och den lokala ASDK installationen när skriptet körs.
4. När skriptet har slutförts, bör du se meddelanden som liknar följande exempel:

    ` De-Activating Azure Stack (this may take up to 10 minutes to complete).` ` Your environment is now unable to syndicate items and is no longer reporting usage data.` ` Remove registration resource from Azure...` ` "Deleting the resource..." on target "/subscriptions/<subscription information>"` ` ********** End Log: Remove-AzsRegistration ********* `



Azure-stacken nu ska har avregistreras från din Azure-prenumeration. Dessutom ska resursgruppen azurestack skapas när du har registrerat ASDK med Azure, även tas bort.

## <a name="redeploy-the-asdk"></a>Distribuera om ASDK
Om du vill distribuera om Azure-stacken börja du om från början som beskrivs nedan. Stegen är olika beroende på om huruvida du skriptet Azure Stack installer (asdk installer.ps1) installerat för ASDK.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Distribuera om ASDK med hjälp av installationsprogrammet för skript
1. Öppna en upphöjd PowerShell-konsol på ASDK-datorn och navigera till asdk installer.ps1 skriptet i den **AzureStack_Installer** katalogen finns på en systemenhet. Kör skript och klicka på **omstart**.

   ![Kör skriptet asdk installer.ps1](media/asdk-redeploy/1.png)

2. Välj det grundläggande operativsystemet (inte **Azure Stack**) och klicka på **nästa**.

   ![Starta om operativsystemet för värden](media/asdk-redeploy/2.png)

3. När det startar om i det grundläggande operativsystemet development kit värden, kan du logga in som lokal administratör. Leta upp och ta bort den **C:\CloudBuilder.vhdx** filen som användes som en del av den tidigare distribueringen. 

4. Upprepa samma steg som du tog till första [distribuera ASDK](asdk-deploy.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Distribuera om ASDK utan att använda installationsprogrammet
Om du inte använde asdk installer.ps1 skriptet för att installera ASDK, måste du manuellt konfigurera om värddatorn development kit innan du omdistribuerar ASDK.

1. Starta verktyget genom att köra **msconfig.exe** på ASDK dator. På den **Start** , Välj dator värdoperativsystem (inte Azure-Stack) klickar du på **anges som standard**, och klicka sedan på **OK**. Klicka på **starta om** när du tillfrågas.

      ![Ange startkonfiguration](media/asdk-redeploy/4.png)

2. När det startar om i det grundläggande operativsystemet development kit värden, kan du logga in som lokal administratör på värddatorn för development kit. Leta upp och ta bort den **C:\CloudBuilder.vhdx** filen som användes som en del av den tidigare distribueringen. 

3. Upprepa samma steg som du tog till första [distribuera ASDK med hjälp av PowerShell](asdk-deploy-powershell.md).


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Ta bort registreringen för Azure 
> * Distribuera om ASDK

Gå vidare till nästa kurs att lära dig hur du lägger till en Azure-stacken marketplace-objektet.

> [!div class="nextstepaction"]
> [Lägg till ett Azure-stacken marketplace-objekt](asdk-marketplace-item.md)




