---
title: "Visual Studio-projekt för Azure-resursgrupper | Microsoft Docs"
description: "Använd Visual Studio för att skapa ett Azure-resursgruppsprojekt och distribuera resurserna till Azure."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 4bd084c8-0842-4a10-8460-080c6a085bec
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 4f541e34e7c0696e4074613c4ab0734a096c6d12
ms.openlocfilehash: dd1161f2b5cf717246391900a616e51c457f5280


---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Skapa och distribuera Azure-resursgrupper via Visual Studio
Med Visual Studio och [Azure SDK](https://azure.microsoft.com/downloads/) kan du skapa ett projekt som distribuerar din infrastruktur och kod till Azure. Du kan till exempel definiera webbvärden, webbplatsen och databasen för din app och distribuera den infrastrukturen tillsammans med koden. Eller så kan du definiera en virtuell dator, ett virtuellt nätverk och ett lagringskonto och distribuera den infrastrukturen tillsammans med ett skript som körs på den virtuella datorn. Med ett projekt för distribution av en **Azure-resursgrupp** kan du distribuera alla nödvändiga resurser i en enda, repeterbara åtgärd. Mer information om hur du distribuerar och hanterar dina resurser finns i [Översikt över Azure Resource Manager](resource-group-overview.md).

Projekt med Azure-resursgrupper innehåller JSON-baserade Azure Resource Manager-mallar, som definierar de resurser som du distribuerar till Azure. Mer information om elementen i Resource Manager-mallen finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md). I Visual Studio kan du redigera dessa mallar och använda verktyg som gör det lättare att arbeta med mallar.

I det här avsnittet ska du distribuera en webbapp och SQL Database. Stegen är dock i princip identiska för andra typer av resurser. Det är lika lätt att distribuera en virtuell dator och dess relaterade resurser. Visual Studio har många olika startmallar som du kan använda för att distribuera vanliga scenarier.

I den här artikeln använder vi Visual Studio 2015 Update 2 och Microsoft Azure SDK för .NET 2.9. Om du har Visual Studio 2013 med Azure SDK 2.9 blir din upplevelse i stort sett densamma. Du kan använda valfri version av Azure SDK från 2.6 och senare. Din upplevelse av användargränssnittet kan dock skilja sig något mot vad du ser i den här artikeln. Vi rekommenderar starkt att du installerar den senaste versionen av [Azure SDK](https://azure.microsoft.com/downloads/) innan du börjar med stegen. 

## <a name="create-azure-resource-group-project"></a>Skapa ett projekt för en Azure-resursgrupp
I den här proceduren ska du skapa ett projekt för en Azure-resursgrupp med en mall av typen **Webbapp + SQL**.

1. I Visual Studio väljer du **Arkiv**, **Nytt projekt** och väljer sedan **C#** eller **Visual Basic**. Välj **Moln** och sedan projektet **Azure-resursgrupp**.
   
    ![Projekt för molndistribution](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)
2. Välj den mall som du vill distribuera till Azure Resource Manager. Observera att det finns många olika alternativ beroende på vilken typ av projekt som du vill distribuera. I det här avsnittet väljer du mallen **Webbapp + SQL**.
   
    ![Välja en mall](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)
   
    Mallen som du väljer är bara en startpunkt. Du kan lägga till och ta bort resurser som passar ditt scenario.
   
   > [!NOTE]
   > Visual Studio hämtar en lista över tillgängliga mallar online. Listan kan ändras.
   > 
   > 
   
    Visual Studio skapar ett distributionsprojekt för resursgrupper för webbappen och SQL-databasen.
3. Expandera noderna i distributionsprojekt för att se vad du har skapat.
   
    ![visa noder](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)
   
    Eftersom vi valde mallen Webbapp + SQL för det här exemplet visas följande filer: 
   
   | Filnamn | Beskrivning |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Ett PowerShell-skript som anropar PowerShell-kommandon för distribution till Azure Resource Manager.<br />**Obs!** Visual Studio använder PowerShell-skript för att distribuera mallen. De ändringar du gör i det här skriptet påverkar distributionen i Visual Studio, så var försiktig. |
   | WebSiteSQLDatabase.json |Resource Manager-mallen som definierar infrastrukturen som du vill distribuera till Azure, och de parametrar som du kan ange under distributionen. Den definierar även beroendena mellan resurserna så att resurserna distribueras i rätt ordning av Resource Manager. |
   | WebSiteSQLDatabase.parameters.json |En fil med parametrar som innehåller värden som krävs av mallen. Du skickar in parametervärden för att anpassa varje distribution. |
   
    Alla distributionsprojekt för resursgrupper innehåller dessa grundläggande filer. Andra projekt kan innehålla ytterligare filer som ger stöd för andra funktioner.

## <a name="customize-the-resource-manager-template"></a>Anpassa Resource Manager-mallen
Du kan anpassa ett distributionsprojekt genom att ändra JSON-mallarna som beskriver de resurser som du vill distribuera. JSON står för JavaScript Object Notation och är ett format för serialiserade data som är lätt att arbeta med. JSON-filerna använder ett schema som du refererar till längst upp i varje fil. Om du vill granska schemat kan du hämta och analysera det. Schemat definierar vilka element som är giltiga, typer och format för fält, de möjliga värdena för uppräknade värden och så vidare. Mer information om elementen i Resource Manager-mallen finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).

