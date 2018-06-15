---
title: Etablera och distribuera mikrotjänster förutsägbart i Azure
description: Lär dig hur du distribuerar ett program som består av mikrotjänster i Azure App Service som en enhet och förutsägbart med hjälp av JSON resurs mallar och PowerShell-skript.
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
ms.openlocfilehash: 3719e037f1564411a8f94d1ca962ba1ef6b5d435
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23837101"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Etablera och distribuera mikrotjänster förutsägbart i Azure
Den här kursen visar hur du etablera och distribuera ett program som består av [mikrotjänster](https://en.wikipedia.org/wiki/Microservices) i [Azure App Service](/services/app-service/) som en enhet och förutsägbart JSON resurs mallar och PowerShell-skript. 

När etablering och distribuera hög skala program som består av hög frikopplad är mikrotjänster, repeterbarhet och förutsägbarhet viktigt att lyckas. [Azure Apptjänst](/services/app-service/) kan du skapa mikrotjänster med webbappar, mobilappar, API apps och logikappar. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) kan du hantera alla mikrotjänster som en enhet, tillsammans med resursberoenden, till exempel databasen och inställningar för åtkomstkontroll av datakälla. Du kan också distribuera sådant program med hjälp av JSON-mallarna och enkla PowerShell-skript. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-do"></a>Vad du ska göra
I självstudierna kommer du distribuera ett program som innehåller:

* Två webbappar (d.v.s. två mikrotjänster)
* En serverdel SQL-databas
* App-inställningar och anslutningssträngar källkontroll
* Programinsikter, aviseringar, autoskalning inställningar

## <a name="tools-you-will-use"></a>Verktyg som du vill använda
I den här självstudiekursen kommer du använda följande verktyg. Eftersom den inte är omfattande diskussion om verktyg ska behålla till scenariot för slutpunkt till slutpunkt och bara får du en kort introduktion till varje, och var du hittar mer information om den. 

### <a name="azure-resource-manager-templates-json"></a>Azure Resource Manager-mallar (JSON)
Varje gång du skapar en webbapp i Azure App Service, till exempel använder Azure Resource Manager en JSON-mall för att skapa hela resursgruppen med komponenten resurser. En komplex mall från den [Azure Marketplace](/marketplace) som den [skalbar WordPress](/marketplace/partners/wordpress/scalablewordpress/) app kan innehålla MySQL-databas, storage-konton, programtjänstplanen, webbprogram själva, Varningsregler, app-inställningar Autoskala inställningar och mer och alla dessa mallar är tillgängliga via PowerShell. Information om hur du hämtar och använder dessa mallar finns i [med hjälp av Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).

