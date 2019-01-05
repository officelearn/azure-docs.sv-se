---
title: Så här schemalägger du Azure-SSIS Integration Runtime | Microsoft Docs
description: Den här artikeln beskriver hur du schemalägger startas och stoppas av Azure-SSIS Integration Runtime med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 12/27/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 5920ec5ec8e864b5bdb986544a3cdc259e7344da
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053644"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Så här startar och stoppar Azure-SSIS Integration Runtime enligt ett schema
Den här artikeln beskriver hur du schemalägger startas och stoppas av Azure-SSIS Integration Runtime (IR) med hjälp av Azure Data Factory (ADF). Azure-SSIS IR är ADF-beräkningsresurs som dedikerade för att köra SQL Server Integration Services (SSIS)-paket. Kör Azure-SSIS IR har ingen associerad kostnad till den. Därför vanligtvis du kör din IR endast när du behöver att köra SSIS-paket i Azure och stoppa din IR när du inte behöver den längre. Du kan använda ADF User Interface (UI) / app eller Azure PowerShell för att [manuellt starta eller stoppa din IR](manage-azure-ssis-integration-runtime.md)).

Du kan också skapa webbaktiviteter i ADF pipelines för att starta/stoppa din IR enligt schema, t.ex. Starta den på morgonen innan du kör dina dagliga ETL-arbetsbelastningar och stoppa den på eftermiddagen när de är klar.  Du kan också länka en aktivitet för köra SSIS-paket mellan två webbaktiviteter som startar och stoppar dina IR så att din IR ska starta/stoppa på begäran, precis i tid före/efter körning av paket. Mer information om aktivitet kör SSIS-paket finns i [kör ett SSIS-paket med aktiviteten kör SSIS-paket i ADF pipeline](how-to-invoke-ssis-package-ssis-activity.md) artikeln.

