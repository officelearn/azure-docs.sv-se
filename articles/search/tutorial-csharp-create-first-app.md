---
title: C# självstudiekurs för att skapa din första app
titleSuffix: Azure Cognitive Search
description: Läs om hur du skapar din första C#-sökapp steg för steg. Självstudien innehåller både en länk till en fungerande app på GitHub och hela processen för att skapa appen från grunden. Lär dig mer om de viktigaste komponenterna i Azure Cognitive Search.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 2b4f67fc448d98239947fd764d4926f1d590c5e2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121582"
---
# <a name="c-tutorial-create-your-first-app---azure-cognitive-search"></a>C# självstudiekurs: Skapa din första app - Azure Cognitive Search

Lär dig hur du skapar ett webbgränssnitt för att fråga och presentera sökresultat från ett index med Hjälp av Azure Cognitive Search. Den här självstudien börjar med ett befintligt, värdbetonat index så att du kan fokusera på att skapa en söksida. Indexet innehåller fiktiva hotelldata. När du har en grundläggande sida kan du förbättra den i efterföljande lektioner för att inkludera personsökning, aspekter och en typ-ahead-upplevelse.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Konfigurera en utvecklingsmiljö
> * Modellera datastrukturer
> * Skapa en webbsida
> * Definiera metoder
> * Testa appen

Du får också lära dig hur enkelt ett söksamtal är. De viktigaste uttalandena i koden som du ska utveckla är inkapslade i följande få rader.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Det här anropet initierar en sökning av Azure-data och returnerar resultaten.

