---
title: C#-självstudie för att skapa din första app
titleSuffix: Azure Cognitive Search
description: Lär dig hur du skapar ditt första C#-sökprogram steg för steg. I självstudien finns både en länk till en fungerande app på GitHub och den fullständiga processen för att bygga appen från grunden. Lär dig mer om de viktigaste komponenterna i Azure Kognitiv sökning.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 2b4f67fc448d98239947fd764d4926f1d590c5e2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "77121582"
---
# <a name="c-tutorial-create-your-first-app---azure-cognitive-search"></a>C#-självstudie: skapa din första app – Azure-Kognitiv sökning

Lär dig hur du skapar ett webb gränssnitt för att fråga och presentera Sök Resultat från ett index med Azure Kognitiv sökning. Den här självstudien börjar med en befintlig, värdbaserad index så att du kan fokusera på att skapa en Sök sida. Indexet innehåller fiktiva hotell data. När du har en grundläggande sida kan du förbättra den i efterföljande lektioner för att inkludera sid indelning, ansikte och en typ i förväg-upplevelse.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera en utvecklings miljö
> * Modell data strukturer
> * Skapa en webb sida
> * Definiera metoder
> * Testa appen

Du får också lära dig hur enkelt ett Sök samtal är. Nyckel uttrycken i koden som du utvecklar kapslas in på följande rader.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Det här anropet initierar en sökning i Azure-data och returnerar resultatet.

