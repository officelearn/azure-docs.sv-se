<properties
   pageTitle="Visual Studio-projekt för Azure-resursgrupper | Microsoft Azure"
   description="Använd Visual Studio för att skapa ett Azure-resursgruppsprojekt och distribuera resurserna till Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/17/2016"
   ms.author="tomfitz" />

# Skapa och distribuera Azure-resursgrupper via Visual Studio

Med Visual Studio och [Azure SDK](https://azure.microsoft.com/downloads/) kan du skapa ett projekt som distribuerar din infrastruktur och kod till Azure. Du kan till exempel definiera webbvärden, webbplatsen och databasen för din app och distribuera den infrastrukturen tillsammans med koden. Eller så kan du definiera en virtuell dator, ett virtuellt nätverk och ett lagringskonto och distribuera den infrastrukturen tillsammans med ett skript som körs på den virtuella datorn. Med ett projekt för distribution av en **Azure-resursgrupp** kan du distribuera alla nödvändiga resurser i en enda, repeterbara åtgärd. Mer information om hur du distribuerar och hanterar dina resurser finns i [Översikt över Azure Resource Manager](resource-group-overview.md).

Azure-resursgruppsprojekt innehåller Azure Resource Manager JSON-mallar, som definierar de resurser som distribueras till Azure. Mer information om elementen i Resource Manager-mallen finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md). I Visual Studio kan du redigera dessa mallar och använda verktyg som gör det lättare att arbeta med mallar.

I det här avsnittet ska du distribuera en webbapp och SQL Database. Observera dock att stegen är i princip samma för alla typer av resurser. Det är lika lätt att distribuera en virtuell dator och dess relaterade resurser. Visual Studio har många olika startmallar som du kan använda för att distribuera vanliga scenarier.

