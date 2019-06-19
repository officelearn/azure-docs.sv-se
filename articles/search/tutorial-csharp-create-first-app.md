---
title: C#självstudie för att skapa ditt första program – Azure Search
description: Den här självstudien innehåller en stegvis guide till att skapa din första app för Azure Search. Självstudiekursen innehåller både en länk till en fungerande app på GitHub och fullständig för att skapa app från grunden. Läs mer om de viktigaste komponenterna för Azure Search.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: 5ca01e8077eb0651dff57be4c7681995764f6992
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166899"
---
# <a name="c-tutorial-create-your-first-app---azure-search"></a>C#Självstudie: Skapa ditt första program – Azure Search

Lär dig hur du skapar ett webbgränssnitt frågar och finns sökresultat från ett index med hjälp av Azure Search. Den här självstudien startar med ett befintligt index som är värdbaserade så att du kan fokusera på att skapa en sida. Indexet innehåller fiktiva Hotelldata. När du har en grundläggande sida kan förbättra du det i efterföljande erfarenheter omfattar växling, fasetter och en frågeifyllningsförslag upplevelse.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera en utvecklingsmiljö
> * Modellen datastrukturer
> * Skapa en webbsida
> * Definiera metoder
> * Testa appen

Du kommer också att se hur enkelt är ett sökanrop. Viktiga instruktioner i koden som du utvecklar kapslas i följande raderna.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Den här ett anrop initierar en sökning i Azure data och returnerar resultaten.