Du kan arbeta med din mall genom att öppna **WebSiteSQLDatabase.json**.

Visual Studio-redigeraren innehåller verktyg som hjälper dig att redigera Resource Manager-mallen. Fönstret **JSON-disposition** gör det enkelt att se elementen som definierats i mallen.

![visa JSON-disposition](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

När du väljer ett element i dispositionen kommer du till den delen av mallen och motsvarande JSON markeras.

![navigera i JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Du kan lägga till en resurs genom att antingen välja knappen **Lägg till resurs** längst upp i fönstret JSON-disposition eller genom att högerklicka på **resurser** och välja **Lägg till ny resurs**.

![lägga till en resurs](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

I den här självstudiekursen väljer du **Lagringskonto** och ger det ett namn. Ange ett namn som innehåller fler än 11 tecken och endast siffror och små bokstäver.

![lägga till ett lagringskonto](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Observera att inte bara resursen lades till, utan även en parameter för typen av lagringskonto och en variabel för namnet på lagringskontot.

![visa disposition](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

Parametern **storageType** är fördefinierad med tillåtna typer och en standardtyp. Du kan lämna dessa värden eller redigera dem för ditt scenario. Om du inte vill att någon ska distribuera ett **Premium_LRS**-lagringskonto med den här mallen tar du bort det från de tillåtna typerna. 

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

Visual Studio tillhandahåller även IntelliSense som gör det lättare för dig att förstå vilka egenskaper som är tillgängliga när du redigerar mallen. Om du till exempel vill redigera egenskaperna för din App Service-plan går du till resursen **HostingPlan** och lägger till ett värde för **properties**. Observera att IntelliSense visar de tillgängliga värdena och en beskrivning av det aktuella värdet.

![visa IntelliSense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Du kan ställa in **numberOfWorkers** till 1.

    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }

## <a name="deploy-the-resource-group-project-to-azure"></a>Distribuera resursgruppsprojektet till Azure
Nu är det dags att distribuera projektet. När du distribuerar ett Azure-resursgruppsprojekt distribuerar du det till en Azure-resursgrupp. Resursgruppen är en logisk gruppering av resurser som har en gemensam livscykel.

1. På snabbmenyn för distributionsprojektets nod väljer du **Distribuera** > **Ny distribution**.
   
    ![Menyalternativet Distribuera, Ny distribution](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)
   
    Dialogrutan **Distribuera till resursgrupp**.
   
    ![Dialogrutan Distribuera till resursgrupp](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)
2. I listrutan **Resursgrupp** väljer du en befintlig resursgrupp eller skapar en ny. Du skapar en resursgrupp genom att öppna listrutan **Resursgrupp** och välja **Skapa nytt**.
   
    ![Dialogrutan Distribuera till resursgrupp](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)
   
    Dialogrutan **Skapa resursgrupp** visas. Ge gruppen ett namn och en plats och välj knappen **Skapa**.
   
    ![Dialogrutan Skapa resursgrupp](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
3. Redigera parametrarna för distributionen genom att välja knappen **Redigera parametrar**.
   
    ![Redigera parametrar](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/edit-parameters.png)
4. Ange värden för de tomma parametrarna och välj knappen **Spara**. De tomma parametrarna är **hostingPlanName**, **administratorLogin**, **administratorLoginPassword**, och **databaseName**.
   
    **hostingPlanName** anger ett namn för [App Service plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) att skapa. 
   
    **administratorLogin** anger SQL Server-administratörens användarnamn. Använd inte vanliga admin-namn som **sa** eller **admin**. 
   
    **administratorLoginPassword** anger SQL Server-administratörens lösenord. Alternativet **Spara lösenord i klartext i parameterfilen** är inte säkert, välj därför inte detta alternativ. Eftersom lösenordet inte sparas som oformaterad text behöver du ange detta lösenord igen vid distributionen. 
   
    **databaseName** anger ett namn för databasen att skapa. 
   
    ![Dialogrutan Redigera parametrar](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
5. Distribuera projektet till Azure genom att välja knappen **Distribuera**. En PowerShell-konsol öppnas utanför Visual Studio-instansen. Ange SQL Server-administratörens lösenord i PowerShell-konsolen när du uppmanas att göra detta. **Din PowerShell-konsol kan vara dold bakom andra objekt eller minimerad i Aktivitetsfältet.** Leta reda på konsolen och välj den för att ange lösenordet.
   
   > [!NOTE]
   > Visual Studio kan uppmana dig att installera Azure PowerShell-cmdlets. Du behöver Azure PowerShell-cmdlets för att distribuera resursgrupper. Installera dem om du uppmanas att göra det.
   > 
   > 
6. Distributionen kan ta några minuter. I fönstren **Utdata** kan du se status för distributionen. När distributionen är klar indikerar det sista meddelandet att distributionen är framgångsrik med något liknande:
   
        ... 
        18:00:58 - Successfully deployed template 'c:\users\user\documents\visual studio 2015\projects\azureresourcegroup1\azureresourcegroup1\templates\websitesqldatabase.json' to resource group 'DemoSiteGroup'.
7. Öppna [Azure-portalen](https://portal.azure.com/) i en webbläsare och logga in på ditt konto. Du visar resursgruppen genom att välja **Resursgrupper** och den resursgrupp som du har distribuerat till.
   
    ![välja grupp](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)
8. Du ser alla distribuerade resurser. Observera att namnet på lagringskontot inte är exakt vad du angav när du lade till resursen. Lagringskontot måste vara unikt. Mallen lägger automatiskt till en sträng med tecken i det namn du angav för att ge ett unikt namn. 
   
    ![visa resurser](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)
9. Om du gör ändringar och vill distribuera om ditt projekt väljer du den befintliga resursgruppen på snabbmenyn för Azure-resursgruppsprojektet. Välj **Distribuera** på snabbmenyn och välj sedan den resursgrupp som du har distribuerat.
   
    ![Azure-resursgrupp som har distribuerats](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## <a name="deploy-code-with-your-infrastructure"></a>Distribuera kod med din infrastruktur
Nu har du distribuerat infrastrukturen för din app, men det finns ingen direkt kod som distribueras med projektet. Det här avsnittet beskriver hur du distribuerar en webbapp och SQL Database-tabeller under distributionen. Om du distribuerar en virtuell dator i stället för en webbapp vill du köra en del kod på datorn som en del av distributionen. Processen för att distribuera koden för en webbapp eller för att konfigurera en virtuell dator är nästan desamma.

1. Lägga till ett projekt i din Visual Studio-lösning. Högerklicka på lösningen och välj **Lägg till** > **nytt projekt**.
   
    ![lägga till projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)
2. Lägga till en **ASP.NET webbapp**. 
   
    ![lägga till webbapp](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
3. Välj **MVC** och avmarkera fältet för **värd i molnet** eftersom resursgruppsprojektet utför den aktiviteten.
   
    ![välja MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
4. Efter att Visual Studio skapat din webbapp visas båda projekten i lösningen.
   
    ![visa projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)
5. Nu måste du kontrollera att ditt resursgrupp-projekt är medvetet om det nya projektet. Gå tillbaka till ditt resursgrupp-projekt (AzureResourceGroup1). Högerklicka på **Referenser** och välj **Lägg till referens**.
   
    ![lägga till referens](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)
6. Välj det webbapp-projekt som du har skapat.
   
    ![lägga till referens](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
   
    Genom att lägga till en referens länkar du webbappsprojektet till resursgruppsprojektet och definierar automatiskt tre nyckelegenskaper. Du ser dessa egenskaper i fönstret **Egenskaper** för referensen.
   
      ![se referens](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
   
    Egenskaperna är:
   
   * **Ytterligare egenskaper** innehåller distributionspaketets mellanlagringsplats som skickas till Azure Storage. Observera mappen (ExampleApp) och filen (package.zip). Du kan ange dessa värden som parametrar när du distribuerar appen. 
   * **Ta med filsökväg** innehåller sökvägen där paketet skapas. **Ta med mål** innehåller det kommando som distributionen kör. 
   * Med standardvärdet **Build;Package** kan distributionen bygga och skapa ett webbdistributionspaket (package.zip).  
     
     Du behöver ingen publiceringsprofil eftersom distributionen hämtar nödvändig information från egenskaperna för att skapa paketet.
7. Lägga till en resurs i mallen.
   
    ![lägga till en resurs](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)
8. Denna gång väljer du **Webbdistribution för webbappar**. 
   
    ![lägga till webbdistribution](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
9. Distribuera resursgruppsprojektet till resursgruppen igen. Den här gången finns det några nya parametrar. Du behöver inte ange värden för **_artifactsLocation** eller **_artifactsLocationSasToken** eftersom Visual Studio genererar dessa värden automatiskt. Du kan dock att ställa in mapp- och filnamn på den sökväg som innehåller distributionspaketet (visas som **ExampleAppPackageFolder** och **ExampleAppPackageFileName** i följande bild). Ange de värden som du såg tidigare i referensegenskaperna (**ExampleApp** och **package.zip**).
   
    ![lägga till webbdistribution](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
   
    Som **Artefaktlagringskonto** kan du välja det som distribuerades med den här resursgruppen.
10. När distributionen är klar väljer du din webbapp i portalen. Välj webbadress för att gå till webbplatsen.
    
     ![bläddra på webbplats](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)
11. Observera att du nu har distribuerat standard-ASP.NET-appen.
    
     ![visa distribuerad app](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="next-steps"></a>Nästa steg
* Information om hur du hanterar dina resurser via portalen finns i [Hantera Azure-resurser med hjälp av Azure-portalen](resource-group-portal.md).
* Mer information om mallar finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).




<!--HONumber=Nov16_HO3-->


