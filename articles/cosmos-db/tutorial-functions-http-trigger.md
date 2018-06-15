---
title: Skapa en HTTP-utlösare med en Azure Cosmos DB-indatabindning | Microsoft Docs
description: Lär dig att använda Azure Functions med HTTP-utlösare för frågor i Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 3ea102d56df1e47fd1d1c35bd23a3e987fa45145
ms.sourcegitcommit: 00e875607372517b4b93ca4b6baa915cbbad8064
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34818907"
---
# <a name="create-an-azure-functions-http-trigger-with-an-azure-cosmos-db-input-binding"></a>Skapa en HTTP-utlösare i Azure Functions med en Azure Cosmos DB-indatabindning

Azure Cosmos DB är en globalt distribuerad databas för flera modeller som är både schemalös och serverlös. Azure Function är en serverlös beräkningstjänst som gör det möjligt att köra kod på begäran. När du kombinerar dessa två Azure-tjänster får du en bra grund för en serverlös arkitektur, där du kan fokusera på att skapa bra appar och inte oroa dig för att behöva etablera och underhålla servrar för din beräknings- och databasbehov.

Den här självstudien bygger på koden som skapades i [Snabbstart för Graph API i .NET](create-graph-dotnet.md). Självstudien lägger till en Azure Function som innehåller en [HTTP-utlösare](../azure-functions/functions-bindings-http-webhook.md). HTTP-utlösaren använder en Azure Cosmos DB-[indatabindning](../azure-functions/functions-triggers-bindings.md) till att hämta data från grafdatabasen som skapades i snabbstarten. Den här specifika HTTP-utlösaren frågar Azure Cosmos DB om data, men indatabindningarna från Azure Cosmos DB kan användas till att hämta indatavärden oavsett vad din funktion kräver.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Skapa ett Azure Function-projekt 
> * Skapa en HTTP-utlösare
> * Publicera Azure Function
> * Ansluta Azure Function till Azure Cosmos DB-databasen

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Visual Studio 2017 version 15.3](https://www.visualstudio.com/vs/preview/), inklusive arbetsbelastningen **Azure Development**.

    ![Installera Visual Studio 2017 med arbetsbelastningen Azure Development](./media/tutorial-functions-http-trigger/functions-vs-workloads.png)
    
- När du har installerat eller uppgraderat till Visual Studio 2017 version 15.3, måste du manuellt uppdatera Visual Studio 2017-verktygen för Azure Functions. Du kan uppdatera verktygen från menyn **Verktyg** under **Tillägg och uppdateringar...**  > **Uppdateringar** > **Visual Studio Marketplace** > **Azure Functions och WebJobs-verktyg** > **Uppdatera**.

- Slutför självstudien [Skapa ett .NET-program med hjälp av Graph API](tutorial-develop-graph-dotnet.md) eller hämta exempelkoden från GitHub-lagringsplatsen [azure-cosmos-db-graph-dotnet-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started) och skapa projektet.
 
## <a name="build-a-function-in-visual-studio"></a>Skapa en funktion i Visual Studio

1. Lägg till ett **Azure Functions**-projekt i lösningen genom att högerklicka på lösningsnoden i **Solution Explorer** och sedan välja **Lägg till** > **Nytt projekt**. Välj **Azure Functions** i dialogrutan och ge den namnet **PeopleDataFunctions**.

   ![Lägga till ett Azure Function-projekt i lösningen](./media/tutorial-functions-http-trigger/01-add-function-project.png)

2. När du har skapat Azure Functions-projektet finns det några NuGet-relaterade uppdateringar och installationer som du kan utföra. 

    a. Om du vill kontrollera att du har de senaste funktions-SDK:erna, kan du använda NuGet Manager till att uppdatera paketet **Microsoft.NET.Sdk.Functions**. Högerklicka på projektet i **Solution Explorer** och välj **Hantera NuGet-paket**. På fliken **Installerat** väljer du Microsoft.NET.Sdk.Functions. Klicka sedan på **Uppdatera**.

   ![Uppdatera NuGet-paket](./media/tutorial-functions-http-trigger/02-update-functions-sdk.png)

    b. På fliken **Bläddra** anger du **azure.graphs** för att hitta paketet **Microsoft.Azure.Graphs**. Klicka sedan på **Installera**. Det här paketet innehåller klient-SDK:n för Graph API i .NET.

   ![Installera Graph API](./media/tutorial-functions-http-trigger/03-add-azure-graphs.png)

    c. På fliken **Bläddra** anger du **mono.csharp** för att hitta paketet **Mono.CSharp**. Klicka sedan på **Installera**.

   ![Installera Mono.CSharp](./media/tutorial-functions-http-trigger/04-add-mono.png)

3. Solution Explorer ska nu innehålla de paket som du har installerat, enligt bilden. 
   
   Nu ska vi skriva kod, så vi lägger till ett nytt **Azure Function**-objekt i projektet. 

    a. Högerklicka på projektnoden i **Solution Explorer** och välj sedan **Lägg till** > **Nytt objekt**.   
    b. I dialogrutan **Lägg till nytt objekt** väljer du **Visual C#-objekt** och **Azure Function**. Skriv **Sök** som namn på projektet och klicka sedan på **Lägg till**.  
 
   ![Skapa en ny funktion med namnet Sök](./media/tutorial-functions-http-trigger/05-add-function.png)

4. Azure Function svarar på HTTP-begäranden, så mallen för HTTP-utlösaren är lämplig här.
   
   I rutan **Ny Azure Function** väljer du **HTTP-utlösare**. Vi vill att denna Azure Function ska vara ”helt öppen”, så vi anger **Åtkomstbehörigheter** till **Anonym** vilket gör att alla har åtkomst till den. Klicka på **OK**.

   ![Ange anonym som åtkomstbehörighet](./media/tutorial-functions-http-trigger/06-http-trigger.png)

5. När du har lagt till Search.cs i Azure Function-projektet, kopierar du dessa **med hjälp av** de instruktioner som ligger ovanpå de befintliga instruktionerna som används:

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

6. Ersätt sedan Azure Function-klasskoden med koden nedan. Koden söker i Azure Cosmos DB-databasen med Graph API efter antingen alla användare eller efter den specifika person som identifierades av `name`-frågesträngparametern.

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

   Koden har i princip samma anslutningslogik som det ursprungliga konsolprogram som angav databasen, med en enkel fråga för att hämta matchande poster.

## <a name="debug-the-azure-function-locally"></a>Felsöka Azure-funktionen lokalt

Nu när koden har slutförts kan du använda lokala felsökningsverktyg och emulatorn i Azure Function för att köra kod lokalt och testa den.

1. Innan koden kan köras korrekt, måste du konfigurera den för lokal körning med din Azure Cosmos DB-anslutningsinformation. Du kan använda filen local.settings.json till att konfigurera Azure Function för lokal körning, ungefär på samma sätt som du skulle använda App.config-filen till att konfigurera det ursprungliga konsolprogrammet för körning.

    Gör detta genom att lägga till följande kodrader i local.settings.json. Kopiera sedan in din slutpunkt och AuthKey från filen App.Config i GraphGetStarted-projektet enligt följande bild.

   ```json
    "Endpoint": "",
    "AuthKey": ""
    ```

   ![Ange slutpunkt och auktoriseringsnyckel i filen local.settings.json](./media/tutorial-functions-http-trigger/07-local-functions-settings.png)

2. Ändra StartUp-projekt till den nya funktionsappen. I **Solution Explorer** högerklickar du på **PeopleDataFunctions** och väljer alternativet för att **ange som StartUp-projekt**.

3. I **Solution Explorer** högerklickar du på **Beroenden** i projektet **PeopleDataFunctions**. Klicka sedan på **Lägg till referens**. Välj System.Configuration i listan och klicka sedan på **OK**.

3. Nu ska vi köra appen. Tryck på F5 för att starta det lokala felsökningsverktyget func.exe med den värdbaserade Azure Function-koden som är redo att användas.

   I slutet av den första utdatan från func.exe ser vi att Azure Function finns på localhost:7071. Det kan vara bra att testa den i en klient.

   ![Testa klienten](./media/tutorial-functions-http-trigger/08-functions-emulator.png)

4. Testa Azure Function genom att använda [Visual Studio Code](http://code.visualstudio.com/) med Huachao Mao-tillägget [REST-klient](https://marketplace.visualstudio.com/items?itemName=humao.rest-client). REST-klienten innehåller funktionen med lokala eller fjärranslutna HTTP-förfrågningar med ett enda högerklick. 

    Om du vill göra detta skapar du en ny fil med namnet test-function-locally.http och lägger till följande kod:

    ```http
    get http://localhost:7071/api/Search

    get http://localhost:7071/api/Search?name=ben
   ```

    Högerklicka nu på den första raden i koden och välj **Skicka förfrågan** enligt följande bild.

   ![Skicka en REST-begäran från Visual Studio-kod](./media/tutorial-functions-http-trigger/09-rest-client-in-vs-code.png)

   Rådata i HTTP-svaret från lokalt körda Azure Function-rubriker, JSON-textinnehåll med mera visas.

   ![REST-svar](./media/tutorial-functions-http-trigger/10-general-results.png)

5. Markera nu den andra kodraden och välj **Skicka förfrågan**. Genom att lägga till frågesträngparametern `name` med ett värde som vi vet finns i databasen, kan vi filtrera resultaten som Azure Function returnerar.

   ![Filtrera resultaten från Azure Function](./media/tutorial-functions-http-trigger/11-search-for-ben.png)

När Azure Function har verifierats och verkar fungera, har vi kvar att publicera den till Azure App Service och konfigurera den till att köras i molnet.

## <a name="publish-the-azure-function"></a>Publicera Azure-funktionen

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

   ![Publicera det nya projektet](./media/tutorial-functions-http-trigger/12-publish-function.png)

2. Vi är redo att publicera det till molnet för att testa det i ett scenario som är allmänt tillgängligt. På fliken **Publicera** väljer du **Azure Function-app**, **Skapa nytt** för att skapa en Azure Function i din Azure-prenumeration. Klicka sedan på **Publicera**.

   ![Skapa en ny Azure Function-app](./media/tutorial-functions-http-trigger/13-publish-panel.png)

3. I dialogrutan **Publicera** gör du följande:
   
    a. I **Appnamn** ger du funktionen ett unikt namn.

    b. I **Prenumeration** väljer du den Azure-prenumeration som ska användas.
   
    c. I **Resursgrupp** skapar du en ny resursgrupp och använder samma namn som appen har.
   
    d. I **App Service-plan** klickar du på **Nytt** för att skapa en ny konsumtionsbaserad App Service-plan, eftersom vi planerar att fakturera per användning för vår serverlösa Azure Function. Använd standardinställningarna på sidan **Konfigurera App Service-plan** och klicka sedan på **OK**.
   
    e. I **Lagringskonto** klickar du dessutom på **Nytt** för att skapa ett nytt lagringskonto som ska användas med Azure Function, om vi behöver stöd för blobar, tabeller eller köer för att utlösa körningen av andra funktioner. Använd standardinställningarna på sidan **Lagringskonto** och klicka sedan på **OK**.

    f. Klicka sedan på knappen **Skapa** i dialogrutan för att skapa alla resurser i din Azure-prenumeration. Visual Studio laddar ner en publiceringsprofil (en enkel XML-fil) som används nästa gång du publicerar Azure Function-koden.

   ![Skapa lagringskontot](./media/tutorial-functions-http-trigger/14-new-function-app.png)

    Visual Studio visar en publiceringssida som du kan använda om du gör ändringar i funktionen och behöver publicera den igen. Du behöver inte göra något på sidan nu.

4. När Azure Function har publicerats kan du gå till sidan [Azure Portal](https://portal.azure.com/) för din Azure Function. Där hittar du en länk till **Programinställningar** i Azure Function. Öppna den här länken för att konfigurera live-anslutningen i Azure Function till Azure Cosmos DB-databasen med dina personliga data.

   ![Granska programinställningar](./media/tutorial-functions-http-trigger/15-function-in-portal.png)

5. Precis som du gjorde tidigare i konsolprogrammets App.config-fil och i Azure Function-appens local.settings.json-fil, måste du lägga till slutpunkten och AuthKey för Azure Cosmos DB-databasen i den publicerade funktionen. Det innebär att du aldrig behöver checka in konfigurationskod som innehåller dina nycklar – du kan konfigurera dem i portalen utan att de lagras i källkontrollen. Lägg till varje värde genom att klicka på knappen **Lägg till ny inställning** och lägga till **Slutpunkt** samt värdet från app.config. Klicka sedan på **Lägg till ny inställning** igen och lägg till **AuthKey**  och ditt anpassade värde. När du har lagt till och sparat värdena, bör inställningarna se ut enligt följande.

   ![Konfigurera slutpunkt och AuthKey](./media/tutorial-functions-http-trigger/16-app-settings.png)

6. När Azure Function har rätt konfiguration i Azure-prenumerationen kan du använda tillägget för REST-klienten i Visual Studio Code igen för att fråga i den allmänt tillgängliga Azure Function-URL:en. Lägg till dessa två kodrader i test-function-locally.http och kör sedan varje rad för att testa funktionen. Ersätt namnet på funktionen i URL:en med namnet på din funktion.

    ```json
    get https://peoplesearchfunction.azurewebsites.net/api/Search

    get https://peoplesearchfunction.azurewebsites.net/api/Search?name=thomas
    ```

    Funktionen svarar med data som hämtats från Azure Cosmos DB.

    ![Använda REST-klienten för att fråga i Azure Function](./media/tutorial-functions-http-trigger/17-calling-function-from-code.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Skapat ett Azure Function-projekt 
> * Skapat en HTTP-utlösare
> * Publicerat Azure Function
> * Anslutit funktionen till Azure Cosmos DB-databasen

Du kan nu gå vidare till avsnittet Begrepp om du vill ha mer information om Cosmos DB.

> [!div class="nextstepaction"]
> [Global distribution](distribute-data-globally.md) 

Den här artikeln är baserad på en blogg i bloggserien [Brady Gaster’s Schemaless & Serverless](http://www.bradygaster.com/category/%20Serverless%20&%20Schemaless). Besök hans blogg om du vill läsa fler inlägg i serien.
