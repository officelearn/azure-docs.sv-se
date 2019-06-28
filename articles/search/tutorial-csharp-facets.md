---
title: C#självstudie om att använda fasetterna för att underlätta navigeringen – Azure Search
description: Den här självstudien bygger på ”sökresultat sidbrytning – Azure Search”-projektet, lägga till aspekten navigering. Lär dig att fasetter kan användas för att enkelt begränsa sökningen.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/20/2019
ms.openlocfilehash: 62326ad3bc5f2d740ce744819df559bce8658eb7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443783"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-search"></a>C#självstudie: Använda fasetterna för att underlätta navigering – Azure Search

Fasetter används för att underlätta navigeringen, genom att ge användaren en uppsättning länkar du använder för att fokusera sökningen. Fasetter är attribut i data (till exempel kategorin eller en specifik funktion i ett hotell i våra exempeldata).

Den här självstudien bygger på sidindelning projektet har skapats i den [ C# självstudien: Sökresultat sidbrytning – Azure Search](tutorial-csharp-paging.md) självstudien.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Ange modellegenskaper som _IsFacetable_
> * Lägg till aspekten navigering till din app

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

Har den [ C# självstudien: Sökresultat sidbrytning – Azure Search](tutorial-csharp-paging.md) projekt och drift. Det här projektet kan vara din egen version eller installera det från GitHub: [Skapa första app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Ange modell-egenskaper som IsFacetable

För en modellegenskap som ska finnas i en aspekten sökning, måste den vara taggad med **IsFacetable**.

1. Granska den **hotell** klass. **Kategori** och **taggar**, till exempel är märkta som **IsFacetable**, men **HotelName** och **beskrivning** finns inte. 

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

2. Vi kommer inte att ändra taggarna som en del av den här kursen, så Stäng filen hotel.cs utan ändringar.

    > [!Note]
    > En sökning för aspekten genereras ett fel om ett fält som efterfrågas i sökningen inte är taggade på rätt sätt.


## <a name="add-facet-navigation-to-your-app"></a>Lägg till aspekten navigering till din app

I det här exemplet vi gör att användaren kan välja en kategori av hotell eller en amenity från en lista över länkar som visas till vänster om resultaten. Användaren startar genom att ange några söktext, och sedan kan du begränsa resultaten av sökningen genom att välja en kategori och kan begränsa resultaten ytterligare genom att välja en amenity eller de kan välja amenity första (ordning inte är viktigt).

Vi behöver den kontrollenheten för att skicka listan över fasetter till vyn. Vi behöver underhålla användarval medan sökning pågår och igen, använder vi tillfälligt lagringsutrymme som mekanism för att bevara data.

![Använda aspekten navigering för att begränsa sökningen av ”pool”](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Lägga till filtret strängar i SearchData-modellen

1. Öppna filen SearchData.cs och Lägg till egenskaper för anslutningssträngen till den **SearchData** klass för aspekten filtersträngar.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Lägg till aspekten åtgärdsmetod

Kontrollanten home måste en ny åtgärd **aspekten**, och uppdateringar av det befintliga **Index** och **sidan** åtgärder, samt uppdateringar till den **RunQueryAsync**  metod.

1. Öppna startsida för styrenheten och Lägg till den **med** -uttryck för att aktivera den **lista&lt;sträng&gt;**  konstruera.

    ```cs
    using System.Collections.Generic;
    ```

2. Ersätt den **Index (SearchData modellen)** åtgärdsmetod.

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

3. Ersätt den **sidan (SearchData modellen)** åtgärdsmetod.

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

4. Lägg till en **aspekten (SearchData modellen)** åtgärdsmetod aktiveras när användaren klickar på en länk för aspekten. Modellen innehåller ett sökfilter för kategorin eller ett amenity sökfilter. Kanske lägga till den när den **sidan** åtgärd.

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

När en användare väljer en viss aspekt (Facet), till exempel de klickar på den **utväg och Spa** kategori och sedan endast hotell som har angetts som den här kategorin ska returneras i resultatet. Om du vill begränsa en sökning på så sätt kan vi behöva ställa in en _filter_.

1. Ersätt den **RunQueryAsync** metoden med följande kod. Främst den tar en Filtersträng i kategorin och en amenity Filtersträngen och anger den **Filter** -parametern för den **SearchParameters**.

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

    Vi har lagt till den **kategori** och **taggar** egenskaper i listan över **Välj** objekt som ska returneras. Det här tillägget är inte ett krav för aspekten navigeringen ska fungera, men vi använder den här informationen för att kontrollera att vi filtrerar korrekt.

### <a name="add-lists-of-facet-links-to-the-view"></a>Lägg till en lista över aspekten länkar till vyn

Vyn kommer att kräva några viktiga ändringar. 

1. Börja med att öppna filen hotels.css (i mappen wwwroot/css) och Lägg till följande klasser.

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

2. Vi ordna utdata i en tabell för vyn, om du vill justera snyggt fasetten visas till vänster och resultaten till höger. Öppna filen index.cshtml. Ersätt hela innehållet i HTML &lt;brödtext&gt; taggar, med följande kod.

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

    Observera användningen av den **Html.ActionLink** anropa. Det här anropet kommunicerar giltigt filtersträngar till kontrollanten, när användaren klickar på en länk för aspekten. 

### <a name="run-and-test-the-app"></a>Kör och testa appen

Fördelen med aspekten navigering för användaren är att de kan du begränsa sökningar med ett enda klick, visar vi i följande ordning.

1. Kör appen, typ ”flygplats” som söktexten. Kontrollera att listan över fasetter snyggt visas till vänster. Dessa fasetter är allt som gäller för hotell som har ”flygplats” i sina textdata med ett antal av hur ofta de inträffar.

    ![Använda aspekten navigering för att begränsa sökningen av ”flygplats”](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Klicka på den **utväg och Spa** kategori. Kontrollera alla resultat i den här kategorin.

    ![Begränsa sökningen till ”utväg och Spa”](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Klicka på den **all bra** amenity. Kontrollera alla resultat är fortfarande i kategorin ”utväg och Spa” med den valda amenity.

    ![Begränsa sökningen till ”all bra”](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Försök att välja en annan kategori, sedan en amenity och visa resultat. Försök tvärtom, en amenity och sedan en kategori.

    >[!Note]
    > När en markering har gjorts i en lista för aspekten (till exempel kategori) åsidosätter eventuella tidigare val i kategorilistan.

## <a name="takeaways"></a>Lärdomar

Överväg följande takeaways från det här projektet:

* Det är absolut nödvändigt att markera varje egenskap som **IsFacetable**, om de ska tas med i aspekten navigering.
* Aspekten navigering ger en användare med ett enkelt och intuitivt sätt att begränsa en sökning.
* Aspekten navigering är bäst indelade i avsnitt (kategorier av hotell), bekvämligheterna på ett hotell, priset intervall, klassificering intervall, osv, varje avsnitt med lämplig rubrik.

## <a name="next-steps"></a>Nästa steg

I nästa självstudie tittar vi på Ordna resultaten. Resultaten ordnas i det här läget bara i den ordning som de finns i databasen.

> [!div class="nextstepaction"]
> [C#självstudie: Sortera resultat – Azure Search](tutorial-csharp-orders.md)
