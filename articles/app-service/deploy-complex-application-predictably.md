---
title: Distribuera appar förutsägbart med ARM
description: Lär dig hur du distribuerar flera Azure App Service-appar som en enda enhet och på ett förutsägbart sätt med hjälp av Azure Resource Management-mallar och PowerShell-skript.
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: 62d0bf776b2d0c97d95b992ed6a1fd2a356e467a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75967384"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Etablera och distribuera mikrotjänster förutsägbart i Azure
Den här självstudien visar hur du etablerar och distribuerar ett program som består av [mikrotjänster](https://en.wikipedia.org/wiki/Microservices) i [Azure App Service](https://azure.microsoft.com/services/app-service/) som en enhet och på ett förutsägbart sätt med JSON-resursgruppmallar och PowerShell-skript. 

Vid etablering och distribution av storskaliga program som består av mycket frikopplade mikrotjänster är repeterbarhet och förutsägbarhet avgörande för att lyckas. [Med Azure App Service](https://azure.microsoft.com/services/app-service/) kan du skapa mikrotjänster som innehåller webbappar, mobila bakåtändningar och API-appar. [Med Azure Resource Manager](../azure-resource-manager/management/overview.md) kan du hantera alla mikrotjänster som en enhet, tillsammans med resursberoenden som inställningar för databas- och källkontroll. Nu kan du också distribuera ett sådant program med JSON-mallar och enkla PowerShell-skript. 

## <a name="what-you-will-do"></a>Vad du ska göra
I självstudien ska du distribuera ett program som innehåller:

* Två App Service-appar (dvs. två mikrotjänster)
* En serverad SQL-databas
* Appinställningar, anslutningssträngar och källkontroll
* Programinsikter, varningar, inställningar för automatisk skalning

## <a name="tools-you-will-use"></a>Verktyg som du kommer att använda
I den här självstudien använder du följande verktyg. Eftersom det inte är omfattande diskussion om verktyg, jag ska hålla sig till end-to-end scenario och bara ge dig ett kort intro till varje, och där du kan hitta mer information om det. 

### <a name="azure-resource-manager-templates-json"></a>Azure Resource Manager-mallar (JSON)
Varje gång du skapar en app i Azure App Service, till exempel, använder Azure Resource Manager en JSON-mall för att skapa hela resursgruppen med komponentresurserna. En komplex mall från [Azure Marketplace](/azure/marketplace) kan innehålla databasen, lagringskonton, App Service-planen, själva appen, varningsregler, appinställningar, inställningar för automatisk skalning med mera och allt detta och alla dessa mallar är tillgängliga för dig via PowerShell. Mer information om Azure Resource Manager-mallarna finns i [Skapa Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 för Visual Studio
Den senaste SDK innehåller förbättringar av Resource Manager-mallens stöd i JSON-redigeraren. Du kan använda detta för att snabbt skapa en resursgruppsmall från grunden eller öppna en befintlig JSON-mall (till exempel en nedladdad gallerimall) för ändring, fylla i parameterfilen och till och med distribuera resursgruppen direkt från en Azure-resurs Grupplösning.

Mer information finns i [Azure SDK 2.6 för Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 eller senare
Från och med version 0.8.0 innehåller Azure PowerShell-installationen Azure Resource Manager-modulen utöver Azure-modulen. Med den här nya modulen kan du skripta distributionen av resursgrupper.

Mer information finns i [Använda Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Azure Resource Explorer
Med det här [förhandsgranskningsverktyget](https://resources.azure.com) kan du utforska JSON-definitionerna för alla resursgrupper i prenumerationen och de enskilda resurserna. I verktyget kan du redigera JSON-definitionerna för en resurs, ta bort en hel resurshierarki och skapa nya resurser.  Den information som är lätt tillgänglig i det här verktyget är till stor hjälp för mall redigering eftersom det visar vilka egenskaper du behöver ange för en viss typ av resurs, rätt värden, etc. Du kan till och med skapa din resursgrupp i [Azure Portal](https://portal.azure.com/)och sedan granska dess JSON-definitioner i explorer-verktyget för att hjälpa dig att templatisera resursgruppen.

### <a name="deploy-to-azure-button"></a>Knappen Distribuera till Azure
Om du använder GitHub för källkontroll kan du placera en [distribution till Azure-knapp](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) i din README. MD, som möjliggör ett nyckelfärdigt distributionsgränssnitt till Azure. Du kan göra detta för alla enkla appar, men du kan utöka detta för att aktivera distribution av en hel resursgrupp genom att placera en azuredeploy.json-fil i databasroten. Den här JSON-filen, som innehåller resursgruppmallen, används av knappen Distribuera till Azure för att skapa resursgruppen. Ett exempel finns i [Exemplet På ToDoApp,](https://github.com/azure-appservice-samples/ToDoApp) som du ska använda i den här självstudien.

## <a name="get-the-sample-resource-group-template"></a>Hämta exempelresursgruppmallen
Så nu går vi rakt på tid.

1. Navigera till exemplet [med ToDoApp-apptjänsten.](https://github.com/azure-appservice-samples/ToDoApp)
2. Klicka på **Distribuera till Azure**readme.md.
3. Du tas till [platsen distribuera till azure](https://deploy.azure.com) och uppmanas att ange distributionsparametrar. Observera att de flesta fälten är ifyllda med databasnamnet och några slumpmässiga strängar åt dig. Du kan ändra alla fält om du vill, men det enda du behöver ange är SQL Server administrativa inloggning och lösenordet, klicka sedan på **Nästa**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Klicka sedan på **Distribuera** för att starta distributionsprocessen. När processen har slutförts http://todoappklickar du på *länken XXXX*.azurewebsites.net för att bläddra i det distribuerade programmet. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   Användargränssnittet skulle vara lite långsam när du först bläddrar till det eftersom apparna just har startat, men övertyga dig själv om att det är ett fullt fungerande program.
5. Klicka på länken Hantera på sidan **Distribuera** igen för att se det nya programmet i Azure Portal.
6. Klicka på länken resursgrupp i listrutan **Essentials.** Observera också att appen redan är ansluten till GitHub-databasen under **Externt projekt**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Observera att det redan finns två appar och en SQL-databas i resursgruppen i resursgruppen.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Allt som du just såg på några minuter är ett fullständigt distribuerat två mikrotjänstprogram, med alla komponenter, beroenden, inställningar, databas och kontinuerlig publicering, som konfigurerats av en automatiserad orkestrering i Azure Resource Manager. Allt detta gjordes av två saker:

* Knappen Distribuera till Azure
* azuredeploy.json i reporoten

Du kan distribuera samma program tiotals, hundratals eller tusentals gånger och har exakt samma konfiguration varje gång. Repeterbarheten och förutsägbarheten för den här metoden gör att du kan distribuera storskaliga program med lätthet och förtroende.

## <a name="examine-or-edit-azuredeployjson"></a>Undersök (eller redigera) AZUREDEPLOY. Json
Nu ska vi titta på hur GitHub-databasen har konfigurerats. Du kommer att använda JSON-redigeraren i Azure .NET SDK, så om du inte redan har installerat [Azure .NET SDK 2.6](https://azure.microsoft.com/downloads/)gör du det nu.

1. Klona [ToDoApp-databasen](https://github.com/azure-appservice-samples/ToDoApp) med ditt favoritgit-verktyg. I skärmdumpen nedan gör jag detta i Team Explorer i Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Öppna azuredeploy.json från databasroten i Visual Studio. Om fönstret JSON-disposition inte visas måste du installera Azure .NET SDK.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Jag tänker inte beskriva varje detalj i JSON-formatet, men avsnittet [Mer resurser](#resources) har länkar för att lära mig resursgruppmallens språk. Här ska jag bara visa dig de intressanta funktioner som kan hjälpa dig att komma igång med att göra din egen anpassade mall för appdistribution.

### <a name="parameters"></a>Parametrar
Ta en titt på parameteravsnittet för att se att de flesta av dessa parametrar är vad knappen **Distribuera till Azure** uppmanar dig att mata in. Platsen bakom knappen **Distribuera till Azure** fyller i indatagränssnittet med hjälp av de parametrar som definierats i azuredeploy.json. Dessa parametrar används i hela resursdefinitionerna, till exempel resursnamn, egenskapsvärden osv.

### <a name="resources"></a>Resurser
I resursnoden kan du se att 4 resurser på den högsta nivån har definierats, inklusive en SQL Server-instans, en App Service-plan och två appar. 

#### <a name="app-service-plan"></a>App Service-plan
Låt oss börja med en enkel rotnivåresurs i JSON. I JSON-dispositionen klickar du på apptjänstplanen som heter **[hostingPlanName]** för att markera motsvarande JSON-kod. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Observera att `type` elementet anger strängen för en App Service-plan (det kallades en servergrupp för länge, länge sedan) och att andra element och egenskaper fylls i med hjälp av de parametrar som definierats i JSON-filen och att den här resursen inte har några kapslade resurser.

> [!NOTE]
> Observera också att `apiVersion` värdet för anger vilken version av REST API:et som ska användas JSON-resursdefinitionen `{}`med, och det kan påverka hur resursen ska formateras inuti . 
> 
> 

#### <a name="sql-server"></a>SQL Server
Klicka sedan på SQL Server-resursen **SQLServer** i JSON Outline.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Observera följande om den markerade JSON-koden:

* Användningen av parametrar säkerställer att de skapade resurserna namnges och konfigureras på ett sätt som gör dem överensstämma med varandra.
* SQLServer-resursen har två kapslade resurser, `type`var och en har olika värde för .
* De kapslade `“resources”: […]`resurserna inuti , där databasen och `dependsOn` brandväggsreglerna definieras, har ett element som anger resurs-ID för SQLServer-resursen på rotnivå. Detta talar om för Azure Resource Manager, "innan du skapar den här resursen måste andra resurser redan finnas. och om den andra resursen definieras i mallen skapar du den först".
  
  > [!NOTE]
  > Detaljerad information om hur `resourceId()` du använder funktionen finns i [Azure Resource Manager Template Functions](../azure-resource-manager/templates/template-functions-resource.md#resourceid).
  > 
  > 
* Effekten av `dependsOn` elementet är att Azure Resource Manager kan veta vilka resurser som kan skapas parallellt och vilka resurser som måste skapas sekventiellt. 

#### <a name="app-service-app"></a>App Service-app
Nu, låt oss gå vidare till själva apps själva, som är mer komplicerade. Klicka på appen [variabler('apiSiteName')] i JSON-dispositionen för att markera dess JSON-kod. Du kommer att märka att saker och ting blir mycket mer intressant. För detta ändamål ska jag prata om funktionerna en efter en:

##### <a name="root-resource"></a>Rotresurs
Appen är beroende av två olika resurser. Det innebär att Azure Resource Manager skapar appen först när både App Service-planen och SQL Server-instansen har skapats.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Appinställningar
Appinställningarna definieras också som en kapslad resurs.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

I `properties` elementet `config/appsettings`för har du två `"<name>" : "<value>"`appinställningar i formatet .

* `PROJECT`är en [KUDU-inställning](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) som talar om för Azure-distributionen vilket projekt som ska användas i en Visual Studio-lösning med flera projekt. Jag kommer att visa dig senare hur källkontroll är konfigurerad, men eftersom ToDoApp-koden finns i en Visual Studio-lösning med flera projekt behöver vi den här inställningen.
* `clientUrl`är helt enkelt en appinställning som programkoden använder.

##### <a name="connection-strings"></a>Anslutningssträngar
Anslutningssträngarna definieras också som en kapslad resurs.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

I `properties` elementet `config/connectionstrings`för definieras även varje anslutningssträng som ett namn:värdepar med det specifika formatet `"<name>" : {"value": "…", "type": "…"}`. För `type` elementet `MySql`är möjliga `SQLServer` `SQLAzure`värden `Custom`, , och .

> [!TIP]
> Om du vill ha en slutgiltig lista över anslutningssträngtyper kör du följande kommando i Azure PowerShell: \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Källkontroll
Källkontrollinställningarna definieras också som en kapslad resurs. Azure Resource Manager använder den här resursen `IsManualIntegration` för att konfigurera kontinuerlig publicering (se förbehåll senare) och även för att starta distributionen av programkod automatiskt under bearbetningen av JSON-filen.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl`och `branch` bör vara ganska intuitivt och bör peka på Git-arkivet och namnet på grenen att publicera från. Återigen definieras dessa av indataparametrar. 

Observera i `dependsOn` det element som, förutom själva `sourcecontrols/web` appresursen, också beror på `config/appsettings` och `config/connectionstrings`. Detta beror `sourcecontrols/web` på att när den är konfigurerad, azure-distributionsprocessen automatiskt försöker distribuera, bygga och starta programkoden. Om du infogar det här beroendet kan du därför se till att programmet har åtkomst till de appinställningar och anslutningssträngar som krävs innan programkoden körs. 

> [!NOTE]
> Observera också `IsManualIntegration` att `true`är inställd på . Den här egenskapen är nödvändig i den här självstudien eftersom du faktiskt inte äger GitHub-databasen och därför inte faktiskt kan bevilja behörighet till Azure för att konfigurera kontinuerlig publicering från [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (d.v.s. skicka automatiska databasuppdateringar till Azure). Du kan bara `false` använda standardvärdet för den angivna databasen om du har konfigurerat ägarens GitHub-autentiseringsuppgifter i [Azure-portalen](https://portal.azure.com/) tidigare. Med andra ord, om du har konfigurerat källkontroll till GitHub eller BitBucket för någon app i [Azure Portal](https://portal.azure.com/) tidigare, med hjälp av dina användaruppgifter, kommer Azure att komma ihåg autentiseringsuppgifterna och använda dem när du distribuerar en app från GitHub eller BitBucket i framtiden. Men om du inte redan har gjort detta misslyckas distributionen av JSON-mallen när Azure Resource Manager försöker konfigurera appens källkontrollinställningar eftersom det inte kan logga in på GitHub eller BitBucket med databasägarens autentiseringsuppgifter.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Jämföra JSON-mallen med distribuerad resursgrupp
Här kan du gå igenom alla appens blad i [Azure Portal](https://portal.azure.com/), men det finns ett annat verktyg som är lika användbart, om inte mer. Gå till förhandsversionen av [Azure Resource Explorer,](https://resources.azure.com) som ger dig en JSON-representation av alla resursgrupper i dina prenumerationer, eftersom de faktiskt finns i Azure-backend. Du kan också se hur resursgruppens JSON-hierarki i Azure motsvarar hierarkin i mallfilen som används för att skapa den.

När jag till exempel går till [Azure Resource Explorer-verktyget](https://resources.azure.com) och expanderar noderna i utforskaren kan jag se resursgruppen och rotnivåresurserna som samlas in under deras respektive resurstyper.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Om du detaljgransar ned till en app bör du kunna se information om appkonfiguration som liknar skärmbilden nedan:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Återigen bör de kapslade resurserna ha en hierarki som är mycket lik dem i din JSON-mallfil, och du bör se appinställningarna, anslutningssträngarna osv., korrekt återspeglade i JSON-fönstret. Frånvaron av inställningar här kan tyda på ett problem med din JSON-fil och kan hjälpa dig att felsöka din JSON-mallfil.

## <a name="deploy-the-resource-group-template-yourself"></a>Distribuera resursgruppsmallen själv
Knappen **Distribuera till Azure** är bra, men du kan distribuera resursgruppmallen i azuredeploy.json endast om du redan har pushat azuredeploy.json till GitHub. Azure .NET SDK innehåller också verktygen för att distribuera alla JSON-mallfiler direkt från din lokala dator. Gör så här:

1. Klicka på **Arkiv** > **nytt** > **projekt**i Visual Studio.
2. Klicka på **Visual C#** > **Cloud** > **Azure Resource Group**och klicka sedan på **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. Välj **Tom mall** i **Välj Azure-mall**och klicka på **OK**.
4. Dra azuredeploy.json till **mappen Mall** för det nya projektet.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. Öppna den kopierade azuredeploy.json från Solution Explorer.
6. Bara för demonstrationens skull, låt oss lägga till några vanliga Application Insight resurser till vår JSON fil, genom att klicka på **Lägg till resurs**. Om du bara är intresserad av att distribuera JSON-filen går du till distributionsstegen.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Välj **Programstatistik för Webbappar**och kontrollera sedan att en befintlig App Service-plan och app är markerad och klicka sedan på **Lägg till**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Nu kan du se flera nya resurser som, beroende på resursen och vad den gör, har beroenden av antingen App Service-planen eller appen. Dessa resurser är inte aktiverade av deras befintliga definition och du kommer att ändra det.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. I JSON-dispositionen klickar du på **AppInsights Autoskala** för att markera JSON-koden. Det här är skalningsinställningen för apptjänstplanen.
9. Leta reda på `location` egenskaperna och ange `enabled` dem enligt beskrivningen nedan i den markerade JSON-koden.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. I JSON-dispositionen klickar du på **CPUHigh-appInsights** för att markera JSON-koden. Det här är en varning.
11. Leta `location` reda `isEnabled` på egenskaperna och ange dem enligt nedan. Gör samma sak för de andra tre varningar (lila lökar).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Du är nu redo att sätta in. Högerklicka på projektet och välj **Distribuera** > **ny distribution**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Logga in på ditt Azure-konto om du inte redan har gjort det.
14. Välj en befintlig resursgrupp i din prenumeration eller skapa en ny, välj **azuredeploy.json**och klicka sedan på **Redigera parametrar**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Du kommer nu att kunna redigera alla parametrar som definieras i mallfilen i en fin tabell. Parametrar som definierar standardvärden har redan sina standardvärden och parametrar som definierar en lista med tillåtna värden visas som listrytor.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Fyll i alla tomma parametrar och använd [GitHub-repoadressen för ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) i **repoUrl**. Klicka sedan på **Spara**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Automatisk skalning är en funktion som erbjuds på **standardnivå** eller högre, och aviseringar på plannivå är funktioner som erbjuds på **basic-nivå** eller högre, du måste ställa in **parametern sku** till **Standard** eller **Premium** för att se alla dina nya App Insights-resurser lysa upp.
    > 
    > 
16. Klicka på **Distribuera**. Om du har valt **Spara lösenord sparas**lösenordet i parameterfilen **i oformaterad text**. Annars blir du ombedd att ange databaslösenordet under distributionsprocessen.

Klart! Nu behöver du bara gå till [Azure Portal](https://portal.azure.com/) och Azure [Resource Explorer-verktyget](https://resources.azure.com) för att se de nya aviseringarna och inställningarna för automatisk skalning som lagts till i ditt JSON-distribuerade program.

Dina steg i det här avsnittet har huvudsakligen åstadkommit följande:

1. Förberedde mallfilen
2. Skapade en parameterfil som ska följa med mallfilen
3. Distribuerad mallfilen med parameterfilen

Det sista steget görs enkelt av en PowerShell-cmdlet. Om du vill se vad Visual Studio gjorde när det distribuerade ditt program öppnar du Skript\Deploy-AzureResourceGroup.ps1. Det finns en hel del kod där, men jag ska bara markera all relevant kod du behöver för att distribuera mallfilen med parameterfilen.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Den sista cmdlet, `New-AzureResourceGroup`, är den som faktiskt utför åtgärden. Allt detta bör visa dig att med hjälp av verktyg, är det relativt enkelt att distribuera ditt molnprogram förutsägbart. Varje gång du kör cmdlet på samma mall med samma parameterfil, kommer du att få samma resultat.

## <a name="summary"></a>Sammanfattning
I DevOps är repeterbarhet och förutsägbarhet nyckeln till en lyckad distribution av ett storskaligt program som består av mikrotjänster. I den här självstudien har du distribuerat ett program med två mikrotjänster till Azure som en enda resursgrupp med azure Resource Manager-mallen. Förhoppningsvis har det gett dig den kunskap du behöver för att börja konvertera ditt program i Azure till en mall och kan etablera och distribuera det förutsägbart. 

<a name="resources"></a>

## <a name="more-resources"></a>Fler resurser
* [Mallspråk för Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Skapa Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md)
* [Mallfunktioner för Azure Resource Manager](../azure-resource-manager/templates/template-functions.md)
* [Distribuera ett program med Azure Resource Manager-mall](../azure-resource-manager/templates/deploy-powershell.md)
* [Använda Azure PowerShell med Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Felsöka resursgruppsdistributioner i Azure](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="next-steps"></a>Nästa steg

Mer information om JSON-syntaxen och egenskaperna för resurstyper som distribueras i den här artikeln finns i:

* [Microsoft.Sql/servrar](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servrar/databaser](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servers/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Webbplatser](/azure/templates/microsoft.web/sites)
* [Microsoft.webbplatser/platser](/azure/templates/microsoft.web/sites/slots)
* [Microsoft.Insights/autoscalesettings Microsoft.Insights/autoscalesettings Microsoft.Insights/autoscalesettings Microsoft.](/azure/templates/microsoft.insights/autoscalesettings)