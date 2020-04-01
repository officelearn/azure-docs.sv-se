---
title: ASP.NET Core MVC-webbapphandledning med Azure Cosmos DB
description: ASP.NET Core MVC-självstudiekurs för att skapa ett MVC-webbprogram med Azure Cosmos DB. Du lagrar JSON och kommer åt data från en todo-app som finns på Azure App Service - ASP NET Core MVC-självstudiekurs steg för steg.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: sngun
ms.openlocfilehash: 1f2051addfa1266b754d230c3804834c63f89002
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274074"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Självstudiekurs: Utveckla ett ASP.NET Core MVC-webbprogram med Azure Cosmos DB med hjälp av .NET SDK

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

Den här självstudien beskriver hur du använder Azure Cosmos DB för lagring av och åtkomst till data från ett ASP.NET MVC-program som hanteras på Azure. I den här självstudien använder du .NET SDK V3. Följande bild visar webbsidan som du ska skapa med hjälp av exemplet i den här artikeln:

![Skärmbild av todo-listan MVC webbprogram som skapats av den här guiden - ASP NET Core MVC handledning steg för steg](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Om du inte har tid att slutföra den här självstudien kan du ladda ned det fullständiga exempelprojektet från [GitHub][GitHub].

Den här självstudiekursen omfattar:

> [!div class="checklist"]
>
> * Skapa ett Azure Cosmos-konto
> * Skapa en ASP.NET Core MVC-app
> * Ansluta appen till Azure Cosmos DB
> * Utföra åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) på data

