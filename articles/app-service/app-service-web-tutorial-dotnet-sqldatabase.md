---
title: Skapa en ASP.NET-app i Azure med SQL-databas | Microsoft Docs
description: "Lär dig hur du hämtar en ASP.NET-app som arbetar i Azure, med anslutning till en SQL-databas."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 06/09/2017
ms.author: cephalin
ms.custom: mvc, devcenter
ms.openlocfilehash: db3be8068ef9e560614daa0e7f0dcf62467fd338
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="build-an-aspnet-app-in-azure-with-sql-database"></a>Skapa en ASP.NET-app i Azure med SQL-databas

Med [Azure Web Apps](app-service-web-overview.md) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst. Den här kursen visar hur du distribuerar en datadrivna ASP.NET-webbapp i Azure och koppla den till [Azure SQL Database](../sql-database/sql-database-technical-overview.md). När du är klar kan du har en ASP.NET-app som körs i Azure och ansluten till SQL-databas.

![Publicerade ASP.NET-program i Azure webbapp](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en SQL-databas i Azure
> * Anslut en ASP.NET-app till SQL-databas
> * Distribuera appen till Azure
> * Uppdatera datamodellen och distribuera appen
> * Dataströmmen loggas från Azure till terminalen
> * Hantera appen i Azure-portalen

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:

* Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med följande arbetsbelastningar:
  - **ASP.NET och webbutveckling**
  - **Azure Development**

  ![ASP.NET och webbutveckling och Azure Development (under webb och moln)](media/app-service-web-tutorial-dotnet-sqldatabase/workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Hämta exemplet

[Hämta exempelprojektet](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).

Extrahera (packa) den *dotnet-sqldb-kursen-master.zip* fil.

Exempelprojektet innehåller en grundläggande [ASP.NET MVC](https://www.asp.net/mvc) CRUD (skapa-Läs-Uppdatera-ta bort) app med hjälp av [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="run-the-app"></a>Kör appen

Öppna den *dotnet-sqldb-kursen-master/DotNetAppSqlDb.sln* filen i Visual Studio. 

Typen `Ctrl+F5` kör appen utan felsökning. Appen visas i din standardwebbläsare. Välj den **Skapa nytt** länka och skapa ett par *uppgiften* objekt. 

![Dialogrutan Nytt ASP.NET-projekt](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

Testa den **redigera**, **information**, och **ta bort** länkar.

Appen använder en databaskontexten för att ansluta till databasen. I det här exemplet använder databaskontexten för en anslutningssträng som heter `MyDbConnection`. Anslutningssträngen har angetts i den *Web.config* fil och refereras till i den *Models/MyDatabaseContext.cs* fil. Namn för anslutningssträngen används senare under kursen för att ansluta Azure-webbapp till Azure SQL-databas. 

## <a name="publish-to-azure-with-sql-database"></a>Publicera till Azure med SQL-databas

I den **Solution Explorer**, högerklicka på din **DotNetAppSqlDb** projektet och välj **publicera**.

![Publicera från Solution Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Se till att **Microsoft Azure App Service** är markerat och klicka på **Publicera**.

![Publicera från projektöversiktssidan](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

Publicera öppnas den **skapa App Service** dialog, som hjälper dig att skapa alla Azure-resurser måste du köra ASP.NET-webbapp i Azure.

### <a name="sign-in-to-azure"></a>Logga in på Azure

I dialogrutan **Skapa App Service** klickar du på **Lägg till ett konto** och logga sedan in på din Azure-prenumeration. Om du redan är inloggad på ett Microsoft-konto kontrollerar du att kontot tillhör din Azure-prenumeration. Om kontot inte tillhör din Azure-prenumeration klickar du på den för att lägga till rätt konto.
   
![Logga in på Azure](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

När du har loggat in kan du skapa alla resurser du behöver för din Azure-webbapp i den här dialogrutan.

### <a name="configure-the-web-app-name"></a>Konfigurera webbprogramnamnet

Du kan behålla genererade webbprogrammets namn eller ändra det till ett annat unikt namn (giltiga tecken är `a-z`, `0-9`, och `-`). Webbprogramnamnet används som en del av standard-URL för din app (`<app_name>.azurewebsites.net`, där `<app_name>` är din webbprogrammets namn). Webbprogramnamnet måste vara unikt inom alla appar i Azure. 

![Skapa app service dialog](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

> [!NOTE]
> Klicka inte på **skapa**. Du måste först konfigurera en SQL-databas i ett senare steg.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [resource-group](../../includes/resource-group.md)]

Klicka på **Ny** bredvid **Resursgrupp**.

![Klicka på ny bredvid resursgrupp.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

Kalla resursgruppen **myResourceGroup**.

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Klicka på **Ny** bredvid **App Service-plan**. 

I dialogrutan **Configure App Service Plan** (Konfigurera App Service-plan) anger du följande inställningar för den nya App Service-planen:

![Skapa apptjänstplan](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

| Inställning  | Föreslaget värde | Mer information |
| ----------------- | ------------ | ----|
|**App Service-Plan**| myAppServicePlan | [App Service-planer](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |
|**Plats**| Västra Europa | [Azure-regioner](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
|**Storlek**| Kostnadsfri | [Prisnivåer](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

### <a name="create-a-sql-server-instance"></a>Skapa en SQL Server-instans

Innan du skapar en databas måste en [logisk Azure SQL Database-server](../sql-database/sql-database-features.md). En logisk server innehåller en uppsättning databaser som hanteras som en grupp.

Välj **utforska ytterligare Azure-tjänster**.

![Ange webbappnamn](media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

I den **Services** klickar du på den  **+**  ikonen bredvid **SQL-databas**. 

![Klicka på fliken tjänster den + ikonen bredvid SQL-databas.](media/app-service-web-tutorial-dotnet-sqldatabase/sql.png)

I den **Konfigurera SQL-databas** dialogrutan klickar du på **ny** bredvid **SQL Server**. 

Ett unikt servernamn genereras. Det här namnet används som en del av standard-URL för din logiska server `<server_name>.database.windows.net`. Det måste vara unikt över alla logiska serverinstanser i Azure. Du kan byta namn på servern, men behålla det genererade värdet för den här självstudiekursen.

Lägg till en administratörsanvändarnamn och lösenord. Kraven på lösenordskomplexitet, se [lösenordsprincip](/sql/relational-databases/security/password-policy).

Kom ihåg detta användarnamn och lösenord. Du måste kunna hantera logiska serverinstansen senare.

![Skapa SQL Server-instans](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

Klicka på **OK**. Stäng inte den **Konfigurera SQL-databas** dialogrutan ännu.

### <a name="create-a-sql-database"></a>Skapa en SQL Database

I den **Konfigurera SQL-databas** dialogrutan: 

* Behåll standardvärdet genereras **databasnamnet**.
* I **namn för anslutningssträngen**, typen *MyDbConnection*. Det här namnet måste matcha den anslutningssträng som refereras i *Models/MyDatabaseContext.cs*.
* Välj **OK**.

![Konfigurera SQL-databas](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

Den **skapa App Service** visar resurserna som du har skapat. Klicka på **Skapa**. 

![de resurser som du har skapat](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)

När guiden är klar att skapa Azure-resurser, publicerar din ASP.NET-app till Azure. Standardwebbläsaren startas med URL-Adressen till den distribuerade appen. 

Lägg till några arbetsuppgifter.

![Publicerade ASP.NET-program i Azure webbapp](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

Grattis! ASP.NET-programmet datadrivna körs live i Azure App Service.

## <a name="access-the-sql-database-locally"></a>Åtkomst till SQL-databasen lokalt

Visual Studio kan du hantera din nya SQL-databas i enkelt och utforska den **SQL Server Object Explorer**.

### <a name="create-a-database-connection"></a>Skapa en databasanslutning

Från den **visa** väljer du **SQL Server Object Explorer**.

Överst i **SQL Server Object Explorer**, klicka på den **Lägg till SQL Server** knappen.

### <a name="configure-the-database-connection"></a>Konfigurera anslutningen till databasen

I den **Anslut** dialogrutan Expandera den **Azure** nod. Alla SQL-databas instanser i Azure anges här.

Välj den SQL-databas som du skapade tidigare. Den anslutning som du skapade tidigare fylls automatiskt längst ned.

Ange databasadministratörens lösenord du skapade tidigare och klicka på **Anslut**.

![Konfigurera databasanslutningen från Visual Studio](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>Tillåta klientanslutning från datorn

Den **skapa en ny brandväggsregel** dialogrutan öppnas. Som standard kan bara anslutningar från Azure-tjänster, till exempel Azure-webbapp i SQL Database-instans. Skapa en brandväggsregel för att ansluta till databasen i SQL Database-instans. Brandväggsregeln kan offentliga IP-adressen för den lokala datorn.

Dialogrutan är redan fylld med datorns offentliga IP-adressen.

Se till att **lägga till klientens IP-Adressen** är markerad och klicka på **OK**. 

![Ställa in brandväggen för SQL Database-instans](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

När Visual Studio har skapat inställningen brandväggen för SQL Database-instans, din anslutning visas i **SQL Server Object Explorer**.

Här kan du utföra de vanligaste databasåtgärder, till exempel kör frågor, skapa vyer och lagrade procedurer och mycket mer. 

Expandera din anslutning > **databaser** > **&lt;databasen >** > **tabeller**. Högerklicka på den `Todoes` tabell och välj **visa Data**. 

![Utforska SQL Database-objekt](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Uppdatera app med Code First Migrations

Du kan använda välbekanta verktyg i Visual Studio för att uppdatera din databas och webb-app i Azure. I det här steget använder du Code First Migrations i Entity Framework gör en ändring i ditt databasschema och publicera den på Azure.

Läs mer om hur du använder Entity Framework Code First Migrations [komma igång med Entity Framework 6 Code First med MVC 5](https://docs.microsoft.com/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

### <a name="update-your-data-model"></a>Uppdatera datamodellen

Öppna _Models\Todo.cs_ i kodredigeraren. Lägg till följande egenskap till den `ToDo` klass:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Kör Code First Migrations lokalt

Kör några kommandon för att göra uppdateringar till den lokala databasen. 

Från den **verktyg** -menyn klickar du på **NuGet Package Manager** > **Pakethanterarkonsolen**.

Aktivera Code First Migrations i fönstret Package Manager-konsolen:

```PowerShell
Enable-Migrations
```

Lägg till en migrering:

```PowerShell
Add-Migration AddProperty
```

Uppdatera den lokala databasen:

```PowerShell
Update-Database
```

Typen `Ctrl+F5` kör appen. Testa redigera, information och skapa länkar.

Om programmet läses in utan fel, har Code First Migrations slutförts. Dock ser sidan fortfarande samma eftersom applogiken inte är ännu använder den här nya egenskapen. 

### <a name="use-the-new-property"></a>Använd den nya egenskapen

Göra några ändringar i din kod för att använda den `Done` egenskapen. För enkelhetens i den här kursen ska du bara ändra det `Index` och `Create` vyer för att se egenskapen i åtgärden.

Öppna _Controllers\TodosController.cs_.

Hitta de `Create()` metod på rad 52 och Lägg till `Done` i listan över egenskaper i den `Bind` attribut. När du är klar din `Create()` Metodsignaturen ser ut som följande kod:

```csharp
public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
```

Öppna _Views\Todos\Create.cshtml_.

I Razor-kod, bör du se en `<div class="form-group">` element som använder `model.Description`, och sedan en annan `<div class="form-group">` element som använder `model.CreatedDate`. Direkt efter dessa två element, lägga till en annan `<div class="form-group">` element som använder `model.Done`:

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
    </div>
</div>
```

Öppna _Views\Todos\Index.cshtml_.

Sök efter tomma `<th></th>` element. Lägg till följande kod i Razor ovanför det här elementet:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Hitta de `<td>` element som innehåller den `Html.ActionLink()` hjälpmetoder. _Ovan_ detta `<td>`, lägga till en annan `<td>` element med följande Razor-kod:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Det är allt du behöver se ändringarna i den `Index` och `Create` vyer. 

Typen `Ctrl+F5` kör appen.

Du kan nu lägga till en att göra-objekt och kontrollera **klar**. Sedan ska den visas i din startsida som en slutförd artikel. Kom ihåg att den `Edit` vyn inte visa den `Done` fältet, eftersom du inte ändra den `Edit` vyn.

### <a name="enable-code-first-migrations-in-azure"></a>Aktivera Code First Migrations i Azure

Nu när koden ändra fungerar, inklusive Databasmigrering publicera till Azure webbapp och uppdatera din SQL-databas med Code First Migrations för.

Precis innan, högerklicka på projektet och välj **publicera**.

Klicka på **inställningar** att öppna guiden Publicera.

![Öppna Publiceringsinställningar](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

I guiden klickar du på **nästa**.

Kontrollera att anslutningssträngen för SQL-databasen fylls i **MyDatabaseContext (MyDbConnection)**. Du kan behöva välja den **myToDoAppDb** databasen i listrutan. 

Välj **köra Code First Migrations (körs vid programstart)**, klicka på **spara**.

![Aktivera Code First Migrations i Azure webbapp](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>Publicera ändringarna

Nu när du har aktiverat Code First Migrations i ditt Azure webbapp publicera kodändringarna.

Klicka på **Publicera** på publiceringssidan.

Försök att lägga till arbetsuppgifter igen och välj **klar**, och de visas i din startsida som en slutförd artikel.

![Azure-webbapp efter koden första migrering](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Alla befintliga arbetsuppgifter fortfarande visas. Befintliga data i SQL-databasen är inte förlorade när du publicerar ASP.NET-programmet. Dessutom Code First Migrations endast ändrar dataschemat och lämnar företaget dina befintliga data.


## <a name="stream-application-logs"></a>Dataströmmen programloggar

Du kan strömma spårning av meddelanden direkt från din Azure-webbapp till Visual Studio.

Öppna _Controllers\TodosController.cs_.

Varje åtgärd som börjar med en `Trace.WriteLine()` metod. Den här koden har lagts till visar hur du lägger till trace-meddelanden till din Azure webbapp.

### <a name="open-server-explorer"></a>Öppna Server Explorer

Från den **visa** väljer du **Server Explorer**. Du kan konfigurera loggning för din Azure-webbapp i **Server Explorer**. 

### <a name="enable-log-streaming"></a>Strömning logg

I **Server Explorer**, expandera **Azure** > **Apptjänst**.

Expandera den **myResourceGroup** resursgrupp, som du skapade när du först skapade Azure-webbapp.

Högerklicka på ditt Azure webbapp och markera **visa strömning loggfiler**.

![Strömning logg](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

Loggar strömmas nu till den **utdata** fönster. 

![Loggen strömning i utdatafönstret](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

Men ser du inte några meddelanden trace ännu. Som eftersom när du väljer **visa strömning loggfiler**, Azure-webbapp anger spårningen för `Error`, som endast loggar felhändelser (med den `Trace.TraceError()` metod).

### <a name="change-trace-levels"></a>Ändra spårningsnivåer

Om du vill ändra spårningsnivåer om du vill spara andra trace-meddelanden, gå tillbaka till **Server Explorer**.

Högerklicka på ditt Azure webbapp igen och välj **visningsinställningarna**.

I den **programloggning (filsystem)** listrutan **utförlig**. Klicka på **Spara**.

![Ändra spårningsnivån utförlig](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> Du kan experimentera med olika spårningsnivåer att se vilka typer av meddelanden visas för varje nivå. Till exempel den **Information** nivån innehåller alla loggar som skapats av `Trace.TraceInformation()`, `Trace.TraceWarning()`, och `Trace.TraceError()`, men inte loggar som skapats av `Trace.WriteLine()`.
>
>

I din webbläsare navigerar du till ditt webbprogram igen vid *http://&lt;appens namn >. azurewebsites.net*, försök att klicka på runt att göra-lista program i Azure. Spåra meddelanden strömmas nu till den **utdata** fönstret i Visual Studio.

```console
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```



### <a name="stop-log-streaming"></a>Stoppa strömning logg

Log-streaming-tjänsten klickar du på den **stoppa övervakningen av** knappen i den **utdata** fönster.

![Stoppa strömning logg](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-web-app"></a>Hantera Azure-webbapp

Gå till den [Azure-portalen](https://portal.azure.com) att se att webbappen som du skapade. 



Klicka på **App Services** på menyn till vänster och klicka sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

Du har landat i ditt webbprogram sidan. 

Som standard på portalen visas den **översikt** sidan. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. Flikar till vänster på sidan Visa sidorna annan konfiguration som du kan öppna. 

![App Service-sidan på Azure Portal](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

<a name="next"></a>

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en SQL-databas i Azure
> * Anslut en ASP.NET-app till SQL-databas
> * Distribuera appen till Azure
> * Uppdatera datamodellen och distribuera appen
> * Dataströmmen loggas från Azure till terminalen
> * Hantera appen i Azure-portalen

Gå vidare till nästa kurs information om hur du mappar en anpassad DNS-namn till webbappen.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](app-service-web-tutorial-custom-domain.md)
