---
title: "Kom igång med API Apps och ASP.NET i Apptjänst | Microsoft Docs"
description: "Lär dig att skapa, distribuera och använda en ASP.NET API-app i Azure Apptjänst med hjälp av Visual Studio 2015."
services: app-service\api
documentationcenter: .net
author: tdykstra
manager: erikre
editor: 
ms.assetid: ddc028b2-cde0-4567-a6ee-32cb264a830a
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: hero-article
ms.date: 09/20/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: c7b4e39e01ae335c3e6a5cf9cb1efe8a64490e35
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-api-apps-aspnet-and-swagger-in-azure-app-service"></a>Kom igång med API Apps, ASP.NET och Swagger i Azure Apptjänst
[!INCLUDE [selector](../../includes/app-service-api-get-started-selector.md)]

Det här är den första i en serie kurser som visar hur du använder funktioner i Azure Apptjänst som är till hjälp för att utveckla och vara värd för RESTful-API:er.  I den här kursen ingår stöd för API-metadata i Swagger-format.

Du får lära dig:

* Skapa och distribuera [API Apps](app-service-api-apps-why-best-platform.md) i Azure Apptjänst med hjälp av verktyg som är inbyggda i Visual Studio 2015.
* Automatisera API-identifiering med paketet Swashbuckle NuGet för att dynamiskt generera Swagger API-metadata.
* Använda Swagger API-metadata för att automatiskt generera klientkod till en API-app.

## <a name="sample-application-overview"></a>Översikt över exempelprogrammet
I den här kursen får du arbeta med ett enkelt exempelprogram med en att göra-lista. Programmet har en SPA-klientdel (Single-page Application), en ASP.NET Web API-mellannivå och en ASP.NET Web API-datanivå.

![Diagram över exempelprogram i API Apps](./media/app-service-api-dotnet-get-started/noauthdiagram.png)

