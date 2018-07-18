---
title: Så här schemalägger du en Azure SSIS integration runtime | Microsoft Docs
description: Den här artikeln beskriver hur du schemalägger startas och stoppas av en Azure SSIS-integreringskörning med hjälp av Azure Automation och Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/16/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f83715d2a382db271686210d9df285c255c09216
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114002"
---
# <a name="how-to-start-and-stop-the-azure-ssis-integration-runtime-on-a-schedule"></a>Så här startar och stoppar Azure SSIS-integreringskörning enligt ett schema
Den här artikeln beskriver hur du schemalägger startas och stoppas av en Azure SSIS integration runtime (IR) med hjälp av Azure Automation och Azure Data Factory. Kör en Azure SSIS (SQL Server Integration Services) integration runtime har (IR) en kostnad som hör till den. Du vill därför vanligtvis körs IR endast när du behöver att köra SSIS-paket i Azure och stoppa IR när du inte behöver den. Du kan använda Data Factory-användargränssnitt eller Azure PowerShell för att [manuellt starta eller stoppa en Azure SSIS IR](manage-azure-ssis-integration-runtime.md)).

Du kan till exempel skapa webbaktiviteter med webhooks till en Azure Automation PowerShell-runbook och länka en aktivitet för köra SSIS-paket mellan dem. Web-aktiviteter kan starta och stoppa din Azure-SSIS IR precis i tid före och efter ditt paket har körts. Mer information om aktiviteten kör SSIS-paket finns i [kör ett SSIS-paket med SSIS-aktivitet i Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md).

## <a name="overview-of-the-steps"></a>Översikt över stegen

Här är övergripande stegen som beskrivs i den här artikeln:

1. **Skapa och testa en Azure Automation-runbook.** I det här steget skapar du en PowerShell-runbook med det skript som startar eller stoppar en Azure-SSIS IR. Sedan kan du testa runbooken i både START och stopp scenarier och bekräfta att IR startar eller stoppar. 
2. **Skapa två scheman för runbook.** Första schemat konfigurerar du runbook med START som åtgärden. Konfigurera runbook med stoppa som åtgärden för det andra schemat. För båda scheman anger du det intervall då denna runbook körs. Du kanske vill schemalägga den första mallen för att köra kl varje dag och den andra körs vid 23: 00 varje dag. När den första runbooken körs, startas i Azure-SSIS IR. När andra runbook körs, stoppas i Azure-SSIS IR. 
3. **Skapa två webhooks för runbook**, en för startåtgärd och andra för STOP-åtgärden. Du kan använda dessa webhooks URL: er när du konfigurerar webbaktiviteter i Data Factory-pipeline. 
4. **Skapa en Data Factory-pipeline**. Den pipeline du skapar består av tre aktiviteter. Först **Web** aktivitet anropar första webhooken om du vill starta Azure-SSIS IR. Den **lagringsprocedur** aktiviteten kör ett SQL-skript som kör SSIS-paket. Andra **Web** aktivitet stoppar Azure-SSIS IR. Läs mer om hur du anropar ett SSIS-paket från en Data Factory-pipeline med hjälp av aktiviteten lagringsprocedur [anropa ett SSIS-paket](how-to-invoke-ssis-package-stored-procedure-activity.md). Sedan skapar du en schemautlösare för att schemalägga pipelinen ska köras med det intervall som du anger.

