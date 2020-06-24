---
title: C#-självstudie om att använda Faces för att under lätta navigering
titleSuffix: Azure Cognitive Search
description: Fortsätt från "växlings resultat" för att lägga till en fasett-navigering. Lär dig hur ansikts kan användas för att enkelt begränsa en sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: 7d91348b727c1c43f010ec9f60ae9abd33978ce8
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85257524"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>Självstudie: Lägg till en fasett-navigering med .NET SDK

Ansikte används för att under lätta navigeringen genom att förse användaren med en uppsättning länkar som ska användas för att fokusera sökningen. Facets är attribut för data (till exempel kategorin eller en viss funktion) av ett hotell i våra exempel data.

Den här självstudien bygger på det växlings projekt som skapats i [C#-själv studie kursen: Sök Resultat sid brytning – Azure kognitiv sökning](tutorial-csharp-paging.md) själv studie kurs.

I de här självstudierna får du lära dig att
> [!div class="checklist"]
> * Ange modell egenskaper som _IsFacetable_
> * Lägg till aspekt navigering till din app

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

Har [C#-själv studie kursen: Sök Resultat sid brytning – Azure kognitiv sökning](tutorial-csharp-paging.md) Project up och igång. Projektet kan antingen vara din egen version eller installeras från GitHub: [skapa första app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Ange modell egenskaper som IsFacetable

För att en modell egenskap ska finnas i en aspekt-sökning måste den märkas med **IsFacetable**.

1. Granska **hotell** klassen. **Kategori** och **taggar**är till exempel Taggade som **IsFacetable**, men **HotelName** och **Description** är inte. 

    ```cs
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
    ```

2. Vi kommer inte att ändra några taggar som en del av den här självstudien, så Stäng hotel.cs-filen utan ändringar.

    > [!Note]
    > En aspekts ökning genererar ett fel om ett fält som begärs i sökningen inte är korrekt taggat.


## <a name="add-facet-navigation-to-your-app"></a>Lägg till aspekt navigering till din app

I det här exemplet ska vi göra det möjligt för användaren att välja en kategori av hotellet eller en Amenity från listor över länkar som visas till vänster om resultatet. Användaren startar genom att ange en Sök text och kan begränsa Sök resultatet genom att välja en kategori och kan begränsa resultaten ytterligare genom att välja en Amenity, eller så kan de välja Amenity först (ordningen är inte viktig).

Vi behöver styrenheten för att skicka listor över ansikte till vyn. Vi måste underhålla användar valen när sökningen pågår, och återigen använder vi tillfällig lagring som mekanism för att bevara data.

![Använda aspekt navigering för att begränsa en sökning av "pool"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Lägg till filter strängar i SearchData-modellen

1. Öppna filen SearchData.cs och Lägg till sträng egenskaper till **SearchData** -klassen för att innehålla fasett filter strängarna.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Lägg till åtgärds metoden aspekt

Hem styrenheten behöver en ny åtgärd, **aspekt**och uppdateringar av befintliga **index** -och **sid** åtgärder, samt uppdateringar av **RunQueryAsync** -metoden.

1. Öppna filen med hemkontrollanten och Lägg till **using** -instruktionen för att **aktivera &lt; list &gt; sträng** konstruktionen.

    ```cs
    using System.Collections.Generic;
    ```

2. Byt ut åtgärds metoden **index (SearchData Model)** .

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the search call for the first page.
                await RunQueryAsync(model, 0, 0, "", "");
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. Ersätt sidan med åtgärds metoden **(SearchData Model)** .

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

                // Calculate the page that should be displayed.
                switch (model.paging)
                {
                    case "prev":
                        page = (int)TempData["page"] - 1;
                        break;

                    case "next":
                        page = (int)TempData["page"] + 1;
                        break;

                    default:
                        page = int.Parse(model.paging);
                        break;
                }

                // Recover the leftMostPage.
                int leftMostPage = (int)TempData["leftMostPage"];

                // Recover the filters.
                string catFilter = TempData["categoryFilter"].ToString();
                string ameFilter = TempData["amenityFilter"].ToString();

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Search for the new page.
                await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

4. Lägg till en **aspekt (SearchData modell)** åtgärds metod som aktive ras när användaren klickar på en aspekt länk. Modellen innehåller antingen ett Sök filter för kategori eller ett Amenity-sökfilter. Kanske lägger du till det efter **sid** åtgärden.

    ```cs
        public async Task<ActionResult> Facet(SearchData model)
        {
            try
            {
                // Filters set by the model override those stored in temporary data.
                string catFilter;
                string ameFilter;
                if (model.categoryFilter != null)
                {
                    catFilter = model.categoryFilter;
                } else
                {
                    catFilter = TempData["categoryFilter"].ToString();
                }

                if (model.amenityFilter != null)
                {
                    ameFilter = model.amenityFilter;
                } else
                {
                    ameFilter = TempData["amenityFilter"].ToString();
                }

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Initiate a new search.
                await RunQueryAsync(model, 0, 0, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

### <a name="set-up-the-search-filter"></a>Konfigurera Sök filtret

När en användare väljer en viss aspekt, till exempel, klickar på kategorin **anläggning och Spa** , kommer bara hotell som anges som den här kategorin att returneras i resultaten. Vi måste skapa ett _filter_för att begränsa en sökning på det här sättet.

1. Ersätt **RunQueryAsync** -metoden med följande kod. Främst tar det en kategori filter sträng och en Amenity filter sträng, och anger **filter** parametern för **SearchParameters**.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
        {
            InitSearch();

            string facetFilter = "";

            if (catFilter.Length > 0 && ameFilter.Length > 0)
            {
                // Both facets apply.
                facetFilter = $"{catFilter} and {ameFilter}"; 
            } else
            {
                // One, or zero, facets apply.
                facetFilter = $"{catFilter}{ameFilter}";
            }

            var parameters = new SearchParameters
            {
                Filter = facetFilter,

                // Return information on the text, and number, of facets in the data.
                Facets = new List<string> { "Category,count:20", "Tags,count:20" },

                // Enter Hotel property names into this list, so only these values will be returned.
                Select = new[] { "HotelName", "Description", "Category", "Tags" },

                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Top = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalResultCount = true,
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ((int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
            {
                // Trigger a switch to a higher page range.
                leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
            }
            model.leftMostPage = leftMostPage;

            // Calculate the number of page numbers to display.
            model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

            // Ensure Temp data is stored for the next call.
            TempData["page"] = page;
            TempData["leftMostPage"] = model.leftMostPage;
            TempData["searchfor"] = model.searchText;
            TempData["categoryFilter"] = catFilter;
            TempData["amenityFilter"] = ameFilter;

            // Return the new view.
            return View("Index", model);
        }
    ```

    Vi har lagt till **kategori** -och **taggar** -egenskaperna i listan med **Välj** objekt att returnera. Detta tillägg är inte ett krav för att aspekt navigeringen ska fungera, men vi använder den här informationen för att kontrol lera att vi filtrerar korrekt.

### <a name="add-lists-of-facet-links-to-the-view"></a>Lägg till listor över aspekt länkar till vyn

Vyn kommer att kräva några betydande ändringar. 

1. Börja med att öppna filen Hotels. CSS (i mappen wwwroot/CSS) och Lägg till följande klasser.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;    
    }
    ```

2. För vyn organiserar vi resultatet i en tabell, så att du kan justera fasett-listorna till vänster och resultatet till höger. Öppna filen index. cshtml. Ersätt hela innehållet i HTML Body- &lt; &gt; taggarna med följande kod.

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Facet Navigation
                    </h1>
                </td>
            </tr>

            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it.-->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.resultList != null)
                        {
                            List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                            if (categories.Count > 0)
                            {
                                <h5 class="facetheader">Category:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < categories.Count; c++)
                                    {
                                        var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                        </li>
                                    }
                                </ul>
                            }

                            List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                            if (tags.Count > 0)
                            {
                                <h5 class="facetheader">Amenities:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < tags.Count; c++)
                                    {
                                        var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                        </li>
                                    }
                                </ul>
                            }
                        }
                    </div>
                </td>
                <td valign="top">
                    <div id="resultsplace">
                        @if (Model != null && Model.resultList != null)
                        {
                            // Show the result count.
                            <p class="sampleText">
                                @Html.DisplayFor(m => m.resultList.Count) Results
                            </p>

                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);

                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nCategory: {Model.resultList.Results[i].Document.Category}";
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                            }
                        }
                    </div>
                </td>
            </tr>

            <tr>
                <td></td>
                <td valign="top">
                    @if (Model != null && Model.pageCount > 1)
                    {
                        // If there is more than one page of results, show the paging buttons.
                        <table>
                            <tr>
                                <td class="tdPage">
                                    @if (Model.currentPage > 0)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">|&lt;</p>
                                    }
                                </td>

                                <td class="tdPage">
                                    @if (Model.currentPage > 0)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">&lt;</p>
                                    }
                                </td>

                                @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
                                {
                                    <td class="tdPage">
                                        @if (Model.currentPage == pn)
                                        {
                                            // Convert displayed page numbers to 1-based and not 0-based.
                                            <p class="pageSelected">@(pn + 1)</p>
                                        }
                                        else
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                                            </p>
                                        }
                                    </td>
                                }

                                <td class="tdPage">
                                    @if (Model.currentPage < Model.pageCount - 1)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">&gt;</p>
                                    }
                                </td>

                                <td class="tdPage">
                                    @if (Model.currentPage < Model.pageCount - 1)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">&gt;|</p>
                                    }
                                </td>
                            </tr>
                        </table>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

    Observera användningen av **HTML. ActionLink-** anropet. Det här anropet kommunicerar giltiga filter strängar till kontroll enheten när användaren klickar på en aspekt länk. 

### <a name="run-and-test-the-app"></a>Kör och testa appen

Fördelen med aspekt navigering till användaren är att de kan begränsa sökningar med ett enda klick, som vi kan visa i följande ordning.

1. Kör appen, skriv "flyg plats" som Sök text. Kontrol lera att listan över ansikte visas snyggt till vänster. Dessa ansikte är allt som gäller för hotell som har "flyg plats" i sina text data, med en räkning av hur ofta de inträffar.

    ![Använda aspekt navigering för att begränsa en sökning efter "flyg plats"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Klicka på kategorin **anläggning och Spa** . Kontrol lera att alla resultat finns i den här kategorin.

    ![Begränsa sökningen till "utväg och Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Klicka på den **kontinentala frukost** Amenity. Kontrol lera att alla resultat fortfarande finns i kategorin "utväg och Spa" med den valda Amenity.

    ![Begränsa sökningen till "fast frukost"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Försök att välja någon annan kategori, sedan en Amenity och Visa de smala resultaten. Prova sedan på det andra sättet, en Amenity, och sedan en kategori.

    >[!Note]
    > När ett val görs i en fasett-lista (t. ex. kategori) åsidosätts alla tidigare val i kategori listan.

## <a name="takeaways"></a>Lärdomar

Tänk på följande takeaways från det här projektet:

* Det är absolut nödvändigt att markera varje egenskap som **IsFacetable**, om de ska tas med i aspekt navigering.
* Aspekt navigering är en användare med ett enkelt och intuitivt sätt att begränsa en sökning.
* Aspekt navigering är bäst indelad i avsnitt (hotell kategorier, bekvämligheterna av ett hotell, pris intervall, klassificerings intervall osv.), varje avsnitt med en lämplig rubrik.

## <a name="next-steps"></a>Nästa steg

I nästa självstudie tittar vi på att beställa resultat. Fram till den här punkten beställs resultatet enkelt i den ordning som de finns i databasen.

> [!div class="nextstepaction"]
> [C#-självstudie: ordna resultaten – Azure Kognitiv sökning](tutorial-csharp-orders.md)
