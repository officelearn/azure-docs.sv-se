---
title: Automatisera distribution av en virtuell dator i Amazon Web Services
description: Den här artikeln visar hur du använder Azure Automation för att automatisera skapandet av en Amazon-webbtjänst-VM
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604830"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation scenario – etablera en virtuell AWS-dator
I den här artikeln får du lära dig hur du kan utnyttja Azure Automation för att etablera en virtuell dator i din AWS-prenumeration (Amazon Web Service) och ge den virtuella datorn ett särskilt namn – som AWS refererar till som "tagga" den virtuella datorn.

## <a name="prerequisites"></a>Krav
Du måste ha ett Azure Automation-konto och en Amazon Web Services-prenumeration (AWS). Mer information om hur du konfigurerar ett Azure Automation konto och konfigurerar det med dina autentiseringsuppgifter för AWS-prenumerationer, finns [i Konfigurera autentisering med Amazon Web Services](automation-config-aws-account.md). Det här kontot ska skapas eller uppdateras med dina autentiseringsuppgifter för AWS-prenumeration innan du fortsätter, när du refererar till det här kontot i avsnitten nedan.

## <a name="deploy-amazon-web-services-powershell-module"></a>Distribuera Amazon Web Services PowerShell-modul
Din VM-etablering av Runbook använder AWS PowerShell-modulen för att utföra sitt arbete. Använd följande steg för att lägga till modulen till ditt Automation-konto som är konfigurerat med dina AWS-prenumerations uppgifter.  

1. Öppna webbläsaren och gå till [PowerShell-galleriet](https://www.powershellgallery.com/packages/AWSPowerShell/) och klicka på **knappen distribuera till Azure Automation**.<br><br> ![Importera AWS PS-modul](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Du kommer till Azure-inloggnings sidan och efter autentiseringen dirigeras du till Azure Portal och visas på följande sida:<br><br> ![Sidan importera modul](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Välj det Automation-konto som ska användas och klicka på **OK** för att starta distributionen.

   > [!NOTE]
   > När Azure Automation importerar en PowerShell-modul extraheras cmdletarna. Aktiviteterna visas inte förrän Automation har slutfört importen av modulen och extraherat cmdletarna. Den här processen kan ta ett par minuter.  
   > <br>

1. Öppna ditt Automation-konto på Azure Portal.
2. Klicka på panelen **till gångar** och välj **moduler**i fönstret till gångar.
3. På sidan moduler visas modulen **AWSPowerShell** i listan.

## <a name="create-aws-deploy-vm-runbook"></a>Skapa AWS distribuera VM-Runbook
När AWS PowerShell-modulen har distribuerats kan du nu skapa en Runbook för att automatisera etablering av en virtuell dator i AWS med hjälp av ett PowerShell-skript. Stegen nedan visar hur du använder ursprungligt PowerShell-skript i Azure Automation.  

> [!NOTE]
> Ytterligare alternativ och information om det här skriptet finns på [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Hämta PowerShell-skriptet New-AwsVM från PowerShell-galleriet genom att öppna en PowerShell-session och skriva följande kommando:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Öppna ditt Automation-konto från Azure Portal och välj **Runbooks** under **process automatisering**.  
3. På sidan Runbooks väljer du **Lägg till en Runbook**.
4. I fönstret Lägg till en Runbook väljer du **snabb registrering** för att skapa en ny Runbook.
5. I rutan Runbook-egenskaper anger du ett namn för din Runbook.
6. I list rutan **Runbook-typ** väljer du **PowerShell**och klickar sedan på **skapa**.<br><br> ![Fönstret Skapa Runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. När sidan Redigera PowerShell-Runbook visas kopierar du och klistrar in PowerShell-skriptet i arbets ytan för Runbook-redigering.<br><br> ![Runbook PowerShell-skript](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Tänk på följande när du arbetar med PowerShell-skriptet:
    > 
    > * Runbooken innehåller ett antal standard parameter värden. Utvärdera alla standardvärden och uppdatera vid behov.
    > * Om du har lagrat dina AWS-autentiseringsuppgifter som en Credential-till `AWScred`gång med namnet annorlunda från måste du uppdatera skriptet på rad 57 för att matcha detta.  
    > * När du arbetar med AWS CLI-kommandona i PowerShell, särskilt med den här exempel-runbooken, måste du ange regionen AWS. Annars fungerar inte cmdletarna. Visa AWS-avsnittet [Ange AWS region](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) i AWS-verktyg för PowerShell-dokument för mer information.  
    >

7. Hämta en lista med avbildnings namn från din AWS-prenumeration genom att starta PowerShell ISE och importera AWS PowerShell-modulen. Autentisera mot AWS genom att `Get-AutomationPSCredential` ersätta i din ISE- `AWScred = Get-Credential`miljö med. I den här instruktionen uppmanas du att ange dina autentiseringsuppgifter och du kan ange ditt åtkomst nyckel-ID för användar namnet och din hemliga åtkomst nyckel för lösen ordet. 

        ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
        ```
        
    Följande utdata returneras:<br><br>
   ![Hämta AWS-avbildningar](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Kopiera och klistra in ett av avbildnings namnen i en Automation-variabel som refereras till i runbooken `$InstanceType`som. Eftersom du i det här exemplet använder den kostnads fria prenumerationen för AWS-nivå, använder du **T2. Micro** för ditt Runbook-exempel.  
9. Spara runbooken och klicka sedan på **publicera** för att publicera runbooken och sedan **Ja** när du uppmanas till det.

### <a name="testing-the-aws-vm-runbook"></a>Testa AWS VM-Runbook
Innan du fortsätter med att testa runbooken måste du verifiera några saker:

* En till gång `AWScred` som kallas för autentisering mot AWS har skapats, eller så har skriptet uppdaterats för att referera till till gångs namnet för din inloggning.    
* AWS PowerShell-modulen har importer ATS i Azure Automation.  
* En ny Runbook har skapats och parameter värden har verifierats och uppdaterats där det behövs.  
* **Logga utförliga poster** och alternativt **Logga status poster** under Runbook-åtgärden **loggning och spårning** har ställts in på **på**.<br><br> ![Runbook-loggning och spårning](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png).  

1. Klicka på **Starta** för att starta runbooken och klicka sedan på **OK** när fönstret starta Runbook öppnas.
2. I fönstret starta Runbook anger du ett namn på den virtuella datorn. Acceptera standardvärdena för de andra parametrarna som du förkonfigurerade i skriptet. Starta Runbook-jobbet genom att klicka på **OK** .<br><br> ![Starta New-AwsVM Runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Ett jobb fönster öppnas för det Runbook-jobb som du har skapat. Stäng det här fönstret.
4. Du kan visa förloppet för jobbet och Visa utgående strömmar genom att välja **alla loggar** från fönstret Runbook-jobb.<br><br> ![Strömma utdata](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. För att bekräfta att den virtuella datorn har allokerats loggar du in på hanterings konsolen för AWS om du inte är inloggad för tillfället.<br><br> ![AWS-konsol distribuerad virtuell dator](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Nästa steg
* För att komma igång med grafiska runbooks, se [min första grafiska Runbook](automation-first-runbook-graphical.md).
* Information om hur du kommer igång med PowerShell Workflow-Runbooks finns i [min första PowerShell Workflow-Runbook](automation-first-runbook-textual.md).
* Om du vill veta mer om Runbook-typer och deras fördelar och begränsningar, se [Azure Automation Runbook-typer](automation-runbook-types.md).
* Mer information om stöd för PowerShell-skript finns [i stöd för interna PowerShell-skript i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).