![Söka efter "pool"](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

[Installera Visual Studio](https://visualstudio.microsoft.com/) som ska användas som IDE.

### <a name="install-and-run-the-project-from-github"></a>Installera och köra projektet från GitHub

1. Leta reda på exemplet på GitHub: [Skapa den första appen](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Välj **Klona eller ladda ned** och gör din privata lokala kopia av projektet.
1. Navigera till och öppna lösningen för den grundläggande söksidan med Visual Studio och välj **Start utan felsökning** (eller tryck på F5).
1. Skriv in några ord (till exempel "wifi", "visa", "bar", "parkering" och undersöka resultaten!

    ![Söka efter "wifi"](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Förhoppningsvis kommer det här projektet att fungera smidigt och du har Azure-appen igång. Många av de viktigaste komponenterna för mer sofistikerade sökningar ingår i den här appen, så det är en bra idé att gå igenom den och återskapa den steg för steg.

Om du vill skapa det här projektet från grunden och därmed bidra till att förstärka komponenterna i Azure Cognitive Search i ditt sinne går du igenom följande steg.

## <a name="set-up-a-development-environment"></a>Skapa en utvecklingsmiljö

1. I Visual Studio 2017 eller senare väljer du **Nytt/Projekt** och **ASP.NET sedan grundläggande webbprogram**. Ge projektet ett namn som "FirstAzureSearchApp".

    ![Skapa ett molnprojekt](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. När du har klickat på **OK** för den här projekttypen får du en andra uppsättning alternativ som gäller för det här projektet. Välj **Webbprogram (Modellvy-Controller)**.

    ![Skapa ett MVC-projekt](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Välj Sedan **NuGet Package Manager** på **Verktyg-menyn** och hantera **nuget-paket för lösning...**. Det finns ett paket som vi måste installera. Välj fliken **Bläddra** och skriv "Azure Cognitive Search" i sökrutan. Installera **Microsoft.Azure.Search** när det visas i listan (version 9.0.1 eller senare). Du måste klicka igenom några ytterligare dialogrutor för att slutföra installationen.

    ![Använda NuGet för att lägga till Azure-bibliotek](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-cognitive-search"></a>Initiera Azure Cognitive Search

För detta exempel använder vi offentligt tillgängliga hotelldata. Dessa data är en godtycklig samling av 50 fiktiva hotellnamn och beskrivningar, som skapats enbart i syfte att tillhandahålla demodata. För att komma åt dessa data måste du ange ett namn och en nyckel för dem.

1. Öppna filen appsettings.json i det nya projektet och ersätt standardraderna med följande namn och nyckel. API-nyckeln som visas här är inte ett exempel på en nyckel, det är _precis_ nyckeln du behöver för att komma åt hotelldata. Filen appsettings.json ska nu se ut så här.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Vi är inte klara med den här filen ännu, välj egenskaper för den här filen och ändra inställningen **Kopiera till utdatakatalog** till **Kopiera om nyare**.

    ![Kopiera appinställningarna till utdata](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Modellera datastrukturer

Modeller (C#-klasser) används för att kommunicera data mellan klienten (vyn), servern (styrenheten) och även Azure-molnet med MVC-arkitekturen (modell, vy, controller). Vanligtvis återspeglar dessa modeller strukturen för de data som används. Dessutom behöver vi en modell för att hantera visa / controller kommunikation.

1. Öppna mappen **Modeller** i projektet med Solution Explorer, så visas en standardmodell där: **ErrorViewModel.cs**.

2. Högerklicka på mappen **Modeller** och välj **Lägg till** sedan **nytt objekt**. Välj sedan **ASP.NET Core** i dialogrutan som visas och sedan det första alternativet **Klass**. Byt namn på CS-filen till Hotel.cs och klicka på **Lägg till**. Ersätt allt innehåll i Hotel.cs med följande kod. Lägg märke till **adress** och **rum** medlemmar i klassen, dessa fält är klasser själva så vi kommer att behöva modeller för dem också.

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

3. Följ samma process för att skapa en modell för klassen **Address,** förutom att namnge filen Address.cs. Ersätt innehållet med följande.

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

4. Och återigen, följ samma process för att skapa **klassen Room** och namnge filen Room.cs. Återigen, ersätta innehållet med följande.

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

5. Uppsättningen **av klasser för hotell,** **adress**och **rum** är vad som kallas i Azure som [_komplexa typer_](search-howto-complex-data-types.md), en viktig funktion i Azure Cognitive Search. Komplexa typer kan vara många nivåer djupt av klasser och underklasser, och göra det möjligt för mycket mer komplexa datastrukturer att representeras än att använda _enkla typer_ (en klass som bara innehåller primitiva medlemmar). Vi behöver en modell till, så gå igenom processen att skapa en ny modellklass igen, men den här gången anropar klassen SearchData.cs och ersätter standardkoden med följande.

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

    Den här klassen innehåller användarens indata (**searchText**) och sökningens utdata **(resultList**). Typen av utdata är kritisk, **DocumentSearchResult&lt;&gt;Hotel**, eftersom denna typ exakt matchar resultaten från sökningen, och vi måste skicka denna hänvisning till vyn.



## <a name="create-a-web-page"></a>Skapa en webbsida

Projektet som du skapade skapar som standard ett antal klientvyer. De exakta vyerna beror på vilken version av Core .NET du använder (vi använder 2.1 i det här exemplet). De finns alla i mappen **Vyer** i projektet. Du behöver bara ändra filen Index.cshtml (i mappen **Vyer/Startsida).**

Ta bort innehållet i Index.cshtml i sin helhet och återskapa filen i följande steg.

1. Vi använder två små bilder i vyn. Du kan använda dina egna eller kopiera över bilderna från GitHub-projektet: azure-logo.png och search.png. Dessa två bilder bör placeras i **mappen wwwroot/images.**

2. Den första raden i Index.cshtml bör referera till den modell vi kommer att använda för att kommunicera data mellan klienten (vyn) och servern (styrenheten), som är **SearchData-modellen** vi skapade. Lägg till den här raden i filen Index.cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Det är standard praxis att ange en rubrik för vyn, så nästa rader bör vara:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Efter rubriken anger du en referens till en HTML-formatmall, som vi kommer att skapa inom kort.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Nu till köttet av utsikten. En viktig sak att komma ihåg är att vyn måste hantera två situationer. För det första måste den hantera visningen när appen först startas och användaren har ännu inte angett någon söktext. För det andra måste den hantera visning av resultat, förutom söktextrutan, för upprepad användning av användaren. För att hantera dessa två situationer måste vi kontrollera om modellen som anges i vyn är null eller inte. En null-modell indikerar att vi befinner oss i den första av de två situationerna (den första körningen av appen). Lägg till följande i filen Index.cshtml och läs igenom kommentarerna.

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
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. Slutligen lägger vi till formatmallen. I Visual Studio väljer du **Ny/Arkiv-formatmall** på **Arkiv-menyn** (med **Allmänt** markerat). **Style Sheet** Ersätt standardkoden med följande. Vi kommer inte att gå in i den här filen mer i detalj, stilar är standard HTML.

    ```html
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

7. Spara formatmallsfilen som hotels.css i mappen wwwroot/css tillsammans med filen site.css.

Det avslutar vår åsikt. Vi gör goda framsteg. Modellerna och vyerna är klara, bara handkontrollen är kvar för att knyta ihop allt.

## <a name="define-methods"></a>Definiera metoder

Vi måste ändra till innehållet i en controller **(Home Controller),** som skapas som standard.

1. Öppna HomeController.cs-filen och ersätt **med hjälpsatserna** med följande.

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

### <a name="add-index-methods"></a>Lägg till indexmetoder

Vi behöver två **indexmetoder,** en som inte tar några parametrar (för fallet när appen öppnas första gången) och en som tar en modell som parameter (för när användaren har angett söktext). Den första av dessa metoder skapas som standard. 

1. Lägg till följande metod efter standardmetoden **Index().**

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

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Lägg märke till **metodens asynkrona** deklaration och **det väntande** anropet till **RunQueryAsync**. Dessa sökord tar hand om att göra våra samtal asynkrona, och så undvik att blockera trådar på servern.

    **Catch-blocket** använder felmodellen som skapades för oss som standard.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Observera felhantering och andra standardvyer och metoder

Beroende på vilken version av .NET Core du använder skapas en något annorlunda uppsättning standardvyer som standard. För .NET Core 2.1 är standardvyerna Index, Om, Kontakt, Sekretess och Fel. För .NET Core 2.2 är till exempel standardvyerna Index, Sekretess och Fel. I båda fallen kan du visa dessa standardsidor när du kör appen och undersöka hur de hanteras i handkontrollen.

Vi kommer att testa felvyn senare i den här självstudien.

I GitHub-exemplet har vi tagit bort oanvända vyer och tillhörande åtgärder.

### <a name="add-the-runqueryasync-method"></a>Lägga till metoden RunQueryAsync

Azure Cognitive Search-anropet är inkapslat i vår **RunQueryAsync-metod.**

1. Lägg först till några statiska variabler för att konfigurera Azure-tjänsten och ett anrop för att initiera dem.

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

2. Lägg nu till själva **metoden RunQueryAsync.**

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

    I den här metoden säkerställer vi först att vår Azure-konfiguration initieras och sedan ange några sökparametrar. Namnen på fälten i parametern **Välj** matchar exakt egenskapsnamnen i **hotellklassen.** Det är möjligt att utelämna parametern **Select,** i vilket fall alla egenskaper returneras. Att ange inga **Select-parametrar** är dock ineffektivt om vi bara är intresserade av en delmängd av data. Genom att ange de egenskaper vi är intresserade av returneras endast dessa egenskaper.

    Den asynkrona uppmaningen att söka (**model.resultList = väntar _indexClient.Documents.SearchAsync&lt;Hotel&gt;(model.searchText, parametrar);**) är vad den här guiden och appen handlar om. Klassen **DocumentSearchResult** är intressant och en bra idé (när appen körs) är att ange en brytpunkt här och använda en felsökare för att undersöka innehållet i **model.resultList**. Du bör upptäcka att det är intuitivt, ger dig de uppgifter du bad om, och inte mycket annat.

Nu till sanningens ögonblick.

### <a name="test-the-app"></a>Testa appen

Nu ska vi kontrollera att appen körs korrekt.

1. Välj **Felsökning/Start utan felsökning** eller tryck på F5. Om du har kodat saker på rätt sätt får du den första indexvyn.

     ![Öppna appen](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Skriv text som "strand" (eller text som kommer att tänka på) och klicka på sökikonen. Du borde få lite resultat.

     ![Söka efter "strand"](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Försök att skriva in "femstjärniga". Notera hur du inte får några resultat. En mer sofistikerad sökning skulle behandla "femstjärniga" som en synonym för "lyx" och returnera dessa resultat. Användningen av synonymer är tillgänglig i Azure Cognitive Search, men vi kommer inte att täcka det i de första självstudierna.
 
4. Prova att skriva in "hot" som söktext. Det returnerar _inte_ poster med ordet "hotell" i dem. Vår sökning är bara att hitta hela ord, men några resultat returneras.

5. Prova med andra ord: "pool", "solsken", "visa", och vad som helst. Du kommer att se Azure Cognitive Search som arbetar på sin enklaste, men ändå övertygande nivå.

## <a name="test-edge-conditions-and-errors"></a>Testa kantförhållanden och fel

Det är viktigt att kontrollera att våra funktioner för felhantering fungerar som de ska, även när saker och ting fungerar perfekt. 

1. Ange **Index** raden **Kasta nytt undantag()** efter försöket { anropa efter **försöket {** efter anropet { . Det här undantaget tvingar fram ett fel när vi söker på text.

2. Kör appen, skriv "bar" som söktext och klicka på sökikonen. Undantaget bör resultera i felvyn.

     ![Tvinga fram ett fel](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Det anses vara en säkerhetsrisk för att returnera interna felnummer på felsidor. Om din app är avsedd för allmänt bruk, gör en undersökning av säkra och bästa praxis för vad som ska returneras när ett fel inträffar.

3. Ta **bort Kasta nytt undantag()** när du är nöjd felhantering fungerar som det ska.

## <a name="takeaways"></a>Lärdomar

Tänk på följande takeaways från det här projektet:

* Ett Azure Cognitive Search-anrop är koncist och det är enkelt att tolka resultaten.
* Asynkrona anrop lägger till en liten mängd komplexitet i styrenheten, men är det bästa sättet att använda om du tänker utveckla kvalitetsappar.
* Denna app utförde en enkel textsökning, definierad av vad som ställs in i **searchParameters**. Den här klassen kan dock fyllas med många medlemmar som lägger till förfining i en sökning. Inte mycket extra arbete behövs för att göra denna app betydligt mer kraftfull.

## <a name="next-steps"></a>Nästa steg

För att ge den bästa användarupplevelsen med Azure Cognitive Search måste vi lägga till fler funktioner, särskilt växling (antingen med hjälp av sidnummer eller oändlig rullning) och automatisk komplettering/förslag. Vi bör också överväga mer sofistikerade sökparametrar (till exempel geografiska sökningar på hotell inom en angiven radie från en viss punkt och sökresultatbeställning).

Dessa nästa steg tas upp i en serie tutorials. Låt oss börja med personsökning.

> [!div class="nextstepaction"]
> [C# Självstudiekurs: Sidnumrering av sökresultat - Azure Cognitive Search](tutorial-csharp-paging.md)


