---
title: "Skapa en HTTP-utlösare med Azure DB som Cosmos-indatabindning | Microsoft Docs"
description: "Lär dig hur du använder Azure Functions med HTTP-utlösare för att fråga Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: mvc
ms.date: 09/25/2017
ms.author: mimig
ms.openlocfilehash: 86a660309fd3fd80f10f706ff460af2309c12174
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-functions-http-trigger-with-an-azure-cosmos-db-input-binding"></a>Skapa en Azure Functions HTTP-utlösare med Azure DB som Cosmos-indatabindning

Azure Cosmos-DB är en global och flera olika modeller databas som är både schemalös och serverlösa. Azure-funktionen är en serverlösa beräknings-tjänst som gör det möjligt att köra kod på begäran. Para ihop dessa två Azure-tjänster och du har grunden för en serverlösa arkitektur som gör att du kan fokusera på att bygga bra appar och oroa dig inte om etablering och underhåll av servrar för din beräknings och databasen måste.

Den här kursen bygger på koden som skapade i den [Graph API Snabbstart för .NET](create-graph-dotnet.md). Den här självstudiekursen lägger till en Azure-funktion som innehåller en [HTTP-utlösaren](https://github.com/MicrosoftDocs/azure-docs-pr/azure-functions/functions-bindings-http-webhook.md#http-trigger). HTTP-utlösaren använder en Azure-Cosmos-DB [inkommande bindningen](https://github.com/MicrosoftDocs/azure-docs-pr/azure-functions/functions-triggers-bindings.md) att hämta data från databasen för diagram som skapats i Snabbstart. Den här specifika http-utlösaren frågor Azure Cosmos DB för data, men inkommande bindningar från Azure Cosmos DB kan användas att hämta data indatavärden för vad din funktion kräver.

Den här kursen ingår följande uppgifter:

> [!div class="checklist"]
> * Skapa ett projekt för Azure-funktion 
> * Skapa en HTTP-utlösare
> * Publicera funktionen Azure
> * Anslut Azure-funktion i Azure DB som Cosmos-databasen

## <a name="prerequisites"></a>Krav

- [Visual Studio 2017 version 15.3](https://www.visualstudio.com/vs/preview/), inklusive arbetsbelastningen **Azure Development**.

    ![Installera Visual Studio 2017 med arbetsbelastningen Azure Development](./media/tutorial-functions-http-trigger/functions-vs-workloads.png)
    
- När du installerar eller uppgraderar till Visual Studio 2017 version 15,3 måste du manuellt uppdatera 2017 för Visual Studio-verktygen för Azure Functions. Du kan uppdatera verktyg från den **verktyg** menyn under **tillägg och uppdateringar...**   >  **Uppdateringar** > **Visual Studio Marketplace** > **Azure Functions och Web jobb verktyg**  >  **Uppdatering**.

- Slutför den [skapar ett .NET-program med hjälp av Graph API](tutorial-develop-graph-dotnet.md) kursen eller get exemplet kod från de [azure-cosmos-db-graph-dotnet-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started) GitHub-repo- och skapa projektet.
 
## <a name="build-a-function-in-visual-studio"></a>Skapa en funktion i Visual Studio

1. Lägg till en **Azure Functions** projekt i lösningen genom att högerklicka på noden lösning i **Solution Explorer**, Välj **Lägg till**  >   **Nytt projekt**. Välj **Azure Functions** från dialogrutan rutan och ge den namnet **PeopleDataFunctions**.

   ![Lägger till ett Azure-funktion-projekt i lösningen](./media/tutorial-functions-http-trigger/01-add-function-project.png)

2. När du skapar projektet Azure Functions finns det några NuGet relaterade uppdateringar och installerar om du vill utföra. 

    a. Om du vill kontrollera att du har de senaste funktionerna SDK: N, kan du använda NuGet Manager för att uppdatera den **Microsoft.NET.Sdk.Functions** paketet. I **Solution Explorer**, högerklicka på projektet och välj **hantera NuGet-paket**. I den **installerad** , Välj Microsoft.NET.Sdk.Functions och klicka sedan på **uppdatering**.

   ![Uppdatera NuGet-paket](./media/tutorial-functions-http-trigger/02-update-functions-sdk.png)

    b. I den **Bläddra** ange **azure.graphs** att hitta den **Microsoft.Azure.Graphs** paketet och klickar sedan på **installera**. Det här paketet innehåller Graph API .NET klient-SDK.

   ![Installera Graph API](./media/tutorial-functions-http-trigger/03-add-azure-graphs.png)

    c. I den **Bläddra** ange **mono.csharp** att hitta den **Mono.CSharp** paketet och klickar sedan på **installera**.

   ![Installera Mono.CSharp](./media/tutorial-functions-http-trigger/04-add-mono.png)

3. Solution Explorer nu innehålla paket som du har installerat, som visas här. 
   
   Nu ska vi behöver skriva kod, så vi lägger till en ny **Azure-funktion** objekt i projektet. 

    a. Högerklicka på projektnoden i **Solution Explorer** och välj sedan **Lägg till** > **Nytt objekt**.   
    b. I den **Lägg till nytt objekt** markerar **Visual C# objekt**väljer **Azure-funktion**, typen **Sök** som namn på ditt projekt och sedan Klicka på **Lägg till**.  
 
   ![Skapa en ny funktion med namnet Sök](./media/tutorial-functions-http-trigger/05-add-function.png)

4. Azure-funktion svarar på HTTP-begäranden, så mallen http-utlösare är lämpligt här.
   
   I den **nya Azure-funktion** väljer **Http-utlösaren**. Vi vill Azure funktionen ”öppet”,, så vi ställa in den **åtkomstbehörigheter** till **anonym**, vilket gör att alla. Klicka på **OK**.

   ![Ange behörigheter till anonym](./media/tutorial-functions-http-trigger/06-http-trigger.png)

5. När du lägger till Search.cs projektet Azure-funktion, kopiera dessa **med** rapporter över den befintliga med-uttryck:

   ```csharp
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Microsoft.Azure.Documents.Linq;
   using Microsoft.Azure.Graphs;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.Http;
   using Microsoft.Azure.WebJobs.Host;
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Linq;
   using System.Net;
   using System.Net.Http;
   using System.Threading.Tasks;
   ```

6. Ersätt sedan funktionen Azure kod med koden nedan. Koden söker Azure DB som Cosmos-databasen med Graph API för antingen alla användare eller för specifika personen som identifieras av den `name` frågesträngparametern.

   ```csharp
   public static class Search
   {
       static string endpoint = ConfigurationManager.AppSettings["Endpoint"];
       static string authKey = ConfigurationManager.AppSettings["AuthKey"];

       [FunctionName("Search")]
       public static async Task<HttpResponseMessage> Run(
           [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
           TraceWriter log)
       {
           log.Info("C# HTTP trigger function processed a request.");

           // the person objects are free-form in structure
           List<dynamic> results = new List<dynamic>();

           // open the client's connection
           using (DocumentClient client = new DocumentClient(
               new Uri(endpoint),
               authKey,
               new ConnectionPolicy
               {
                   ConnectionMode = ConnectionMode.Direct,
                   ConnectionProtocol = Protocol.Tcp
               }))
           {
               // get a reference to the database the console app created
               Database database = await client.CreateDatabaseIfNotExistsAsync(
                   new Database
                   {
                       Id = "graphdb"
                   });

               // get an instance of the database's graph
               DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
                   UriFactory.CreateDatabaseUri("graphdb"),
                   new DocumentCollection { Id = "graphcollz" },
                   new RequestOptions { OfferThroughput = 1000 });

               // build a gremlin query based on the existence of a name parameter
               string name = req.GetQueryNameValuePairs()
                   .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                   .Value;

               IDocumentQuery<dynamic> query = (!String.IsNullOrEmpty(name))
                   ? client.CreateGremlinQuery<dynamic>(graph, string.Format("g.V('{0}')", name))
                   : client.CreateGremlinQuery<dynamic>(graph, "g.V()");

               // iterate over all the results and add them to the list
               while (query.HasMoreResults)
                   foreach (dynamic result in await query.ExecuteNextAsync())
                       results.Add(result);
           }

           // return the list with an OK response
           return req.CreateResponse<List<dynamic>>(HttpStatusCode.OK, results);
       }
   }
   ```

   Koden är i princip samma anslutning logik som den ursprungliga konsolprogram som dirigeras databasen med en enkel fråga att hämta matchande poster.

## <a name="debug-the-azure-function-locally"></a>Felsöka Azure funktionen lokalt

Koden har slutförts, kan du använda funktionen Azure lokala felsökningsverktyg och -emulatorn kör kod lokalt för att testa den.

1. Innan koden körs korrekt, måste du konfigurera den för lokal körning med din Azure Cosmos DB-anslutningsinformationen. Du kan använda filen local.settings.json för att konfigurera Azure-funktion för lokal körning mycket på samma sätt som du skulle använda App.config-filen för att konfigurera ursprungliga konsolprogrammet för körning.

    För att göra detta, Lägg till följande rader med kod i local.settings.json och kopiera i din slutpunkt och auktoriseringsnyckel från filen App.Config i GraphGetStarted-projektet enligt följande bild.

   ```json
    "Endpoint": "",
    "AuthKey": ""
    ```

   ![Ange den slutpunkt och auktorisering nyckeln i filen local.settings.json](./media/tutorial-functions-http-trigger/07-local-functions-settings.png)

2. Ändra Startprojekt till appen med nya funktioner. I **Solution Explorer**, högerklicka på **PeopleDataFunctions**, och välj **Set as StartUp Project**.

3. I **Solution Explorer**, högerklicka på **beroenden** i den **PeopleDataFunctions** projektet och klicka sedan på **Lägg till referens**. Välj System.Configuration i listan och klicka sedan på **OK**.

3. Nu ska vi köra appen. Tryck på F5 för att starta det lokala felsökningsverktyget, func.exe med Azure Funktionskoden värdbaserade och redo att användas.

   I slutet av den första utdatan från func.exe finns att Azure-funktion finns på localhost:7071. Det är bra att testa den i en klient.

   ![Testa klienten](./media/tutorial-functions-http-trigger/08-functions-emulator.png)

4. Testa Azure-funktion med [Visual Studio Code](http://code.visualstudio.com/) med Huachao Mao tillägget [REST-klient](https://marketplace.visualstudio.com/items?itemName=humao.rest-client). REST-klient ger lokal eller fjärransluten HTTP-begäran funktioner i en enda högerklicka. 

    Om du vill göra detta måste skapa en ny fil med namnet test-funktionen-locally.http och Lägg till följande kod:

    ```http
    get http://localhost:7071/api/Search

    get http://localhost:7071/api/Search?name=ben
   ```

    Nu högerklickar du på den första raden i koden och välj sedan **skicka begäran** som visas i följande bild.

   ![Skicka en REST-begäran från Visual Studio code](./media/tutorial-functions-http-trigger/09-rest-client-in-vs-code.png)

   Rådata HTTP-svaret från körs lokalt Azure-funktion huvuden innehållet i JSON-text, visas allt.

   ![REST-svar](./media/tutorial-functions-http-trigger/10-general-results.png)

5. Markera den andra raden med kod och välj sedan **skicka begäran**. Genom att lägga till den `name` frågesträngparametern med ett värde som har visat sig vara i databasen, kan vi filtrera resultatet returnerar funktionen Azure.

   ![Filtrera resultaten för Azure-funktion](./media/tutorial-functions-http-trigger/11-search-for-ben.png)

När Azure-funktion har verifierats och verkar vara fungerar, är det sista steget att publicera det till Azure App Service och konfigurera den att köras i molnet.

## <a name="publish-the-azure-function"></a>Publicera funktionen Azure

1. I **Solution Explorer**, högerklicka på projektet och välj sedan **publicera**.

   ![Publicera det nya projektet](./media/tutorial-functions-http-trigger/12-publish-function.png)

2. Vi är redo att publicera det till molnet för att testa den i ett scenario med offentligt tillgängliga. I den **publicera** väljer **Azure Funktionsapp**väljer **Skapa nytt** för att skapa en Azure-funktion i din Azure-prenumeration, klicka på **publicera** .

   ![Skapa en ny app i Azure-funktion](./media/tutorial-functions-http-trigger/13-publish-panel.png)

3. I den **publicera** dialogrutan gör du följande:
   
    a. I **Appnamn**, ger ett unikt namn för funktionen.

    b. I **prenumeration**, Välj Azure-prenumeration ska användas.
   
    c. I **resursgruppen**, skapa en ny resursgrupp och använda samma namn som namnet på appen.
   
    d. För **Apptjänstplan**, klickar du på **ny** att skapa en ny förbrukningsbaserad App Service-Plan eftersom vi planerar att använda metoden per tillfälle fakturering för serverlösa Azure-funktion. Använda standardinställningarna på den **konfigurera Apptjänstplan** , och klickar sedan på **OK**.
   
    e. För **Lagringskonto**, också klicka på **ny** att skapa ett nytt Lagringskonto kan använda med funktionen Azure om vi behöver stöd för Blobbar, tabeller eller köer att utlösa körning av andra funktioner. Använda standardinställningarna på den **Lagringskonto** , och klickar sedan på **OK**.

    f. Klicka på den **skapa** knapp i dialogrutan för att skapa alla resurser i din Azure-prenumeration. Visual Studio hämtar en publiceringsprofil (en enkel XML-fil) som används för nästa gång du publicerar koden Azure-funktion.

   ![Skapa lagringskontot](./media/tutorial-functions-http-trigger/14-new-function-app.png)

    Visual Studio visar sedan en publicera sida som du kan använda om du gör ändringar i funktionen och behöver publicera det igen. Du behöver inte göra något nu på sidan.

4. När Azure-funktion har publicerats kan du gå till den [Azure-portalen](https://portal.azure.com/) för din Azure-funktion. Där hittar du en länk till Azure-funktion **programinställningar**. Öppna den här länken om du vill konfigurera live Azure-funktion för anslutningen till Azure DB som Cosmos-databasen med Person data.

   ![Granska inställningar för program](./media/tutorial-functions-http-trigger/15-function-in-portal.png)

5. Precis som du gjorde tidigare i den konsolprogram App.config-fil och appen Azure-funktion local.settings.json fil, behöver du lägga till slutpunkten och auktoriseringsnyckel i Azure DB som Cosmos-databasen till publicerade funktionen. På så sätt kan du behöver aldrig Kontrollera i konfigurationskod som innehåller dina nycklar – du kan konfigurera dem i portalen och se till att de inte lagras i källkontroll. Lägg till varje värde, klicka på den **lägga till nya inställningen** knappen, lägga till **Endpoint** och värdet från app.config, klicka på **lägga till nya inställningen** igen och Lägg till **auktoriseringsnyckel**  och ditt eget värde. När du har lagt till och sparat värdena, inställningarna bör se ut som följande.

   ![Konfigurera slutpunkt och auktoriseringsnyckel](./media/tutorial-functions-http-trigger/16-app-settings.png)

6. När Azure-funktion har konfigurerats korrekt i Azure-prenumeration kan använda du igen tillägget för Visual Studio Code REST-klient för att fråga den allmänt tillgängliga Azure funktions-URL. Lägg till dessa två rader med kod i test-funktionen-locally.http och kör sedan varje rad testa funktionen. Ersätt namnet på funktionen i URL: en med namnet på din funktion.

    ```json
    get https://peoplesearchfunction.azurewebsites.net/api/Search

    get https://peoplesearchfunction.azurewebsites.net/api/Search?name=thomas
    ```

    Funktionen svarar med data som hämtats från Azure Cosmos DB.

    ![Använda REST-klient för att fråga Azure-funktion](./media/tutorial-functions-http-trigger/17-calling-function-from-code.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen kommer du har gjort följande:

> [!div class="checklist"]
> * Skapa ett projekt för Azure-funktion 
> * Skapa en HTTP-utlösare
> * Publicerade funktionen Azure
> * Ansluten funktionen till Azure Cosmos-DB-databas

Du kan gå vidare till avsnittet begrepp för mer information om Cosmos DB.

> [!div class="nextstepaction"]
> [Global distribution](distribute-data-globally.md) 

Den här artikeln är baserad på en blogg från [Andersson Gaster Schemaless & utan Server](http://www.bradygaster.com/category/%20Serverless%20&%20Schemaless) bloggserie. Besök hans blogg för ytterligare inlägg i serien.