![Söker efter "pool"](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

[Installera Visual Studio](https://visualstudio.microsoft.com/) för att använda som IDE.

### <a name="install-and-run-the-project-from-github"></a>Installera och kör projektet från GitHub

1. Leta upp exemplet på GitHub: [skapa första app](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Välj **klona eller ladda ned** och gör din privata lokala kopia av projektet.
1. Använd Visual Studio, gå till och öppna lösningen för den grundläggande Sök sidan och välj **starta utan fel sökning** (eller tryck på F5).
1. Skriv några ord (till exempel "WiFi", "View", "bar", "parkering") och granska resultaten.

    ![Söker efter "WiFi"](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Förhoppnings vis kommer det här projektet att köras smidigt och du har Azure-appen igång. Många av de viktigaste komponenterna för mer avancerade sökningar ingår i den här appen, så det är en bra idé att gå igenom den och återskapa den steg för steg.

Gå igenom följande steg för att skapa projektet från grunden och därmed hjälpa till att förstärka komponenterna i Azure Kognitiv sökning i åtanke.

## <a name="set-up-a-development-environment"></a>Konfigurera en utvecklings miljö

1. I Visual Studio 2017 eller senare väljer du **nytt/projekt** och **ASP.net Core webb program**. Ge projektet ett namn, till exempel "FirstAzureSearchApp".

    ![Skapa ett moln projekt](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. När du har klickat på **OK** för den här projekt typen får du en andra uppsättning alternativ som gäller för projektet. Välj **webb program (modell-se-Controller)**.

    ![Skapa ett MVC-projekt](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Gå sedan till **verktyg** -menyn och välj **NuGet Package Manager** och sedan **Hantera NuGet-paket för lösning...**. Det finns ett paket som vi behöver installera. Välj fliken **Bläddra** och skriv sedan "Azure kognitiv sökning" i sökrutan. Installera **Microsoft. Azure. Sök** när det visas i listan (version 9.0.1 eller senare). Du kommer att behöva klicka igenom några ytterligare dialog rutor för att slutföra installationen.

    ![Använda NuGet för att lägga till Azure-bibliotek](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-cognitive-search"></a>Initiera Azure-Kognitiv sökning

För det här exemplet använder vi offentligt tillgängliga hotell data. Dessa data är en godtycklig samling med 50 fiktiva hotell namn och beskrivningar, som skapas enbart för att tillhandahålla demo data. Du måste ange ett namn och en nyckel för att kunna komma åt dessa data.

1. Öppna filen appSettings. json i det nya projektet och ersätt standard raderna med följande namn och nyckel. API-nyckeln som visas här är inte ett exempel på en nyckel, det är _exakt_ den nyckel du behöver för att komma åt hotell data. Filen appSettings. JSON bör nu se ut så här.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Vi är inte klar med den här filen ännu, Välj egenskaperna för den här filen och ändra inställningen **Kopiera till utdata-katalog** för att **Kopiera om nyare**.

    ![Kopiera appens inställningar till utdata](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Modell data strukturer

Modeller (C#-klasser) används för att kommunicera data mellan klienten (vyn), servern (kontrollanten) och Azure-molnet med MVC-arkitekturen (modell, View, Controller). Dessa modeller visar vanligt vis strukturen för de data som ska nås. Dessutom behöver vi en modell för att hantera kommunikationen mellan vyer och kontrollanter.

1. Öppna mappen **modeller** i projektet med Solution Explorer, så visas en standard modell i: **ErrorViewModel.cs**.

2. Högerklicka på mappen **modeller** och välj **Lägg till** **nya objekt**. Välj **ASP.net Core** sedan den första alternativ **klassen**i dialog rutan som visas. Byt namn på. cs-filen till Hotel.cs och klicka på **Lägg till**. Ersätt allt innehåll i Hotel.cs med följande kod. Lägg märke till klassens **adress** och **rums** medlemmar, dessa fält är klasser själva så att vi behöver modeller för dem.

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

3. Följ samma process för att skapa en modell för **adress** klassen, förutom att namnge filen address.cs. Ersätt innehållet med följande.

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

4. Och återigen följer du samma process för att skapa **rums** klassen och namnger filen Room.cs. Ersätt innehållet igen med följande.

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

5. Uppsättningen **hotell**-, **adress**-och **rums** klasser är vad som är känt i Azure som [_komplexa typer_](search-howto-complex-data-types.md), en viktig funktion i Azure kognitiv sökning. Komplexa typer kan vara många nivåer djup i klasser och underklasser och gör det möjligt att visa mycket mer komplexa data strukturer än att använda _enkla typer_ (en klass som bara innehåller primitiva medlemmar). Vi behöver en mer modell, så gå igenom processen för att skapa en ny modell klass igen, men den här gången anropar klassen SearchData.cs och ersätter standard koden med följande.

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

    Den här klassen innehåller användarens indata (**searchText**) och sökningens utdata (**resultList**). Typen av utdata är kritisk, **DocumentSearchResult&lt;hotellet&gt;**, eftersom den här typen exakt matchar resultatet från sökningen och vi måste skicka den här referensen till vyn.



## <a name="create-a-web-page"></a>Skapa en webb sida

Det projekt som du skapade kommer som standard att skapa ett antal klient visningar. De exakta vyerna beror på vilken version av Core .NET du använder (vi använder 2,1 i det här exemplet). De finns i mappen **vyer** i projektet. Du behöver bara ändra filen index. cshtml (i mappen **vyer/hem** ).

Ta bort innehållet i index. cshtml i sin helhet och återskapa filen i följande steg.

1. Vi använder två små bilder i vyn. Du kan använda din egen eller kopiera över bilderna från GitHub-projektet: Azure-logo. png och search. png. De här två avbildningarna ska placeras i mappen **wwwroot/images** .

2. Den första raden i index. cshtml ska referera till modellen som vi ska använda för att kommunicera data mellan klienten (vyn) och servern (kontrollanten), som är den **SearchData** -modell som vi skapade. Lägg till den här raden i filen index. cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Det är standard praxis att ange en rubrik för vyn, så att följande rader ska vara:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Efter rubriken anger du en referens till en HTML-formatmall som vi kommer att skapa snart.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Nu till köttet i vyn. En viktig sak att komma ihåg är att vyn måste hantera två situationer. För det första måste den hantera visningen när appen startas första gången, och användaren har ännu inte angett någon Sök text. För det andra måste den hantera visningen av resultat, förutom sökrutan för att användaren ska kunna använda den upprepade gånger. För att hantera dessa två situationer måste vi kontrol lera om modellen som har angetts till vyn är null eller inte. En null-modell indikerar att vi är i den första av de två situationerna (den första körningen av appen). Lägg till följande i filen index. cshtml och Läs igenom kommentarerna.

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

6. Slutligen lägger vi till format mal len. I Visual Studio väljer du **ny/fil** på **Arkiv** -menyn och sedan **format blad** (med **Allmänt** markerat). Ersätt standard koden med följande. Vi kommer inte att gå till den här filen i mer detalj, formatmallarna är standard-HTML.

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

7. Spara format filen som Hotels. CSS, i mappen wwwroot/CSS, tillsammans med standard platsen. css-filen.

Vi slutför vyn. Vi gör ett lyckat framsteg. Modellerna och vyerna har slutförts, men det är bara kontroll enheten som är kvar att knyta samman allt.

## <a name="define-methods"></a>Definiera metoder

Vi måste ändra innehållet i en kontrollant (**start kontroll**) som skapas som standard.

1. Öppna filen HomeController.cs och Ersätt **using** -instruktionerna med följande.

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

### <a name="add-index-methods"></a>Lägg till index metoder

Vi behöver två **index** metoder, ett som inte tar några parametrar (för det fall då appen först öppnas) och en som tar en modell som en parameter (för när användaren har angett söktext). Den första av dessa metoder skapas som standard. 

1. Lägg till följande metod efter standard **index ()** -metoden.

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

    Lägg märke till metodens **asynkrona** deklaration och **await** -anropet till **RunQueryAsync**. De här nyckelorden tar hand om att göra våra anrop asynkrona och Undvik att blockera trådar på servern.

    **Catch** -blocket använder fel modellen som har skapats för oss som standard.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Observera fel hanteringen och andra standardvyer och metoder

Beroende på vilken version av .NET Core som du använder skapas en något annorlunda uppsättning standardvyer som standard. För .NET Core 2,1 är standardvyerna index, om, kontakt, sekretess och fel. För .NET Core 2,2 är till exempel standardvyerna index, Privacy och Error. I båda fallen kan du visa dessa standard sidor när du kör appen och kontrol lera hur de hanteras i kontrollanten.

Vi kommer att testa fel visningen senare i den här självstudien.

I GitHub-exemplet har vi tagit bort oanvända vyer och deras associerade åtgärder.

### <a name="add-the-runqueryasync-method"></a>Lägg till RunQueryAsync-metoden

Azure Kognitiv sökning-anropet är inkapslat i vår **RunQueryAsync** -metod.

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

2. Lägg nu till själva **RunQueryAsync** -metoden.

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

    I den här metoden ser vi först till att vår Azure-konfiguration initieras och anger sedan vissa Sök parametrar. Namnen på fälten i **Select** -parametern matchar exakt egenskaps namnen i **hotellet** -klassen. Det går att lämna ut den **valda** parametern, i vilket fall alla egenskaper returneras. Att ange inga **val** parametrar är dock inte effektivt om vi bara är intresserade av en delmängd av data. Genom att ange de egenskaper som vi är intresse rad av returneras endast dessa egenskaper.

    Det asynkrona anropet till search (**modell. resultList = await _indexClient. Documents. SearchAsync&lt;hotell&gt;(Model. searchText, Parameters);**) är vad den här själv studie kursen och appen är mer om. **DocumentSearchResult** -klassen är en intressant och en bra idé (när appen körs) är att ange en Bryt punkt här och använda en fel sökare för att undersöka innehållet i **Model. resultList**. Du bör se att det är intuitivt, som ger dig de data du bad om och inte mycket annat.

För tillfället för sanningen.

### <a name="test-the-app"></a>Testa appen

Nu ska vi kontrol lera att appen fungerar korrekt.

1. Välj **Felsök/starta utan fel sökning** eller tryck på F5-tangenten. Om du har kodat saker korrekt visas den inledande index vyn.

     ![Öppnar appen](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Ange text som "strand" (eller någon text som kommer till åtanke) och klicka på Sök ikonen. Du bör få några resultat.

     ![Söker efter "strand"](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Försök att ange "fem stjärnor". Observera att du inte får några resultat. En mer avancerad sökning skulle behandla "fem stjärnor" som synonymer för "lyxen" och returnera resultaten. Användningen av synonymer är tillgänglig i Azure Kognitiv sökning, men vi kommer inte att täcka det i de första självstudierna.
 
4. Försök att ange "Het" som söktext. Det returnerar _inte_ poster med ordet "hotell" i dem. Sök funktionen söker bara hela ord, även om några resultat returneras.

5. Försök med andra ord: "pool", "solsken", "View" och vad som är. Du kommer att se Azure Kognitiv sökning som arbetar på det enklaste, men ändå övertygandenivå.

## <a name="test-edge-conditions-and-errors"></a>Villkor för testning och fel

Det är viktigt att kontrol lera att våra fel hanterings funktioner fungerar som de ska, även om saker fungerar perfekt. 

1. I **index** -metoden, efter **try {** Call, anger du raden **Throw New Exception ()**. Detta undantag innebär ett fel när vi söker efter text.

2. Kör appen, ange "bar" som söktext och klicka på Sök ikonen. Undantaget bör resultera i vyn fel.

     ![Framtvinga ett fel](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Det betraktas som en säkerhets risk för att returnera interna fel nummer på felsidor. Om din app är avsedd för allmän användning kan du utföra en undersökning i säkra och bästa metoder för vad som ska returneras när ett fel uppstår.

3. Ta bort **Utlös nytt undantag ()** när du är nöjd med fel hanteringen som det ska.

## <a name="takeaways"></a>Lärdomar

Tänk på följande takeaways från det här projektet:

* Ett Azure Kognitiv sökning-anrop är koncist och det är enkelt att tolka resultaten.
* Asynkrona anrop lägger till en liten mängd komplexitet för kontrollanten, men är den bästa metoden om du planerar att utveckla kvalitets appar.
* Den här appen utförde en enkel texts ökning, som definieras av vad som har ställts in i **searchParameters**. Den här en klass kan dock fyllas i med många medlemmar som lägger till riktigt ambitiös i en sökning. Inte mycket ytterligare arbete krävs för att göra den här appen avsevärt mer kraftfull.

## <a name="next-steps"></a>Nästa steg

För att tillhandahålla den bästa användar upplevelsen med Azure Kognitiv sökning behöver vi lägga till fler funktioner, särskilt sid indelning (antingen med sid nummer eller oändlig rullning) och Autoavsluta/förslag. Vi bör också överväga mer avancerade Sök parametrar (till exempel geografisk sökning på hotell inom en angiven radie av en given punkt och Sök Resultat ordningen).

Dessa nästa steg beskrivs i en serie självstudier. Vi börjar med växling.

> [!div class="nextstepaction"]
> [C#-självstudie: sid brytning för Sök Resultat – Azure Kognitiv sökning](tutorial-csharp-paging.md)


