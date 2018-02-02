---
title: "Så här schemalägger du Azure SSIS-integrering runtime | Microsoft Docs"
description: "Den här artikeln beskriver hur du schemalägger starta och stoppa en körning för Azure SSIS-integrering med Azure Automation och Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: article
ms.date: 01/25/2018
ms.author: spelluru
ms.openlocfilehash: 60a4afdb8a78cffdc7eb1ee82c7daf3b06e5fe15
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-schedule-starting-and-stopping-of-an-azure-ssis-integration-runtime"></a>Så här schemalägger du starta och stoppa en integration Azure SSIS-körning 
Kör en Azure SSIS (SQL Server Integration Services) integration körning har (IR) en avgift som associeras med den. Du vill därför IR endast körs när du behöver köra SSIS-paket i Azure och stoppa den när du inte behöver den. Du kan använda Data Factory Användargränssnittet eller PowerShell för Azure att [manuellt starta eller stoppa ett Azure SSIS-IR](manage-azure-ssis-integration-runtime.md)). Den här artikeln beskriver hur du schemalägger starta och stoppa en Azure SSIS-integrering körning (IR) med hjälp av Azure Automation och Azure Data Factory. Här följer de övergripande stegen som beskrivs i den här artikeln:

1. **Skapa och testa en Azure Automation-runbook.** I det här steget skapar du en PowerShell-runbook med skriptet som startar eller stoppar ett Azure SSIS IR. Sedan testa runbook i både START och stopp scenarier och bekräfta att IR startar eller stoppar. 
2. **Skapa två scheman för runbook.** För det första schemat konfigurerar du runbook med START som igen. Konfigurera runbook med stoppa som åtgärden för andra schemat. För båda scheman anger du det intervall som denna runbook körs. Du kanske vill schemalägga den första som ska köras vid 8: 00 varje dag och den andra som ska köras vid varje dag kl. När den första runbooken körs startar den Azure SSIS IR. När andra runbook körs, stoppas Azure SSIS IR. 
3. **Skapa två webhooks för runbook**, en för att starta åtgärden och den andra för STOP-åtgärd. Du kan använda URL: er för dessa webhooks när du konfigurerar webbprogram aktiviteter i en Data Factory-pipelinen. 
4. **Skapa Data Factory-pipelinen**. Du skapar pipeline består av fyra aktiviteter. Först **Web** aktivitet anropar första webhooken för att starta Azure SSIS IR. Den **vänta** aktiviteten ska vänta i 30 minuter (1 800 sekunder) för SSIS-IR Azure att starta. Den **lagrade proceduren** aktiviteten körs ett SQL-skript som körs SSIS-paket. Andra **Web** aktiviteten slutar Azure SSIS IR. Mer information om hur du anropar ett SSIS-paket från Data Factory-pipelinen med hjälp av aktiviteten lagrad procedur finns [anropa ett SSIS-paket](how-to-invoke-ssis-package-stored-procedure-activity.md). Sedan kan skapa du en utlösare för schemat om du vill schemalägga pipelinen ska köras med det intervall som du anger.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [anropa SSIS-paket med hjälp av aktiviteten lagrad procedur i version 1](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

 
## <a name="prerequisites"></a>Förutsättningar
Om du inte har etablerat en Azure SSIS-integrering körning redan, etablera du genom att följa anvisningarna i den [kursen](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-test-an-azure-automation-runbook"></a>Skapa och testa en Azure Automation-runbook
I det här avsnittet kan du utföra följande steg: 

1. Skapa ett Azure Automation-konto.
2. Skapa en PowerShell-runbook i Azure Automation-konto. PowerShell-skript som är associerade med runbook startar eller stoppar ett Azure SSIS-IR baserat på det kommando som du anger för parameter för ÅTGÄRDEN. 
3. Testa runbook i båda börja och sluta med scenarier för att bekräfta att det fungerar. 

### <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto
Om du inte har ett Azure Automation-konto kan du skapa en genom att följa anvisningarna i det här steget. Detaljerade anvisningar finns i [skapa ett Azure Automation-konto](../automation/automation-quickstart-create-account.md). Som en del av det här steget kan du skapa en **Azure kör som** konto (en tjänstens huvudnamn i Azure Active Directory) och lägger till den i den **deltagare** rollen för din Azure-prenumeration. Kontrollera att det är samma som den prenumeration som innehåller data factory med Azure SSIS IR. Azure Automation använder kontot för att autentisera till Azure Resource Manager och fungerar på dina resurser. 

1. Logga in på [Azure-portalen](https://portal.azure.com/).    
2. Välj **ny** på den vänstra menyn väljer **övervakning + Management**, och välj **Automation**. 

    ![-> Ny övervakning + Management Automation ->](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. I den **lägga till Automation-konto** fönster, gör du följande: 

    1. Ange en **namn** för automation-kontot. 
    2. Välj den **prenumeration** som har data factory med Azure SSIS IR. 
    3. För **resursgruppen**väljer **Skapa nytt** att skapa en ny resursgrupp eller välj **använda befintliga** att välja en befintlig resursgrupp. 
    4. Välj en **plats** för automation-kontot. 
    5. Bekräfta att **skapa kör som-konto** är inställd på **Ja**. Ett huvudnamn för tjänsten har skapats i Azure Active Directory. Den har lagts till i **deltagare** rollen för din Azure-prenumeration
    6. Välj **fäst på instrumentpanelen** så att du ser på instrumentpanelen i portalen. 
    7. Välj **Skapa**. 

        ![-> Ny övervakning + Management Automation ->](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. Du ser den **Distributionsstatus** på instrumentpanelen och meddelanden. 
    
    ![Distribuera automation](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. Du kan se startsidan för automation-konto när den har skapats. 

    ![Startsidan för Automation](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>Importera Data Factory-moduler

1. Välj **moduler** i den **delade resurser** på den vänstra menyn, och kontrollera om du har **AzureRM.Profile** och **AzureRM.DataFactoryV2** i listan över moduler. Om det inte är det, väljer **Bläddra galleriet** i verktygsfältet.

    ![Startsidan för Automation](./media/how-to-schedule-azure-ssis-integration-runtime/automation-modules.png)
2. I den **Bläddra galleriet** fönster, Skriv **AzureRM.Profile** i sökfönstret och tryck på **RETUR**. Välj **AzureRM.Profile** i listan. Klicka på **importera** i verktygsfältet. 

    ![Välj AzureRM.Profile](./media/how-to-schedule-azure-ssis-integration-runtime/select-azurerm-profile.png)
1. I den **importera** väljer **jag samtycker till att uppdatera alla Azure-moduler** alternativ och klickar på **OK**.  

    ![Importera AzureRM.Profile](./media/how-to-schedule-azure-ssis-integration-runtime/import-azurerm-profile.png)
4. Stäng windows för att gå tillbaka till den **moduler** fönster. Du bör se status för import i listan. Om du vill uppdatera listan väljer du **Uppdatera**. Vänta tills du ser den **STATUS** som **tillgänglig**.

    ![Importera status](./media/how-to-schedule-azure-ssis-integration-runtime/module-list-with-azurerm-profile.png)
1. Upprepa stegen för att importera den **AzureRM.DataFactoryV2** modul. Bekräfta att status för den här modulen har angetts till **tillgänglig** innan du fortsätter. 

    ![Importera slutlig status](./media/how-to-schedule-azure-ssis-integration-runtime/module-list-with-azurerm-datafactoryv2.png)

### <a name="create-a-powershell-runbook"></a>Skapa en PowerShell-runbook
Följande procedur innehåller steg för att skapa en PowerShell-runbook. Skriptet som är associerat med denna runbook antingen startar/stoppar ett Azure SSIS-IR baserat på det kommando som du anger för den **ÅTGÄRDEN** parameter. Det här avsnittet ger inte några allt för att skapa en runbook. Mer information finns i [skapar en runbook](../automation/automation-quickstart-create-runbook.md) artikel.

1. Växla till den **Runbooks** fliken och markera **+ Lägg till en runbook** i verktygsfältet. 

    ![Lägga till en runbook-knapp](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. Välj **skapa en ny runbook**, och utför följande steg: 

    1. För **namn**, typen **StartStopAzureSsisRuntime**.
    2. För **runbooktyp**väljer **PowerShell**.
    3. Välj **Skapa**.
    
        ![Lägga till en runbook-knapp](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Kopiera och klistra in följande skript för att skriptfönstret runbook. Spara och publicera en runbook med hjälp av den **spara** och **publicera** i verktygsfältet. 

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
        Add-AzureRmAccount `
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
5. Testa runbook genom att välja **starta** i verktygsfältet. 

    ![Start-knappen i runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. I den **starta Runbook** och utför följande steg: 

    1. För **RESURSGRUPPENS namn**, ange namnet på resursgruppen med data factory med Azure SSIS IR. 
    2. För **DATAFABRIKSNAMNET**, ange namnet på data factory med Azure SSIS IR. 
    3. För **AZURESSISNAME**, ange namnet på Azure SSIS IR. 
    4. För **ÅTGÄRDEN**, ange **starta**. 
    5. Välj **OK**.  

        ![Starta runbook-fönster](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. I fönstret Välj **utdata** panelen. Vänta tills meddelandet i utdatafönstret för jobbet **### slutförd ###** efter att du ser **### Start ###**. Starta en Azure SSIS-IR tar cirka 20 minuter. Stäng den **jobbet** fönstret och gå tillbaka till den **Runbook** fönster.

    ![Azure SSIS-IR - igång](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  Upprepa föregående två steg, men med hjälp av **stoppa** som värde för den **ÅTGÄRDEN**. Starta runbook igen genom att välja den **starta** i verktygsfältet. Ange resursgruppens namn, datafabriksnamnet och Azure SSIS IR-namn. För **ÅTGÄRDEN**, ange **stoppa**. 

    Vänta tills meddelandet i utdatafönstret för jobbet **### slutförd ###** efter att du ser **### stoppar ###**. Stoppa ett Azure SSIS-IR tar inte så länge som startar Azure SSIS IR. Stäng den **jobbet** fönstret och gå tillbaka till den **Runbook** fönster.

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>Skapa scheman för runbook för att starta/stoppa SSIS-IR Azure
Du har skapat ett Azure Automation-runbook som kan starta eller stoppa ett Azure SSIS IR. i föregående avsnitt I det här avsnittet skapar du två scheman för runbook. När du konfigurerar det första schemat måste ange du START för parametern ÅTGÄRDEN. På samma sätt när du konfigurerar andra schemat måste ange du stopp för ÅTGÄRDEN. Detaljerade anvisningar för att skapa scheman finns [skapa ett schema](../automation/automation-schedules.md#creating-a-schedule).

1. I den **Runbook** väljer **scheman**, och välj **+ Lägg till ett schema** i verktygsfältet. 

    ![Azure SSIS-IR - igång](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. I den **schema Runbook** och utför följande steg: 

    1. Välj **länka ett schema till din runbook**. 
    2. Välj **skapa ett nytt schema**.
    3. I den **nytt schema** fönster, Skriv **starta IR dagligen** för **namn**. 
    4. I den **avsnittet börjar**, för tid, ange en tidpunkt för några minuter efter den aktuella tiden. 
    5. För **återkommande**väljer **återkommande**. 
    6. I den **utför varje** väljer **dag**. 
    7. Välj **Skapa**. 

        ![Schemat för Azure SSIS IR start](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. Växla till den **parametrar och körinställningar** fliken. Ange resursgruppens namn, datafabriksnamnet och Azure SSIS IR-namn. För **ÅTGÄRDEN**, ange **starta**. Välj **OK**. Välj **OK** igen för att se schemat på den **scheman** sida av runbook. 

    ![Schema för när du startar SSIS-IR Azure](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. Upprepa föregående två steg för att skapa ett schema med namnet **stoppa IR dagligen**. Den här gången anger tid minst 30 minuter efter tidpunkten du angett för den **starta IR dagligen** schema. För **ÅTGÄRDEN**, ange **stoppa**. 
5. I den **Runbook** väljer **jobb** på den vänstra menyn. Du bör se jobben som skapats av scheman på den angivna tidpunkter och deras status. Du kan se information om jobbet, till exempel dess utdata som liknar har du sett när du testar runbook. 

    ![Schema för när du startar SSIS-IR Azure](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. När du är klar testning, inaktivera scheman genom att redigera dem och välja **nr** för **aktiverad**. Välj **scheman** i den vänstra menyn väljer du den **starta IR varje dag eller stoppa IR dagligen**, och välj **nr** för **aktiverad**. 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>Skapa webhooks för att starta och stoppa SSIS-IR Azure
Följ instruktionerna i [skapa en webhook](../automation/automation-webhooks.md#creating-a-webhook) att skapa två webhooks för runbook. Ange START som ÅTGÄRDEN för den första, och ange stoppa som ÅTGÄRDEN för det andra. Spara URL: er för både webhooks någonstans (till exempel en textfil eller en bärbar dator OneNote). Du kan använda dessa URL: er när du konfigurerar webbprogram aktiviteter i Data Factory-pipelinen. Följande bild visas ett exempel på hur du skapar en webhook som startar SSIS-IR Azure:

1. I den **Runbook** väljer **Webhooks** från den vänstra menyn och välj **+ Lägg till Webhook** i verktygsfältet. 

    ![Webhooks -> Add Webhook](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. I den **lägga till Webhook** väljer **skapa nya webhook**, och göra följande: 

    1. För **namn**, ange **StartAzureSsisIR**. 
    2. Bekräfta att **aktiverad** är inställd på **Ja**. 
    3. Kopiera den **URL** och spara den någonstans. Det här steget är viktigt. Visas inte Webbadressen senare. 
    4. Välj **OK**. 

        ![Nytt Webhook-fönster](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. Växla till den **parametrar och körinställningar** fliken. Ange resursgruppens namn, data factory-namnet och Azure SSIS IR-namn. För **ÅTGÄRDEN**, ange **starta**. Klicka på **OK**. Klicka på **Skapa**. 

    ![Webhook - parametrar och körinställningar](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. Upprepa föregående tre steg för att skapa en annan webhook med namnet **StopAzureSsisIR**. Glöm inte att kopiera URL-Adressen. När du anger parametrar och körinställningar, ange **stoppa** för **ÅTGÄRDEN**. 

Du bör ha två webbadresser, en för den **StartAzureSsisIR** webhook och en för den **StopAzureSsisIR** webhooken. Du kan skicka en HTTP POST-begäran till dessa URL: er för att starta/stoppa din Azure SSIS IR. 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>Skapa och schemalägga en Data Factory-pipelinen som startar/stoppar IR
Det här avsnittet visar hur du använder en webbaktivitet för att anropa webhooks som du skapade i föregående avsnitt.

Du skapar pipeline består av fyra aktiviteter. 

1. Först **Web** aktivitet anropar första webhooken för att starta Azure SSIS IR. 
2. Den **vänta** aktiviteten ska vänta i 30 minuter (1 800 sekunder) för SSIS-IR Azure att starta. 
3. Den **lagrade proceduren** aktiviteten körs ett SQL-skript som körs SSIS-paket. Andra **Web** aktiviteten slutar Azure SSIS IR. Mer information om hur du anropar ett SSIS-paket från Data Factory-pipelinen med hjälp av aktiviteten lagrad procedur finns [anropa ett SSIS-paket](how-to-invoke-ssis-package-stored-procedure-activity.md). 
4. Andra **Web** aktivitet anropar webhooken om du vill stoppa Azure SSIS IR. 

När du skapar och testar pipeline, skapa en schema-utlösare och associera med pipeline. Utlösaren schema definierar ett schema för pipeline. Anta att du skapar en utlösare som är schemalagda att köras varje dag kl 23. Utlösaren körs pipeline kl 23 varje dag. Pipelinen startar Azure SSIS-IR kör SSIS-paket och stoppar Azure SSIS IR. 

### <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Logga in på [Azure-portalen](https://portal.azure.com/).    
2. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. På sidan **Ny datafabrik** anger du **MyAzureSsisDataFactory** som **namn**. 
      
     ![Sida för ny datafabrik](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel returneras ändrar du namnet på datafabriken (till exempel dittnamnMyAzureSsisDataFactory) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
      Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
4. Välj **V2 (förhandsgranskning)** för **versionen**.
5. Välj **plats** för datafabriken. Endast de platser som har stöd för att skapa datafabriker visas i listan.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
8. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
   ![Datafabrikens startsida](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Klicka på **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik.

### <a name="create-a-pipeline"></a>Skapa en pipeline

1. I den **Kom igång** väljer **skapa pipeline**. 

   ![Sidan Kom igång](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. I den **aktiviteter** verktygslådan Expandera **allmänna**, musen den **Web** aktiviteten till designytan pipeline. I den **allmänna** för den **egenskaper** och ändra namnet på aktiviteten för **StartIR**.

   ![Första webbaktivitet – fliken Allmänt](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. Växla till den **inställningar** fliken i den **egenskaper** fönstret och göra följande: 

    1. För **URL**, klistra in Webbadressen för webhooken som startar Azure SSIS IR. 
    2. För **metoden**väljer **efter**. 
    3. För **brödtext**, ange `{"message":"hello world"}`. 
   
        ![Första webbaktivitet – fliken Inställningar](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)
4. I den **aktiviteter** verktygslådan Expandera **Iteration & villkorlig sats**, musen och den **vänta** aktiviteten till designytan pipeline. I den **allmänna** fliken, ändrar namnet på aktiviteten för **WaitFor30Minutes**. 
5. Växla till den **inställningar** fliken i den **egenskaper** fönster. För **Väntetid i sekunder**, ange **1800**. 
6. Anslut den **Web** aktivitet och **vänta** aktivitet. Börja dra i rutan grön kvadratisk kopplade till aktiviteten Web för aktiviteten vänta för att ansluta dem. 

    ![Ansluta till webben och vänta](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-wait.png)
5. Musen aktiviteten lagrad procedur från den **allmänna** avsnitt i den **aktiviteter** verktygslådan. Ange namnet på aktiviteten för **RunSSISPackage**. 
6. Växla till den **konto för SQL** fliken i den **egenskaper** fönster. 
7. För **länkade tjänsten**, klickar du på **+ ny**.
8. I den **ny länkade tjänst** och utföra följande åtgärder: 

    1. Välj **Azure SQL Database** för **typen**.
    2. Välj din Azure SQL-server som är värd för den **SSISDB** databasen för den **servernamn** fältet. Azure SSIS IR etableringsprocessen skapar en SSIS-katalog (SSIDB-databasen) i Azure SQL-server som du anger.
    3. Välj **SSISDB** för **databasnamnet**.
    4. För **användarnamn**, ange namnet på användaren som har åtkomst till databasen.
    5. För **lösenord**, ange lösenordet för användaren. 
    6. Testa anslutningen till databasen genom att klicka på **Anslutningstestet** knappen.
    7. Spara den länkade tjänsten genom att klicka på den **spara** knappen.
1. I den **egenskaper** och växla till den **lagrade proceduren** fliken från den **konto för SQL** fliken och gör följande: 

    1. För **lagrat procedurnamn**väljer **redigera** , och ange **sp_executesql**. 
    2. Välj **+ ny** i den **lagrade procedurparametrar** avsnitt. 
    3. För **namn** för parametern, ange **infogandekolumner**. 
    4. För **typen** för parametern, ange **sträng**. 
    5. För **värdet** för parametern, ange följande SQL-fråga:

        I SQL-frågan anger rätt värden för den **mappnamn**, **projekt_namn**, och **paketnamn** parametrar. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END   
        ```
10. Anslut den **vänta** aktivitet för att den **lagrade proceduren** aktivitet. 

    ![Ansluta vänta och lagrade proceduren aktiviteter](./media/how-to-schedule-azure-ssis-integration-runtime/connect-wait-sproc.png)
11. Dra släpp den **Web** aktiviteten till höger om den **lagrade proceduren** aktivitet. Ange namnet på aktiviteten för **StopIR**. 
12. Växla till den **inställningar** fliken i den **egenskaper** fönstret och göra följande: 

    1. För **URL**, klistra in Webbadressen för webhooken som slutar Azure SSIS IR. 
    2. För **metoden**väljer **efter**. 
    3. För **brödtext**, ange `{"message":"hello world"}`.  
4. Anslut den **lagrade proceduren** aktiviteten till senast **Web** aktivitet.

    ![Fullständig pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
5. Verifiera inställningarna för pipeline genom att klicka på **verifiera** i verktygsfältet. Stäng den **Pipeline verifieringsrapport** genom att klicka på  **>>**  knappen. 

    ![Verifiera pipeline](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>Testkör pipelinen

1. Välj **testkörning** i verktygsfältet för pipeline. Du ser resultatet i den **utdata** fönstret längst ned i fönstret. 

    ![Kör test](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. I den **Runbook** sidan för Azure Automation-konto kan du kontrollera att jobb har utförts för att starta och stoppa Azure SSIS IR. 

    ![Runbook-jobb](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. Starta SQL Server Management Studio. I den **Anslut till Server** och göra följande: 

    1. För **servernamn**, ange  **&lt;Azure SQL database&gt;. database.windows.net**.
    2. Välj **alternativ >>**.
    3. För **Anslut till databas**väljer **SSISDB**.
    4. Välj **Anslut**. 
    5. Expandera **Integration Services kataloger** -> **SSISDB** -> mappen -> **projekt** -> din SSIS-projekt -> **paket** . 
    6. Högerklicka på SSIS-paket och välj **rapporter** -> **standardrapporter** -> **alla körningar**. 
    7. Kontrollera att SSIS-paket har körts. 

        ![Kontrollera SSIS-paket kör](./media/how-to-schedule-azure-ssis-integration-runtime/verfiy-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>Schemalägga pipeline 
Nu när pipeline fungerar som väntat, kan du skapa en utlösare för att köra den här pipelinen vid en angiven takt. Mer information om hur du kopplar en schema-utlösare med en rörledning finns [utlösa pipeline enligt ett schema](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. I verktygsfältet för pipeline, Välj **utlösaren**, och välj **ny/redigera**. 

    ![Utlösaren -> Ny/redigera](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. I den **lägger till utlösare** väljer **+ ny**.

    ![Lägga till utlösare - nya](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. I den **ny utlösare**, göra följande: 

    1. För **namn**, ange ett namn för utlösaren. I följande exempel **köras dagligen** är namnet på utlösaren. 
    2. För **typen**väljer **schema**. 
    3. För **startdatum**, väljer du datum och tid. 
    4. För **återkommande**, ange intervall för utlösaren. I följande exempel dagliga när. 
    5. För **End**, du kan ange datum och tid genom att välja den **på datum** alternativet. 
    6. Välj **aktiverat**. Utlösaren aktiveras omedelbart när du har publicerat lösningen till Data Factory. 
    7. Välj **Nästa**.

        ![Utlösaren -> Ny/redigera](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. I den **Trigger kör parametrar** , granska varningen och väljer **Slutför**. 
5. Publicera Data Factory lösningen genom att välja **publicera alla** i den vänstra rutan. 

    ![Publicera alla](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)
6. Om du vill övervaka utlösaren körs och körs i pipeline, Använd den **övervakaren** fliken till vänster. Detaljerade anvisningar finns i [övervaka pipeline](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

    ![Pipelinekörningar](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
7. Välj första länken om du vill visa aktivitetskörningar som är associerade med en rörledning kör (**visa aktiviteten körs**) i den **åtgärder** kolumn. Du ser de fyra aktivitetskörningar som är associerade med varje aktivitet i pipelinen (Web först aktivitet, väntaktiviteter, lagrad procedur, och den andra Web aktiviteten). Om du vill växla tillbaka om du vill visa pipeline-körs, Välj **Pipelines** längst upp.

    ![Aktivitetskörningar](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
8. Du kan också visa utlösaren körs genom att välja **utlösa körs** från den nedrullningsbara listan bredvid den **Pipeline körs** längst upp. 

    ![Utlösarkörning](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

## <a name="next-steps"></a>Nästa steg
Se följande artiklar från SSIS-dokumentationen: 

- [Distribuera, köra och övervaka ett SSIS-paket på Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Ansluta till SSIS-katalogen på Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schemalägga paketetkörning på Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Ansluta till lokala data med Windows-autentisering](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)