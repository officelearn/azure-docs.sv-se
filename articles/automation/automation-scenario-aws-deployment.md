---
title: Automatisera distributionen av en virtuell dator i Amazon Web Services
description: Den här artikeln visar hur du använder Azure Automation för att automatisera genereringen av en Amazon Web Service VM
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4f49adf006e8d55337220fad9ee84de65209880b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Azure Automation-scenario – etablera en virtuell dator i AWS
I den här artikeln får du lära dig hur du kan utnyttja Azure Automation för att etablera en virtuell dator i prenumerationen Amazon Web Service (AWS) och ge den virtuella datorn ett specifikt namn – AWS refererar till som ”märkning” den virtuella datorn.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha ett Azure Automation-konto och en AWS-prenumeration för den här artikeln. Mer information om att konfigurera en Azure Automation-konto och konfigurera den med dina autentiseringsuppgifter för AWS-prenumeration, granska [konfigurera autentisering med Amazon Web Services](automation-config-aws-account.md). Det här kontot ska skapas eller uppdateras med autentiseringsuppgifterna AWS prenumeration innan du fortsätter, som du refererar till det här kontot i stegen nedan.

## <a name="deploy-amazon-web-services-powershell-module"></a>Distribuera Amazon Web Services PowerShell-modul
Den virtuella datorn etablering runbook utnyttjar AWS PowerShell-modulen för att utföra sitt arbete. Utför följande steg om du vill lägga till modulen på ditt Automation-konto som har konfigurerats med dina autentiseringsuppgifter för AWS-prenumeration.  

1. Öppna webbläsaren och gå till den [PowerShell-galleriet](http://www.powershellgallery.com/packages/AWSPowerShell/) och klicka på den **till Azure Automation-knappen**.<br><br> ![AWS PS Modulimporten](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Du kommer till inloggningssidan för Azure och efter autentisering, du kommer att dirigeras till Azure-portalen och visas följande sida:<br><br> ![Importera modulsida](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Välj Automation-kontot och klickar på **OK** Inled distributionen.

   > [!NOTE]
   > När du importerar en PowerShell-modul till Azure Automation kan den också extraherar cmdlets och dessa aktiviteter visas inte förrän modulen är helt klar importerar och extrahering av cmdlets. Den här processen kan ta några minuter.  
   > <br>

1. Öppna ditt Automation-konto i steg 3 i Azure-portalen.
2. Klicka på den **tillgångar** panelen och på den **tillgångar** väljer den **moduler** panelen.
3. På den **moduler** sidan du ser den **AWSPowerShell** modul i listan.

## <a name="create-aws-deploy-vm-runbook"></a>Skapa AWS distribuera VM-runbook
När AWS PowerShell-modulen har distribuerats, kan du skapa en runbook för att automatisera etablera en virtuell dator i AWS använder ett PowerShell-skript. Stegen nedan visar hur man utnyttjar inbyggda PowerShell-skript i Azure Automation.  

> [!NOTE]
> Ytterligare alternativ och information om det här skriptet finns på [PowerShell-galleriet](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript).
> 

1. Hämta PowerShell-skriptet ny AwsVM från PowerShell-galleriet genom att öppna en PowerShell-session och skriva följande:<br>
   ```
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Öppna ditt Automation-konto i Azure Portal och välj **Runbooks** under avsnittet **Processautomatisering** till vänster.  
3. Från den **Runbooks** väljer **lägga till en runbook**.
4. På den **lägga till en runbook** väljer **Snabbregistrering** (skapa en ny runbook).
5. På den **Runbook** egenskapsrutan, ange ett namn i rutan namn för din runbook och från den **runbooktyp** listrutan Välj **PowerShell**, och klicka sedan på **Skapa**.<br><br> ![Skapa runbook-fönstret](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. När sidan Redigera PowerShell-Runbook visas, kopiera och klistra in PowerShell-skriptet i runbook authoring arbetsytan.<br><br> ![Runbook PowerShell-skript](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Observera följande när du arbetar med exempel PowerShell-skript:
    > 
    > * Runbook innehåller ett antal standardparametervärden. Utvärdera alla standardvärden och uppdatera vid behov.
    > * Om du har lagrat AWS-autentiseringsuppgifter som en autentiseringstillgång med namnet på ett annat sätt än **AWScred**, måste du uppdatera skriptet på raden 57 att matcha därefter.  
    > * När du arbetar med AWS CLI-kommandon i PowerShell, särskilt med exempel-runbook måste du ange AWS-region. Annars misslyckas cmdlets. Visa AWS avsnittet [ange AWS Region](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) i AWS-verktyg för PowerShell-dokumentet för mer information.  
    >

7. Om du vill hämta en lista över namn på bilder från prenumerationen AWS, starta PowerShell ISE och importera AWS PowerShell-modulen. Autentisera mot AWS genom att ersätta **Get-AutomationPSCredential** i ISE-miljön med **AWScred = Get-Credential**. Detta uppmanar dig att dina autentiseringsuppgifter och du kan ge din **åtkomst nyckel-ID** för användarnamnet och **hemlighet åtkomstnyckel** för lösenordet. Se exemplet nedan:  

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

    Returneras följande utdata:<br><br>
   ![Hämta AWS bilder](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Kopiera och klistra in något av namnen i en Automation-variabel som refereras till i en runbook som **$InstanceType**. Eftersom du är i det här exemplet använder ledigt AWS nivåer prenumeration kan du använda **t2.micro** runbook-exempel.  
9. Spara runbook och klicka sedan på **publicera** publicera en runbook och sedan **Ja** när du tillfrågas.

### <a name="testing-the-aws-vm-runbook"></a>Testa AWS VM-runbook
Du måste verifiera några saker innan du fortsätter med att testa runbook. Närmare bestämt:  

* En tillgång för att autentisera mot AWS har skapats kallas **AWScred** eller skriptet har uppdaterats för att referera till namnet på din autentiseringsuppgiftstillgång.    
* AWS PowerShell-modulen har importerats i Azure Automation  
* En ny runbook har skapats och parametervärden har verifierats och uppdateras vid behov  
* **Logga utförliga poster** och eventuellt **Förloppsposterna** under inställningen runbook **loggning och spårning** har ställts in på **på**.<br><br> ![Runbook-loggning och spårning](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Du vill starta runbook, så klicka på **starta** och klicka sedan på **OK** när starta Runbook-fönstret öppnas.
2. I rutan Starta Runbook ger en **VMname**. Acceptera standardvärdena för de andra parametrarna som du tidigare förkonfigurerade i skriptet. Klicka på **OK** att starta runbook-jobbet.<br><br> ![Starta ny AwsVM runbook](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Ett jobb-fönstret har öppnats för runbook-jobbet som du skapade. Stäng det här fönstret.
4. Du kan visa förloppet för jobbet och visa utdata **dataströmmar** genom att välja den **alla loggar** panelen från sidan runbook-jobbet.<br><br> ![Strömmad utdata](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. För att bekräfta att den virtuella datorn har etablerats kan du logga in på AWS-hanteringskonsolen om du för närvarande inte har loggat in.<br><br> ![AWS-konsolen distribueras VM](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Nästa steg
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
* Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
* Mer information om PowerShell-skriptstöd finns i [Inbyggt PowerShell-skriptstöd i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

