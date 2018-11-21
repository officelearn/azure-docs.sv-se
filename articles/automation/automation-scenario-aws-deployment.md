---
title: Automatisera distributionen av en virtuell dator i Amazon Web Services
description: Den här artikeln visar hur du använder Azure Automation för att automatisera genereringen av en Amazon Web Service-VM
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5133a3d724a362a3d022e0dfd29f798a509c4743
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275477"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation-scenario – etablera en virtuell dator i AWS
I den här artikeln får du lära dig hur du kan utnyttja Azure Automation för att etablera en virtuell dator i Amazon Web Service (AWS)-prenumeration och ge den virtuella datorn till ett visst namn – AWS refererar till som ”taggning” den virtuella datorn.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha ett Azure Automation-konto och en AWS-prenumeration för den här artikeln. Mer information om hur du konfigurerar ett Azure Automation-konto och konfigurera den med autentiseringsuppgifterna för din AWS-prenumeration [konfigurera autentisering med Amazon Web Services](automation-config-aws-account.md). Det här kontot ska skapas eller uppdateras med autentiseringsuppgifterna för din AWS prenumeration innan du fortsätter när du refererar till det här kontot i stegen nedan.

## <a name="deploy-amazon-web-services-powershell-module"></a>Distribuera Amazon Web Services PowerShell-modul
Den virtuella datorn etablering runbook utnyttjar AWS PowerShell-modulen för att utföra sitt arbete. Utför följande steg för att lägga till modulen till Automation-kontot som är konfigurerad med autentiseringsuppgifterna för din AWS-prenumeration.  

1. Öppna webbläsaren och navigera till den [PowerShell-galleriet](http://www.powershellgallery.com/packages/AWSPowerShell/) och klicka på den **distribuera till Azure Automation-knappen**.<br><br> ![AWS PS Modulimport](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Du kommer till inloggningssidan för Azure och när de har autentiserat, du ska dirigeras till Azure-portalen och visas följande sida:<br><br> ![Importera modulsida](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Välj det Automation-kontot du använder och klicka på **OK** att starta distributionen.

   > [!NOTE]
   > Medan du importerar en PowerShell-modul i Azure Automation, det också extrahering av cmdletarna och dessa aktiviteter visas inte förrän modulen är helt klar importerar och extrahering av cmdlets. Den här processen kan ta några minuter.  
   > <br>

1. Öppna ditt Automation-konto i steg 3 i Azure-portalen.
2. Klicka på den **tillgångar** panelen på den **tillgångar** väljer den **moduler** panelen.
3. På den **moduler** sidan du ser den **AWSPowerShell** modul i listan.

## <a name="create-aws-deploy-vm-runbook"></a>Skapa AWS distribuera VM-runbook
När PowerShell-modulen AWS har distribuerats kan skapa du nu en runbook för att automatisera etablering av en virtuell dator i AWS med hjälp av ett PowerShell-skript. Stegen nedan visar hur man utnyttjar inbyggda PowerShell-skript i Azure Automation.  

> [!NOTE]
> För ytterligare alternativ och information om det här skriptet finns i [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript).
> 

1. Hämta PowerShell-skript New AwsVM från PowerShell-galleriet genom att öppna en PowerShell-session och skriva följande:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Öppna ditt Automation-konto från Azure-portalen och välj **Runbooks** under avsnittet **Processautomatisering** till vänster.  
3. Från den **Runbooks** väljer **Lägg till en runbook**.
4. På den **Lägg till en runbook** väljer **Snabbregistrering** (skapa en ny runbook).
5. På den **Runbook** egenskapsfönster anger du ett namn i rutan namn för din runbook och från den **runbooktyp** listrutan Välj **PowerShell**, och klicka sedan på **Skapa**.<br><br> ![Skapa runbook-fönstret](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. När sidan Redigera PowerShell-Runbook visas, kopiera och klistra in PowerShell-skript i runbook Redigeringsyta.<br><br> ![Runbook PowerShell-skript](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Observera följande när du arbetar med exempel PowerShell-skript:
    > 
    > * Denna runbook innehåller ett antal standardparametervärden. Utvärdera alla standardvärden och uppdatera vid behov.
    > * Om du har sparat dina AWS-autentiseringsuppgifter som en autentiseringsuppgifttillgång med namnet på ett annat sätt än **AWScred**, måste du uppdatera skriptet på rad 57 så att de matchar därefter.  
    > * När du arbetar med AWS CLI-kommandon i PowerShell, särskilt i denna exempel-runbook, måste du ange AWS-region. Annars misslyckas cmdlets. Visa AWS avsnittet [ange AWS Region](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) i AWS-verktyg för PowerShell-dokument för mer information.  
    >

7. Om du vill hämta en lista med avbildningsnamn från din AWS-prenumeration, starta PowerShell ISE och importera PowerShell-modulen AWS. Autentisera mot AWS genom att ersätta **Get-AutomationPSCredential** i ISE-miljön med **AWScred = Get-Credential**. Detta uppmanar dig att dina autentiseringsuppgifter och du kan ange din **Åtkomstnyckelns ID** för användarnamnet och **hemliga åtkomstnyckel** för lösenordet. Se exemplet nedan:  

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
   ![Hämta AWS-bilder](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Kopiera och klistra in något av namnen i ett Automation-variabel som anges i runbook-flödet som **$InstanceType**. Eftersom du är i det här exemplet med hjälp av den kostnadsfria AWS nivåer prenumeration kan du använda **t2.micro** för runbook-exemplet.  
9. Spara runbooken och klicka sedan på **publicera** att publicera runbooken och sedan **Ja** när du tillfrågas.

### <a name="testing-the-aws-vm-runbook"></a>Testa AWS VM-runbook
Innan du fortsätter med att testa en runbook kommer behöva du kontrollera några saker. Närmare bestämt:  

* En tillgång för att autentisera mot AWS har skapats kallas **AWScred** eller skriptet har uppdaterats för att referera till namnet på din autentiseringsuppgiftstillgång.    
* AWS PowerShell-modulen har importerats i Azure Automation  
* En ny runbook har skapats och parametervärden har verifierats och uppdateras vid behov  
* **Logga utförliga poster** och eventuellt **Förloppsposterna** under inställningen för runbook **loggning och spårning** har ställts in på **på**.<br><br> ![Runbook-loggning och spårning](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Du vill starta runbooken, så klicka på **starta** och klicka sedan på **OK** när fönstret starta Runbook öppnas.
2. I fönstret starta Runbook ger en **VMname**. Godkänn standardvärdena för de andra parametrarna som du tidigare förkonfigurerade i skriptet. Klicka på **OK** att starta runbook-jobbet.<br><br> ![Starta ny AwsVM runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Ett Jobbfönster öppnas för runbook-jobbet som du skapade. Stäng det här fönstret.
4. Du kan visa förloppet för jobbet och visa utdata **strömmar** genom att välja den **alla loggar** panelen från sidan för runbook-jobbet.<br><br> ![Stream-utdata](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. För att bekräfta att den virtuella datorn håller på att etableras, du logga in på AWS-hanteringskonsolen om du för närvarande inte har loggat in.<br><br> ![Distribuerat AWS-konsolen](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Nästa steg
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
* Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
* Mer information om PowerShell-skriptstöd finns i [Inbyggt PowerShell-skriptstöd i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

