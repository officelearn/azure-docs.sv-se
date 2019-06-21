---
title: C#självstudie om att använda fasetterna för navigering och nätverket effektivitet – Azure Search
description: Den här självstudien bygger på ”sökresultat sidbrytning – Azure Search”-projektet, att lägga till aspekten sökningar. Lär dig att fasetter kan användas i både navigering och kommandofönstret Slutför automatiskt.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/20/2019
ms.openlocfilehash: a81042869564533050fef42a983f2f8fb9bc7b23
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304664"
---
# <a name="c-tutorial-use-facets-for-navigation-and-network-efficiency---azure-search"></a>C#självstudie: Använd fasetterna för navigering och nätverket effektivitet – Azure Search

Fasetter har två distinkta använder Azure Search. Fasetter kan användas för att underlätta navigeringen, genom att ge användaren en uppsättning kryssrutorna om du vill använda för att fokusera sökningen. De kan också användas för ökad nätverkseffektivitet, när de används i kommandofönstret Slutför automatiskt. Aspekten sökningar är effektivt eftersom de utförs endast en gång för varje sidhämtning snarare än en gång för varje tangenttryckning. 

Fasetter är attribut i data (till exempel kategorin på ett hotell i våra exempeldata) och hålla dig relevant för livslängden för en sökning.