![Söka efter ”pool”](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

[Installera Visual Studio](https://visualstudio.microsoft.com/) ska användas som IDE.

### <a name="install-and-run-the-project-from-github"></a>Installera och köra projektet från GitHub

1. Leta upp exemplet på GitHub: [Skapa första app](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Välj **klona eller ladda ned** och gör den privata lokala kopian av projektet.
1. Med Visual Studio kan du navigera till och öppna lösningen för grundläggande söksidan och välj **starta utan felsökning** (eller tryck på F5).
1. Skriv några ord (till exempel ”wifi”, ”view”, ”-fältet”, ”parkeringssidans”) och granska resultaten!

    ![Söka efter ”wifi”](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Förhoppningsvis det här projektet körs smidigt och du har Azure-app som körs. Många av de viktiga komponenterna för mer avancerade sökningar ingår i den här en app, så det är en bra idé att gå igenom den och återskapa det steg för steg.

Om du vill skapa det här projektet från grunden och kan därför bidra till att komponenterna i Azure Search i minnet, går du igenom följande steg.

## <a name="set-up-a-development-environment"></a>Konfigurera en utvecklingsmiljö

1. I Visual Studio 2017 eller senare, Välj **New/projektet** sedan **ASP.NET Core-Webbapp**. Ge projektet ett namn, till exempel ”FirstAzureSearchApp”.
    ![Skapa en cloud-projekt](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. När du har klickat på **OK** för den här projekttypen får du en andra uppsättning med alternativ som gäller för det här projektet. Välj **webbprogram (Model-View-Controller)** .

    ![Skapa ett MVC-projekt](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. I den **verktyg** menyn och välj **NuGet-Pakethanteraren** och sedan **hantera NuGet-paket för lösningen...** . Det finns ett paket som vi behöver installera. Välj den **Bläddra** fliken och Skriv ”Azure Search” i sökrutan. Installera **Microsoft.Azure.Search** när den visas i listan (version 9.0.1, eller senare). Du måste klicka dig igenom några ytterligare dialogrutor för att slutföra installationen.

    ![Med NuGet för att lägga till Azure-bibliotek](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-search"></a>Initiera Azure Search

I det här exemplet använder vi offentligt tillgängliga Hotelldata. Dessa data är en godtycklig samling av 50 fiktiva hotell namn och beskrivningar, som skapats enbart för att tillhandahålla demodata. För att komma åt dessa data, måste du ange ett namn och nyckel för den.

1. Öppna filen appsettings.json i det nya projektet och Ersätt standard raderna med följande namn och nyckel. API-nyckel som visas här inte är ett exempel på en nyckel, är det _exakt_ den nyckel som du behöver komma åt Hotelldata. Filen appsettings.json bör nu se ut så här.

```cs
{
  "SearchServiceName": "azs-playground",
  "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
}
```

2. Vi är inte klar med den här filen ännu, väljer egenskaperna för den här filen och ändra den **kopiera till utdatakatalog** att ställa in **kopiera om nyare**.

    ![Kopiera appinställningar till utdata](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Modellen datastrukturer

Modeller (C# klasser) används för kommunikation mellan klient (view), server (controller) och även Azure-molnet med arkitekturen MVC (modell, view, controller). Dessa modeller visas normalt strukturen för de data som bearbetas. Vi behöver även en modell för att hantera vykontroll /-kommunikation.

1. Öppna den **modeller** mapp i ditt projekt med hjälp av Solution Explorer och du ser en standardmodell där: **ErrorViewModel.cs**.

2. Högerklicka på den **modeller** mapp och välj **Lägg till** sedan **nytt objekt**. Välj i dialogrutan som visas, **ASP.NET Core** sedan det första alternativet **klass**. Byt namn på filen .cs till Hotel.cs klicka sedan på **Lägg till**. Ersätt hela innehållet i Hotel.cs med följande kod. Observera den **adress** och **rummet** medlemmarna i klassen, de här fälten är själva klasserna så vi behöver modeller för dem för.

```cs
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

namespace FirstAzureSearchApp.Models
{
    public partial class Hotel
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsFilterable]
        public string HotelId { get; set; }

        [IsSearchable, IsSortable]
        public string HotelName { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnLucene)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Category { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public bool? ParkingIncluded { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [IsFilterable, IsSortable]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }
    }
}
```

3. Följ samma process för att skapa en modell för den **adress** class, förutom att namnge filen Address.cs. Ersätt innehållet med följande.

```cs
using Microsoft.Azure.Search;

namespace FirstAzureSearchApp.Models
{
    public partial class Address
    {
        [IsSearchable]
        public string StreetAddress { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string City { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string StateProvince { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string PostalCode { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Country { get; set; }
    }
}
```

4. Och igen och följer samma process att skapa den **rummet** klass, namnge filen Room.cs. Igen och Ersätt innehållet med följande.

```cs
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace FirstAzureSearchApp.Models
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]

        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

5. Uppsättningen **hotell**, **adress**, och **rummet** klasser är känt i Azure som [ _komplexa typer_ ](search-howto-complex-data-types.md), en viktig funktion i Azure Search. Komplexa typer kan vara många nivåer av klasser och underklasser och aktivera mycket mer komplexa datastrukturer kan representeras än att använda _enkla typer_ (en klass som innehåller endast primitiva medlemmar). Vi behöver göra en mer modell, så går igenom processen för att skapa en ny modellklass igen, men nu anropa klassen SearchData.cs och ersätta standardkoden med följande.

```cs
using Microsoft.Azure.Search.Models;

namespace FirstAzureSearchApp.Models
{
    public class SearchData
    {
        // The text to search for.
        public string searchText { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;
    }
}
```

Den här klassen innehåller användarens indata (**searchText**), och utdata från sökningen (**resultList**). Vilken typ av utdata är viktigt, **DocumentSearchResult&lt;hotell&gt;** , enligt den här typen matchar exakt resultatet från sökningen och vi behöver för att skicka den här referensen via till vyn.



## <a name="create-a-web-page"></a>Skapa en webbsida

Det som du skapade skapas som standard ett antal klienten vyer. De exakta vyerna beroende på vilken version av .NET Core-du använder (vi använder 2.1 i det här exemplet). De finns på den **vyer** mappen i projektet. Du behöver bara ändra filen Index.cshtml (i den **vyer/Home** mapp).

Ta bort innehållet i Index.cshtml i sin helhet och återskapa filen i följande steg.

1. Vi använder två små bilder i vyn. Du kan använda din egen eller kopiera över avbildningarna från GitHub-projektet: azure-logo.png och search.png. Dessa två avbildningar ska placeras i den **wwwroot/avbildningar** mapp.

2. Den första raden Index.cshtml ska referera till modellen kommer vi att använda för kommunikation mellan klient (view) och server (controller), vilket är den **SearchData** modellen som vi skapade. Lägg till följande rad i filen Index.cshtml.

```cs
@model FirstAzureSearchApp.Models.SearchData
```

3. Är det praxis att ange en rubrik för vyn, så nästa raderna ska vara:

```cs
@{
    ViewData["Title"] = "Home Page";
}
```

4. Följande rubriken, referera till en HTML-formatmall som vi skapar inom kort.

```cs
<head>
    <link rel="stylesheet" href="~/css/hotels.css" />
</head>
```

5. Nu till kött för vyn. Ett viktigt att komma ihåg är att vyn har att hantera två situationer. Det måste det första hantera visningen när appen startas först, och användaren har ännu inte angett någon söktext. För det andra, måste den hantera visningen av resultat utöver rutan Sök text för upprepad användning av användaren. För att hantera dessa två situationer som vi behöver kontrollera om modellen tillhandahålls till vyn är null eller inte. En null-modell anger vi finns i först av två situationer (första körningen av appen). Lägg till följande i filen Index.cshtml och Läs igenom kommentarerna.

```cs
<body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea("desc", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
</body>
```

6. Slutligen kan vi lägga till formatmallen. I Visual Studio i den **filen** väljer du menyn **New/fil** sedan **formatmall** (med **Allmänt** markerade). Ersätt standardkoden med följande. Vi kommer inte gå till den här filen i något mer detaljerat, formaten är vanlig HTML-kod.

```cs
   textarea.box1 {
    width: 648px;
    height: 30px;
    border: none;
    background-color: azure;
    font-size: 14pt;
    color: blue;
    padding-left: 5px;
}

textarea.box2 {
    width: 648px;
    height: 100px;
    border: none;
    background-color: azure;
    font-size: 12pt;
    padding-left: 5px;
    margin-bottom: 24px;
}

.sampleTitle {
    font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
    margin: 20px 0;
    font-size: 32px;
    text-align: left;
}

.sampleText {
    font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
    margin: 20px 0;
    font-size: 14px;
    text-align: left;
    height: 30px;
}

.searchBoxForm {
    width: 648px;
    box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
    background-color: #fff;
    display: inline-block;
    border-collapse: collapse;
    border-spacing: 0;
    list-style: none;
    color: #666;
}

.searchBox {
    width: 568px;
    font-size: 16px;
    margin: 5px 0 1px 20px;
    padding: 0 10px 0 0;
    border: 0;
    max-height: 30px;
    outline: none;
    box-sizing: content-box;
    height: 35px;
    vertical-align: top;
}

.searchBoxSubmit {
    background-color: #fff;
    border-color: #fff;
    background-image: url(/images/search.png);
    background-repeat: no-repeat;
    height: 20px;
    width: 20px;
    text-indent: -99em;
    border-width: 0;
    border-style: solid;
    margin: 10px;
    outline: 0;
}
```

7. Spara filen formatmall som hotels.css, i mappen wwwroot/css, tillsammans med site.css standardfilen.

Det är klart vår vy. Vi gör bra pågår. Modeller och vyer har slutförts, endast kontrollanten återstår samman allt.

## <a name="define-methods"></a>Definiera metoder

Vi behöver ändra innehållet i en kontrollenhet (**Start Controller**), som skapas som standard.

1. Öppna filen HomeController.cs och Ersätt den **med** instruktioner med följande.

```cs
using System;
using System.Diagnostics;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using FirstAzureSearchApp.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

### <a name="add-index-methods"></a>Lägg till Index-metoder

Vi ha två **Index** metoder, en som tar inga parametrar (för fallet när appen öppnas) och en som tar en modell som en parameter (för när användaren har angett söktext). Först av dessa metoder skapas som standard. 

1. Lägg till följande metod, efter att standard- **Index()** metod.

```cs
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
```

Observera den **async** deklaration av metoden och **await** anrop till **RunQueryAsync**. Dessa nyckelord ta hand om anropar vår asynkrona och så Undvik blockerar trådar på servern.

Den **fånga** block använder modellen fel som har skapats för oss som standard.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Observera felhanteringen och andra standardvyer och metoder

Beroende på vilken version av .NET Core som används, en något annorlunda uppsättning standard skapas vyer som standard. För .NET Core 2.1 finns standardvyer Index, om och kontakt, sekretess och fel. För .NET Core 2.2 är standardvyer exempelvis Index, sekretess och fel. I båda fallen kan du visa dessa standardsidor som när du kör appen och undersöka hur de ska hanteras på Kontrollpanelen.

Vi kommer att testa problemet finns senare i den här självstudien.

Vi har tagit bort de oanvända vyerna och tillhörande åtgärder i GitHub-exemplet.

### <a name="add-the-runqueryasync-method"></a>Lägg till RunQueryAsync-metod

Azure Search-anropet är inkapslade i vår **RunQueryAsync** metod.

1. Lägg till några statiska variabler som du ställer in Azure-tjänsten och ett anrop för att starta dem först.

```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
```

2. Lägg nu till den **RunQueryAsync** metoden.

```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
```

I den här metoden kan vi först se till vår Azure-konfiguration är initierad och ange sedan vissa sökparametrar. Namnen på fälten i den **Välj** parametern matchar exakt egenskapsnamnen i den **hotell** klass. Det är möjligt att lämna den **Välj** parameter, då alla egenskaper som returneras. Men att ingen **Välj** parametrar är ineffektiv om vi bara är intresserad av en delmängd av data. Genom att ange egenskaper som vi är intresserade, returneras endast dessa egenskaper.

Asynkront anrop för att söka (**model.resultList = await _indexClient.Documents.SearchAsync&lt;hotell&gt;(model.searchText, parametrar);** ) är vad den här självstudien och app handlar om. Den **DocumentSearchResult** klass är ett intressant och bra (när appen körs) är att konfigurera en brytpunkt här och använda en felsökare för att granska innehållet i **model.resultList**. Du bör hitta som är intuitivt, vilket ger dig de data du tillfrågad om, och inte mycket annat.

Nu för tillfället av sanningen.

### <a name="test-the-app"></a>Testa appen

Nu ska vi Kontrollera appen körs korrekt.

1. Välj **Debug/starta utan felsökning** eller tryck på F5. Om du har korrekt kodad saker, får du startvyn i indexet.

     ![Öppna appen](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Ange text, till exempel ”stranden” (eller valfri text som till exempel) och klicka på sökikonen. Du bör få vissa resultat.

     ![Söka efter ”stranden”](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Försök att ange ”five star”. Observera hur du får inga resultat. En mer avancerad sökning skulle behandla ”five star” som en synonym för ”Lyxig” och returnera dessa resultat. Användning av synonymer är tillgänglig i Azure Search, men vi inte kommer igenom den i de första självstudierna.
 
4. Försök att ange ”frekvent” som söktext. Den gör _inte_ returnerar poster med ordet ”hotell” i dem. Vår search är bara hitta hela ord, även om några resultat som returneras.

5. Försök med andra ord: ”pool”, ”Solstrålen”, ”visa” och allt. Azure Search fungerar i sin enklaste, men fortfarande övertygande nivå visas.

## <a name="test-edge-conditions-and-errors"></a>Edge förhållanden och fel

Det är viktigt att verifiera att våra funktioner för felhantering fungerar som de ska, även när allt fungerar perfekt. 

1. I den **Index** metod, efter den **försök {** anropa, ange raden **genererar nya Exception()** . Detta undantag tvingar ett fel när vi söker efter text.

2. Kör appen och ange ”fältet” som söktext och klicka på sökikonen. Undantaget bör leda till problemet finns.

     ![Framtvinga ett fel](./media/tutorial-csharp-create-first-app/azure-search-error.png)

> [!Important]
> Det anses vara en säkerhetsrisk att returnera internt fel siffror i felsidor. Om din app är avsedd för allmänt bruk gör du vissa undersöka säker och bästa praxis vad som returneras när ett fel inträffar.

3. Ta bort **genererar nya Exception()** när du är nöjd felhantering fungerar som den ska.

## <a name="takeaways"></a>Lärdomar

Överväg följande takeaways från det här projektet:

* Ett Azure Search-anrop är kortfattade och det är enkelt att tolka resultaten.
* Asynkrona anrop lägga till en liten mängd komplexiteten kontrollanten, men är den bästa metoden om du planerar att utveckla appar med hög kvalitet.
* Den här appen utförs en enkel textsökning, enligt vad som har ställts in i **searchParameters**. Det här en klass kan dock fyllas med många medlemmar som lägger ambitiös till en sökning. Inte mycket extra arbete krävs för att skapa den här appen betydligt kraftfullare.

## <a name="next-steps"></a>Nästa steg

För att tillhandahålla den bästa användarupplevelsen med hjälp av Azure Search, behöver vi lägga till fler funktioner, särskilt växling (antingen med hjälp av sidan tal eller oändlig rullning), och automatisk komplettering/förslag. Vi kan också mer sofistikerade sökparametrar (till exempel geografiska sökningar på hotels inom en angiven radie en viss tidpunkt och sökresultaten ordning).

Dessa nästa steg täcks i en serie självstudier. Låt oss börja med sidindelning.

> [!div class="nextstepaction"]
> [C#Självstudie: Sökresultat sidbrytning – Azure Search](tutorial-csharp-paging.md)