Det här är en skärmbild av klientdelen i [AngularJS](https://angularjs.org/)

![Att göra-lista för exempelprogrammet i API Apps](./media/app-service-api-dotnet-get-started/todospa.png)

Visual Studio-lösningen innehåller tre projekt:

![](./media/app-service-api-dotnet-get-started/projectsinse.png)

* **ToDoListAngular** – Klientdelen: en AngularJS SPA som anropar mellannivån.
* **ToDoListAPI**  – Mellannivån: ett ASP.NET Web API-projekt som anropar datanivån för att utföra CRUD-åtgärder på arbetsuppgifter.
* **ToDoListDataAPI** – Datanivån: ett ASP.NET Web API-projekt som utför CRUD-åtgärder på arbetsuppgifter.

Arkitekturen med tre nivåer är en av många arkitekturer som du kan implementera med hjälp av API Apps och används här endast i demonstrationssyfte. Koden i varje nivå är så enkel som möjligt för att demonstrera API Appsnas funktioner. Datanivån använder till exempel serverminne i stället för en databas som persistencemekanism.

När du har slutfört den här kursen fungerar de två Web API-projekten i molnet i API Appsna i Apptjänst.

I nästa kurs i serien får du distribuera SPA-klientdelen i molnet.

## <a name="prerequisites"></a>Krav
* ASP.NET Web API – Instruktionerna i kursen förutsätter att du har grundläggande kunskaper i att arbeta med ASP.NET [Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) i Visual Studio.
* Azure-konto – Du kan [öppna ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
  
    Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto kan du gå till [Prova Apptjänst](https://azure.microsoft.com/try/app-service/). Där kan du omedelbart skapa en tillfällig startapp i Apptjänst – **inget kreditkort behövs** och du gör inga åtaganden.
* Visual Studio 2015 med [Azure SDK för .NET](https://azure.microsoft.com/downloads/archive-net-downloads/) – SDK installerar Visual Studio 2015 automatiskt om du inte redan har det.
  
  * I Visual Studio klickar du på Hjälp -> Om Microsoft Visual Studio och ser till att du har "Azure Apptjänst-verktyg v2.9.1" eller senare installerat.
    
    ![Azure App-verktyg version](./media/app-service-api-dotnet-get-started/apiversion.png)
    
    > [!NOTE]
    > Beroende på hur många av SDK-beroendena du redan har på datorn, kan det några minuter till en halvtimme och mer att installera SDK:n.
    > 
    > 

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet
1. Hämta databasen [Azure-Samples/app-service-api-dotnet-to-do-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list).
   
    Du kan klicka på knappen **Hämta ZIP** eller klona databasen på din lokala dator.
2. Öppna ToDoList-lösningen i Visual Studio 2015 eller 2013.
   
   1. Du måste kunna lita på varje lösning.
         ![Säkerhetsvarning](./media/app-service-api-dotnet-get-started/securitywarning.png)
3. Skapa lösningen (CTRL + SHIFT + B) om du vill återställa NuGet-paketen.
   
    Om du vill se hur programmet fungerar innan du distribuerar det, kan du köra det lokalt. Säkerställ att ToDoListDataAPI är ditt startprojekt och kör lösningen. Du bör förväntar dig att se ett HTTP 403-fel i webbläsaren.

## <a name="use-swagger-api-metadata-and-ui"></a>Använd Swagger API-metadata och -användargränssnitt
Stöd för [Swagger](http://swagger.io/) 2.0 API-metadata är inbyggt i Azure Apptjänst. Varje API-app kan ange en URL-slutpunkt som returnerar metadata för API:n i Swagger JSON-format. De metadata som returneras från slutpunkten kan användas för att generera klientkod.

Ett ASP.NET Web API-projekt kan dynamiskt generera Swagger-metadata med hjälp av [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet-paketet. Swashbuckle NuGet-paketet är redan installerat i de ToDoListDataAPI- och ToDoListAPI-projekt som du har hämtat.

I det här avsnittet av kursen får du titta på de genererade Swagger 2.0-metadata och sedan får du prova på ett testanvändargränssnitt som är baserat på dessa Swagger-metadata.

1. Ange ToDoListDataAPI-projektet (**inte** ToDoListAPI-projektet) som startprojekt.
   
    ![Ange ToDoDataAPI som startprojekt](./media/app-service-api-dotnet-get-started/startupproject.png)
2. Tryck på F5 eller klicka på **Felsök > Starta felsökning** för att köra projektet i felsökningsläge.
   
    Webbläsaren öppnas och visar sidan HTTP 403-fel.
3. Lägg till `swagger/docs/v1` i slutet av raden i webbläsarens adressfält och tryck sedan på RETUR. (URL:en är `http://localhost:45914/swagger/docs/v1`.)
   
    Detta är den standard-URL som används av Swashbuckle för att returnera Swagger 2.0 JSON-metadata för API:n.
   
    Om du använder Internet Explorer uppmanar webbläsaren dig att hämta en *v1.json*-fil.
   
    ![Hämta JSON-metadata i IE](./media/app-service-api-dotnet-get-started/iev1json.png)
   
    Om du använder Chrome, Firefox eller Microsoft Edge visar webbläsaren JSON i webbläsarfönstret. Olika webbläsare hanterar JSON på olika sätt och ditt webbläsarfönster ser kanske inte precis likadant ut som exemplet.
   
    ![JSON-metadata i Chrome](./media/app-service-api-dotnet-get-started/chromev1json.png)
   
    I följande exempel visas den första delen av Swagger-metadata för API:n, med definitionen för Hämta-metoden. Dessa metadata styr det Swagger-användargränssnitt som du använder i de följande stegen, och du får använda dem i ett senare avsnitt av kursen för att automatiskt generera klientkod.
   
        {
          "swagger": "2.0",
          "info": {
            "version": "v1",
            "title": "ToDoListDataAPI"
          },
          "host": "localhost:45914",
          "schemes": [ "http" ],
          "paths": {
            "/api/ToDoList": {
              "get": {
                "tags": [ "ToDoList" ],
                "operationId": "ToDoList_GetByOwner",
                "consumes": [ ],
                "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
                "parameters": [
                  {
                    "name": "owner",
                    "in": "query",
                    "required": true,
                    "type": "string"
                  }
                ],
                "responses": {
                  "200": {
                    "description": "OK",
                    "schema": {
                      "type": "array",
                      "items": { "$ref": "#/definitions/ToDoItem" }
                    }
                  }
                },
                "deprecated": false
              },
4. Stäng webbläsaren och stoppa felsökning i Visual Studio.
5. I ToDoListDataAPI-projektet i **Solution Explorer** öppnar du *App_Start\SwaggerConfig.cs*-filen och rullar sedan ned till rad 174 och avkommenterar följande kod.
   
        /*
            })
        .EnableSwaggerUi(c =>
            {
        */
   
    *SwaggerConfig.cs*-filen skapas när du installerar Swashbuckle-paketet i ett projekt. Filen visar flera olika sätt att konfigurera Swashbuckle.
   
    Den kod som du har tagit bort kommentarstecken från aktiverar det Swagger-användargränssnitt som du använder under de följande stegen. När du skapar ett Web API-projekt med hjälp av mallen för API-app-projekt tas kommentarstecknen bort från koden som en säkerhetsåtgärd.
6. Kör projektet igen.
7. Lägg till `swagger` i slutet av raden i webbläsarens adressfält och tryck sedan på RETUR. (URL:en är `http://localhost:45914/swagger`.)
8. När Swaggers användargränssnittssida visas klickar du på **ToDoList** där tillgängliga metoder visas.
   
    ![Tillgängliga metoder för Swagger-användargränssnittet](./media/app-service-api-dotnet-get-started/methods.png)
9. Klicka på den första **Hämta**-knappen i listan.
10. I avsnittet **Parametrar** anger du en asterisk som värde för `owner`-parametern och klickar sedan på **Prova**.
    
    När du under senare kurser lägger till autentisering ger mellannivån det faktiska användar-ID som används till datanivån. För tillfället har alla uppgifter en asterisk som ägar-ID medan programmet körs utan att autentisering är aktiverad.
    
    ![Prova Swaggers användargränssnitt](./media/app-service-api-dotnet-get-started/gettryitout1.png)
    
    Swagger-användargränssnitt anropar Hämta-metoden för ToDoList och visar svarskoden och JSON-resultaten.
    
    ![Resultat av att prova Swaggers användargränssnitt](./media/app-service-api-dotnet-get-started/gettryitout.png)
11. Klicka på **Posta** och klicka sedan i rutan under **Modellschema**.
    
    När du klickar på modellschemat fylls indatarutan i på förhand och där kan du ange parametervärdet för Post-metoden. (Om det inte fungerar i Internet Explorer använder du en annan webbläsare eller anger parametervärdet manuellt i nästa steg.)  
    
    ![Prova att posta i Swaggers användargränssnitt](./media/app-service-api-dotnet-get-started/post.png)
12. Ändra JSON i indatarutan för `todo`-parametern så att det ser ut som följande exempel eller använd din egen beskrivningstext:
    
        {
          "ID": 2,
          "Description": "buy the dog a toy",
          "Owner": "*"
        }
13. Klicka på **Prova**.
    
    ToDoList API:n returnerar en HTTP 204-svarskod som indikerar att det lyckades.
14. Klicka på den första **Hämta**-knappen och klicka sedan på**Prova**-knappen i det avsnittet av sidan.
    
    Hämta-metodsvaret innehåller nu det nya att göra-objektet.
15. Valfritt: Prova också på Placera-, Ta bort- och Hämta med-ID-metoderna.
16. Stäng webbläsaren och stoppa felsökning i Visual Studio.

Swashbuckle fungerar med alla ASP.NET Web API-projekt. Om du vill lägga till generering av Swagger-metadata i ett befintligt projekt installerar du bara Swashbuckle-paketet.

> [!NOTE]
> Swagger-metadata innehåller ett unikt ID för varje API-åtgärd. Som standard kan Swashbuckle generera duplicerade Swagger-åtgärds-ID till dina kontrollantmetoder för Web API.  Det händer om din kontrollant har överbelastade http-metoder, exempelvis `Get()` och `Get(id)`. Information om hur du hanterar överbelastningar finns i [Anpassa Swashbuckle-genererade API-definitioner](app-service-api-dotnet-swashbuckle-customize.md). Om du skapar ett Web API-projekt i Visual Studio med mallen för Azure API Apps läggs koden som skapar unika åtgärds-ID automatiskt till i *SwaggerConfig.cs*-filen.  
> 
> 

## <a id="createapiapp"></a> Skapa en API-app i Azure och distribuera kod till den
I det här avsnittet använder du Azure-verktyg som är inbyggda i Visual Studio-guiden **Publicera webbplats** för att skapa en ny API-app i Azure. Sedan distribuerar du ToDoListDataAPI-projektet till den nya API-appen och anropar API:n genom att köra Swagger-användargränssnittet.

1. I **Solution Explorer** högerklickar du på ToDoListDataAPI-projektet och sedan på **Publicera**.
   
    ![Klicka på Publicera i Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu.png)
2. I steget **Profil** i guiden **Publicera webbplats** klickar du på  **Microsoft Azure Apptjänst**.
   
   ![Klicka på Azure Apptjänst i Publicera webbplats](./media/app-service-api-dotnet-get-started/selectappservice.png)
3. Logga in på ditt Azure-konto om du inte redan har gjort det eller uppdatera dina autentiseringsuppgifter om de har upphört att gälla.
4. I dialogrutan för Apptjänst väljer du den Azure-**prenumeration** du vill använda och klickar sedan på **Ny**.
   
    ![Klicka på Ny i dialogrutan för Apptjänst](./media/app-service-api-dotnet-get-started/clicknew.png)
   
    Fliken **Värd** i dialogrutan **Skapa Apptjänst** visas.
   
    Eftersom du distribuerar ett Web API-projekt där Swashbuckle är installerat, förutsätter Visual Studio att du vill skapa en API-App. Detta anges av rubriken för **API-appnamn** och av det faktum att listrutan **Ändra typ** är inställd på **API-app**.
   
    ![Typ av app i dialogrutan för Apptjänst](./media/app-service-api-dotnet-get-started/apptype.png)
5. Ange ett **API-appnamn** som är unikt för domänen *azurewebsites.net*. Du kan godkänna standardnamnet som Visual Studio föreslår.
   
    Om du anger ett namn som någon annan redan har använt visas ett rött utropstecken till höger.
   
    URL:en för API-appen blir `{API app name}.azurewebsites.net`.
6. I listrutan **Resursgrupp** klickar du på **Ny** och anger sedan "ToDoListGroup" eller ett annat namn om du föredrar det.
   
    En resursgrupp är en samling Azure-resurser, till exempel API Apps, databaser eller virtuella datorer.    När du jobbar med den här kursen är det bäst att skapa en ny resursgrupp eftersom du då i bara ett steg kan ta bort alla Azure-resurser som du skapar för kursen.
   
    I den här rutan kan du välja en befintlig [resursgrupp](../azure-resource-manager/resource-group-overview.md) eller skapa en ny genom att skriva in ett namn som skiljer sig från befintliga resursgrupper i din prenumeration.
7. Klicka på knappen **Ny** bredvid listrutan **Apptjänstplan**.
   
    Skärmbilden visar exempelvärden för **API-appnamn**, **Prenumeration** och **Resursgrupp** – dina värden skiljer sig från dessa.
   
    ![Skapa en dialogruta för Apptjänst](./media/app-service-api-dotnet-get-started/createas.png)
   
    I följande steg får du skapa en Apptjänstplan för den nya resursgruppen. I en Apptjänstplan anges beräkningsresurserna som API-appen körs på. Om du till exempel väljer den kostnadsfria nivån körs API-appen på delade virtuella datorer medan den körs på dedikerade virtuella datorer för vissa betalnivåer. Information om Apptjänstplaner finns i [Översikt över Apptjänstplaner](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
8. I dialogrutan **Konfigurera Apptjänstplan** anger du "ToDoListPlan" eller ett annat namn om du föredrar det.
9. I listrutan **Plats** väljer du den plats som är närmast dig.
   
    Den här inställningen anger vilket Azure-datacenter appen ska köras i. Välj en plats i närheten av dig för att minimera [svarstiden](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).
10. I listrutan **Nivå** klickar du på **Kostnadsfri**.
    
    För den här kursens syfte räcker det att välja den kostnadsfria prisnivån.
11. I dialogrutan **Konfigurera Apptjänstplan** klickar du på **OK**.
    
    ![Klicka på OK i Konfigurera Apptjänstplan](./media/app-service-api-dotnet-get-started/configasp.png)
12. I dialogrutan **Skapa Apptjänst** klickar du på **Skapa**.
    
    ![Klicka på Skapa i dialogrutan Skapa Apptjänst](./media/app-service-api-dotnet-get-started/clickcreate.png)
    
    Visual Studio skapar API-appen och en publiceringsprofil som innehåller alla de nödvändiga inställningarna för API-appen. Sedan öppnas guiden **Publicera webbplats** som du använder för att distribuera projektet.
    
    Guiden **Publicera webbplats** öppnas på tabben **Anslutning** (se nedan).
    
    På fliken **Anslutning** pekar inställningarna för **servern** och **webbplatsnamnet** på din API-app. **Användarnamn** och **lösenord** är autentiseringsuppgifter för distribution som Azure skapar åt dig. Efter distributionen öppnar Visual Studio en webbläsare på **mål-URL:en** (som är det enda syftet för **mål-URL:er**).  
13. Klicka på **Nästa**.
    
    ![Klicka på Nästa på fliken Anslutning i Publicera webbplats](./media/app-service-api-dotnet-get-started/connnext.png)
    
    Nästa flik är fliken **Inställningar** (se nedan). Här kan du ändra fliken för versionskonfiguration så att den distribuerar en felsökningsversion för [fjärrfelsökning](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). På fliken finns också flera **Alternativ för filpublicering**:
    
    * Ta bort extra filer från destinationen
    * Förkompilera vid publicering
    * Undanta filer från mappen App_Data
    
    Under den här kursen behöver du ingen av dem. Utförliga förklaringar av vad de gör finns i [Så här distribuerar du ett webbprojekt med Publicera med ett klick i Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx).
14. Klicka på **Next**.
    
    ![Klicka på Nästa på fliken Inställningar i Publicera webbplats](./media/app-service-api-dotnet-get-started/settingsnext.png)
    
    Nästa är fliken **Förhandsgranskning** (se nedan), som ger dig en möjlighet att se vilka filer som ska kopieras från ditt projekt till API-appen. När du distribuerar ett projekt till en API-app som du redan har distribuerat till tidigare, kopieras bara ändrade filer. Om du vill se en lista över det som ska kopieras klickar du på knappen **Starta förhandsgranskning**.
15. Klicka på **Publicera**.
    
    ![Klicka på Publicera på fliken Förhandsgranska i Publicera webbplats](./media/app-service-api-dotnet-get-started/clickpublish.png)
    
    Visual Studio distribuerar ToDoListDataAPI-projektet till den nya API-appen. Fönstret **Utdata** loggar slutförd distribution och en "har skapats"-sida visas i ett webbläsarfönster som öppnas med API-appens URL.
    
    ![Utdatafönster visar slutförd distribution](./media/app-service-api-dotnet-get-started/deploymentoutput.png)
    
    ![Sida som visar att en ny API-app har skapats](./media/app-service-api-dotnet-get-started/appcreated.png)
16. Lägg till "swagger" till URL:en i webbläsarens adressfält och tryck på Retur. (URL:en är `http://{apiappname}.azurewebsites.net/swagger`.)
    
    Webbläsaren visar samma Swagger-användargränssnitt som du såg tidigare, men nu körs det i molnet. Prova Hämta-metoden och du ser att du är tillbaka till standardlistan med 2 att göra-objekt. De ändringar du gjort tidigare har sparats i minnet på den lokala datorn.
17. Öppna [Azure-portalen](https://portal.azure.com/).
    
    Azure-portalen är ett webbgränssnitt för att hantera Azure-resurser, till exempel API Apps.
18. Klicka på **Fler tjänster > Apptjänster**.
    
    ![Bläddra i Apptjänster](./media/app-service-api-dotnet-get-started/browseas.png)
19. Sök efter och klicka på din nya API-app på bladet **Apptjänster**. (I Azure-portalen kallas fönster som öppnas till höger *blad*.)
    
    ![Apptjänstblad](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)
    
    Två blad öppna. Det ena bladet visar en översikt över API-appen och det andra en lång lista över inställningar som du kan visa och ändra.
20. På bladet **Inställningar** letar du rätt på**API**-avsnittet och klickar på **API-definition**.
    
    ![API-definition på bladet Inställningar](./media/app-service-api-dotnet-get-started/apidefinsettings.png)
    
    På bladet **API-definition** kan du ange den URL som returnerar Swagger 2.0-metadata i JSON-format. När Visual Studio skapar API-appen anger den API-definitions-URL:en till det standardvärde för Swashbuckle-genererade metadata som du såg tidigare, vilket är API-appens grund-URL plus `/swagger/docs/v1`.
    
    ![URL för API-definition](./media/app-service-api-dotnet-get-started/apidefurl.png)
    
    När du väljer en API-app för att generera klientkod till den hämtar Visual Studio metadata från denna URL.

## <a id="codegen"></a> Generera klientkod för datanivån
En av fördelarna med att integrera Swagger i Azure API Apps är automatisk kodgenerering. Genererade klientklasser gör det enklare att skriva kod som anropar en API-app.

ToDoListAPI-projektet har redan genererat klientkoden, men i följande steg ska du ta bort den och återskapa den för att se hur du utför kodgenereringen.

1. I ToDoListAPI-projektet i Visual Studio **Solution Explorer** tar du bort mappen *ToDoListDataAPI*. **Varning: Ta endast bort mappen, inte ToDoListDataAPI-projektet.**
   
    ![Ta bort genererad klientkod](./media/app-service-api-dotnet-get-started/deletecodegen.png)
   
    Den här mappen har skapats genom att använda den kodgenereringsprocess som du nu ska gå igenom.
2. Högerklicka på ToDoListAPI-projektet och klicka sedan på **Lägg till > REST API-klient**.
   
    ![Lägg till REST API-klienten i Visual Studio](./media/app-service-api-dotnet-get-started/codegenmenu.png)
3. I dialogrutan **Lägg till REST API-klient** klickar du på **Swagger URL** och sedan på **Välj Azure-tillgång**.
   
    ![Välj Azure-tillgång](./media/app-service-api-dotnet-get-started/codegenbrowse.png)
4. I dialogrutan **Apptjänst** utökar du den resursgrupp som du använder under den här kursen och väljer din API-app. Klicka sedan på **OK**.
   
    ![Välj API-app för kodgenerering](./media/app-service-api-dotnet-get-started/codegenselect.png)
   
    Observera att när du går tillbaka till dialogrutan **Lägg till REST API-klient** har textrutan fyllts i med det värde för API-definitions-URL:en som du såg tidigare i portalen.
   
    ![API-definitions-URL](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)
   
   > [!TIP]
   > Ett annat sätt att hämta metadata för kodgenerering är att ange URL:en direkt i stället för att gå igenom dialogrutan. Om du vill generera klientkoden innan du distribuerar till Azure, kan du köra Web API-projektet lokalt, gå till den URL som innehåller Swagger JSON-filen, spara filen och använda alternativet **Välj en befintlig Swagger-metadatafil**.
   > 
   > 
5. I dialogrutan **Lägg till REST API-klient** klickar du på **OK**.
   
    Visual Studio skapar en mapp med samma namn som API-appen och genererar klientklasser.
   
    ![Koda filer för en genererad klient](./media/app-service-api-dotnet-get-started/codegenfiles.png)
6. Öppna *Controllers\ToDoListController.cs* i ToDoListAPI-projektet för att se koden på rad 40 som anropar API:n genom att använda den genererade klienten.
   
    Följande utdrag visar hur koden skapar instanser av klientobjektet och anropar Hämta-metoden.
   
        private static ToDoListDataAPI NewDataAPIClient()
        {
            var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
            return client;
        }
   
        public async Task<IEnumerable<ToDoItem>> Get()
        {
            using (var client = NewDataAPIClient())
            {
                var results = await client.ToDoList.GetByOwnerAsync(owner);
                return results.Select(m => new ToDoItem
                {
                    Description = m.Description,
                    ID = (int)m.ID,
                    Owner = m.Owner
                });
            }
        }
   
    Konstruktorparametern hämtar slutpunkts-URL:en från `toDoListDataAPIURL` appinställningen. I Web.config-filen anges det värdet till den lokala IIS Express-URL:en i API-projektet så att du kan köra programmet lokalt. Om du utelämnar konstruktorparametern är standardslutpunkten den URL som du skapade koden från.
7. Klientklassen kommer att genereras med ett annat namn baserat på namnet på din API-app. Ändra koden i *Controllers\ToDoListController.cs* så att typnamnet matchar det som har genererats i projektet. Om du till exempel har gett din API-app namnet ToDoListDataAPI071316 ändrar du den här koden:
   
        private static ToDoListDataAPI NewDataAPIClient()
        {
            var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));

till denna:

        private static ToDoListDataAPI071316 NewDataAPIClient()
        {
            var client = new ToDoListDataAPI071316(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));


## <a name="create-an-api-app-to-host-the-middle-tier"></a>Skapa en API-app som är värd för mellannivån
Tidigare [skapade du datanivå-API-appen och distribuerade kod till den](#createapiapp).  Nu gör du på samma sätt med mellannivå-API-appen.

1. I**Solution Explorer** högerklickar du på ToDoListAPI-projektet på mellannivå (inte ToDoListDataAPI på datanivå) och klickar sedan på **Publicera**.
   
    ![Klicka på Publicera i Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu2.png)
2. På fliken **Profil** i guiden **Publicera webbplats** klickar du på **Microsoft Azure Apptjänst**.
3. I dialogrutan **Apptjänst** klickar du på **Ny**.
4. På fliken **Värd** i dialogrutan **Skapa Apptjänst** godkänner du det förinställda **API-appnamnet** eller anger ett namn som är unikt i domänen *azurewebsites.net*.
5. Välj den Azure-**prenumeration** du har använt.
6. I listrutan **Resursgrupp** väljer du samma resursgrupp som du skapade tidigare.
7. I listrutan **Apptjänstplan** väljer du samma plan som du skapade tidigare. Den anger det värdet som standard.
8. Klicka på **Skapa**.
   
    Visual Studio skapar API-appen, en publiceringsprofil för den och visar steget **Anslutning** i guiden **Publicera webbplats**.
9. I steget **Anslutning** i guiden **Publicera webbplats** klickar du på **Publicera**.
   
   Visual Studio distribuerar ToDoListAPI-projektet till den nya API-appen och öppnar en webbläsare på API-appens URL. Sidan "har skapats" visas.

## <a name="configure-the-middle-tier-to-call-the-data-tier"></a>Konfigurera mellannivån så att den anropar datanivån
Om du nu anropade mellannivå-API-appen skulle den försöka anropa datanivån med den localhost-URL som finns kvar i Web.config-filen. I det här avsnittet anger du datanivå-API-appens URL i en miljöinställning i API-appen på mellannivå. När koden i mellannivå-API-appen hämtar inställningen för datanivå-URL åsidosätter miljöinställningen det som ingår i  Web.config-filen.

1. Gå till [Azure-portalen](https://portal.azure.com/) och navigera sedan till bladet **API-app** för den API-app som du skapade för att vara värd för TodoListAPI-projektet (mellannivån).
2. På bladet för API-appens **inställningar** klickar du på **Programinställningar**.
3. I API-appens blad **Programinställningar** rullar du ned till avsnittet **Appinställningar** och lägger till följande nyckel och värde. Värdet kommer att vara URL för den första API-app som du publicerade i de här självstudierna.
   
   | **Nyckel** | toDoListDataAPIURL |
   | --- | --- |
   | **Värde** |https://{namnet på din API på datanivå}.azurewebsites.net |
   | **Exempel** |https://todolistdataapi.azurewebsites.net |
4. Klicka på **Spara**.
   
    ![Klicka på Spara för app-inställningar](./media/app-service-api-dotnet-get-started/asinportal.png)
   
    När koden körs i Azure åsidosätter det här värdet URL:en för den lokala värden som finns i filen Web.config.

## <a name="test"></a>Testa
1. I ett webbläsarfönster bläddrar du till URL:en för den nya API-appen på mellannivå som du nyss skapade för ToDoListAPI. Du kommer dit genom att klicka på URL:en på API-appens huvudblad i portalen.
2. Lägg till "swagger" till URL:en i webbläsarens adressfält och tryck på Retur. (URL:en är `http://{apiappname}.azurewebsites.net/swagger`.)
   
    Webbläsaren visar samma Swagger-användargränssnitt som du tidigare såg för ToDoListDataAPI, men nu är `owner` inte ett obligatoriskt fält för Hämta-åtgärden eftersom API-appen på mellannivå skickar det värdet till API-appen på datanivå åt dig. (När du genomför autentiseringskurserna skickar mellannivån faktiska användar-ID för `owner`-parametern. För tillfället hårdkodar det en asterisk.)
3. Prova, Hämta-metoden och de andra metoderna för att verifiera att API-appen på mellannivå anropar API-appen på datanivå.
   
    ![Hämta-metoden i Swaggers användargränssnitt](./media/app-service-api-dotnet-get-started/midtierget.png)

## <a name="troubleshooting"></a>Felsökning
Om du stöter på problem när du går igenom den här kursen får du några felsökningsidéer här:

* Säkerställ att du använder den senaste versionen av [Azure SDK för .NET](http://go.microsoft.com/fwlink/?linkid=518003).
* Två av projektnamnen liknar varandra (ToDoListAPI, ToDoListDataAPI). Om saker och ting inte ser ut som i anvisningarna när du arbetar med ett projekt ska du kontrollera att du har öppnat rätt projekt.
* Om du befinner dig i ett företagsnätverk och försöker distribuera till Azure Apptjänst via en brandvägg ska du kontrollera att portarna 443 och 8172 är öppna för webbdistribution. Om du inte kan öppna de portarna kan du använda andra metoder för distribution.  Se [Distribuera din app till Azure Apptjänst](../app-service-web/web-sites-deploy.md).
* Fel av typen "ruttnamn måste vara unika" – du kan få dem om du av misstag distribuerar fel projekt till en API-app och senare distribuerar rätt projekt till den. Åtgärda detta genom att omdistribuera rätt projekt till API-appen och genom att på tabben **Inställningar** i guiden **Publicera webbplats** välja **Ta bort extra filer från destinationen**.

När din ASP.NET API-app körs i Azure Apptjänst vill du kanske veta mer om Visual Studio-funktioner som förenklar felsökningen. Information om bland annat loggning och fjärrfelsökning finns i [Felsöka Azure Apptjänst-appar i Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## <a name="next-steps"></a>Nästa steg
Du har lärt dig hur du distribuerar befintliga Web API-projekt till API Apps, genererar klientkod till API Apps och använder API Apps från .NET-klienter. I nästa kurs i den här serien får du lära dig att [använda API Apps från JavaScript-klienter med hjälp av CORS](app-service-api-cors-consume-javascript.md).

Mer information om klientkodgenerering finns i databasen [Azure/AutoRest](https://github.com/azure/autorest) på GitHub.com. Om du behöver hjälp med att använda den genererade klienten öppnar du ett [problem i databasen AutoRest](https://github.com/azure/autorest/issues).

Om du vill skapa nya API-app-projekt från grunden ska du använda mallen för **Azure API Apps**

![Mall för API Apps i Visual Studio](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

Projektmallen för **Azure API Apps** motsvarar att välja den **tomma** ASP.NET 4.5.2-mallen, markera kryssrutan för att lägga till stöd för Web API och installera Swashbuckle NuGet-paketet. Mallen lägger dessutom till en del Swashbuckle-konfigurationskod som är skapad för att förhindra att duplicerade Swagger-åtgärds-ID skapas. När du har skapat ett API-app-projekt kan du distribuera det till en API-app på samma sätt som du lärt dig i den här kursen.