Den här kursen skapar två projekt, en för aspekten navigering och det andra för aspekten kommandofönstret Slutför automatiskt. Båda projekten bygger på sidindelning projektet har skapats i den [ C# självstudien: Sökresultat sidbrytning – Azure Search](tutorial-csharp-paging.md) självstudien.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Ange modellegenskaper som _IsFacetable_
> * Lägg till aspekten navigering till din app
> * Lägg till aspekten kommandofönstret Slutför automatiskt till din app
> * Avgöra när du ska använda aspekten kommandofönstret Slutför automatiskt

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

Har den [ C# självstudien: Sökresultat sidbrytning – Azure Search](tutorial-csharp-paging.md) projekt och drift. Detta kan vara din egen version eller installera det från GitHub: [Skapa första app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-fields-as-isfacetable"></a>Ange modell-fält som IsFacetable

För en modellegenskap som ska finnas i en sökning för aspekten (navigering eller kommandofönstret Slutför automatiskt), måste den vara taggad med **IsFacetable**.

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

2. Vi kommer inte att ändra alla taggar i den här självstudien. En sökning för aspekten genereras ett fel om ett fält som efterfrågas i sökningen inte är taggade på rätt sätt.


## <a name="add-facet-navigation-to-your-app"></a>Lägg till aspekten navigering till din app

I det här exemplet ska vi att aktivera användaren att välja en eller flera kategorier av hotell, i en lista som visas till vänster om resultaten. Vi behöver styrenheten veta listan över kategorier när appen körs först och skicka den här listan till vyn som ska visas när den första skärmen återges. Vi måste se till att vi har underhålls både listan över fasetter och de aktuella användarens val till att överföras till efterföljande sidor som varje sida återges. Igen, använder vi tillfälligt lagringsutrymme som mekanism för att bevara data.

![Använda aspekten navigering för att begränsa sökningen av ”pool”](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="modify-the-searchdata-model"></a>Ändra SearchData modellen

1. Öppna filen SearchData.cs och lägga till det här ytterligare **med** instruktionen. Vi behöver att aktivera den **lista&lt;sträng&gt;**  konstruera.

    ```cs
    using System.Collections.Generic;
    ```

2. Lägg till följande rader till i samma fil i **SearchData** klass. Ta inte bort några befintliga klassegenskaper, men Lägg till följande Konstruktormetoder och matriser av egenskaperna.

    ```cs
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each facet.
        public string[] facetText { get; set; }

        // Array to hold the check box setting.
        public bool[] facetOn { get; set; }
    ```


### <a name="search-for-facets-on-the-first-index-call"></a>Sök efter fasetter på det första anropet Index

Kontrollanten home måste ha en betydande förändring. Det första anropet till **Index()** inte längre returnerar en vy med ingen vidare bearbetning. Vi vill ge vyn med en fullständig lista över fasetter och det första anropet är rätt typ för detta ändamål.

1. Öppna filen home controller och Lägg till två **med** instruktioner.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

2. Ersätt raderna i aktuellt **Index()** metoden med en metod som utför en aspekten söker efter hotell kategorier. Sökningen ska utföras asynkront, vi måste deklarera den **Index** metod som **async**.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 categories.
                // Field names specified here must be marked as "IsFacetable" in the model, or the search call will throw an exception.
                Facets = new List<string> { "Category,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> categories = searchResult.Facets["Category"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(categories);

            // Save the facet text for the next view.
            SaveFacets(model);

            // Render the view including the facets.
            return View(model);
        }
    ```

    Några saker att Observera här. Det konvertera resultatet av sökanrop till en lista med strängar och sedan strängarna aspekten läggs till i en **SearchData** modeller för kommunikation till vyn. Även spara det dessa strängar till ett tillfälligt lagringsutrymme innan slutligen återges vyn. Den här spara görs så att den här listan är tillgänglig för nästa anrop till en domänkontrollant-åtgärd.

3. Vi lägger till de två privata metoderna för att spara och återställa fasetter till modellen och till ett tillfälligt lagringsutrymme.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

### <a name="save-and-restore-facet-text-on-all-calls"></a>Spara och återställa aspekten text på alla anrop

1. De två åtgärderna av kontrollanten home **Index (SearchData modellen)** och **sidan (SearchData modellen)** , både måste du återställa fasetterna innan sökanropet och spara dem igen när sökanropet. Ändra den **Index (SearchData modellen)** att göra dessa två anrop.

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

                // Recover the facet text.
                RecoverFacets(model);

                // Make the search call for the first page.
                await RunQueryAsync(model, 0, 0);

                // Ensure temporary data is stored for the next call.
                TempData["page"] = 0;
                TempData["leftMostPage"] = 0;
                TempData["searchfor"] = model.searchText;

                // Facets
                SaveFacets(model, true);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

2. Nu göra detsamma den **sidan (SearchData modellen)** metod. Vi har bara visas den relevanta koden nedan. Lägg till den **RecoverFacets** och **SaveFacets** anropar runt den **RunQueryAsync** anropa.

    ```cs
                // Recover facet text and check marks.
                RecoverFacets(model, true);

                await RunQueryAsync(model, page, leftMostPage);

                // Save facets and check marks.
                SaveFacets(model, true);
    ```

### <a name="set-up-a-search-filter"></a>Konfigurera ett sökfilter

När en användare väljer vissa aspekter, till exempel säga att de klickar på den **Budget** och **utväg och Spa** kategorier och sedan hotell som har angetts som en av dessa två kategorier ska returneras i de resultat. För att optimera en sökning på så sätt kan vi behöva ställa in en _filter_.

1. I den **RunQueryAsync** metoden lägger du till kod för att bläddra i den angivna modellen aspekten inställningar, för att skapa en Filtersträng. Och Lägg till filter till den **SearchParameters**, enligt följande kod.

    ```cs
            // Create a filter for selected facets.
            string selectedFacets = "";

            for (int f = 0; f < model.facetText.Length; f++)
            {
                if (model.facetOn[f])
                {
                    if (selectedFacets.Length > 0)
                    {
                        // If there is more than one selected facet, logically OR them together.
                        selectedFacets += " or ";
                    }
                    selectedFacets += "(Category eq \'" + model.facetText[f] + "\')";
                }
            }

            var parameters = new SearchParameters
            {
                // Facets: add the filter.
                Filter = selectedFacets,

                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description", "Category" },
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Top = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalResultCount = true,
            };
    ```

    Vi har lagt till den **kategori** egenskap i listan över **Välj** objekt som ska returneras. Att lägga till den här egenskapen är inte ett krav, men på så sätt kan vi Kontrollera att vi filtrerar korrekt.

### <a name="define-a-few-additional-html-styles"></a>Definiera några ytterligare HTML-format

Vyn kommer att kräva några viktiga ändringar. 

1. Börja med att öppna filen hotels.css (i mappen wwwroot/css) och Lägg till följande klasser.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 200px;
        background-color: lightgoldenrodyellow;
        display: normal;
        color: #666;
        margin: 10px;
    }
    ```

### <a name="add-a-list-of-facet-checkboxes-to-the-view"></a>Lägg till en lista över aspekten kryssrutorna i vyn

För vyn organisera vi utdata i en tabell, snyggt justera fasetterna till vänster och resultaten till höger. Öppna filen index.cshtml.

1. Ersätt hela innehållet i HTML &lt;brödtext&gt; taggar, med följande kod.

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
                        <h5>Filter by Category:</h5>
                        <ul class="facetlist">
                            @for (var i = 0; i < Model.facetText.Length; i++)
                            {
                                <li> @Html.CheckBoxFor(m => m.facetOn[i], new { @id = "check" + i.ToString() }) @Model.facetText[i] </li>
                            }
                        </ul>
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
                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                                @Html.TextArea("desc", Model.resultList.Results[i].Document.Description + "\nCategory:  " +  Model.resultList.Results[i].Document.Category, new { @class = "box2" })
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

    Observera användningen av den **CheckBoxFor** anrop för att fylla i den **facetOn** matris med användarens val. Dessutom har vi lagt till kategorin för hotell i slutet av hotell beskrivning. Den här texten är helt enkelt att bekräfta att vår search fungerar korrekt. Inte mycket annat har ändrats från tidigare självstudier, förutom att vi har organiserat utdata till en tabell.

### <a name="run-and-test-the-app"></a>Kör och testa appen

1. Kör appen och kontrollera att listan över fasetter snyggt visas till vänster.

2. Prova att välja en, två, tre eller flera kryssrutor och kontrollera resultatet.

    ![Använda aspekten navigering för att begränsa sökningen av ”wifi”](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

3. Det finns en liten complication med aspekten navigering. Vad som ska hända om en användare ändrade vilka fasettera (markerar eller ta bort markerar du kryssrutorna) och klickar sedan på något av alternativen växling och inte sökfältet? I praktiken ska ändra valet initiera en ny sökning som de aktuella sidorna kommer inte längre att rätt. Du kan också användaren ändrar att ignoreras, och nästa sida i resultatet får, baserat på det ursprungliga aspekt du valt. Vi har valt den sistnämnda lösningen i det här exemplet, men kanske överväga hur du kan implementera den tidigare lösningen. Utlös en ny sökning kanske om det senaste urvalet av valda fasetter inte matchar exakt val i tillfällig lagring?

Det är klart vårt exempel med aspekten navigering. Men kanske du kan också bestämma hur den här appen kan utökas. Listan över aspekten kan utökas med andra aspekten kan fält (säger **taggar**), så att en användare kan välja en rad alternativ, till exempel en pool, Wi-Fi, fält, kostnadsfri parkering och så vidare. 

Fördelen med aspekten navigering för användaren är att de inte behöver hålla samma textinmatning, sina aspekten val bevaras för livslängden för den aktuella sessionen med appen. De kan välja kategorier, och eventuellt andra attribut med ett enda klick kan sedan söka på andra viss text.

Nu ska vi undersöka en annan användning av fasetter.

## <a name="add-facet-autocompletion-to-your-app"></a>Lägg till aspekten kommandofönstret Slutför automatiskt till din app

Aspekten kommandofönstret Slutför automatiskt fungerar genom att göra en första sökning när appen körs. Den här sökningen samlar in en lista över fasetter ska användas som förslag när användaren skriver.

![Skriv ”SV” visar tre aspekter](./media/tutorial-csharp-create-first-app/azure-search-facet-type-re.png)

Vi använder numrerade sidindelning appen som du kanske har slutfört i den andra självstudien som bas för det här exemplet.

Om du vill implementera aspekten kommandofönstret Slutför automatiskt, behöver vi inte ändra några av modeller (dataklasser). Vi behöver lägga till vissa skript i vyn och en åtgärd för att kontrollanten.

### <a name="add-an-autocomplete-script-to-the-view"></a>Lägg till ett skript för automatisk komplettering till vyn

För att initiera en aspekten sökning, måste vi skickar en fråga. Följande JavaScript som lagts till i filen index.cshtml innehåller frågans logik och presentation som vi behöver.

1. Leta upp den **@Html.TextBoxFor(m = > m.searchText,...)** instruktionen och Lägg till ett unikt ID, som liknar följande.

    ```cs
    <div class="searchBoxForm">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresearchfacets" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

2. Lägg till följande JavaScript (efter avslutande **&lt;/div&gt;** ovan fungerar bra).

    ```JavaScript
     <script>
            $(function () {
                $.getJSON("/Home/Facets", function (data) {

                    $("#azuresearchfacets").autocomplete({
                        source: data,
                        minLength: 2,
                        position: {
                            my: "left top",
                            at: "left-23 bottom+10"
                        }
                    });
                });
            });
        </script>
    ```

    Lägg märke till att skriptet anropar den **Fasetter** åtgärden i kontrollanten home utan några andra parametrar när en minsta längd på två tecken har uppnåtts.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Lägg till referenser till jquery skript till vyn

Funktionen Komplettera automatiskt i skriptet ovan är inte något vi behöver skriva själva eftersom den är tillgänglig i jquery-biblioteket. 

1. Om du vill få tillgång till jquery-bibliotek, ersätter den &lt;head&gt; i Visa filen med följande kod.

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Facets demo</title>
        <link href="https://code.jquery.com/ui/1.10.4/themes/ui-lightness/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.10.4/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Vi också behöva ta bort eller kommentera ut, en rad som refererar till jquery i filen _Layout.cshtml (i den **vyer/delade** mapp). Leta upp följande rader och kommentera ut den första raden i skriptet som visas. Genom att ta bort den här raden, undvika vi tvetydiga referenser till jquery.

    ```html
     <environment include="Development">
            <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
            <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
            <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

Nu kan vi använda de fördefinierade automatisk komplettering jquery-funktionerna.

### <a name="add-a-facet-action-to-the-controller"></a>Lägg till en åtgärd för aspekten till styrenhet

1. Öppna kontrollanten hem och Lägg till följande två **med** instruktioner till chefen för filen.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

2. JavaScript i vyn utlösare den **Fasetter** åtgärd i styrenhet, då ska vi lägga till åtgärden till kontrollanten home (exempelvis nedan den **sidan** åtgärd).

    ```cs
        public async Task<ActionResult> Facets()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search all Tags, but limit the total number to 100, and add up to 20 categories.
                // Field names specified here must be marked as "IsFacetable" in the model, or the search call will throw an exception.
                Facets = new List<string> { "Tags,count:100", "Category,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to two lists that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();
            List<string> categories = searchResult.Facets["Category"].Select(x => x.Value.ToString()).ToList();

            // Combine and return the lists.
            facets.AddRange(categories);
            return new JsonResult(facets);
        }
    ```

    Observera att vi begär upp till 100 fasetter från den **taggar** fält, och upp till 20 från den **kategori** fält. Den **antal** är valfria, om inget antal anger standarden är 10.

    Vi behöver två listor, som sedan kombineras till en enda, eftersom vi efterfrågat två fält som ska sökas igenom (**taggar** och **kategori**). Om vi hade tillfrågad om tre fält som ska sökas igenom, skulle vi behöva kombinera tre listor och så vidare.

    > [!NOTE]
    > Det är möjligt att ange en eller flera av följande parametrar för varje fält i en aspekten sökning: **antal**, **sortera**, **intervall**, och **värden**. Mer information finns i [implementera aspektbaserad navigering i Azure Search](https://docs.microsoft.com/azure/search/search-faceted-navigation).

### <a name="compile-and-run-your-project"></a>Kompilera och köra projektet

Nu ska du testa programmet.

1. Försök att skriva ”fr” i sökrutan som ska visa flera resultat.

    ![Att skriva ”fr” visar tre aspekter](./media/tutorial-csharp-create-first-app/azure-search-facet-type-fr.png)

2. Nu ska du lägga till en ”o” för att göra ”från” och Lägg märke till olika alternativ minskar till en.

3. Ange andra kombinationer av två bokstäver och se vad som visas. Observera att när du skriver är servern *inte* som anropas. Fasetterna cachelagras lokalt när appen startas, och nu ett endast anrop till servern när användaren begär en sökning.

## <a name="decide-when-to-use-a-facet-autocompletion-search"></a>Avgöra när du ska söka på aspekten kommandofönstret Slutför automatiskt

Rensa skillnaden mellan aspekten sökningar och andra sökningar, till exempel förslag och kommandofönstret Slutför automatiskt, är att den aspekt sökningen är _utformats_ bara utföras en gång när en sida har lästs in. Andra sökningar i kommandofönstret Slutför automatiskt är _utformats_ anropas när varje tecknet. Med hjälp av fasetterna sparar vis potentiellt många anrop till servern. 

Men när ska aspekten kommandofönstret Slutför automatiskt användas?

Aspekten kommandofönstret Slutför automatiskt är bäst används när:
* Den främsta orsaken är att prestanda för andra sökningar som anropsserver varje tangenttryckning är ett problem.
* Fasetterna returnerade ger användaren en lista med alternativ för rimlig när HEN skriver i några få tecken.
* Fasetterna returnerade ger ett snabbt sätt att komma åt mest eller helst alla data som är tillgängliga.
* Maximalt antal kan de flesta aspekter som ska tas med. I vår kod vi ställa in högst 100 fasetterna för **taggar** och 20 aspekter för **kategori**. Uppsättningen maxvärden måste fungera bra med storleken på datauppsättningen. Om för många potentiella fasetter klipps sedan är kanske sökningen inte så bra som det ska.

> [!NOTE]
> Även om aspekten sökningar är utformade för att anropas när per sidhämtning, de kan förstås anropas mycket oftare, beror det på din JavaScript. Lika true är att kommandofönstret Slutför automatiskt/förslag sökningar kan utföras mer sällan än en gång per tangenttryckning. Igen bestäms av din JavaScript, inte Azure Search. Aspekten search har dock utformats för att bara anropas en gång per sida som fasetter skapas av Azure Search från sökt dokument med detta i åtanke. Det är bra att tänka på aspekten kommandofönstret Slutför automatiskt sökningar som en något mindre flexibel men nätverk-effektivare form av hjälp.

## <a name="takeaways"></a>Lärdomar

Överväg följande takeaways från det här projektet:

* Det är absolut nödvändigt att markera varje fält som **IsFacetable**, om de ska tas med i aspekten navigering eller kommandofönstret Slutför automatiskt.
* Aspekten navigering ger en användare med ett enkelt och intuitivt sätt att begränsa en sökning.
* Aspekten navigering är bäst indelade i avsnitt (kategorier av hotell), funktionerna i ett hotell, priset intervall, osv, varje avsnitt med lämplig rubrik.
* Aspekten kommandofönstret Slutför automatiskt är ett effektivt sätt för att få en bra användarupplevelse utan upprepade serveranrop över andra kommandofönstret Slutför automatiskt sökningar.
* Aspekten kommandofönstret Slutför automatiskt är en _alternativa_ till förslag, inte ett tillägg.

## <a name="next-steps"></a>Nästa steg

Du har slutfört den här serien med C# kurser, du bör ha fått värdefull kunskap om Azure Search-API: er.

Ytterligare referens och självstudier kan du bläddra [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure), eller andra självstudier i den [dokumentation om Azure Search](https://docs.microsoft.com/azure/search/).
