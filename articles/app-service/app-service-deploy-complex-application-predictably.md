---
title: Etablera och distribuera mikrotjänster förutsägbart i Azure
description: Lär dig hur du distribuerar ett program som består av mikrotjänster i Azure App Service som en enhet och på ett förutsägbart sätt med hjälp av JSON-resursgruppsmallar och PowerShell-skript.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin
ms.openlocfilehash: c4071da60ed1311d8dd75d6a369c48cf711778cb
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243222"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Etablera och distribuera mikrotjänster förutsägbart i Azure
Den här kursen visar hur du etablerar och distribuera ett program som består av [mikrotjänster](https://en.wikipedia.org/wiki/Microservices) i [Azure App Service](https://azure.microsoft.com/services/app-service/) som en enhet och på ett förutsägbart sätt med hjälp av JSON-resursgruppsmallar och PowerShell-skript. 

Mikrotjänster, repeterbarhet och förutsägbarheten är avgörande för framgång när etablering och distribution av storskaliga program som består av mycket fristående. [Azure App Service](https://azure.microsoft.com/services/app-service/) kan du skapa mikrotjänster som innehåller webbappar, mobilappar, API apps och logic apps. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) gör att du kan hantera alla mikrotjänster som en enhet, tillsammans med beroenden, till exempel databas och källtabellen kontrollinställningar. Du kan också distribuera sådant program med hjälp av JSON-mallar och enkel PowerShell-skript. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-do"></a>Vad du ska göra
I självstudien distribuerar du ett program som innehåller:

* Två webbappar (dvs. två mikrotjänster)
* En SQL Database-serverdel
* Appinställningar, anslutningssträngar och källkontroll
* Application insights, aviseringar, inställningar för automatisk skalning

## <a name="tools-you-will-use"></a>Verktyg som du ska använda
I den här självstudien använder du följande verktyg. Eftersom det inte är omfattande diskussion om verktyg, jag tänker behålls i ett scenario för slutpunkt till slutpunkt och bara ger dig en kort introduktion till var och en, och var du hittar mer information om den. 

### <a name="azure-resource-manager-templates-json"></a>Azure Resource Manager-mallar (JSON)
Varje gång du skapar en webbapp i Azure App Service, till exempel använder Azure Resource Manager en JSON-mall för att skapa hela resursgruppen med komponenten-resurser. En komplex mall från den [Azure Marketplace](/azure/marketplace) får innehålla databasen, lagringskonton, App Service-planen, själva webbappen, Varningsregler, app-inställningar, inställningarna för automatisk skalning och mycket mer, och dessa mallar är tillgängliga för dig via PowerShell. Information om hur du hämtar och använder dessa mallar finns i [med hjälp av Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).

Mer information om Azure Resource Manager-mallar finns i [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 för Visual Studio
De senaste SDK innehåller förbättringar av mallstöd för Resource Manager-i JSON-redigerare. Du kan använda detta för att snabbt skapa en resursgruppmall från grunden eller öppna en befintlig mall för JSON (till exempel en hämtade gallerimall) för ändring av, fylla i parameterfilen och även distribuera resursgruppen direkt från en Azure-resurs Gruppen lösning.

Mer information finns i [Azure SDK 2.6 för Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 eller senare
Från och med version 0.8.0, innehåller Azure PowerShell-installationen Azure Resource Manager-modulen utöver Azure-modulen. Den här nya modulen kan du skriva distributionen av resursgrupper.

Mer information finns i [med hjälp av Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Azure Resource Explorer
Detta [förhandsversion av verktyget](https://resources.azure.com) möjligheten att utforska JSON-definitioner för alla resursgrupper i din prenumeration och enskilda resurser. I verktyget, kan du redigera JSON-definitioner av en resurs, ta bort en hel hierarki av resurser och skapa nya resurser.  Informationen som är tillgängliga i det här verktyget är användbart för redigering av mallen eftersom den visar vilka egenskaper som du måste ange för en viss typ av resurs, rätt värden, osv. Du kan även skapa en resursgrupp i den [Azure-portalen](https://portal.azure.com/), granska dess JSON-definitioner i explorer-verktyg som hjälper dig mallanpassa resursgruppen.

### <a name="deploy-to-azure-button"></a>Distribuera till Azure-knappen
Om du använder GitHub för källkontroll kan du placera en [distribuera till Azure knappen](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) i din viktigt. MD, vilket gör att en nyckelfärdig distribution Användargränssnittet till Azure. Medan du kan göra detta för en enkel webbapp, kan du utöka detta om du vill aktivera distribuera en hela resursgruppen genom att placera en filen azuredeploy.json i Lagringsplatsens rot. Det här JSON-fil som innehåller resursgruppsmall ska användas av för att distribuera till Azure knappen Skapa resursgruppen. Ett exempel finns i den [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) exemplet som du ska använda i den här självstudien.

## <a name="get-the-sample-resource-group-template"></a>Hämta exempel-resursgruppsmall
Nu ska vi hämta rätt till den.

1. Navigera till den [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) App Service-exemplet.
2. I readme.md, klickar du på **distribuera till Azure**.
3. Kommer du till den [distribuera till azure](https://deploy.azure.com) plats och ombeds indataparametrar för distribution. Observera att de flesta fälten fylls med namnet på lagringsplatsen och vissa slumpmässiga strängar för dig. Du kan ändra alla fält om du vill, men de enda saker som du behöver ange är administrativa SQL Server-inloggning och lösenord och klicka sedan på **nästa**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Klicka sedan på **distribuera** att starta distributionsprocessen. När processen körs kan slutföras, klickar du på den http://todoapp *XXXX*. azurewebsites.net länk som leder det distribuerade programmet. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   Användargränssnittet skulle vara lite långsam när du först gå till den eftersom apparna som bara startar, men övertyga dig om att det är ett fullt fungerande program.
5. Tillbaka i distribuera-sidan klickar du på den **hantera** länk för att visa det nya programmet i Azure Portal.
6. I den **Essentials** listrutan klickar du på länken resource group. Observera också att webbappen är redan ansluten till GitHub-lagringsplatsen under **externt projekt**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. I bladet för resursgruppen, Observera att det finns redan två webbappar och en SQL-databas i resursgruppen.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Allt som du såg precis i en kort stund ett fullständigt distribuerad två-mikrotjänst-program med alla komponenter, beroenden, inställningar, databas och kontinuerlig publicering ställs in som en automatiserad orkestrering i Azure Resource Manager. Allt detta gjordes genom två saker:

* För att distribuera till Azure-knappen
* azuredeploy.JSON i roten för lagringsplatsen

Du kan distribuera den här samma program tiotals, hundratals eller tusentals gånger och har exakt samma konfiguration varje gång. Repeterbarheten och förutsägbarheten hos den här metoden kan du distribuera program i stor skala enkelt och tryggt.

## <a name="examine-or-edit-azuredeployjson"></a>Granska (eller redigera) AZUREDEPLOY. JSON
Nu ska vi titta på hur GitHub-lagringsplatsen är inställd. Du kommer att använda JSON-redigeraren i Azure .NET SDK, så om du inte redan har installerat [Azure .NET SDK 2.6](https://azure.microsoft.com/downloads/), gör det nu.

1. Klona den [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) lagringsplatsen med ditt favorit git. I skärmbilden nedan gör jag detta i Team Explorer i Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Öppna azuredeploy.json i Visual Studio från Lagringsplatsens rot. Om du inte ser fönstret JSON-disposition, måste du installera Azure .NET SDK.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Jag tänker inte beskriver varje detalj i JSON-format, men [mer resurser](#resources) avsnittet innehåller länkar för Mallspråket resource group. Här kan jag bara att visa de intressanta funktioner som kan hjälpa dig att komma igång i att göra en egen anpassad mall för distribution av appen.

### <a name="parameters"></a>Parametrar
Ta en titt på avsnittet Parametrar för att se att de flesta av dessa parametrar är vad den **distribuera till Azure** knappen uppmanas du att ange. Webbplatsen bakom den **distribuera till Azure** knappen fyller indata Användargränssnittet med hjälp av parametrar som definierats i azuredeploy.json. De här parametrarna används i resursdefinitionerna resursnamn, egenskapsvärden, t.ex.

### <a name="resources"></a>Resurser
I resursnoden kan du se att 4 översta resurserna definieras, inklusive en SQL Server-instans, en apptjänstplan och två webbappar. 

#### <a name="app-service-plan"></a>App Service-plan
Låt oss börja med en enkel rotnivå resurs i JSON. I JSON-disposition klickar du på App Service-plan med namnet **[hostingPlanName]** att markera motsvarande JSON-koden. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Observera att den `type` elementet anger strängen för en App Service-plan (den anropades en servergrupp en lång, lång tid sedan) och andra element och egenskaper fylls i med de parametrar som definierats i JSON-filen den här resursen inte innehåller något kapslade resurser.

> [!NOTE]
> Observera också att värdet för `apiVersion` informerar du Azure vilken version av REST-API för att använda JSON-resource definition med och det kan påverka hur resursen ska formateras inuti den `{}`. 
> 
> 

#### <a name="sql-server"></a>SQL Server
Klicka på SQL Server-resursen med namnet **SQLServer** i JSON-disposition.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Observera följande om den markerade JSON-koden:

* Användningen av parametrar säkerställer att de skapade resurserna är med namnet och konfigurerats på ett sätt som att de överensstämmer med varandra.
* SQLServer-resursen har två kapslade resurser, var och en har ett annat värde för `type`.
* Kapslade resurser i `“resources”: […]`, där databasen och brandväggsreglerna definieras, har en `dependsOn` element som anger resurs-ID för SQLServer-resursen på rotnivå. Detta talar om Azure Resource Manager ”innan du skapar den här resursen som annan resurs måste redan finnas; och om den andra resursen har definierats i mallen, skapa sedan den först ”.
  
  > [!NOTE]
  > Detaljerad information om hur du använder den `resourceId()` finns [Azure Resource Manager-Mallfunktioner](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid).
  > 
  > 
* Effekten av den `dependsOn` elementet är att Azure Resource Manager vet vilka resurser kan skapas parallellt och vilka resurser måste vara sekventiellt. 

#### <a name="web-app"></a>Webbapp
Nu ska vi gå vidare till de faktiska web apps själva, som är mer komplicerad. Klicka på [variables('apiSiteName')]-webbapp i JSON-disposition att markera dess JSON-koden. Lägg märke till att saker får mycket mer intressant. För detta ändamål vi pratar mer om funktioner i taget:

##### <a name="root-resource"></a>Rot-resurs
Webbappen är beroende av två olika resurser. Det innebär att Azure Resource Manager skapar webbappen förrän både App Service-planen och SQL Server-instansen har skapats.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Appinställningar
Appinställningarna också definieras som en kapslad resurs.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

I den `properties` element för `config/appsettings`, du har två appinställningar i formatet `"<name>" : "<value>"`.

* `PROJECT` är en [KUDU inställningen](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) som talar om Azure-distribution projektet som ska använda i en Visual Studio-lösning med flera projekt. Kommer jag att visa hur källkontroll har konfigurerats, men eftersom ToDoApp koden finns i en Visual Studio-lösning med flera projekt, behöver vi den här inställningen.
* `clientUrl` är helt enkelt en app som inställning som programkoden använder.

##### <a name="connection-strings"></a>Anslutningssträngar
Anslutningssträngar för också definieras som en kapslad resurs.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

I den `properties` element för `config/connectionstrings`, varje anslutningssträngen också definieras som ett namn: värde-par med det specifika formatet för `"<name>" : {"value": "…", "type": "…"}`. För den `type` element, möjliga värden är `MySql`, `SQLServer`, `SQLAzure`, och `Custom`.

> [!TIP]
> En slutgiltig lista över strängtyper anslutning kör du följande kommando i Azure PowerShell: \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Källkontroll
Inställningar för källkontrollen också definieras som en kapslad resurs. Azure Resource Manager använder den här resursen för att konfigurera kontinuerlig publicering (se villkor på `IsManualIntegration` senare) och även för att sätta igång distributionen av programkoden automatiskt under bearbetning av JSON-filen.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` och `branch` ska vara ganska intuitiv och måste peka på Git-lagringsplats och namnet på den gren som ska publicera från. Dessa definieras igen av indataparametrar. 

Observera att i den `dependsOn` element att förutom webbappresursen, `sourcecontrols/web` beror också på `config/appsettings` och `config/connectionstrings`. Detta beror på att när `sourcecontrols/web` är konfigurerat kan Azure distributionsprocessen kommer automatiskt att försöka distribuera, skapa och starta programkoden. Därför kan lägga till det här beroendet hjälper dig att se till att programmet har åtkomst till de nödvändiga appinställningar och anslutningssträngar innan programkoden körs. 

> [!NOTE]
> Observera också att `IsManualIntegration` är inställd på `true`. Den här egenskapen är nödvändigt i den här självstudien eftersom du äger inte faktiskt GitHub-lagringsplatsen och därför kan inte faktiskt bevilja behörighet till Azure för att konfigurera kontinuerlig publicering från [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (d.v.s. push automatisk databasen uppdateringar till Azure). Du kan använda standardvärdet `false` för den angivna databasen endast om du har konfigurerat ägarens GitHub-autentiseringsuppgifter på den [Azure-portalen](https://portal.azure.com/) innan. Med andra ord, om du har ställt in källkontrollen till GitHub eller BitBucket för en app i den [Azure-portalen](https://portal.azure.com/) tidigare använda dina autentiseringsuppgifter för Azure kommer ihåg autentiseringsuppgifterna och använda dem när du distribuerar appar från GitHub eller BitBucket i framtiden. Men om du inte redan har gjort det, misslyckas distribution av JSON-mallen när Azure Resource Manager försöker konfigurera inställningar för kontroll av webbappens datakälla eftersom den inte kan logga in GitHub eller BitBucket med autentiseringsuppgifter för den databasägaren.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Jämför JSON-mallen med distribuerade resursgruppen
Här kan du kan gå igenom alla webbappens blad i den [Azure-portalen](https://portal.azure.com/), men det finns ett annat verktyg som inte är precis som är användbart, om mer. Gå till den [Azure Resource Explorer](https://resources.azure.com) förhandsversion verktyg, vilket ger dig en JSON-representation av alla resursgrupper i dina prenumerationer, eftersom de finns i Azure-serverdel. Du kan också se hur den resursgrupp JSON-hierarki i Azure överensstämmer med hierarkin i mallfilen som används för att skapa den.

Till exempel när jag går till den [Azure Resource Explorer](https://resources.azure.com) verktyget och Expandera noderna i Utforskaren, ser jag resursgruppen och resurserna på rotnivå som samlas in under deras respektive resurstyper.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Om du ökar detaljnivån till en webbapp du ska kunna se web app konfigurationsinformation ungefär som på skärmbilden nedan:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Återigen kapslade resurser bör ha en hierarki som liknar de i din mall för JSON-fil och du bör se appinställningar, anslutningssträngar, osv., korrekt visas i fönstret JSON. Avsaknad av inställningar kan tyda på ett problem med din JSON-fil och kan hjälpa dig att felsöka dina JSON-mallfil.

## <a name="deploy-the-resource-group-template-yourself"></a>Distribuera resursgruppsmall själv
Den **distribuera till Azure** knappen är bra, men du kan distribuera resursgruppsmall i azuredeploy.json endast om du har redan push-överfört azuredeploy.json i GitHub. Azure .NET SDK innehåller dessutom verktyg som du kan distribuera alla JSON-mallfil direkt från din lokala dator. Följ stegen nedan om du vill göra detta:

1. I Visual Studio klickar du på **Arkiv** > **Nytt** > **Projekt**.
2. Klicka på **Visual C#** > **molnet** > **Azure-resursgrupp**, klicka sedan på **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. I **Välj Azure-mall**väljer **tom mall** och klicka på **OK**.
4. Dra azuredeploy.json i den **mall** mappen för det nya projektet.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. Från Solution Explorer öppnar du kopierade azuredeploy.json.
6. Bara för bevis på nu ska vi lägga till några standard Application Insights-resurser till vår JSON-fil genom att klicka på **Lägg till resurs**. Om du bara vill distribuera JSON-fil, kan du hoppa över steg som distribuera.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Välj **Application Insights för Web Apps**, kontrollera att en befintlig App Service-plan och web app är markerad och klicka sedan på **Lägg till**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Du kommer nu att kunna visa flera nya resurser som beroende på resurs så att den inte, har beroenden på App Service-plan eller webbappen. Dessa resurser är inte aktiverade som deras befintliga definitionen och du kommer att ändra.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. I JSON-disposition klickar du på **appInsights Autoskala** att markera dess JSON-koden. Det här är skalinställningen för App Service-planen.
9. I den markerade JSON-koden letar du upp den `location` och `enabled` egenskaper och ange dem som visas nedan.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. I JSON-disposition klickar du på **CPUHigh appInsights** att markera dess JSON-koden. Det här är en avisering.
11. Leta upp den `location` och `isEnabled` egenskaper och ange dem som visas nedan. Gör samma sak för de andra tre aviseringarna (lila lökar).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Du är nu redo att distribuera. Högerklicka på projektet och välj **distribuera** > **ny distribution**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Logga in på ditt Azure-konto om du inte redan gjort det.
14. Välj en befintlig resursgrupp i din prenumeration eller skapa en ny en, väljer **azuredeploy.json**, och klicka sedan på **redigera parametrar**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Du kommer nu att kunna redigera de parametrar som angetts i mallfilen i en bra tabell. Parametrar som definierar standardvärden har redan sina standardvärden och parametrar som definierar en lista över tillåtna värden visas som listrutor.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Fyll i de tomma parametrarna och använda den [GitHub-repo-adress för ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) i **repo-URL**. Klicka sedan på **spara**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Automatisk skalning är en funktion som erbjuds i **Standard** nivå eller högre och plan på servernivå aviseringar är funktioner som erbjuds i **grundläggande** nivå eller högre, måste du ange den **sku** parameter att **Standard** eller **Premium** om du vill se alla dina nya App Insights-resurser tänds.
    > 
    > 
16. Klicka på **distribuera**. Om du har valt **spara lösenord**, lösenordet sparas i parameterfilen **i klartext**. Annars kan att du ombes att ange lösenordet för databasen under distributionsprocessen.

Klart! Nu behöver du bara gå till den [Azure-portalen](https://portal.azure.com/) och [Azure Resource Explorer](https://resources.azure.com) verktyg för att se nya aviseringar och inställningarna för automatisk skalning som lagts till i din JSON distribuerat program.

Dina steg i det här avsnittet gjort huvudsakligen följande:

1. Förberedd mallfilen
2. Skapa en parameterfil att mallfilen
3. Distribuerat mallfilen med parameterfilen

Det sista steget är enkelt utföras av en PowerShell-cmdlet. Öppna Scripts\Deploy AzureResourceGroup.ps1 om du vill se vad Visual Studio gjorde när det distribuerade programmet. Det finns en massa kod det, men jag tänker bara markera alla relevanta koden som du behöver distribuera mallfilen med parameterfilen.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Senaste cmdlet, `New-AzureResourceGroup`, är det som faktiskt utför åtgärden. Du bör allt detta visar att med hjälp av verktyg, det är det relativt enkelt att distribuera ditt molnprogram förutsägbart. Varje gång du kör cmdleten på samma mall med samma parameterfilen, ska du få samma resultat.

## <a name="summary"></a>Sammanfattning
Är nycklar för alla framgångsrika distribution av en stor skala program som består av mikrotjänster i DevOps, repeterbarhet och förutsägbarhet. I den här självstudien har du distribuerat ett två-mikrotjänst program till Azure som en enskild resursgrupp med hjälp av Azure Resource Manager-mall. Förhoppningsvis, nu har fått den kunskap du behöver för att starta konvertera ditt program i Azure till en mall och kan etablera och distribuera den förutsägbart. 

<a name="resources"></a>

## <a name="more-resources"></a>Fler resurser
* [Språk med Azure Resource Manager-mall](../azure-resource-manager/resource-group-authoring-templates.md)
* [Skapa Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure Resource Manager-Mallfunktioner](../azure-resource-manager/resource-group-template-functions.md)
* [Distribuera ett program med Azure Resource Manager-mall](../azure-resource-manager/resource-group-template-deploy.md)
* [Använda Azure PowerShell med Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Felsökning av resursgrupp distributioner i Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md)