## <a name="prerequisites"></a>Förutsättningar
Om du inte har etablerat din Azure-SSIS IR redan, etablerar du den genom att följa instruktionerna i den [självstudien](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Skapa och schemalägga ADF-pipelines som startar och eller stoppar Azure-SSIS IR
Det här avsnittet visar hur du använder webbaktiviteterna i ADF pipelines för att starta/stoppa din Azure-SSIS IR enligt schema eller starta och stoppa den på begäran. Vi hjälper dig att skapa tre pipelines: 

1. Den första pipelinen innehåller en webbaktivitet som startar ditt Azure-SSIS IR. 
2. Den andra pipelinen innehåller en webbaktivitet som stoppar Azure-SSIS IR.
3. Tredje pipelinen innehåller en aktivitet för köra SSIS-paket som har länkats mellan två webbaktiviteter som Starta/Stoppa din Azure-SSIS IR. 

När du har skapat och testa dessa pipelines kan du skapa en schemautlösare och associera det med en pipeline. Schemautlösare definierar ett schema för att köra den associerade pipelinen. 

Till exempel kan du skapa två utlösare, den första som är schemalagda att köras varje dag kl. 06: 00 och som är associerade med den första pipelinen, medan den andra mallen är schemalagd att köras varje dag kl. 18: 00 och som är associerade med den andra pipelinen.  På så sätt kan ha du en viss mellan 06: 00 till 18: 00 varje dag när din IR körs och redo att köra dina dagliga ETL-arbetsbelastningar.  

Om du skapar en tredje utlösare som är schemalagda att köras varje dag vid midnatt och som är associerade med den tredje pipelinen, att pipelinen körs vid midnatt varje dag, börjar din IR precis före körning av paket, därefter kör paketet, och omedelbart Stoppa din IR precis efter körning av paket, så din IR inte körs databassidor.

### <a name="create-your-adf"></a>Skapa din ADF

1. Logga in på [Azure-portalen](https://portal.azure.com/).    
2. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. I den **ny datafabrik** anger **MyAzureSsisDataFactory** för **namn**. 
      
   ![Sida för ny datafabrik](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Namnet på din ADF måste vara globalt unikt. Om du får följande fel ändrar du namnet på din ADF (till exempel Dittnamnmyazuressisdatafactory) och försöker skapa igen. Se [Data Factory – namnregler](naming-rules.md) du lär dig om namnregler för ADF artefakter.
  
   `Data factory name �MyAzureSsisDataFactory� is not available`
      
4. Välj Azure **prenumeration** under som du vill skapa din ADF. 
5. Gör något av följande för **Resursgrupp**:
     
   - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
   - Välj **Skapa nytt**, och ange namnet på din nya resursgrupp.   
         
   Mer om resursgrupper finns i [hantera Azure-resurser med hjälp av resursgrupper](../azure-resource-manager/resource-group-overview.md) artikeln.
   
6. För **Version**väljer **V2** .
7. För **plats**, väljer du något av de platser som stöds för skapande av ADF från den nedrullningsbara listan.
8. Välj **fäst till instrumentpanelen**.     
9. Klicka på **Skapa**.
10. På Azure-instrumentpanelen visas följande panel med statusen: **Distribuera Data Factory**. 

   ![panelen distribuerar datafabrik](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. När datafabriken har skapats, kan du se din ADF-sida som visas nedan.
   
   ![Datafabrikens startsida](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Klicka på **författare och Övervakare** att starta Användargränssnittet för ADF/app på en separat flik.

### <a name="create-your-pipelines"></a>Skapa pipelines

1. I **nu sätter vi igång** väljer **skapa pipeline**. 

   ![Sidan Kom igång](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. I **aktiviteter** verktygslådan Expandera **Allmänt** -menyn och dra och släpp en **Web** aktiviteten till pipelinedesignytan. I **Allmänt** fliken ändra aktivitetsnamnet på i egenskapsfönstret aktivitet **startMyIR**. Växla till **inställningar** fliken och göra följande.

    1. För **URL**, ange följande URL för REST-API som startar Azure-SSIS IR ersätta `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}`, och `{integrationRuntimeName}` med de faktiska värdena för din IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01`.
    
    Du kan alternativt kan du också kopiera och klistra in resurs-ID för din IR från den övervakning sidan på ADF användargränssnitt/app för att ersätta följande del av URL: en ovan: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`.
    
   ![ADF SSIS IR resurs-ID](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. För **metoden**väljer **POST**. 
    3. För **brödtext**, ange `{"message":"Start my IR"}`. 
    4. För **autentisering**väljer **MSI** för att använda den hanterade identitet för din ADF, se [tjänstidentitet för Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) artikeln för mer information.
    5. För **Resource**, ange `https://management.azure.com/`. 
    
   ![ADF Web aktivitetsschemat SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Klona den första pipelinen för att skapa en andra profil, ändra aktivitetsnamnet på **stopMyIR** och ersätter följande egenskaper.

    1. För **URL**, ange följande URL för REST-API som stoppar Azure-SSIS IR ersätta `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}`, och `{integrationRuntimeName}` med de faktiska värdena för din IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`.
  
    2. För **brödtext**, ange `{"message":"Stop my IR"}`. 

4. Skapa en tredje pipeline, dra och släpp en **köra SSIS-paket** aktivitet från **aktiviteter** verktygslådan till pipelinedesignern pipelinedesignytan och konfigurera den följa anvisningarna i [ Anropa ett SSIS-paket med aktiviteten kör SSIS-paket i ADF](how-to-invoke-ssis-package-ssis-activity.md) artikeln.  Du kan också använda en **lagringsprocedur** aktivitet i stället och konfigurera den följa anvisningarna i [anropa ett SSIS-paket med lagringsprocedur-aktivitet i ADF](how-to-invoke-ssis-package-stored-procedure-activity.md) artikeln.  Länka sedan kör SSIS paket/lagrad procedur-aktivitet mellan två webbaktiviteter som Starta/Stoppa din IR som liknar dessa webbaktiviteterna i pipelines första/sekund.

   ![ADF webbaktivitet på begäran SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Tilldela den hanterade identitet för din ADF en **deltagare** rollen till sig själv, så webbaktiviteterna i dess pipelines kan anropa REST API för att starta/stoppa Azure-SSIS IR etablerade i den.  På sidan med ADF i Azure-portalen klickar du på **åtkomstkontroll (IAM)**, klickar du på **+ Lägg till rolltilldelning**, och klicka sedan på **Lägg till rolltilldelning** bladet göra följande.

    1. För **rollen**väljer **deltagare**. 
    2. För **tilldela åtkomst till**väljer **Azure AD-användare, grupp eller tjänstens huvudnamn**. 
    3. För **Välj**, Sök efter namnet på din ADF och markera den. 
    4. Klicka på **Spara**.
    
   ![ADF hanterad identitet rolltilldelning](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Verifiera din ADF och alla pipeline inställningar genom att klicka på **Kontrollera att alla / Validera** i verktygsfältet factory-pipeline. Stäng **Datafabrik/Utdatarapport** genom att klicka på **>>** knappen.  

   ![Verifiera pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Dina pipelines för testkörning

1. Välj **testkörning** i verktygsfältet för varje pipeline och se **utdata** fönster längst ned i fönstret. 

   ![Kör test](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Starta SQL Server Management Studio (SSMS) för att testa tredje pipelinen. I **Anslut till Server** fönstret göra följande. 

    1. För **servernamn**, ange  **&lt;din Azure SQL Database-servernamn&gt;. database.windows.net**.
    2. Välj **alternativ >>**.
    3. För **Anslut till databas**väljer **SSISDB**.
    4. Välj **Anslut**. 
    5. Expandera **Integration Services kataloger** -> **SSISDB** -> din mapp -> **projekt** -> din SSIS-projekt -> **paket** . 
    6. Högerklicka på det SSIS-paketet som angetts för att köra och välj **rapporter** -> **standardrapporter** -> **alla körningar**. 
    7. Kontrollera att det körts. 

   ![Kontrollera körning av SSIS-paket](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Schemalägga pipelines

Nu när dina pipelines fungerar som förväntat kan skapa du utlösare för att köra dem i angivna cadences. Mer information om hur du kopplar en utlösare med pipelines finns i [Utlös pipelinen enligt ett schema](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) artikeln.

1. Pipeline-verktygsfältet och välj **utlösaren** och välj **ny/redigera**. 

   ![Utlösare -> Nytt/Redigera](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. I **Lägg till utlösare** väljer **+ ny**.

   ![Lägg till utlösare – ny](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. I **ny utlösare** fönstret göra följande: 

    1. För **namn**, ange ett namn för utlösaren. I följande exempel **köras varje dag** är Utlösarens namn. 
    2. För **typ**väljer **schema**. 
    3. För **starta datum (UTC)**, ange ett startdatum och tid i UTC. 
    4. För **upprepning**, ange en takt för utlösaren. I följande exempel är det **dagliga** när. 
    5. För **slutet**väljer **No End** eller ange ett slutdatum och tidpunkt när du har valt **på datumet**. 
    6. Välj **aktiverad** utlösaren ska aktiveras omedelbart när du har publicerat hela ADF-inställningarna. 
    7. Välj **Nästa**.

   ![Utlösare -> Nytt/Redigera](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. I **parametrar för Objektutlösare kör** granskar alla varningar, och välj **Slutför**. 
5. Publicera hela ADF inställningarna genom att välja **publicera alla** i verktygsfältet factory. 

   ![Publicera alla](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Övervaka pipelines och utlösare i Azure-portalen

1. Övervaka utlösarkörningar och pipelinekörningar genom att använda **övervakaren** fliken till vänster i ADF användargränssnitt/app. Detaljerade anvisningar finns i [övervaka pipelinen](quickstart-create-data-factory-portal.md#monitor-the-pipeline) artikeln.

   ![Pipelinekörningar](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Om du vill visa de aktivitetskörningar som är associerade med en pipeline som körs, väljer du den första länken (**visa Aktivitetskörningar**) i **åtgärder** kolumn. Tredje pipelinen visas tre aktivitetskörningar en för varje länkad aktivitet i pipelinen (webbaktivitet för att starta din IR, lagringsprocedur-aktivitet för att köra ditt paket och webb-aktivitet för att stoppa din IR). Visa pipelinen körs igen, Välj **Pipelines** länken längst upp.

   ![Aktivitetskörningar](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Om du vill visa utlösaren körs, Välj **Utlösarkörningar** från listrutan under **Pipelinekörningar** högst upp. 

   ![Utlösarkörning](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Övervaka pipelines och utlösare med PowerShell

Du kan använda skript som i följande exempel för att övervaka pipelines och utlösare.

1. Hämta status för en pipeline-körning.

  ```powershell
  Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
  ```

2. Få information om en utlösare.

  ```powershell
  Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
  ```

3. Hämta status för en utlösarkörning.

  ```powershell
  Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
  ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Skapa och schemalägga Azure Automation-runbook som börjar/slutar Azure-SSIS IR

I det här avsnittet får du lära dig att skapa Azure Automation-runbook som kör PowerShell-skript, starta/stoppa din Azure-SSIS IR enligt ett schema.  Detta är användbart när du vill köra ytterligare skript före/efter Starta/Stoppa din IR för före/efter-bearbetning.

### <a name="create-your-azure-automation-account"></a>Skapa ett Azure Automation-konto

Om du inte redan har ett Azure Automation-konto skapar du en genom att följa anvisningarna i det här steget. Detaljerade anvisningar finns i [skapa ett Azure Automation-konto](../automation/automation-quickstart-create-account.md) artikeln. Som en del av det här steget kan du skapa en **Azure kör som** kontot (ett huvudnamn för tjänsten i Azure Active Directory) och tilldela den en **deltagare** roll i din Azure-prenumeration. Kontrollera att det är samma prenumeration som innehåller din ADF med Azure-SSIS IR. Azure Automation använder det här kontot för att autentisera till Azure Resource Manager och arbeta med dina resurser. 

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. För närvarande stöds endast ADF användargränssnitt/app i Microsoft Edge och Google Chrome-webbläsare.
2. Logga in på [Azure-portalen](https://portal.azure.com/).    
3. Välj **New** på menyn till vänster väljer **övervakning + hantering**, och välj **Automation**. 

   ![-> Ny övervakning + Management Automation ->](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. I **Lägg till Automation-konto** fönstret göra följande.

    1. För **namn**, ange ett namn för ditt Azure Automation-konto. 
    2. För **prenumeration**, Välj den prenumeration som har din ADF med Azure-SSIS IR. 
    3. För **resursgrupp**väljer **Skapa nytt** att skapa en ny resursgrupp eller **Använd befintlig** att välja en befintlig. 
    4. För **plats**, Välj en plats för ditt Azure Automation-konto. 
    5. Bekräfta **skapa kör som-konto** som **Ja**. Ett huvudnamn för tjänsten skapas i Azure Active Directory och tilldelas en **deltagare** roll i din Azure-prenumeration.
    6. Välj **fäst på instrumentpanelen** att visa den permanent i Azure-instrumentpanelen. 
    7. Välj **Skapa**. 

   ![-> Ny övervakning + Management Automation ->](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Distributionsstatus för ditt Azure Automation-konto i Azure-instrumentpanelen och meddelanden visas. 
    
   ![Distribuera automation](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Du kan se startsidan för Azure Automation-kontot när den har skapats. 

   ![Startsida för Automation](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Importera ADF-moduler

1. Välj **moduler** i **delade resurser** på den vänstra menyn och kontrollera om du har **AzureRM.DataFactoryV2**  +   **AzureRM.Profile** i listan över moduler.

   ![Kontrollera modulerna som krävs](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Om du inte har **AzureRM.DataFactoryV2**går du till PowerShell-galleriet för [AzureRM.DataFactoryV2 modulen](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/)väljer **distribuera till Azure Automation**, Välj din Azure Automation-konto och välj sedan **OK**. Gå tillbaka och visa **moduler** i **delade resurser** avsnittet på den vänstra menyn och vänta tills du ser **STATUS** av **AzureRM.DataFactoryV2** modul ändras till **tillgänglig**.

    ![Verifiera Data Factory-modulen](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Om du inte har **AzureRM.Profile**går du till PowerShell-galleriet för [modulen AzureRM.Profile](https://www.powershellgallery.com/packages/AzureRM.profile/)väljer **distribuera till Azure Automation**, Välj ditt Azure Automation konto och välj sedan **OK**. Gå tillbaka och visa **moduler** i **delade resurser** avsnittet på den vänstra menyn och vänta tills du ser **STATUS** av den **AzureRM.Profile** modul ändras till **tillgänglig**.

    ![Verifiera modulen](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Skapa PowerShell-runbook

Följande avsnitt innehåller steg för att skapa en PowerShell-runbook. Skriptet som är associerat med din runbook antingen börjar/slutar Azure-SSIS IR baserat på kommandot som du anger för **ÅTGÄRDEN** parametern. Det här avsnittet ger inte den fullständiga informationen för att skapa en runbook. Mer information finns i [skapa en runbook](../automation/automation-quickstart-create-runbook.md) artikeln.

1. Växla till **Runbooks** fliken och markera **+ Lägg till en runbook** från verktygsfältet. 

   ![Lägg till en runbook-knapp](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Välj **skapa en ny runbook** och göra följande: 

    1. För **namn**, ange **StartStopAzureSsisRuntime**.
    2. För **runbooktyp**väljer **PowerShell**.
    3. Välj **Skapa**.
    
   ![Lägg till en runbook-knapp](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Kopiera och klistra in följande PowerShell-skript till din runbook skript-fönstret. Spara och publicera sedan din runbook med hjälp av **spara** och **publicera** knappar i verktygsfältet. 

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
        Connect-AzureRmAccount `
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
        Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![Redigera PowerShell-runbook](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Testa din runbook genom att välja **starta** i verktygsfältet. 

   ![Runbook-knappen Starta](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. I **starta Runbook** fönstret gör följande ations: 

    1. För **RESURSGRUPPENS namn**, ange namnet på resursgruppen som innehåller din ADF med Azure-SSIS IR. 
    2. För **DATAFABRIKSNAMNET**, anger du namnet på din ADF med Azure-SSIS IR. 
    3. För **AZURESSISNAME**, anger du namnet på Azure-SSIS IR. 
    4. För **ÅTGÄRDEN**, ange **starta**. 
    5. Välj **OK**.  

   ![Starta runbook-fönstret](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. I fönstret jobb väljer **utdata** panelen. Vänta tills meddelandet i utdatafönstret **### slutförd ###** efter att du ser **### startar ###**. Starta Azure-SSIS IR tar cirka 20 minuter. Stäng **jobbet** fönster och få tillbaka till **Runbook** fönster.

   ![Azure SSIS IR – igång](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Upprepa föregående två steg med **stoppa** som värde för **ÅTGÄRDEN**. Starta din runbook igen genom att välja **starta** i verktygsfältet. Ange din resursgrupp och ADF Azure-SSIS IR namn. För **ÅTGÄRDEN**, ange **stoppa**. Vänta tills meddelandet i utdatafönstret **### slutförd ###** efter att du ser **### stoppar ###**. Stoppar Azure-SSIS IR tar inte så länge som startar om den. Stäng **jobbet** fönster och få tillbaka till **Runbook** fönster.

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Skapa scheman för din runbook för att starta/stoppa Azure-SSIS IR

I det föregående avsnittet har du skapat din Azure Automation-runbook som kan starta eller stoppa Azure-SSIS IR. I det här avsnittet skapar du två scheman för din runbook. När du konfigurerar det första schemat kan du ange **starta** för **ÅTGÄRDEN**. På samma sätt när du konfigurerar den andra mallen kan du ange **stoppa** för **ÅTGÄRDEN**. Detaljerade steg för att skapa scheman, se [skapa ett schema](../automation/automation-schedules.md#creating-a-schedule) artikeln.

1. I **Runbook** väljer **scheman**, och välj **+ Lägg till ett schema** i verktygsfältet. 

   ![Azure SSIS IR – igång](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. I **schema Runbook** fönstret göra följande: 

    1. Välj **länka ett schema till din runbook**. 
    2. Välj **skapa ett nytt schema**.
    3. I **nytt schema** rutan Ange **starta IR dagligen** för **namn**. 
    4. För **startar**, ange en tid som är ett par minuter efter aktuell tid. 
    5. För **upprepning**väljer **återkommande**. 
    6. För **Upprepa varje**, ange **1** och välj **dag**. 
    7. Välj **Skapa**. 

   ![Schema för Azure SSIS IR-start](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Växla till **parametrar och körinställningar** fliken. Ange din resursgrupp och ADF Azure-SSIS IR namn. För **ÅTGÄRDEN**, ange **starta** och välj **OK**. Välj **OK** igen för att se schemat på **scheman** sidan i din runbook. 

   ![Schema för börjar Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Upprepa föregående två steg för att skapa ett schema med namnet **stoppa IR dagligen**. Ange en tid som är minst 30 minuter efter tidpunkten du angav för **starta IR dagligen** schema. För **ÅTGÄRDEN**, ange **stoppa** och välj **OK**. Välj **OK** igen för att se schemat på **scheman** sidan i din runbook. 

5. I **Runbook** väljer **jobb** på den vänstra menyn. Du bör se jobb som skapats av scheman på den angivna gånger och deras status. Du kan se jobbinformation, till exempel dess utdata, liknar vad du har sett när du har testat din runbook. 

   ![Schema för börjar Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. När du är klar testning, inaktivera scheman genom att redigera dem. Välj **scheman** på menyn till vänster väljer **starta IR varje dag/stoppa IR dagligen**, och välj **nr** för **aktiverad**. 

## <a name="next-steps"></a>Nästa steg
Se i följande blogginlägg:
-   [Modernisera och utöka dina ETL/ELT-arbetsflöden med SSIS-aktiviteter i ADF pipelines](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)

Se följande artiklar från SSIS-dokumentationen: 

- [Distribuera, köra och övervaka ett SSIS-paket på Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Ansluta till SSIS-katalogen på Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schemalägga paketetkörning på Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Ansluta till lokala data med Windows-autentisering](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
