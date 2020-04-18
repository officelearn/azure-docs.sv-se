---
title: Automatisera distribution av en virtuell dator i Amazon Web Services
description: Den här artikeln visar hur du använder Azure Automation för att automatisera skapandet av en virtuell dator för Amazon Web Service
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604830"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation-scenario - etablera en virtuell AWS-dator
I den här artikeln får du lära dig hur du kan utnyttja Azure Automation för att etablera en virtuell dator i din AMAZON Web Service -prenumeration och ge den virtuella datorn ett specifikt namn – som AWS kallar "taggning" av den virtuella datorn.

## <a name="prerequisites"></a>Krav
Du måste ha ett Azure Automation-konto och en AMAZON Web Services -prenumeration (AWS). Mer information om hur du konfigurerar ett Azure Automation-konto och konfigurerar det med dina AWS-prenumerationsuppgifter finns i [Konfigurera autentisering med Amazon Web Services](automation-config-aws-account.md). Det här kontot ska skapas eller uppdateras med dina AWS-prenumerationsuppgifter innan du fortsätter, eftersom du refererar till det här kontot i avsnitten nedan.

## <a name="deploy-amazon-web-services-powershell-module"></a>Distribuera Amazon Web Services PowerShell-modul
Den vm-etableringskörningen använder AWS PowerShell-modulen för att utföra sitt arbete. Följ följande steg för att lägga till modulen i ditt Automation-konto som är konfigurerat med dina AWS-prenumerationsuppgifter.  

1. Öppna webbläsaren och navigera till [PowerShell-galleriet](https://www.powershellgallery.com/packages/AWSPowerShell/) och klicka på **knappen Distribuera till Azure Automation**.<br><br> ![Aws PS-modulimport](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Du tas till Azure-inloggningssidan och efter autentisera kommer du att dirigeras till Azure-portalen och presenteras med följande sida:<br><br> ![Sidan Importera modul](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Välj det Automation-konto som ska användas och klicka på **OK** för att starta distributionen.

   > [!NOTE]
   > När Azure Automation importerar en PowerShell-modul extraheras cmdlets. Aktiviteterna visas inte förrän Automation har importerat modulen helt och hållet. Den här processen kan ta ett par minuter.  
   > <br>

1. Öppna ditt Automation-konto på Azure Portal.
2. Klicka på panelen **Tillgångar** och välj **Moduler**i fönstret Tillgångar .
3. På sidan Moduler visas **MODULEN AWSPowerShell** i listan.

## <a name="create-aws-deploy-vm-runbook"></a>Skapa AWS-distribuera VM-runbook
När AWS PowerShell-modulen har distribuerats kan du nu skapa en runbook för att automatisera etablering av en virtuell dator i AWS med ett PowerShell-skript. Stegen nedan visar hur du använder inbyggt PowerShell-skript i Azure Automation.  

> [!NOTE]
> Mer information om skriptet finns i [PowerShell Gallery](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Hämta PowerShell-skriptet New-AwsVM från PowerShell Gallery genom att öppna en PowerShell-session och skriva följande kommando:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Öppna ditt Automation-konto på Azure-portalen och välj **Runbooks** under **Process Automation**.  
3. På sidan Runbooks väljer du **Lägg till en runbook**.
4. I fönstret Lägg till en runbook väljer du **Snabbskapa** för att skapa en ny runbook.
5. Skriv in ett namn på runbooken i egenskapsfönstret Runbook.
6. Välj **PowerShell**i listrutan **Runbook-typ** och klicka sedan på **Skapa**.<br><br> ![Skapa runbook-fönster](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. När sidan Redigera PowerShell Runbook visas kopierar och klistrar du in PowerShell-skriptet i arbetsytan för runbook-redigering.<br><br> ![PowerShell-skript för Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Observera följande när du arbetar med exemplet PowerShell-skript:
    > 
    > * Runbooken innehåller ett antal standardparametervärden. Utvärdera alla standardvärden och uppdatera vid behov.
    > * Om du har lagrat dina AWS-autentiseringsuppgifter som `AWScred`en referenstillgång som namnges på ett annat sätt än måste du uppdatera skriptet på rad 57 för att matcha i enlighet med detta.  
    > * När du arbetar med AWS CLI-kommandona i PowerShell, särskilt med den här exempelkörningsboken, måste du ange AWS-regionen. Annars misslyckas cmdlets. Visa AWS-avsnittet [Ange AWS-region](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) i AWS-verktygen för PowerShell-dokumentet för mer information.  
    >

7. Om du vill hämta en lista med bildnamn från din AWS-prenumeration startar du PowerShell ISE och importerar AWS PowerShell-modulen. Autentisera mot AWS genom att `Get-AutomationPSCredential` `AWScred = Get-Credential`ersätta i din ISE-miljö med . Den här policyn frågar efter dina autentiseringsuppgifter och du kan ange ditt nyckel-ID för användarnamnet och din hemliga åtkomstnyckel för lösenordet. 

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
   ![Få AWS-bilder](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Kopiera och klistra in ett av bildnamnen i en automatiseringsvariabel som refereras i runbooken som `$InstanceType`. Eftersom du i det här exemplet använder den kostnadsfria AWS-nivåindelad prenumerationen använder du **t2.micro** för runbook-exemplet.  
9. Spara runbooken och klicka sedan på **Publicera** för att publicera runbooken och sedan **Ja** när du uppmanas.

### <a name="testing-the-aws-vm-runbook"></a>Testa AWS VM-runbooken
Innan du fortsätter med att testa runbooken måste du verifiera några saker:

* En tillgång `AWScred` som kallas för autentisering mot AWS har skapats, eller skriptet har uppdaterats för att referera till ditt referensnamn för autentiseringsuppgifter.    
* AWS PowerShell-modulen har importerats i Azure Automation.  
* En ny runbook har skapats och parametervärden har verifierats och uppdaterats vid behov.  
* **Logga utförliga poster** och logga **eventuellt förloppsposter** under runbook-åtgärden **Loggning och spårning** har ställts in på **På**.<br><br> ![Körboksloggning](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)och spårning .  

1. Klicka på **Start** för att starta runbooken och klicka sedan på **OK** när fönstret Startkörning öppnas.
2. Ange ett VM-namn i fönstret Startkörningsbok. Acceptera standardvärdena för de andra parametrar som du förkonfigurerade i skriptet. Klicka på **OK** för att starta runbook-jobbet.<br><br> ![Starta ny-AwsVM runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Ett jobbfönster öppnas för det runbook-jobb som du skapade. Stäng den här rutan.
4. Du kan visa förloppet för jobbet och visa utdataströmmar genom att välja **Alla loggar** i fönstret Runbook Job.<br><br> ![Strömma utdata](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. För att bekräfta att den virtuella datorn etableras loggar du in på AWS Management Console om du för närvarande inte är inloggad.<br><br> ![AWS-konsol distribuerad virtuell dator](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Nästa steg
* Kom igång med grafiska runbooks finns i [Min första grafiska runbook](automation-first-runbook-graphical.md).
* Information om hur du kommer igång med PowerShell Workflow runbooks finns i [Min första PowerShell-arbetsflödeskörningsbok](automation-first-runbook-textual.md).
* Mer information om runbook-typer och deras fördelar och begränsningar finns i [Azure Automation-runbooktyper](automation-runbook-types.md).
* Mer information om PowerShell-skriptstödfunktionen finns i [Stöd för Inbyggt PowerShell-skript i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).