Mer information om Azure Resource Manager-mallar finns [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 för Visual Studio
Den senaste SDK innehåller förbättringar av stöd för Resource Manager-mall i JSON-redigerare. Används för att snabbt skapa en mall för resursen från grunden eller öppna en befintlig JSON-mall (t.ex en mall för hämtade galleriet) för redigering, Fyll i parameterfilen och även distribuera resursgruppen direkt från en Azure-resurs Gruppen lösning.

Mer information finns i [2.6 av Azure SDK för Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 eller senare
Från och med version 0.8.0, innehåller Azure PowerShell-installationen Azure Resource Manager-modulen utöver Azure-modulen. Den här nya modulen kan du skript distributionen av resursgrupper.

Mer information finns i [med hjälp av Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Azure Resource Explorer
Detta [preview verktyget](https://resources.azure.com) gör att du kan utforska JSON-definitioner för alla resursgrupper i din prenumeration och de enskilda resurserna. Du kan redigera JSON-definitioner av en resurs, ta bort en hel hierarki av resurser och skapa nya resurser i verktyget.  Informationen som är tillgänglig i det här verktyget är mycket användbart vid redigering av mallen eftersom den visar vilka egenskaper som du måste ange för en viss typ av resurs, rätt värden, osv. Du kan även skapa en resursgrupp i den [Azure Portal](https://portal.azure.com/), granska definitionerna JSON i explorer-verktyg som hjälper dig templatize resursgruppen.

### <a name="deploy-to-azure-button"></a>Distribuera till Azure-knappen
Om du använder GitHub för källkontroll kan du placera en [till Azure-knappen](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) i din viktigt. MD, vilket gör att en nyckelfärdig distribution användargränssnitt till Azure. Du kan göra detta för ett enkelt webbprogram, kan du utöka här om du vill aktivera distribution av en hel resursgrupp genom att placera en fil för azuredeploy.json i Lagringsplatsens rot. Den här JSON-fil som innehåller gruppen resource-mallen, kommer att användas av distribuera Azure-knappen för att skapa resursgruppen. Ett exempel finns i [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) exempel som ska användas i den här kursen.

## <a name="get-the-sample-resource-group-template"></a>Hämta exempelmall resurs grupp
Nu ska vi gå rätt till den.

1. Navigera till den [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) Apptjänst exempel.
2. Klicka på readme.md, **till Azure**.
3. Du förflyttas till den [distribuera till azure](https://deploy.azure.com) plats och ombeds indataparametrar för distribution. Observera att de flesta fält fylls i med databasnamnet på och vissa slumpmässiga strängar för dig. Du kan ändra alla fält om du vill, men endast saker du måste ange är administrativa SQL Server-inloggning och lösenord och klicka sedan på **nästa**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Klicka sedan på **distribuera** att starta distributionsprocessen. När processen körs kan slutföras, klickar du på http://todoapp*XXXX*. azurewebsites.net länk för att bläddra i det distribuerade programmet. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   Användargränssnittet ska gå lite långsamt när du först bläddrar till den eftersom apparna bara startar utan övertyga dig om att det är ett fullt fungerande program.
5. Klicka på tillbaka i sidan distribuera den **hantera** länk för att visa det nya programmet i Azure Portal.
6. I den **Essentials** listrutan, klickar du på länken resurs grupp. Observera också att webbprogrammet är redan ansluten till GitHub-lagringsplatsen under **externt projekt**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. I bladet för resursgruppen, Observera att det finns redan två webbprogram och en SQL-databasen i resursgruppen.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Allt som du just har sett i en kort stund ett fullständigt distribuerad två mikrotjänster program med alla komponenter, beroenden, inställningar, databas och kontinuerlig publicering ställs in med en automatisk orchestration i Azure Resource Manager. Detta görs genom att två saker:

* Distribuera till Azure-knappen
* azuredeploy.JSON i lagringsplatsen rot

Du kan distribuera den här samma program flera, hundratals eller tusentals gånger och har exakt samma konfiguration varje gång. Repeterbarhet och förutsägbarheten hos den här metoden kan du distribuera hög skala program med enkel och förtroende.

## <a name="examine-or-edit-azuredeployjson"></a>Granska (eller redigera) AZUREDEPLOY. JSON
Nu ska vi titta på hur GitHub-lagringsplatsen har ställts in. Du kommer att använda JSON-redigerare i Azure .NET SDK, så om du inte redan har installerat [Azure .NET SDK 2.6](https://azure.microsoft.com/downloads/), göra det nu.

1. Klona den [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) databas med hjälp av din favorit git-verktyget. På skärmbilden nedan göra jag detta i teamet Explorer i Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Öppna azuredeploy.json i Visual Studio Lagringsplatsens rot. Om du inte ser fönstret JSON-disposition, måste du installera Azure .NET SDK.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Ska inte beskrivs av JSON-format, men [mer resurser](#resources) avsnitt finns länkar för resursen grupp mallspråk. Här kan ska bara visa intressanta funktioner som kan hjälpa dig komma igång med att göra en egen anpassad mall för distribution av appen.

### <a name="parameters"></a>Parametrar
Ta en titt på avsnittet Parametrar för att se att de flesta av dessa parametrar är vad den **till Azure** knappen uppmanas du att ange. Webbplatsen bakom den **till Azure** knappen fyller indata användargränssnitt med de parametrar som definierats i azuredeploy.json. Dessa parametrar används i resursdefinitionerna resursnamn, egenskapsvärden, t.ex.

### <a name="resources"></a>Resurser
Du kan se att 4 översta resurser definieras, inklusive en SQL Server-instans, en apptjänstplan och två webbappar i noden resurser. 

#### <a name="app-service-plan"></a>App Service-plan
Låt oss börja med en enkel rotnivå resurs i JSON. Klicka på App Service-plan som heter i JSON-disposition **[hostingPlanName]** att markera motsvarande JSON-kod. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Observera att den `type` elementet anger strängen för en App Service-plan (den anropades en servergrupp med en lång, lång tid sedan) och andra element och egenskaper fylls i med de parametrar som definierats i JSON-filen och den här resursen har inte någon kapslade resurser.

> [!NOTE]
> Observera också att värdet för `apiVersion` anger vilken version av REST-API för att använda JSON resursdefinitionen med, och det kan påverka hur resursen ska formateras i Azure i `{}`. 
> 
> 

#### <a name="sql-server"></a>SQL Server
Klicka på SQL Server-resurs med namnet **SQLServer** i JSON-disposition.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Observera följande om den markerade JSON-koden:

* Användning av parametrar säkerställer att skapade resurserna är namnet och konfigurerats på ett sätt som gör dem överensstämmer med varandra.
* SQLServer-resursen har två kapslade resurser, var och en har ett annat värde för `type`.
* Kapslade resurser i `“resources”: […]`, där databasen och brandväggsreglerna definieras, har en `dependsOn` element som anger resurs-ID för rotnivå SQLServer resursen. Detta talar om Azure Resource Manager ”innan du skapar den här resursen som andra resurser måste redan finnas; och om den andra resursen har definierats i mallen, sedan skapa den första ”.
  
  > [!NOTE]
  > Detaljerad information om hur du använder den `resourceId()` finns [Azure Resource Manager mallen Functions](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid).
  > 
  > 
* Effekten av den `dependsOn` elementet är att Azure Resource Manager vet vilka resurser kan skapas parallellt och vilka resurser måste vara sekventiellt. 

#### <a name="web-app"></a>Webbapp
Nu ska vi gå vidare till de faktiska webbappar själva, vilket är mer komplicerad. Klicka på [variables('apiSiteName')] webbappen i JSON-disposition att markera sin JSON-kod. Lägg märke till att saker får mycket mer intressant. För detta ändamål ska jag berätta om funktionerna i taget:

##### <a name="root-resource"></a>Rot-resurs
Webbprogrammet beror på två olika resurser. Det innebär att Azure Resource Manager skapar webbappen endast när både App Service-plan och SQL Server-instansen har skapats.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>App-inställningar
App-inställningar också definieras som en kapslad resurs.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

I den `properties` element för `config/appsettings`, du har två appinställningar i formatet `“<name>” : “<value>”`.

* `PROJECT`är en [KUDU inställningen](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) som talar om Azure-distribution som projektet ska användas i en Visual Studio-lösning med flera projekt. Jag får du lära dig hur källkontrollen har konfigurerats, men eftersom ToDoApp koden i ett Visual Studio-lösning med flera projekt kan vi behöver den här inställningen.
* `clientUrl`är helt enkelt en app inställningen som programkoden använder.

##### <a name="connection-strings"></a>Anslutningssträngar
Anslutningssträngar också definieras som en kapslad resurs.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

I den `properties` element för `config/connectionstrings`, varje anslutningssträngen har definierats som ett namn: värde-par med specifika format `“<name>” : {“value”: “…”, “type”: “…”}`. För den `type` elementet möjliga värden är `MySql`, `SQLServer`, `SQLAzure`, och `Custom`.

> [!TIP]
> Kör följande kommando i Azure PowerShell för en fullständig förteckning över sträng anslutningstyper: \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Källkontrollen
Inställningar för källkontrollen också definieras som en kapslad resurs. Azure Resource Manager använder den här resursen för att konfigurera kontinuerlig publicering (se begränsning på `IsManualIntegration` senare) samt att startar distributionen av programkoden automatiskt under bearbetning av JSON-filen.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl`och `branch` bör vara ganska intuitiva och måste peka på Git-lagringsplats och namnet på grenen publicera från. Dessa definieras igen av indataparametrar. 

Observera i den `dependsOn` element att förutom app webbresursen, `sourcecontrols/web` beror också på `config/appsettings` och `config/connectionstrings`. Detta beror på att när `sourcecontrols/web` är konfigurerad, Azure distributionsprocessen kommer automatiskt att försöka distribuera, skapa och starta programkoden. Därför hjälper lägga till detta beroende dig att kontrollera att programmet har åtkomst till de nödvändiga app-inställningar och anslutningssträngar innan programkoden som körs. 

> [!NOTE]
> Observera också att `IsManualIntegration` är inställd på `true`. Den här egenskapen är nödvändiga i den här självstudiekursen eftersom du inte verkligen äger GitHub-lagringsplatsen och därför kan inte faktiskt bevilja behörighet till Azure för att konfigurera kontinuerlig publicering från [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (d.v.s. push automatisk databasen uppdateringar till Azure). Du kan använda standardvärdet `false` för den angivna databasen endast om du har konfigurerat ägarens GitHub autentiseringsuppgifter i den [Azure-portalen](https://portal.azure.com/) innan. Med andra ord, om du har ställt in källkontrollen till GitHub eller BitBucket för en app i den [Azure Portal](https://portal.azure.com/) tidigare med ditt användar-autentiseringsuppgifter, och sedan Azure kommer ihåg autentiseringsuppgifterna och använda dem när du distribuerar en app från GitHub eller BitBucket i framtiden. Men om du inte redan har gjort det, att distribution av JSON-mall misslyckas när Azure Resource Manager försöker konfigurera inställningar för webbappens källkontrollen eftersom den inte kan logga in GitHub eller BitBucket med databasen ägarens autentiseringsuppgifter.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Jämför JSON-mall med distribuerade resursgruppen.
Här kan du gå igenom alla webbappens blad i den [Azure Portal](https://portal.azure.com/), men det finns ett annat verktyg som inte är precis som användbart, om mer. Gå till den [resursutforskaren Azure](https://resources.azure.com) preview verktyg som ger dig en JSON-representation av alla resursgrupper i dina prenumerationer som de finns i Azure-serverdel. Du kan också se hur den resursgrupp JSON-hierarki i Azure motsvarar hierarkin i mallfilen som används för att skapa den.

Till exempel när jag går du till den [resursutforskaren Azure](https://resources.azure.com) verktyg och Expandera noderna i Utforskaren, syns resursgruppen och resurserna rotnivå som samlas in under deras respektive resurstyper.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Om du öka detaljnivån till en webbapp du ska kunna se web app konfigurationsinformation liknar den nedan skärmbild:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Återigen kapslade resurser bör ha en hierarki som liknar de som finns i mallen JSON-filen och du bör se app-inställningar, anslutningssträngar, etc., avspeglas korrekt i fönstret JSON. Avsaknad av inställningar här kan tyda på ett problem med JSON-filen och kan hjälpa dig att felsöka en JSON-fil i mallen.

## <a name="deploy-the-resource-group-template-yourself"></a>Distribuera mallen resurs gruppen själv
Den **till Azure** knappen är bra, men du kan distribuera resource group mallen i azuredeploy.json endast om du redan har pushas azuredeploy.json i GitHub. Azure .NET SDK innehåller dessutom verktyg som du kan distribuera en JSON-mallfil direkt från din lokala dator. Följ stegen nedan om du vill göra detta:

1. I Visual Studio klickar du på **filen** > **ny** > **projekt**.
2. Klicka på **Visual C#** > **moln** > **Azure-resursgrupp**, klicka på **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. I **Välj Azure-mall**väljer **tom mall** och på **OK**.
4. Dra azuredeploy.json i den **mallen** mapp med det nya projektet.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. Öppna den kopierade azuredeploy.json i Solution Explorer.
6. Precis som för enkelhetens bevis på ska vi lägga till vissa standard Application Insights-resurser till vårt JSON-fil genom att klicka på **Lägg till resurs**. Om du är intresserad av distribution av JSON-fil, går du vidare till steg för att distribuera.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Välj **Programinsikter för Web Apps**, kontrollera att en befintlig App Service-plan och webb-app är markerad och klicka sedan på **Lägg till**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Du ska nu kunna se flera nya resurser som, beroende på resursen och syfte, är beroende av App Service-plan eller webbprogrammet. Dessa resurser är inte aktiverade som deras befintliga definitionen och du ska ändra som.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. Klicka på i JSON-disposition **appInsights Autoskala** att markera sin JSON-kod. Det här är inställningen skala för din programtjänstplan.
9. Leta upp i den markerade JSON-koden i `location` och `enabled` egenskaper och ange dem som visas nedan.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. Klicka på i JSON-disposition **CPUHigh appInsights** att markera sin JSON-kod. Detta är en varning.
11. Leta upp den `location` och `isEnabled` egenskaper och ange dem som visas nedan. Gör samma sak för andra tre aviseringar (lila lökar).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Nu är du redo att distribuera. Högerklicka på projektet och välj **distribuera** > **ny distribution**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Logga in på ditt Azure-konto om du inte redan gjort det.
14. Välj en befintlig resursgrupp i din prenumeration eller skapa ett nytt en, Välj **azuredeploy.json**, och klicka sedan på **redigera parametrar**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Du ska nu kunna redigera de parametrar som angetts i mallfilen i en bra tabell. Parametrar som definierar standardvärden har redan sina standardvärden och parametrar som definierar en lista över tillåtna värden som ska visas som nedrullningsbara listorna.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Fyll i alla tomma parametrar och använda den [GitHub-repo-adress för ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) i **repoUrl**. Klicka på **spara**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Autoskalning är en funktion som erbjuds i **Standard** nivå eller högre och planera nivå aviseringar finns funktioner som erbjuds i **grundläggande** tjänstnivån eller högre, måste du ange den **sku** parameter att **Standard** eller **Premium** för att visa alla nya App Insights resurser lysa.
    > 
    > 
16. Klicka på **distribuera**. Om du har valt **spara lösenord**, lösenordet sparas i parameterfilen **i klartext**. Annars kommer du ombedd att ange lösenordet för databasen under distributionsprocessen.

Klart! Nu måste du gå till den [Azure Portal](https://portal.azure.com/) och [resursutforskaren Azure](https://resources.azure.com) verktyg för att se nya aviseringar och Autoskala inställningar har lagts till i din JSON distribuerat program.

Steg i det här avsnittet göra huvudsakligen följande:

1. Förbereda mallfilen
2. Skapa en parameterfil att gå med mallfilen
3. Distribuera mallfil med parametern

Det sista steget görs enkelt genom en PowerShell-cmdlet. Öppna Scripts\Deploy AzureResourceGroup.ps1 om du vill se vad Visual Studio gjorde när det distribuerade programmet. Det finns en mängd kod det, men ska bara markera alla viktig kod som du behöver distribuera mallfil med parametern.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Senaste cmdlet, `New-AzureResourceGroup`, är det som faktiskt utför åtgärden. Du bör allt detta visar att det med hjälp av verktygsuppsättning är relativt enkelt att distribuera programmet molnet förutsägbart. Varje gång du kör cmdleten på samma mall med samma parameterfil ska du få samma resultat.

## <a name="summary"></a>Sammanfattning
DevOps är repeterbarhet och förutsägbarhet nycklar till en lyckad distribution av en hög skala program består av mikrotjänster. Du har distribuerat en två-mikrotjänster programmet till Azure som en enskild resursgrupp med hjälp av Azure Resource Manager-mall i den här självstudiekursen. Förhoppningsvis har det angivna kunskap du behöver för att starta konvertera ditt program i Azure till en mall och etablera och distribuera den förutsägbart. 

<a name="resources"></a>

## <a name="more-resources"></a>Fler resurser
* [Språk för Azure Resource Manager-mallen](../azure-resource-manager/resource-group-authoring-templates.md)
* [Redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure Resource Manager Mallfunktioner](../azure-resource-manager/resource-group-template-functions.md)
* [Distribuera ett program med Azure Resource Manager-mall](../azure-resource-manager/resource-group-template-deploy.md)
* [Använda Azure PowerShell med Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Felsökning av resursgruppen distributioner i Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md)