## <a name="prerequisites"></a>Förutsättningar
Om du inte har etablerat en Azure SSIS integration runtime redan, etablerar du den genom att följa instruktionerna i den [självstudien](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-test-an-azure-automation-runbook"></a>Skapa och testa en Azure Automation-runbook
I det här avsnittet ska utföra du följande steg: 

1. Skapa ett Azure Automation-konto.
2. Skapa en PowerShell-runbook i Azure Automation-konto. PowerShell-skriptet som är kopplat till runbook startar eller stoppar en Azure SSIS IR baserat på kommandot som du anger för parameter för ÅTGÄRDEN. 
3. Testa runbooken i båda starta och stoppa scenarier för att bekräfta att den fungerar. 

### <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto
Om du inte har ett Azure Automation-konto kan du skapa en genom att följa anvisningarna i det här steget. Detaljerade anvisningar finns i [skapa ett Azure Automation-konto](../automation/automation-quickstart-create-account.md). Som en del av det här steget kan du skapa en **Azure kör som** kontot (ett huvudnamn för tjänsten i Azure Active Directory) och lägger till den till den **deltagare** rollen för din Azure-prenumeration. Kontrollera att det är samma som den prenumeration som innehåller data factory som har Azure-SSIS IR. Azure Automation använder kontot för att autentisera till Azure Resource Manager och arbeta med dina resurser. 

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
2. Logga in på [Azure-portalen](https://portal.azure.com/).    
3. Välj **New** på menyn till vänster väljer **övervakning + hantering**, och välj **Automation**. 

    ![-> Ny övervakning + Management Automation ->](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. I den **Lägg till Automation-konto** och gör följande: 

    1. Ange en **namn** för automation-kontot. 
    2. Välj den **prenumeration** som har data factory med Azure-SSIS IR. 
    3. För **resursgrupp**väljer **Skapa nytt** att skapa en ny resursgrupp eller välj **Använd befintlig** att välja en befintlig resursgrupp. 
    4. Välj en **plats** för automation-kontot. 
    5. Bekräfta att **skapa kör som-konto** är inställd på **Ja**. Ett huvudnamn för tjänsten har skapats i Azure Active Directory. Den har lagts till i **deltagare** rollen för din Azure-prenumeration
    6. Välj **fäst på instrumentpanelen** så att du ser det på instrumentpanelen i portalen. 
    7. Välj **Skapa**. 

        ![-> Ny övervakning + Management Automation ->](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. Du ser den **Distributionsstatus** på instrumentpanelen och i meddelanden. 
    
    ![Distribuera automation](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. Du kan se startsidan för automation-kontot när den har skapats. 

    ![Startsida för Automation](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>Importera Data Factory-moduler

1. Välj **moduler** i den **delade resurser** på den vänstra menyn, och kontrollera om du har **AzureRM.Profile** och **AzureRM.DataFactoryV2** i listan över moduler.

    ![Kontrollera modulerna som krävs](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Gå till PowerShell-galleriet för den [AzureRM.DataFactoryV2 modulen](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/)väljer **distribuera till Azure Automation**, väljer du ditt Automation-konto och välj sedan **OK**. Gå tillbaka och visa **moduler** i den **delade resurser** avsnitt i den vänstra menyn och vänta tills du ser den **STATUS** av den **AzureRM.DataFactoryV2** ändring av modul **tillgänglig**.

    ![Verifiera Data Factory-modulen](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Gå till PowerShell-galleriet för den [modulen AzureRM.Profile](https://www.powershellgallery.com/packages/AzureRM.profile/), klicka på **distribuera till Azure Automation**, väljer du ditt Automation-konto och välj sedan **OK**. Gå tillbaka och visa **moduler** i den **delade resurser** avsnitt i den vänstra menyn och vänta tills du ser den **STATUS** av den **AzureRM.Profile**ändring av modul **tillgänglig**.

    ![Verifiera modulen](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-a-powershell-runbook"></a>Skapa en PowerShell-runbook
Följande procedur innehåller steg för att skapa en PowerShell-runbook. Skriptet som är associerat med denna runbook antingen börjar/slutar en Azure SSIS IR baserat på kommandot som du anger för den **ÅTGÄRDEN** parametern. Det här avsnittet ger inte all information för att skapa en runbook. Mer information finns i [skapa en runbook](../automation/automation-quickstart-create-runbook.md) artikeln.

1. Växla till den **Runbooks** och sedan **+ Lägg till en runbook** från verktygsfältet. 

    ![Lägg till en runbook-knapp](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. Välj **skapa en ny runbook**, och utför följande steg: 

    1. För **namn**, typ **StartStopAzureSsisRuntime**.
    2. För **runbooktyp**väljer **PowerShell**.
    3. Välj **Skapa**.
    
        ![Lägg till en runbook-knapp](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Kopiera och klistra in följande skript för att skriptfönstret runbook. Spara och publicera en runbook med hjälp av den **spara** och **publicera** knappar i verktygsfältet. 

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
5. Testa runbooken genom att välja **starta** i verktygsfältet. 

    ![Runbook-knappen Starta](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. I den **starta Runbook** och utför följande steg: 

    1. För **RESURSGRUPPENS namn**, ange namnet på resursgruppen med data factory som har Azure-SSIS IR. 
    2. För **DATAFABRIKSNAMNET**, ange namnet på datafabriken som har Azure-SSIS IR. 
    3. För **AZURESSISNAME**, anger du namnet på Azure-SSIS IR. 
    4. För **ÅTGÄRDEN**, ange **starta**. 
    5. Välj **OK**.  

        ![Starta runbook-fönstret](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. I fönstret jobb väljer **utdata** panelen. I utdatafönstret för jobbet, vänta tills du ser meddelandet **### slutförd ###** efter att du ser **### startar ###**. Starta en Azure SSIS IR tar cirka 20 minuter. Stäng den **jobbet** fönstret och gå tillbaka till den **Runbook** fönster.

    ![Azure SSIS IR – igång](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  Upprepa föregående två steg, men genom att använda **stoppa** som värde för den **ÅTGÄRDEN**. Starta runbooken igen genom att välja den **starta** i verktygsfältet. Ange resursgruppens namn, datafabriksnamnet och Azure SSIS IR namn. För **ÅTGÄRDEN**, ange **stoppa**. 

    I utdatafönstret för jobbet, vänta tills du ser meddelandet **### slutförd ###** efter att du ser **### stoppar ###**. Stoppa en Azure SSIS IR tar inte upp från Azure-SSIS IR. Stäng den **jobbet** fönstret och gå tillbaka till den **Runbook** fönster.

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>Skapa scheman för runbook att starta/stoppa Azure SSIS IR
I det föregående avsnittet skapat du ett Azure Automation-runbook som kan starta eller stoppa en Azure-SSIS IR. I det här avsnittet skapar du två scheman för runbook. När du konfigurerar det första schemat, anger du START för parametern igen. På samma sätt när du konfigurerar andra schemat kan ange du stoppa för ÅTGÄRDEN. Detaljerade anvisningar för att skapa scheman i [skapa ett schema](../automation/automation-schedules.md#creating-a-schedule).

1. I den **Runbook** väljer **scheman**, och välj **+ Lägg till ett schema** i verktygsfältet. 

    ![Azure SSIS IR – igång](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. I den **schema Runbook** och utför följande steg: 

    1. Välj **länka ett schema till din runbook**. 
    2. Välj **skapa ett nytt schema**.
    3. I den **nytt schema** fönster, Skriv in **starta IR dagligen** för **namn**. 
    4. I den **avsnittet börjar**, för tiden, ange en tid ett par minuter efter aktuell tid. 
    5. För **upprepning**väljer **återkommande**. 
    6. I den **Upprepa varje** väljer **dag**. 
    7. Välj **Skapa**. 

        ![Schema för Azure SSIS IR-start](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. Växla till den **parametrar och körinställningar** fliken. Ange resursgruppens namn, datafabriksnamnet och Azure SSIS IR namn. För **ÅTGÄRDEN**, ange **starta**. Välj **OK**. Välj **OK** igen för att se schemat på den **scheman** för runbook. 

    ![Schema för börjar Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. Upprepa föregående två steg för att skapa ett schema med namnet **stoppa IR dagligen**. Den här tiden kan ange tid minst 30 minuter efter tidpunkten du angav för den **starta IR dagligen** schema. För **ÅTGÄRDEN**, ange **stoppa**. 
5. I den **Runbook** väljer **jobb** på den vänstra menyn. Du bör se jobb som skapats av scheman på den angivna gånger och deras status. Du kan se information om jobb, till exempel dess utdata som liknar vad du har sett när du testade runbooken. 

    ![Schema för börjar Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. När du är klar testning, inaktivera scheman genom att redigera dem och välja **nr** för **aktiverad**. Välj **scheman** i den vänstra menyn väljer du den **starta IR varje dag/stoppa IR dagligen**, och välj **nr** för **aktiverad**. 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>Skapa webhooks för att starta och stoppa Azure SSIS IR
Följ instruktionerna i [skapa en webhook](../automation/automation-webhooks.md#creating-a-webhook) att skapa två webhooks för runbook. Ange START som ÅTGÄRDEN för den första, och ange stoppa som ÅTGÄRDEN för den andra mallen. Spara URL: er för båda webhookarna någonstans (till exempel en textfil eller en OneNote-anteckningsbok). Du kan använda dessa URL: er när du konfigurerar webbaktiviteter i Data Factory-pipeline. Följande bild visas ett exempel på hur du skapar en webhook som startar Azure SSIS IR:

1. I den **Runbook** väljer **Webhooks** från den vänstra menyn och välj **+ Lägg till Webhook** i verktygsfältet. 

    ![Webhooks -> Lägg till Webhook](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. I den **Lägg till Webhook** väljer **Skapa ny webhook**, och göra följande: 

    1. För **namn**, ange **StartAzureSsisIR**. 
    2. Bekräfta att **aktiverad** är inställd på **Ja**. 
    3. Kopiera den **URL** och spara den någonstans. Det här steget är viktigt. Du ser inte URL: en senare. 
    4. Välj **OK**. 

        ![Nytt Webhook-fönster](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. Växla till den **parametrar och körinställningar** fliken. Ange resursgruppens namn och datafabriksnamnet Azure SSIS IR-namn. För **ÅTGÄRDEN**, ange **starta**. Klicka på **OK**. Klicka på **Skapa**. 

    ![Webhook - parametrar och körinställningar](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. Upprepa föregående tre steg för att skapa en annan webhook med namnet **StopAzureSsisIR**. Glöm inte att kopiera URL: en. När du anger parametrar och körinställningar, ange **stoppa** för **ÅTGÄRDEN**. 

Du bör ha två URL: er, en för den **StartAzureSsisIR** webhook och en för den **StopAzureSsisIR** webhook. Du kan skicka en HTTP POST-begäran till dessa URL: er för att starta/stoppa din Azure-SSIS IR. 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>Skapa och schemalägga en Data Factory-pipeline som börjar/slutar IR
Det här avsnittet visar hur du använder en webbaktivitet för att anropa webhooks som du skapade i föregående avsnitt.

Den pipeline du skapar består av tre aktiviteter. 

1. Först **Web** aktivitet anropar första webhooken om du vill starta Azure-SSIS IR. 
2. Den **köra SSIS-paket** aktivitet eller **lagringsprocedur** aktivitetskörningar SSIS-paket.
3. Andra **Web** aktivitet anropar webhooken om du vill stoppa Azure-SSIS IR. 

När du har skapat och testa pipeline kan du skapa en schemautlösare och associerar med pipelinen. Schemautlösare definierar ett schema för pipelinen. Anta att du skapar en utlösare som är schemalagda att köras varje dag kl. 23: 00. Utlösaren kör pipelinen klockan 23: 00 varje dag. Pipelinen startar Azure SSIS IR kör SSIS-paket och stoppar Azure-SSIS IR. 

### <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Logga in på [Azure-portalen](https://portal.azure.com/).    
2. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. På sidan **Ny datafabrik** anger du **MyAzureSsisDataFactory** som **namn**. 
      
     ![Sida för ny datafabrik](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel returneras ändrar du namnet på datafabriken (till exempel dittnamnMyAzureSsisDataFactory) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
       `Data factory name �MyAzureSsisDataFactory� is not available`
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
      Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
4. Välj **V2** för **versionen**.
5. Välj **plats** för datafabriken. Endast de platser som har stöd för att skapa datafabriker visas i listan.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
8. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
   ![Datafabrikens startsida](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Klicka på **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik.

### <a name="create-a-pipeline"></a>Skapa en pipeline

1. I den **börjar** väljer **skapa pipeline**. 

   ![Sidan Kom igång](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. I den **aktiviteter** verktygslådan Expandera **Allmänt**, dra och släpp den **Web** aktiviteten till pipelinedesignytan. I den **Allmänt** fliken den **egenskaper** fönstret Ändra namnet på aktiviteten **StartIR**.

   ![Första webbaktiviteten – fliken Allmänt](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. Växla till den **inställningar** fliken i den **egenskaper** fönstret och göra följande: 

    1. För **URL**, klistra in Webbadressen för webhooken som startar Azure-SSIS IR. 
    2. För **metoden**väljer **POST**. 
    3. För **brödtext**, ange `{"message":"hello world"}`. 
   
        ![Första webbaktiviteten - inställningsfliken](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)

4. Dra och släpp aktiviteten kör SSIS-paket eller lagringsprocedur-aktivitet från den **Allmänt** delen av den **aktiviteter** verktygslådan. Ange namnet på aktiviteten **RunSSISPackage**. 

5. Om du väljer aktiviteten kör SSIS-paket, följ instruktionerna i [kör ett SSIS-paket med SSIS-aktivitet i Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md) slutföra aktivitet.  Se till att du anger ett tillräckligt antal nya försök som förekommer ofta att vänta på tillgängligheten för Azure-SSIS IR, eftersom det tar upp till 30 minuter att starta. 

    ![Inställningar för nya försök](media/how-to-schedule-azure-ssis-integration-runtime/retry-settings.png)

6. Om du väljer aktiviteten lagringsprocedur, följ instruktionerna i [anropa ett SSIS-paket med hjälp av aktivitet för lagrad procedur i Azure Data Factory](how-to-invoke-ssis-package-stored-procedure-activity.md) slutföra aktivitet. Se till att du infogar ett Transact-SQL-skript som väntar på tillgängligheten för Azure-SSIS IR, eftersom det tar upp till 30 minuter att starta.
    ```sql
    DECLARE @return_value int, @exe_id bigint, @err_msg nvarchar(150)

    -- Wait until Azure-SSIS IR is started
    WHILE NOT EXISTS (SELECT * FROM [SSISDB].[catalog].[worker_agents] WHERE IsEnabled = 1 AND LastOnlineTime > DATEADD(MINUTE, -10, SYSDATETIMEOFFSET()))
    BEGIN
        WAITFOR DELAY '00:00:01';
    END

    EXEC @return_value = [SSISDB].[catalog].[create_execution] @folder_name=N'YourFolder',
        @project_name=N'YourProject', @package_name=N'YourPackage',
        @use32bitruntime=0, @runincluster=1, @useanyworker=1,
        @execution_id=@exe_id OUTPUT 

    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1

    EXEC [SSISDB].[catalog].[start_execution] @execution_id = @exe_id, @retry_count = 0

    -- Raise an error for unsuccessful package execution, check package execution status = created (1)/running (2)/canceled (3)/
    -- failed (4)/pending (5)/ended unexpectedly (6)/succeeded (7)/stopping (8)/completed (9) 
    IF (SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id = @exe_id) <> 7 
    BEGIN
        SET @err_msg=N'Your package execution did not succeed for execution ID: '+ CAST(@execution_id as nvarchar(20))
        RAISERROR(@err_msg, 15, 1)
    END
    ```

7. Anslut den **Web** aktivitet för att den **köra SSIS-paket** eller **lagringsprocedur** aktivitet. 

    ![Anslut webb- och lagrade proceduraktiviteter](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-sproc.png)

8. Dra och släpp en annan **Web** aktiviteten till höger om den **köra SSIS-paket** aktivitet eller **lagringsprocedur** aktivitet. Ange namnet på aktiviteten **StopIR**. 
9. Växla till den **inställningar** fliken i den **egenskaper** fönstret och göra följande: 

    1. För **URL**, klistra in Webbadressen för webhooken som stoppar Azure-SSIS IR. 
    2. För **metoden**väljer **POST**. 
    3. För **brödtext**, ange `{"message":"hello world"}`.  
10. Anslut den **köra SSIS-paket** aktivitet eller **lagringsprocedur** aktiviteten till senast **Web** aktivitet.

    ![Fullständig pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
11. Verifiera pipelineinställningarna genom att klicka på **verifiera** i verktygsfältet. Stäng den **verifieringsrapporten för Pipeline** genom att klicka på **>>** knappen. 

    ![Verifiera pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>Testkör pipelinen

1. Välj **testkörning** i verktygsfältet för pipelinen. Du ser utdata i den **utdata** fönster längst ned i fönstret. 

    ![Kör test](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. I den **Runbook** sidan för ditt Azure Automation-konto, du kan kontrollera att jobb som körts för att starta och stoppa Azure-SSIS IR. 

    ![Runbook-jobb](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. Starta SQL Server Management Studio. I den **Anslut till Server** fönstret göra följande: 

    1. För **servernamn**, ange  **&lt;Azure SQL database&gt;. database.windows.net**.
    2. Välj **alternativ >>**.
    3. För **Anslut till databas**väljer **SSISDB**.
    4. Välj **Anslut**. 
    5. Expandera **Integration Services kataloger** -> **SSISDB** -> din mapp -> **projekt** -> din SSIS-projekt -> **paket** . 
    6. Högerklicka på ditt SSIS-paket och välj **rapporter** -> **standardrapporter** -> **alla körningar**. 
    7. Kontrollera att SSIS-paket har körts. 

        ![Kontrollera körning av SSIS-paket](./media/how-to-schedule-azure-ssis-integration-runtime/verfiy-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>Schemalägga en hel pipeline 
Nu när pipelinen fungerar som förväntat, kan du skapa en utlösare för att köra denna pipeline i en angiven takt. Mer information om hur du kopplar en schemautlösare med en pipeline finns i [Utlös pipelinen enligt ett schema](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. I verktygsfältet för pipelinen väljer **utlösaren**, och välj **ny/redigera**. 

    ![Utlösare -> Nytt/Redigera](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. I den **Lägg till utlösare** väljer **+ ny**.

    ![Lägg till utlösare – ny](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. I den **ny utlösare**, göra följande: 

    1. För **namn**, ange ett namn för utlösaren. I följande exempel **köras varje dag** är namnet på utlösaren. 
    2. För **typ**väljer **schema**. 
    3. För **startdatum**, Välj ett startdatum och starttid. 
    4. För **upprepning**, ange intervall för utlösaren. I följande exempel dagliga gång. 
    5. För **slutet**, du kan ange datum och tid genom att välja den **på datumet** alternativet. 
    6. Välj **aktiverat**. Utlösaren aktiveras omedelbart när du har publicerat lösningen till Data Factory. 
    7. Välj **Nästa**.

        ![Utlösare -> Nytt/Redigera](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. I den **parametrar för Objektutlösare kör** , läser du varningen och välj **Slutför**. 
5. Publicera lösningen till Data Factory genom att välja **publicera alla** i den vänstra rutan. 

    ![Publicera alla](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-the-pipeline-and-trigger-in-the-azure-portal"></a>Övervaka pipeline och utlösare i Azure portal

1. Övervaka utlösarkörningar och pipelinekörningar genom att använda den **övervakaren** fliken till vänster. Detaljerade anvisningar finns i [övervaka pipelinen](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

    ![Pipelinekörningar](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
2. Om du vill visa de aktivitetskörningar som är associerade med en pipeline som körs, väljer du den första länken (**visa Aktivitetskörningar**) i den **åtgärder** kolumn. Du ser de tre aktivitetskörningar som är associerade med varje aktivitet i pipelinen (Web först aktiviteten lagringsprocedur-aktivitet och den andra webbaktiviteten). Om du vill växla tillbaka om du vill visa pipelinekörningarna väljer **Pipelines** länken längst upp.

    ![Aktivitetskörningar](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
3. Du kan också visa utlösarkörningar genom att välja **utlösa körningar** från listrutan bredvid den **Pipelinekörningar** högst upp. 

    ![Utlösarkörning](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-the-pipeline-and-trigger-with-powershell"></a>Övervaka pipeline och utlösare med PowerShell

Du kan använda skript som i följande exempel för att övervaka pipelinen och utlösaren.

1. Hämta status för en pipeline-körning.

  ```powershell
  Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
  ```

2. Få information om utlösaren.

  ```powershell
  Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
  ```

3. Hämta status för en utlösarkörning.

  ```powershell
  Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
  ```

## <a name="next-steps"></a>Nästa steg
Se i följande blogginlägg:
-   [Modernisera och utöka dina ETL/ELT-arbetsflöden med SSIS-aktiviteter i ADF pipelines](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)

Se följande artiklar från SSIS-dokumentationen: 

- [Distribuera, köra och övervaka ett SSIS-paket på Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Ansluta till SSIS-katalogen på Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schemalägga paketetkörning på Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Ansluta till lokala data med Windows-autentisering](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
