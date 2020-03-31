---
title: Så här schemalägger du Azure-SSIS Integration Runtime
description: I den här artikeln beskrivs hur du schemalägger start och stopp av Azure-SSIS Integration Runtime med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 8/2/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: 5263af2708ee30566e90cdf59ef69f52f76a9d32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440325"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Så startar och stoppar du Azure-SSIS Integration Runtime enligt ett schema
I den här artikeln beskrivs hur du schemalägger start och stopp av Azure-SSIS Integration Runtime (IR) med hjälp av Azure Data Factory (ADF). Azure-SSIS IR är ADF-beräkningsresurs som är dedikerad för att köra SSIS-paket (SQL Server Integration Services). Köra Azure-SSIS IR har en kostnad som är associerad med det. Därför vill du vanligtvis bara köra din IR när du behöver köra SSIS-paket i Azure och stoppa din IR när du inte behöver det längre. Du kan använda ADF User Interface (UI)/app eller Azure PowerShell för att [manuellt starta eller stoppa din IR](manage-azure-ssis-integration-runtime.md)).

Alternativt kan du skapa webbaktiviteter i ADF-pipelines för att starta/stoppa din IR i tid, till exempel starta den på morgonen innan du kör dina dagliga ETL-arbetsbelastningar och stoppa den på eftermiddagen efter att de är klara.  Du kan också kedja en kör SSIS-paketaktivitet mellan två webbaktiviteter som startar och stoppar din IR, så att din IR startar/stoppas på begäran, precis i tid före/efter att paketkörningen. Mer information om Kör SSIS-paketaktivitet finns i [Kör ett SSIS-paket med Kör SSIS-paketaktivitet i ADF-pipelineartikeln.](how-to-invoke-ssis-package-ssis-activity.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav
Om du inte redan har etablerat din Azure-SSIS IR etablerar du den genom att följa instruktionerna i [självstudien](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Skapa och schemalägga ADF-pipelines som startar och eller stoppar Azure-SSIS IR
I det här avsnittet visas hur du använder webbaktiviteter i ADF-pipelines för att starta/stoppa din Azure-SSIS IR i schema eller starta & stoppa den på begäran. Vi guidar dig att skapa tre pipelines: 

1. Den första pipelinen innehåller en webbaktivitet som startar din Azure-SSIS IR. 
2. Den andra pipelinen innehåller en webbaktivitet som stoppar din Azure-SSIS IR.
3. Den tredje pipelinen innehåller en körning SSIS-paketaktivitet kedjad mellan två webbaktiviteter som startar/stoppar din Azure-SSIS IR. 

När du har skapat och testat dessa pipelines kan du skapa en schemautlösare och associera den med valfri pipeline. Schemautlösaren definierar ett schema för att köra den associerade pipelinen. 

Du kan till exempel skapa två utlösare, den första är schemalagd att köras dagligen klockan 6 AM och associeras med den första pipelinen, medan den andra är schemalagd att köras dagligen vid 6 PM och associeras med den andra pipelinen.  På så sätt har du en period mellan 06:00 till 18:00 varje dag när din IR körs, redo att köra dina dagliga ETL-arbetsbelastningar.  

Om du skapar en tredje utlösare som är schemalagd att köras dagligen vid midnatt och associeras med den tredje pipelinen, körs den pipelinen vid midnatt varje dag, startar din IR strax före paketkörningen, kör sedan paketet och omedelbart stoppa din IR strax efter paketkörning, så din IR kommer inte att köras idly.

### <a name="create-your-adf"></a>Skapa din ADF

1. Logga in på [Azure Portal](https://portal.azure.com/).    
2. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. På sidan **Ny datafabrik** anger du **MyAzureSsisDataFactory** för **namn**. 
      
   ![Sidan Ny datafabrik](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Namnet på din ADF måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet på din ADF (t.ex. dittnamnMyAzureSsisDataFactory) och försöker skapa den igen. Se artikel [om datafabrik – namngivningsregler](naming-rules.md) om du vill veta mer om namngivningsregler för ADF-artefakter.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Välj din **Azure-prenumeration** som du vill skapa din ADF under. 
5. För **Resursgrupp**gör du något av följande:
     
   - Välj **Använd befintlig**och välj en befintlig resursgrupp i listrutan. 
   - Välj **Skapa ny**och ange namnet på den nya resursgruppen.   
         
   Mer information om resursgrupper finns i Använda resursgrupper för att hantera din [Azure-resursartikel.](../azure-resource-manager/management/overview.md)
   
6. För **Version**väljer du **V2** .
7. För **Plats**väljer du en av de platser som stöds för att skapa ADF i listrutan.
8. Välj **fäst till instrumentpanelen**.     
9. Klicka på **Skapa**.
10. På Azure-instrumentpanelen visas följande panel med status: **Distribuera datafabrik**. 

    ![panelen distribuerar datafabrik](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. När skapandet är klart kan du se din ADF-sida som visas nedan.
   
    ![Datafabrikens startsida](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Klicka på **Författare & Övervakare** om du vill starta ADF-användargränssnitt/app på en separat flik.

### <a name="create-your-pipelines"></a>Skapa dina pipelines

1. Välj **Skapa pipeline**i Sidan Låt oss **komma igång** . 

   ![Sidan Kom igång](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. Expandera menyn **Allmänt** i **verktygslådan Aktiviteter** och dra & släppa en **webbaktivitet** på pipelinedesignerytan. På fliken **Allmänt** i fönstret Aktivitetsegenskaper ändrar du aktivitetsnamnet så att **det startMyIR .** Växla till fliken **Inställningar** och gör följande åtgärder.

    1. För **URL**anger du följande URL för REST API som `{subscriptionId}`startar `{resourceGroupName}` `{factoryName}`Azure-SSIS IR och ersätter , och `{integrationRuntimeName}` med de faktiska värdena för din IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` Alternativt kan du också kopiera & klistra in resurs-IR:et från dess övervakningssida på ADF-användargränssnittet/appen för att ersätta följande del av ovanstående URL:`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ADF SSIS IR-resurs-ID](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. För **Metod**väljer du **POST**. 
    3. För **Brödtext**anger du `{"message":"Start my IR"}`. 
    4. För **autentisering**väljer du **MSI** för att använda den hanterade identiteten för din ADF, se Artikeln [Hanterad identitet för datafabrik](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) för mer information.
    5. För **Resurs** `https://management.azure.com/`anger du .
    
       ![ADF webbaktivitet schema SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Klona den första pipelinen för att skapa en andra, ändra aktivitetsnamnet för att **stoppaMyIR** och ersätta följande egenskaper.

    1. För **URL**anger du följande URL för REST API som `{subscriptionId}`stoppar `{resourceGroupName}` `{factoryName}`Azure-SSIS IR, ersätter , och `{integrationRuntimeName}` med de faktiska värdena för din IR:`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. För **Brödtext**anger du `{"message":"Stop my IR"}`. 

4. Skapa en tredje pipeline, dra & släppa en **kör SSIS-paketaktivitet** från aktivitetsverktygslådan på pipelinedesignerns yta och konfigurera den enligt instruktionerna i [Anropa ett SSIS-paket med hjälp av Kör SSIS-paketaktivitet i ADF-artikeln.](how-to-invoke-ssis-package-ssis-activity.md) **Activities**  Du kan också använda en **aktivitet för lagrad procedur** i stället och konfigurera den enligt instruktionerna i [Anropa ett SSIS-paket med hjälp av den lagrade procedurens aktivitet i ADF-artikeln.](how-to-invoke-ssis-package-stored-procedure-activity.md)  Kedja sedan aktiviteten Kör SSIS-paket/lagrad procedur mellan två webbaktiviteter som startar/stoppar din IR, liknande de webbaktiviteter i den första/andra pipelinen.

   ![ADF webbaktivitet på begäran SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Tilldela den hanterade identiteten för din ADF en **deltagarroll** till sig själv, så webbaktiviteter i sina pipelines kan anropa REST API för att starta/stoppa Azure-SSIS IRs som etablerats i den.  På ADF-sidan i Azure-portalen klickar du på **Åtkomstkontroll (IAM),** klickar på **+ Lägg till rolltilldelning**och gör sedan följande åtgärder på **Lägg till rolltilldelningsblad.**

    1. För **Roll**väljer du **Deltagare**. 
    2. För **Tilldela åtkomst till**väljer du Azure **AD-användare, grupp eller tjänsthuvudnamn**. 
    3. För **Välj**söker du efter ADF-namnet och väljer det. 
    4. Klicka på **Spara**.
    
   ![ADF-rolltilldelning för hanterad identitet](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Verifiera ADF-enheten och alla pipelineinställningar genom att klicka på **Validera alla/verifiera** i verktygsfältet fabrik/pipeline. Stäng **Verifieringsutdata för fabrik/pipeline** genom att **>>** klicka på knappen.  

   ![Verifiera pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Testa kör dina pipelines

1. Välj **Provkörning** i verktygsfältet för varje pipeline och se **Utdatafönstret** i det nedre fönstret. 

   ![Provkörning](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Om du vill testa den tredje pipelinen startar du SQL Server Management Studio (SSMS). Gör följande i fönstret **Anslut till server.** 

    1. För **Servernamn**anger du ** &lt;servernamnet&gt;.database.windows.net för Azure SQL Database**.
    2. Välj **Alternativ >>**.
    3. Välj **SSISDB** **för Anslutning till databas**.
    4. Välj **Anslut**. 
    5. Expandera **Integration Services-kataloger** -> **SSISDB** -> Din mapp -> **projekt** -> ditt SSIS-projekt -> **paket**. 
    6. Högerklicka på det angivna SSIS-paketet för att köra och välj **Rapporter** -> **standardrapporter** -> **alla körningar**. 
    7. Kontrollera att den kördes. 

   ![Verifiera SSIS-paketkörning](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Schemalägg dina pipelines

Nu när dina pipelines fungerar som förväntat kan du skapa utlösare för att köra dem vid angivna kadenser. Mer information om hur du associerar utlösare med pipelines finns i [Utlösa pipelinen i en schemaartikel.](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)

1. Välj **Utlösare** i verktygsfältet Pipeline och välj **Ny/Redigera**. 

   ![Utlösare -> ny/redigera](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. Välj **+ Nytt**i fönstret Lägg **till utlösare** .

   ![Lägg till utlösare - Ny](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. Gör följande i fönstret **Ny utlösare:** 

    1. För **Namn**anger du ett namn på utlösaren. I följande exempel är **Kör dagligen** utlösarnamnet. 
    2. För **Typ**väljer du **Schemalägg**. 
    3. För **STARTDATUM (UTC)** anger du ett startdatum och en starttid i UTC. 
    4. För **Återkommande**anger du en kadens för utlösaren. I följande exempel är det **Dagligen** en gång. 
    5. För **Avsluta**väljer du **Inget slut** eller anger ett slutdatum och sluttid efter att du har valt På **datum**. 
    6. Välj **Aktiverad** om du vill aktivera utlösaren direkt efter att du har publicerat hela ADF-inställningarna. 
    7. Välj **Nästa**.

   ![Utlösare -> ny/redigera](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. Granska alla varningar på sidan **Parametrar för triggerkörning** och välj **Slutför**. 
5. Publicera hela ADF-inställningarna genom att välja **Publicera alla** i fabriksverktygsfältet. 

   ![Publicera alla](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Övervaka dina pipelines och utlösare i Azure Portal

1. Om du vill övervaka utlösarkörningar och pipelinekörningar använder du fliken **Övervaka** till vänster om ADF-användargränssnittet/-appen. Detaljerade steg finns [i Övervaka pipeline-artikeln.](quickstart-create-data-factory-portal.md#monitor-the-pipeline)

   ![Pipelinekörningar](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Om du vill visa aktivitetskörningar som är associerade med en pipelinekörning väljer du den första länken (**Visa aktivitetskörningar**) i kolumnen **Åtgärder.** För den tredje pipelinen visas tre aktivitetskörningar, en för varje kedjad aktivitet i pipelinen (webbaktivitet för att starta din IR, Lagrad proceduraktivitet för att köra paketet och webbaktivitet för att stoppa din IR). Om du vill visa pipelinekörningarna igen väljer du **Pipelines-länk** högst upp.

   ![Aktivitetskörningar](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Om du vill visa **utlösarkörningarna** väljer du Utlösarkörningar i listrutan under **Pipeline körs** högst upp. 

   ![Utlösarkörning](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Övervaka dina pipelines och utlösare med PowerShell

Använd skript som följande exempel för att övervaka dina pipelines och utlösare.

1. Hämta status för en pipeline-körning.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Få information om en utlösare.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Hämta status för en utlösarkörning.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Skapa och schemalägg Azure Automation-runbook som startar/stoppar Azure-SSIS IR

I det här avsnittet får du lära dig att skapa Azure Automation-runbook som kör PowerShell-skript, starta/stoppa din Azure-SSIS IR enligt ett schema.  Detta är användbart när du vill köra ytterligare skript före/efter start/stopp av din IR för för-/efterbearbetning.

### <a name="create-your-azure-automation-account"></a>Skapa ditt Azure Automation-konto

Om du inte redan har ett Azure Automation-konto skapar du ett genom att följa instruktionerna i det här steget. Detaljerade steg finns i Skapa en artikel [med Ett Azure Automation-konto.](../automation/automation-quickstart-create-account.md) Som en del av det här steget skapar du ett **Azure Run As-konto** (ett tjänsthuvudnamn i din Azure Active Directory) och tilldelar det en **deltagarroll** i din Azure-prenumeration. Kontrollera att det är samma prenumeration som innehåller din ADF med Azure SSIS IR. Azure Automation använder det här kontot för att autentisera till Azure Resource Manager och arbeta med dina resurser. 

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. För närvarande stöds ADF UI/app endast i microsoft edge- och Google Chrome-webbläsare.
2. Logga in på [Azure Portal](https://portal.azure.com/).    
3. Välj **Nytt** på den vänstra menyn, välj **Övervakning + Hantering**och välj **Automatisering**. 

   ![Ny -> Övervakning + Management -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. Gör följande åtgärder i fönstret **Lägg till automationskonto.**

    1. För **Namn**anger du ett namn för ditt Azure Automation-konto. 
    2. För **prenumeration**väljer du den prenumeration som har din ADF med Azure-SSIS IR. 
    3. För **resursgrupp**väljer du **Skapa ny** om du vill skapa en ny resursgrupp eller Använd **befintlig** för att välja en befintlig. 
    4. För **Plats**väljer du en plats för ditt Azure Automation-konto. 
    5. Bekräfta **Skapa Azure Kör som konto** som **Ja**. Ett tjänsthuvudnamn skapas i din Azure Active Directory och tilldelas en **deltagarroll** i din Azure-prenumeration.
    6. Välj **Fäst på instrumentpanelen** för att visa den permanent i Azure-instrumentpanelen. 
    7. Välj **Skapa**. 

   ![Ny -> Övervakning + Management -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Distributionsstatus för ditt Azure Automation-konto i Azure-instrumentpanelen och meddelanden. 
    
   ![Distribuera automatisering](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Du kommer att se startsidan för ditt Azure Automation-konto när det har skapats. 

   ![Startsida för automatisering](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Importera ADF-moduler

1. Välj **Moduler** i avsnittet **DELADE RESURSER** på den vänstra menyn och kontrollera om du har **Az.DataFactory** + **Az.Profile** i listan över moduler.

   ![Verifiera de moduler som krävs](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Om du inte har **Az.DataFactory**går du till PowerShell Gallery for [Az.DataFactory- modulen](https://www.powershellgallery.com/packages/Az.DataFactory/), väljer **Distribuera till Azure Automation**, väljer ditt Azure Automation-konto och väljer sedan **OK**. Gå tillbaka för att visa **modulen Moduler** i avsnittet **DELADE RESURSER** på den vänstra menyn och vänta tills modulen **STATUS** för **Az.DataFactory** har ändrats till **Tillgänglig**.

    ![Verifiera modulen Data Factory](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Om du inte har **Az.Profile**går du till PowerShell Gallery for [Az.Profile-modulen](https://www.powershellgallery.com/packages/Az.profile/), väljer **Distribuera till Azure Automation**, väljer ditt Azure Automation-konto och väljer sedan **OK**. Gå tillbaka för att visa **modulen Moduler** i avsnittet **STATUS** **DELADE RESURSER** på den vänstra menyn och vänta tills status för **modulen Az.Profile** har ändrats till **Tillgänglig**.

    ![Verifiera profilmodulen](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Skapa din PowerShell-runbook

I följande avsnitt finns steg för att skapa en PowerShell-runbook. Skriptet som är associerat med runbooken startar/stoppar antingen Azure-SSIS IR baserat på det kommando som du anger för **OPERATION-parametern.** Det här avsnittet innehåller inte fullständig information om hur du skapar en runbook. Mer information finns i [Skapa en runbook-artikel.](../automation/automation-quickstart-create-runbook.md)

1. Växla till fliken Runbooks och välj **+ Lägg till en runbook** i **verktygsfältet.** 

   ![Lägga till en runbook-knapp](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Välj **Skapa en ny runbook** och gör följande åtgärder: 

    1. För **Namn**anger du **StartStopAzureSsisRuntime**.
    2. För **Runbook-typ**väljer du **PowerShell**.
    3. Välj **Skapa**.
    
   ![Lägga till en runbook-knapp](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Kopiera & klistra in följande PowerShell-skript i runbook-skriptfönstret. Spara och publicera sedan runbooken med hjälp av **knapparna Spara** och **publicera** i verktygsfältet. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![Redigera PowerShell-runbook](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Testa runbooken genom att välja **Start-knappen** i verktygsfältet. 

   ![Knappen Starta runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. Gör följande i fönstret **Starta runbook:** 

    1. För **RESURSGRUPPNAMN**anger du namnet på resursgruppen som har din ADF med Azure-SSIS IR. 
    2. För **DATA FACTORY NAME**anger du namnet på din ADF med Azure-SSIS IR. 
    3. För **AZURESSISNAME**anger du namnet på Azure-SSIS IR. 
    4. För **OPERATION**anger du **START**. 
    5. Välj **OK**.  

   ![Fönstret Starta runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. Välj **Utdatapanel** i projektfönstret. I utdatafönstret väntar du på meddelandet **##### Completed #####** när du ser **##### Starting #####**. Att starta Azure-SSIS IR tar cirka 20 minuter. Stäng **jobbfönstret** och gå tillbaka till **Runbook-fönstret.**

   ![Azure SSIS IR - startad](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Upprepa de två föregående stegen med **STOP** som värde för **OPERATION**. Starta runbooken igen genom att välja **Start-knappen** i verktygsfältet. Ange dina resursgrupps-, ADF- och Azure-SSIS IR-namn. För **OPERATION**anger du **STOP**. I utdatafönstret väntar du på meddelandet **##### Completed #####** när du ser **##### Stoppa #####**. Att stoppa Azure-SSIS IR tar inte lika lång tid som att starta den. Stäng **jobbfönstret** och gå tillbaka till **Runbook-fönstret.**

8. Du kan också utlösa din runbook via en webhook som kan skapas genom att välja **Webhooks** menyalternativ eller på ett schema som kan skapas genom att välja **menyalternativet Scheman** enligt nedan.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Skapa scheman för din runbook för att starta/stoppa Azure-SSIS IR

I föregående avsnitt har du skapat din Azure Automation-runbook som antingen kan starta eller stoppa Azure-SSIS IR. I det här avsnittet ska du skapa två scheman för runbooken. När du konfigurerar det första schemat anger du **START** för **OPERATION**. När du konfigurerar den andra anger du på samma sätt **STOP** för **OPERATION**. Detaljerade steg för att skapa scheman finns i [Skapa en schemaartikel.](../automation/shared-resources/schedules.md#creating-a-schedule)

1. I **runbook-fönstret** väljer du **Scheman**och väljer **+ Lägg till ett schema** i verktygsfältet. 

   ![Azure SSIS IR - startad](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. Gör följande i rutan **Schemakörning:** 

    1. Välj **Länka ett schema till runbooken**. 
    2. Välj **Skapa ett nytt schema**.
    3. I fönstret **Nytt schema** anger du **Start-IR dagligen** för **Namn**. 
    4. För **Starter**anger du en tid som är några minuter efter den aktuella tiden. 
    5. För **Återkommande**väljer du **Återkommande**. 
    6. För **Upprepa varje**anger du **1** och väljer **Dag**. 
    7. Välj **Skapa**. 

   ![Schema för Azure SSIS IR start](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Växla till **fliken Parametrar och kör inställningar.** Ange dina resursgrupper, ADF och Azure-SSIS IR-namn. För **OPERATION**anger du **START** och väljer **OK**. Välj **OK** igen om du vill se schemat på sidan **Scheman** i runbooken. 

   ![Schema för att stirra på Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Upprepa de två föregående stegen för att skapa ett schema med namnet **Stop IR dagligen**. Ange en tid som är minst 30 minuter efter den tid du angav för **Start IR dagliga** schema. För **OPERATION**anger du **STOP** och väljer **OK**. Välj **OK** igen om du vill se schemat på sidan **Scheman** i runbooken. 

5. Välj **Jobb** på den vänstra menyn i **Runbook-fönstret.** Du bör se de jobb som skapas av dina scheman vid angivna tider och deras status. Du kan se jobbinformationen, till exempel dess utdata, som liknar vad du har sett när du har testat runbooken. 

   ![Schema för att stirra på Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. När du har testat klart inaktiverar du dina scheman genom att redigera dem. Välj **Scheman** på den vänstra menyn, välj **Start IR dagligen/Stopp IR dagligen**och välj **Nej** för **Aktiverad**. 

## <a name="next-steps"></a>Nästa steg
Se följande blogginlägg:
-   [Modernisera och utöka dina ETL/ELT-arbetsflöden med SSIS-aktiviteter i ADF-pipelines](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

Se följande artiklar från SSIS-dokumentationen: 

- [Distribuera, köra och övervaka ett SSIS-paket på Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Ansluta till SSIS-katalogen på Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schemalägga paketetkörning på Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Ansluta till lokala data med Windows-autentisering](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