> [!TIP]
> Den här självstudien förutsätter att du har tidigare erfarenhet av att använda ASP.NET Core MVC och Azure App Service. Om du inte har ASP.NET Core eller [de nödvändiga verktygen](#prerequisites)rekommenderar vi att du hämtar hela exempelprojektet från [GitHub,][GitHub]lägger till de nödvändiga NuGet-paketen och kör det. När du skapar projektet kan du läsa den här artikeln för att få information om koden i projektets sammanhang.

## <a name="prerequisites"></a><a name="prerequisites"></a>Krav

Innan du följer instruktionerna i den här artikeln bör du kontrollera att du har följande resurser:

* Ett aktivt Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019. [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Alla skärmdumpar i den här artikeln kommer från Microsoft Visual Studio Community 2019. Om du använder en annan version kanske skärmarna och alternativen inte matchar helt. Lösningen ska fungera om du uppfyller förutsättningarna.

## <a name="step-1-create-an-azure-cosmos-account"></a><a name="create-an-azure-cosmos-account"></a>Steg 1: Skapa ett Azure Cosmos-konto

Vi börjar med att skapa ett Azure Cosmos-konto. Om du redan har ett Azure Cosmos DB SQL API-konto eller om du använder Azure Cosmos DB-emulatorn går du till [steg 2: Skapa ett nytt ASP.NET MVC-program](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

I nästa avsnitt skapar du ett nytt ASP.NET Core MVC-program.

## <a name="step-2-create-a-new-aspnet-core-mvc-application"></a><a name="create-a-new-mvc-application"></a>Steg 2: Skapa ett nytt ASP.NET Core MVC-program

1. Öppna Visual Studio och välj **Skapa ett nytt projekt**.

1. Leta reda på och välja **ASP.NET Core Web Application** for C#i Skapa ett nytt **projekt.** Välj **Nästa** för att fortsätta.

   ![Skapa ett nytt webbprogramprojekt för ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

1. I **Konfigurera det nya projektet**namnger du *projektets att göra* och väljer **Skapa**.

1. I **Skapa ett nytt ASP.NET Core Web Application**väljer du **Webbprogram (Model-View-Controller)**. Välj **Skapa** för att fortsätta.

   Visual Studio skapar ett tomt MVC-program.

1. Välj **Felsökning** > **Avsökning** av felsökning eller F5 om du vill köra ditt ASP.NET-program lokalt.

## <a name="step-3-add-azure-cosmos-db-nuget-package-to-the-project"></a><a name="add-nuget-packages"></a>Steg 3: Lägg till Azure Cosmos DB NuGet-paketet i projektet

Nu när vi har de flesta av de ASP.NET Core MVC-ramkoden som vi behöver för den här lösningen, ska vi lägga till NuGet-paket som krävs för att ansluta till Azure Cosmos DB.

1. Högerklicka på projektet i **Solution Explorer**och välj **Hantera NuGet-paket**.

1. Sök efter och välj **Microsoft.Azure.Cosmos**i **NuGet Package Manager**. Välj **Installera**.

   ![Installera NuGet-paketet](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png)

   Visual Studio hämtar och installerar Azure Cosmos DB-paketet och dess beroenden.

   Du kan också använda **Package Manager Console** för att installera NuGet-paketet. Det gör du genom att välja **Verktyg** > **NuGet Package Manager** > **Package Manager Console**. Skriv följande kommando i prompten:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="step-4-set-up-the-aspnet-core-mvc-application"></a><a name="set-up-the-mvc-application"></a>Steg 4: Konfigurera ASP.NET Core MVC-programmet

Nu ska vi lägga till modeller, vyer och styrenheter till detta MVC-program.

### <a name="add-a-model"></a><a name="add-a-model"></a> Lägga till en modell

1. Högerklicka på mappen **Modeller** i **Solution Explorer**och välj **Lägg till** > **klass**.

1. I **Lägg till nytt objekt**namnger du den nya klassen *Item.cs* och väljer Lägg **till**.

1. Ersätt innehållet i *Item.cs* klass med följande kod:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

Azure Cosmos DB använder JSON för att flytta och lagra data. Du kan `JsonProperty` använda attributet för att styra hur JSON serialiserar och avserialiserar objekt. Klassen `Item` visar attributet. `JsonProperty` Den här koden styr formatet på egenskapsnamnet som går till JSON. Den byter också namn `Completed`på egenskapen .NET .

### <a name="add-views"></a><a name="add-views"></a>Lägg till vyer

Låt oss sedan skapa följande tre vyer.

* Lägga till en listobjektvy
* Lägga till en ny objektvy
* Lägga till en redigeringsobjektvy

#### <a name="add-a-list-item-view"></a><a name="AddItemIndexView"></a>Lägga till en listobjektvy

1. Högerklicka på mappen **Vyer** i **Solution Explorer**och välj Lägg **till** > **ny mapp**. Namnge mappen *Objekt*.

1. Högerklicka på den tomma **objektmappen** och välj sedan **Lägg till** > **vy**.

1. Ange följande värden i **Lägg till MVC-vy:**

   * Ange *Index*i **Visa namn**.
   * Välj **Lista**i **Mall**.
   * Välj Artikel (todo i **klassen** **Modell. Modeller)**.
   * Välj **Använd en layoutsida** och skriv *~/Visningar/Delad/_Layout.cshtml*.

   ![Skärmbild som visar dialogrutan Lägg till MVC-vy](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png)

1. När du har lagt till de här värdena väljer du **Lägg till** och låter Visual Studio skapa en ny mallvy.

När det är gjort öppnar Visual Studio den *cshtml-fil* som den skapar. Du kan stänga filen i Visual Studio. Vi återkommer till det senare.

#### <a name="add-a-new-item-view"></a><a name="AddNewIndexView"></a>Lägga till en ny objektvy

På ett liknande sätt som du skapade en vy för listobjekt skapar du en ny vy för att skapa objekt med hjälp av följande steg:

1. Högerklicka på mappen **Objekt** igen i **Solution Explorer**och välj **Lägg till** > **vy**.

1. Gör följande ändringar i **Lägg till MVC-vy:**

   * I **Visa namn**anger du *Skapa*.
   * Välj **Skapa**i **Mall**.
   * Välj Artikel (todo i **klassen** **Modell. Modeller)**.
   * Välj **Använd en layoutsida** och skriv *~/Visningar/Delad/_Layout.cshtml*.
   * Välj **Lägg till**.

#### <a name="add-an-edit-item-view"></a><a name="AddEditIndexView"></a>Lägga till en redigeringsobjektvy

Och slutligen lägger du till en vy för att redigera ett objekt med följande steg:

1. Välj Lägg **till** > **vy**i **Item** **Utforskaren.**

1. Gör följande ändringar i **Lägg till MVC-vy:**

   * I rutan **Vynamn** skriver du *Redigera*.
   * I rutan**Mall** väljer du **Redigera**.
   * I rutan **Modellklass** väljer du **Objekt (todo.Models)**.
   * Välj **Använd en layoutsida** och skriv *~/Visningar/Delad/_Layout.cshtml*.
   * Välj **Lägg till**.

När du har slutfört de här stegen stänger du alla *cshtml-dokument* i Visual Studio när du går tillbaka till dessa vyer senare.

### <a name="declare-and-initialize-services"></a><a name="initialize-services"></a>Deklarera och initiera tjänster

Först lägger vi till en klass som innehåller logiken för att ansluta till och använda Azure Cosmos DB. För den här självstudien ska vi kapsla `CosmosDBService` in den `ICosmosDBService`här logiken i en klass som heter och ett gränssnitt som heter . Den här tjänsten gör CRUD-åtgärderna. Det läser också feed-åtgärder som att lista ofullständiga objekt, skapa, redigera och ta bort objekten.

1. Högerklicka på projektet i **Solution Explorer**och välj **Lägg till** > **ny mapp**. Namnge mappen *Tjänster*.

1. Högerklicka på mappen **Tjänster,** välj **Lägg till** > **klass**. Namnge den nya klassen *CosmosDBService* och välj **Lägg till**.

1. Ersätt innehållet i *CosmosDBService.cs* med följande kod:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Högerklicka på mappen **Tjänster,** välj **Lägg till** > **klass**. Namnge den nya klassen *ICosmosDBService* och välj **Lägg till**.

1. Lägg till följande kod i klassen *ICosmosDBService:*

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Öppna *Startup.cs* filen i din lösning `ConfigureServices` och ersätt metoden med:

    :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

    Koden i det här steget initierar klienten baserat på konfigurationen som en singleton-instans som ska injiceras genom [beroendeinjektion i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).

1. I samma fil lägger du till följande metod **InitializeCosmosClientInstanceAsync**, som läser konfigurationen och initierar klienten.

   [!code-csharp[](~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs?name=InitializeCosmosClientInstanceAsync)]

1. Definiera konfigurationen i projektets *appsettings.json-fil* enligt följande utdrag:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a><a name="add-a-controller"></a>Lägg till en controller

1. Högerklicka på mappen **Controllers** i **Solution Explorer**och välj **Lägg till** > **kontrollant**.

1. I **Lägg till byggnadsställning**väljer du **MVC Controller - Tom** och väljer Lägg **till**.

   ![Välj MVC Controller - Tom i Lägg till byggnadsställning](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Namnge den nya handkontrollen *ItemController*.

1. Ersätt innehållet i *ItemController.cs* med följande kod:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

Attributet **ValidateAntiForgeryToken** används här för att skydda programmet mot attacker med förfalskning av begäran mellan webbplatser. Dina åsikter bör fungera med denna anti-förfalskning token också. Mer information och exempel finns [i Förhindra CSRF-attacker (Cross-Site Request Forgery) i ASP.NET MVC Application][Preventing Cross-Site Request Forgery]. Källkoden på [GitHub][GitHub] innehåller den fullständiga implementeringen.

Vi använder även attributet **Bind** på metodparametern för att skydda mot overposting-attacker. Mer information finns [i Självstudiekurs: Implementera CRUD-funktioner med entitetsramverket i ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

## <a name="step-5-run-the-application-locally"></a><a name="run-the-application"></a>Steg 5: Kör programmet lokalt

Så här testar du programmet på den lokala datorn:

1. Tryck på F5 i Visual Studio för att bygga appen i felsökningsläge. Den ska bygga appen och starta en webbläsare med den tomma rutnätssidan vi såg tidigare:

   ![Skärmbild av webbprogrammet för att göra-listan som skapats av den här självstudien](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
   Om programmet i stället öppnas till `/Item` startsidan lägger du till webbadressen.

1. Markera länken **Skapa ny** och lägg till värden i fälten **Namn** och **Beskrivning.** Lämna kryssrutan **Slutförd** avmarkerad. Om du väljer det lägger appen till det nya objektet i ett slutfört tillstånd. Objektet visas inte längre i den ursprungliga listan.

1. Välj **Skapa**. Appen skickar dig tillbaka till **indexvyn** och ditt objekt visas i listan. Du kan lägga till några fler objekt i **Att göra-listan.**

    ![Skärmbild av indexvyn](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
1. Välj **Redigera** bredvid ett **objekt** i listan. Appen öppnar **redigeringsvyn** där du kan uppdatera alla egenskaper för objektet, inklusive flaggan **Slutförd.** Om du väljer **Slutförd** och väljer **Spara**visas objektet **som** slutfört i listan i appen.

   ![Skärmbild av indexvyn med rutan Slutförd markerad](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

1. Verifiera tillståndet för data i Azure Cosmos DB-tjänsten med [Cosmos Explorer](https://cosmos.azure.com) eller Azure Cosmos DB Emulator's Data Explorer.

1. När du har testat appen väljer du Ctrl+F5 för att sluta felsöka appen. Nu är du redo att distribuera!

## <a name="step-6-deploy-the-application"></a><a name="deploy-the-application-to-azure"></a>Steg 6: Distribuera programmet

När hela programmet fungerar som det ska med Azure Cosmos DB är det dags att distribuera webbappen till Azure App Service.  

1. Om du vill publicera det här programmet högerklickar du på projektet i **Lösningsutforskaren** och väljer **Publicera**.

1. Välj **Apptjänst**i **Välj ett publiceringsmål**.

1. Om du vill använda en befintlig apptjänstprofil väljer du **Välj befintlig**och väljer sedan **Publicera**.

1. Välj en **prenumeration** **i App Service**. Använd filtret **Visa** om du vill sortera efter resursgrupp eller resurstyp.

1. Hitta din profil och välj sedan **OK**. Sök sedan efter den Azure App Service som krävs och välj **OK**.

   ![Dialogrutan App Service i Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png)

Ett annat alternativ är att skapa en ny profil:

1. Precis som i föregående procedur högerklickar du på projektet i **Lösningsutforskaren** och väljer **Publicera**.
  
1. Välj **Apptjänst**i **Välj ett publiceringsmål**.

1. Välj **Skapa nytt** i Välj **ett publiceringsmål**och välj **Publicera**.

1. I **App Service**anger du ditt webbprogramnamn och lämplig prenumerations-, resursgrupps- och värdplan och väljer sedan **Skapa**.

   ![Dialogrutan Create App Service (Skapa apptjänst) i Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png)

På några sekunder publicerar Visual Studio ditt webbprogram och startar en webbläsare där du kan se ditt projekt som körs i Azure!

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar ett ASP.NET Core MVC-webbprogram. Ditt program kan komma åt data som lagras i Azure Cosmos DB. Du kan nu fortsätta med dessa resurser:

* [Partitionering i Azure Cosmos DB](./partitioning-overview.md)
* [Komma igång med SQL-frågor](./how-to-sql-query.md)
* [Så här modellerar och partitionerar du data i Azure Cosmos DB med ett verkligt exempel](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
