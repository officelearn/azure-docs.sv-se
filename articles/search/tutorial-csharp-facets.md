---
title: 'C # handledning om hur du använder aspekter för att underlätta navigering'
titleSuffix: Azure Cognitive Search
description: Den här självstudien bygger på projektet "Sökresultat sidnumrering - Azure Cognitive Search" för att lägga till aspektnavigering. Lär dig hur aspekter kan användas för att enkelt begränsa en sökning.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: d88a9d7efdabd493fd31b961748bb6ad3bd8d738
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121561"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-cognitive-search"></a>C# självstudiekurs: Använd aspekter för att underlätta navigering - Azure Cognitive Search

Fasanter används för att underlätta navigering, genom att ge användaren en uppsättning länkar att använda för att fokusera sin sökning. Faser är attribut för data (t.ex. kategori, eller en specifik funktion, för ett hotell i våra exempeldata).

Den här självstudien bygger på det växlingsprojekt som skapats i [C#-självstudien: Sökresultatuppväxling -](tutorial-csharp-paging.md) Azure Cognitive Search-självstudien.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Ange modellegenskaper som _IsFacetable_
> * Lägga till aspektnavigering i appen

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

Har [C# Tutorial: Sökresultat sidnumrering - Azure Cognitive Search](tutorial-csharp-paging.md) projektet igång. Det här projektet kan antingen vara din egen version eller installera den från GitHub: [Skapa den första appen](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Ange modellegenskaper som IsFacetable

För att en modellegenskap ska placeras i en aspektsökning måste den vara taggad med **IsFacetable**.

1. Undersök **hotellklassen.** **Kategori** och **taggar**, till exempel, är taggade som **IsFacetable**, men **HotelName** och **Beskrivning** är inte. 

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

2. Vi kommer inte att ändra några taggar som en del av den här guiden, så stäng hotel.cs filen oförändrad.

    > [!Note]
    > En aspektsökning genererar ett fel om ett fält som begärs i sökningen inte har taggats på rätt sätt.


## <a name="add-facet-navigation-to-your-app"></a>Lägga till aspektnavigering i appen

I det här exemplet kommer vi att göra det möjligt för användaren att välja en kategori av hotell, eller en skönhet, från listor med länkar som visas till vänster om resultaten. Användaren börjar med att ange en del söktext, sedan kan begränsa resultatet av sökningen genom att välja en kategori, och kan begränsa resultaten ytterligare genom att välja en skönhet, eller de kan välja skönhet först (ordern är inte viktigt).

Vi behöver den registeransvarige att passera listorna över aspekter till utsikten. Vi måste behålla användarvalen allteftersom sökningen fortskrider, och återigen använder vi tillfällig lagring som mekanism för att bevara data.

![Använda aspektnavigering för att begränsa en sökning av "pool"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Lägga till filtersträngar i SearchData-modellen

1. Öppna SearchData.cs-filen och lägg till strängegenskaper i klassen **SearchData** för att hålla i fasningens filtersträngar.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Lägga till facet-åtgärdsmetoden

Hemkontrollanten behöver en ny åtgärd, **Aspekt**och uppdateringar av sina befintliga **index-** och **sidåtgärder,** samt uppdateringar av **metoden RunQueryAsync.**

1. Öppna hemkontrollfilen och lägg till **den med statement** som ska aktivera **&lt;liststrängkonstruktionen.&gt; **

    ```cs
    using System.Collections.Generic;
    ```

2. Ersätt åtgärdsmetoden **Index(SearchData-modell).**

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

3. Ersätt åtgärdsmetoden **Page(SearchData-modell).**

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

4. Lägg till en **facet(SearchData-modell)** åtgärdsmetod som ska aktiveras när användaren klickar på en aspektlänk. Modellen innehåller antingen ett kategorisökfilter eller ett sökfilter för bekvämligheter. Kanske lägga till **Page** den efter sidåtgärden.

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

### <a name="set-up-the-search-filter"></a>Konfigurera sökfiltret

När en användare väljer en viss aspekt, till exempel, klickar de på **resort- och** spakategorin, då bör endast hotell som anges som denna kategori returneras i resultaten. För att begränsa en sökning på detta sätt måste vi ställa in ett _filter_.

1. Ersätt metoden **RunQueryAsync** med följande kod. I första hand krävs en kategorifiltersträng och en filtersträng för **SearchParameters**bekvämligheter och **filterparameterns filterparameters** filter.

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

    Vi har lagt till egenskaperna **Kategori** och **Taggar** i listan **Över Välj** objekt som ska returneras. Detta tillägg är inte ett krav för aspektna navigering att fungera, men vi använder denna information för att kontrollera att vi filtrerar korrekt.

### <a name="add-lists-of-facet-links-to-the-view"></a>Lägga till listor med aspektlänkar i vyn

Utsikten kommer att kräva några betydande förändringar. 

1. Börja med att öppna filen hotels.css (i mappen wwwroot/css) och lägg till följande klasser.

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

2. För vyn organiserar vi utdata i en tabell, för att snyggt justera fasettlistorna till vänster och resultaten till höger. Öppna filen index.cshtml. Ersätt hela innehållet i &lt;&gt; HTML-brödtexttaggarna med följande kod.

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

    Lägg märke till användningen av **Html.ActionLink-samtalet.** Det här anropet kommunicerar giltiga filtersträngar till styrenheten när användaren klickar på en aspektlänk. 

### <a name="run-and-test-the-app"></a>Kör och testa appen

Fördelen med aspekt navigering till användaren är att de kan begränsa sökningar med ett enda klick, som vi kan visa i följande sekvens.

1. Kör appen, skriv "flygplats" som söktext. Kontrollera att listan med fasor visas snyggt till vänster. Dessa aspekter är allt som gäller för hotell som har "flygplats" i sina textdata, med en räkning av hur ofta de förekommer.

    ![Använda aspekt navigering för att begränsa en sökning av "flygplats"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Klicka på kategorin **Resort och Spa.** Kontrollera att alla resultat finns i den här kategorin.

    ![Begränsa sökandet till "Resort and Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Klicka på den **kontinentala frukosten.** Kontrollera att alla resultat fortfarande är i kategorin "Resort and Spa", med de valda bekvämligheterna.

    ![Begränsa sökandet till "kontinental frukost"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Prova att välja någon annan kategori, sedan en skönhet, och visa förträngning resultat. Försök sedan tvärtom, en skönhet, sedan en kategori.

    >[!Note]
    > När ett val görs i en aspektlista (t.ex. kategori) åsidosätter det alla tidigare val i kategorilistan.

## <a name="takeaways"></a>Lärdomar

Tänk på följande takeaways från det här projektet:

* Det är absolut nödvändigt att markera varje fastighet som **IsFacetable**, om de ska ingå i aspektnavigering.
* Fastnavigering ger en användare ett enkelt och intuitivt sätt att begränsa en sökning.
* Fastnavigervigering är bäst indelad i sektioner (kategorier av hotell, bekvämligheter på ett hotell, prisklasser, betygsintervall, etc.), varje avsnitt med en lämplig rubrik.

## <a name="next-steps"></a>Nästa steg

I nästa handledning tittar vi på att beställa resultat. Fram till denna punkt, resultat sorteras helt enkelt i den ordning som de finns i databasen.

> [!div class="nextstepaction"]
> [C# självstudiekurs: Beställ resultaten- Azure Cognitive Search](tutorial-csharp-orders.md)
