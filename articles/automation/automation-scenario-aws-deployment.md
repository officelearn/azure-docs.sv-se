---
title: Automatisera distribution av en virtuell dator i Amazon Web Services
description: Den här artikeln visar hur du använder Azure Automation för att automatisera skapandet av en Amazon-webbtjänst-VM
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2a58d3e79301f277143d8c4b6e810a377a211b9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849640"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation scenario – etablera en virtuell AWS-dator
I den här artikeln får du lära dig hur du kan utnyttja Azure Automation för att etablera en virtuell dator i din AWS-prenumeration (Amazon Web Service) och ge den virtuella datorn ett särskilt namn – som AWS refererar till som "tagga" den virtuella datorn.

## <a name="prerequisites"></a>Krav
I den här artikeln måste du ha ett Azure Automation-konto och en AWS-prenumeration. Mer information om hur du konfigurerar ett Azure Automation konto och konfigurerar det med dina autentiseringsuppgifter för AWS-prenumerationer, finns [i Konfigurera autentisering med Amazon Web Services](automation-config-aws-account.md). Det här kontot ska skapas eller uppdateras med dina autentiseringsuppgifter för AWS-prenumeration innan du fortsätter, när du refererar till det här kontot i stegen nedan.

## <a name="deploy-amazon-web-services-powershell-module"></a>Distribuera Amazon Web Services PowerShell-modul
Din virtuella dator etablerings Runbook använder AWS PowerShell-modulen för att utföra sitt arbete. Utför följande steg för att lägga till modulen till ditt Automation-konto som är konfigurerat med dina autentiseringsuppgifter för AWS-prenumeration.  

1. Öppna webbläsaren och gå till [PowerShell-galleriet](https://www.powershellgallery.com/packages/AWSPowerShell/) och klicka på **knappen distribuera till Azure Automation**.<br><br> Importera ![AWS PS-modul](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Du kommer till Azure-inloggnings sidan och efter autentiseringen dirigeras du till Azure Portal och visas på följande sida:<br><br> ![Sidan importera modul](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Välj det Automation-konto som ska användas och klicka på **OK** för att starta distributionen.

   > [!NOTE]
   > När du importerar en PowerShell-modul till Azure Automation, extraheras även cmdletarna och dessa aktiviteter visas inte förrän modulen har importer ATS och extraherat cmdletarna. Den här processen kan ta några minuter.  
   > <br>

1. I Azure Portal öppnar du ditt Automation-konto som refereras i steg 3.
2. Klicka på panelen **till gångar** och välj panelen **moduler** **i fönstret till gångar.**
3. På sidan **moduler** visas modulen **AWSPowerShell** i listan.

## <a name="create-aws-deploy-vm-runbook"></a>Skapa AWS distribuera VM-Runbook
När AWS PowerShell-modulen har distribuerats kan du nu skapa en Runbook för att automatisera etablering av en virtuell dator i AWS med hjälp av ett PowerShell-skript. Stegen nedan visar hur du utnyttjar det inbyggda PowerShell-skriptet i Azure Automation.  

> [!NOTE]
> Ytterligare alternativ och information om det här skriptet finns på [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Hämta PowerShell-skriptet New-AwsVM från PowerShell-galleriet genom att öppna en PowerShell-session och skriva följande:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Öppna ditt Automation-konto från Azure Portal och välj **Runbooks** under avsnittet **bearbeta automatisering** till vänster.  
3. På sidan **Runbooks** väljer du **Lägg till en Runbook**.
4. I fönstret **Lägg till en Runbook** väljer du **snabb registrering** (skapa en ny Runbook).
5. I rutan **Runbook** -egenskaper anger du ett namn i rutan namn för din Runbook och från List rutan **Runbook-typ** väljer du **PowerShell**och klickar sedan på **skapa**.<br><br> ![skapa Runbook-fönstret](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. När sidan Redigera PowerShell-Runbook visas kopierar du och klistrar in PowerShell-skriptet i arbets ytan för Runbook-redigering.<br><br> ![Runbook PowerShell-skript](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Tänk på följande när du arbetar med PowerShell-skriptet:
    > 
    > * Runbooken innehåller ett antal standard parameter värden. Utvärdera alla standardvärden och uppdatera vid behov.
    > * Om du har lagrat dina AWS-autentiseringsuppgifter som en Credential-till gång med namnet annorlunda än **AWScred**, måste du uppdatera skriptet på rad 57 för att matcha detta.  
    > * När du arbetar med AWS CLI-kommandona i PowerShell, särskilt med den här exempel-runbooken, måste du ange regionen AWS. Annars fungerar inte cmdletarna. Visa AWS-avsnittet [Ange AWS region](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) i AWS-verktyg för PowerShell-dokument för mer information.  
    >

7. Hämta en lista med avbildnings namn från din AWS-prenumeration genom att starta PowerShell ISE och importera AWS PowerShell-modulen. Autentisera mot AWS genom att ersätta **Get-AutomationPSCredential** i din ISE-miljö med **AWScred = Get-Credential**. Då uppmanas du att ange dina autentiseringsuppgifter och du kan ange ditt **åtkomst nyckel-ID** för användar namnet och **nyckeln för hemlig åtkomst** för lösen ordet. Se exemplet nedan:  

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
8. Kopiera och klistra in ett av avbildnings namnen i en Automation-variabel som refereras till i runbooken som **$InstanceType**. Eftersom du använder den kostnads fria AWS-nivån i det här exemplet använder du **T2. Micro** för ditt Runbook-exempel.  
9. Spara runbooken och klicka sedan på **publicera** för att publicera runbooken och sedan **Ja** när du uppmanas till det.

### <a name="testing-the-aws-vm-runbook"></a>Testa AWS VM-Runbook
Innan du fortsätter med att testa runbooken måste du verifiera några saker. Närmare bestämt:  

* En till gång för autentisering mot AWS har skapats med namnet **AWScred** eller så har skriptet uppdaterats för att referera till namnet på din inloggnings till gång.    
* AWS PowerShell-modulen har importer ATS i Azure Automation  
* En ny Runbook har skapats och parameter värden har verifierats och uppdaterats vid behov  
* **Logga utförliga poster** och om du vill **Logga status poster** under inställningen **loggning och spårning** av Runbook-inställningar har angetts till **på**.<br><br> ![Runbook-loggning och spårning](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Du vill starta runbooken, så klicka på **Start** och sedan på **OK** när fönstret starta Runbook öppnas.
2. I fönstret starta Runbook anger du en **VMName**. Acceptera standardvärdena för de andra parametrarna som du förkonfigurerade i skriptet tidigare. Starta Runbook-jobbet genom att klicka på **OK** .<br><br> ![starta New-AwsVM Runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Ett jobb fönster öppnas för det Runbook-jobb som du har skapat. Stäng det här fönstret.
4. Du kan visa förloppet för jobbet och Visa utgående **strömmar** genom att välja panelen **alla loggar** på sidan Runbook-jobb.<br><br> ](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png) för ![Stream-utdata
5. För att bekräfta att den virtuella datorn har allokerats loggar du in på hanterings konsolen för AWS om du inte är inloggad för tillfället.<br><br> ![AWS-konsol distribuerad virtuell dator](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Nästa steg
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
* Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
* Mer information om PowerShell-skriptstöd finns i [Inbyggt PowerShell-skriptstöd i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)