Den här artikeln skrevs med Visual Studio 2015 Update 2 och Microsoft Azure SDK för .NET 2.9. Om du använder Visual Studio 2013 med Azure SDK 2.9 blir din upplevelse i stort sett densamma. Du kan använda versioner av Azure SDK från 2.6 eller senare, men din upplevelse kan skilja sig en aning mot det du ser i den här artikeln. Vi rekommenderar starkt att du installerar den senaste versionen av [Azure SDK](https://azure.microsoft.com/downloads/) innan du börjar med stegen. 

## Skapa ett projekt för en Azure-resursgrupp

I den här proceduren ska du skapa ett projekt för en Azure-resursgrupp med en mall av typen **Webbapp + SQL**.

1. I Visual Studio väljer du **Arkiv**, **Nytt projekt** och väljer sedan **C#** eller **Visual Basic**. Välj **Moln** och sedan projektet **Azure-resursgrupp**.

    ![Projekt för molndistribution](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Välj den mall som du vill distribuera till Azure Resource Manager. Observera att det finns många olika alternativ beroende på vilken typ av projekt som du vill distribuera. I det här avsnittet väljer du mallen **Webbapp + SQL**.

    ![Välja en mall](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    Mallen som du väljer är bara en startpunkt. Du kan lägga till och ta bort resurser som passar ditt scenario.

    >[AZURE.NOTE] Listan över tillgängliga mallar hämtas online och kan ändras.

    Visual Studio skapar ett distributionsprojekt för resursgrupper för webbappen och SQL-databasen.

1. Expandera noderna i distributionsprojektet för att se vad som skapats.

    ![visa noder](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Eftersom vi valde mallen Webbapp + SQL för det här exemplet visas följande filer. 

  	|Filnamn|Beskrivning|
  	|---|---|
  	|Deploy-AzureResourceGroup.ps1|Ett PowerShell-skript som anropar PowerShell-kommandon för distribution till Azure Resource Manager.<br />**Obs!** Detta PowerShell-skript används av Visual Studio för att distribuera mallen. De ändringar du gör i det här skriptet påverkar även distributionen i Visual Studio, så var försiktig.|
  	|WebSiteSQLDatabase.json|Resource Manager-mallen som definierar infrastrukturen som du vill distribuera till Azure, och de parametrar som du kan ange under distributionen. Den definierar även beroenden mellan resurserna så att de distribueras i rätt ordning.|
  	|WebSiteSQLDatabase.parameters.json|En fil med parametrar som innehåller värden som krävs av mallen. Det här är de värden som du anger för att anpassa varje distribution.|

    Alla distributionsprojekt för resursgrupper innehåller dessa grundläggande filer. Andra projekt kan innehålla ytterligare filer som ger stöd för andra funktioner.

## Anpassa Resource Manager-mallen

Du kan anpassa ett distributionsprojekt genom att ändra JSON-mallarna som beskriver de resurser som du vill distribuera. JSON står för JavaScript Object Notation och är ett format för serialiserade data som är lätt att arbeta med. JSON-filerna använder ett schema som refereras längst upp i varje fil. Du kan hämta schemat och analysera det om du vill förstå det bättre. Schemat definierar vilka element som är tillåtna, typer och format för fält, de möjliga värdena för uppräknade värden och så vidare. Mer information om elementen i Resource Manager-mallen finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).

Du kan arbeta med din mall genom att öppna **WebSiteSQLDatabase.json**.

Visual Studio-redigeraren innehåller verktyg som hjälper dig att redigera Resource Manager-mallen. Fönstret **JSON-disposition** gör det enkelt att se elementen som definierats i mallen.

![visa JSON-disposition](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

När du väljer ett element i dispositionen kommer du till den delen av mallen och motsvarande JSON markeras.

![navigera i JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Du kan lägga till en ny resurs i mallen genom att antingen välja knappen **Lägg till resurs** längst upp i fönstret JSON-disposition eller genom att högerklicka på **resurser** och välja **Lägg till ny resurs**.

![lägga till en resurs](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

I den här självstudiekursen väljer du **Lagringskonto** och ger det ett namn. Namnet på ett lagringskonto får endast innehålla siffror och gemener och får inte innehålla mer än 24 tecken. Projektet lägger till en unik 13 tecken lång sträng till det namn som du anger, så se till att namnet inte innehåller mer än 11 tecken.

![lägga till ett lagringskonto](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Observera att inte bara resursen lades till, utan även en parameter för typen av lagringskonto och en variabel för namnet på lagringskontot.

![visa disposition](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

Parametern **storageType** är fördefinierad med tillåtna typer och en standardtyp. Du kan lämna dessa värden eller redigera dem för ditt scenario. Om du inte vill tillåta att vem som helst distribuerar ett **Premium_LRS**-lagringskonto med den här mallen tar du bara bort det från de tillåtna typerna, som du ser nedan. 

    "storageType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS"
      ]
    }

Visual Studio tillhandahåller även IntelliSense som gör det lättare för dig att förstå vilka egenskaper som är tillgängliga när du redigerar mallen. Om du till exempel vill redigera egenskaperna för App Service-plan navigerar du till resursen **HostingPlan** och lägger till ett nytt värde för **properties**. Observera att IntelliSense visar de tillgängliga värdena och en beskrivning av det aktuella värdet.

![visa IntelliSense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Du kan ställa in **numberOfWorkers** till 1.

    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }

## Distribuera resursgruppsprojektet till Azure

Nu är det dags att distribuera projektet. När du distribuerar ett Azure-resursgruppsprojekt kan du distribuera det till en Azure-resursgrupp, som bara är en logisk gruppering av resurser i Azure som exempelvis webbappar, databaser osv.

1. På snabbmenyn för distributionsprojektets nod väljer du **Distribuera** > **Ny distribution**.

    ![Menyalternativet Distribuera, Ny distribution](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

    Dialogrutan **Distribuera till resursgrupp**.

    ![Dialogrutan Distribuera till resursgrupp](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. I listrutan **Resursgrupp** väljer du en befintlig resursgrupp eller skapar en ny. Om du vill skapa en resursgrupp öppnar du listrutan **Resursgrupp** och väljer **Skapa nytt**.

    ![Dialogrutan Distribuera till resursgrupp](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)

    Dialogrutan **Skapa resursgrupp** visas. Ge gruppen ett namn och en plats och välj knappen **Skapa**.

    ![Dialogrutan Skapa resursgrupp](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
   
1. Du kan redigera parametrarna för distributionen genom att välja knappen **Redigera parametrar**. Ange värden för parametrarna och välj knappen **Spara**.

    ![Dialogrutan Redigera parametrar](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
    
    Alternativet **Spara lösenord i klartext i parameterfilen** är inte säkert.

1. Distribuera projektet till Azure genom att välja knappen **Distribuera**. Du kan se distributionsförloppet i fönstret **Utdata**. Distributionen kan ta flera minuter beroende på din konfiguration. Ange databasadministratörens lösenord i PowerShell-konsolen när du uppmanas att göra det. Om distributionsförloppet har stoppats kan det bero på att processen väntar på att du ska ange lösenordet i PowerShell-konsolen.

    >[AZURE.NOTE] Du kan uppmanas att installera Azure PowerShell-cmdlets. Eftersom dessa cmdlets krävs för att distribuera Azure-resursgrupper måste du installera dem.
    
1. När distributionen är klar bör du se ett meddelande liknande följande i fönstret **Utdata**:

        ...
        15:19:19 - DeploymentName     : websitesqldatabase-0212-2318
        15:19:19 - CorrelationId      : 6cb43be5-86b4-478f-9e2c-7e7ce86b26a2
        15:19:19 - ResourceGroupName  : DemoSiteGroup
        15:19:19 - ProvisioningState  : Succeeded
        ...

1. Öppna [Azure Portal](https://portal.azure.com/) i en webbläsare och logga in på ditt konto. Du visar resursgruppen genom att välja **Resursgrupper** och den resursgrupp som du har distribuerat till.

    ![välja grupp](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)

1. Alla distribuerade resurser visas.

    ![visa resurser](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

1. Om du gör ändringar och vill omdistribuera ditt projekt kan du välja den befintliga resursgruppen direkt från Azure-resursgruppsprojektets snabbmeny. Välj **Distribuera** på snabbmenyn och välj sedan den resursgrupp som du precis distribuerat till.

    ![Azure-resursgrupp som har distribuerats](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## Distribuera kod med din infrastruktur

Nu har du distribuerat infrastrukturen för din app, men det finns ingen direkt kod som distribueras med projektet. Det här avsnittet beskriver hur du distribuerar en webbapp och SQL Database-tabeller under distributionen. Om du distribuerar en virtuell dator i stället för en webbapp vill du köra en del kod på datorn som en del av distributionen. Processen för att distribuera koden för en webbapp eller för att konfigurera en virtuell dator är nästan desamma.

1. I din Visual Studio-lösning lägger du till en **ASP.NET-webbapp**. 

    ![lägga till webbapp](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
    
1. Välj **MVC** och avmarkera fältet för **Värd i molnet** eftersom resursgruppsprojektet ska utföra den aktiviteten.

    ![välja MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
    
1. När din webbapp har skapats lägger du till en referens i resursgruppsprojektet till webbappsprojektet.

    ![lägga till referens](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
    
    Genom att lägga till en referens länkar du webbappsprojektet till resursgruppsprojektet och definierar automatiskt tre nyckelegenskaper.  
    
    - **Ytterligare egenskaper** innehåller distributionspaketets mellanlagringsplats som ska skickas till Azure Storage. 
    - **Ta med filsökväg** innehåller sökvägen där paketet ska skapas.  **Ta med mål** innehåller det kommando som distributionen ska köra. 
    - Med standardvärdet **Build;Package** kan distributionen bygga och skapa ett webbdistributionspaket (package.zip).  
    
    Ingen publiceringsprofil behövs eftersom distributionen hämtar nödvändig information från egenskaperna för att skapa paketet.
    
      ![se referens](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
      
1. Lägg till en ny resurs i mallen och välj **Webbdistribution för Web Apps** den här gången. 

    ![lägga till webbdistribution](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
    
1. Distribuera resursgruppsprojektet till resursgruppen igen. Den här gången finns det några nya parametrar. Du behöver inte ange värden för **_artifactsLocation** eller **_artifactsLocationSasToken** eftersom de genereras automatiskt. Ange mappen och filnamnet till sökvägen som innehåller distributionspaketet.

    ![lägga till webbdistribution](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
    
    För **Artefaktlagringskonto** kan du använda det som distribueras med den här resursgruppen.
    
När distributionen är klar kan du bläddra till platsen och kontrollera att ASP.NET-standardappen har distribuerats.

![visa distribuerad app](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## Nästa steg

- Information om hur du hanterar dina resurser via portalen finns i [Hantera Azure-resurser med hjälp av Azure Portal](./azure-portal/resource-group-portal.md).
- Mer information om mallar finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).



<!--HONumber=Jun16_HO2-->


