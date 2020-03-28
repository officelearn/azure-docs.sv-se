---
title: C# handledning om sökresultat sidnumrering
titleSuffix: Azure Cognitive Search
description: Den här självstudien visar växling av sökresultat. Den bygger på ett befintligt hotellprojekt, med växling med första, nästa, föregående, sista och numrerade knappar. Ett andra växlingssystem använder oändlig rullning, utlöses genom att flytta en vertikal rullningslist till sin nedre gräns.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 9abfeb54be6e22885b8e973034a6d89df8272146
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121519"
---
# <a name="c-tutorial-search-results-pagination---azure-cognitive-search"></a>C# självstudiekurs: Sidnumrering av sökresultat - Azure Cognitive Search

Lär dig hur du implementerar två olika växlingssystem, den första baserat på sidnummer och den andra på oändlig rullning. Båda systemen för personsökning används i stor utsträckning, och att välja rätt beror på vilken användarupplevelse du vill med resultaten. Den här självstudien skapar växlingssystemen till projektet som skapas i [C#-självstudien: Skapa din första app - Azure Cognitive](tutorial-csharp-create-first-app.md) Search-självstudien.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Utöka appen med numrerad växling
> * Utöka appen med oändlig rullning

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

Ha [C# Tutorial: Skapa din första app - Azure Cognitive Search-projektet](tutorial-csharp-create-first-app.md) igång. Det här projektet kan antingen vara din egen version eller installera den från GitHub: [Skapa den första appen](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Utöka appen med numrerad växling

Numrerade personsökning är personsökningssystem val av de viktigaste sökmotorerna på Internet och de flesta andra sökwebbplatser. Numrerad växling innehåller vanligtvis ett "nästa" och "föregående" alternativ utöver ett intervall med faktiska sidnummer. Även en "första sida" och "sista sida" alternativ kan också vara tillgängliga. Dessa alternativ ger verkligen en användare kontroll över att navigera genom sidbaserade resultat.

Vi kommer att lägga till ett system som innehåller första, föregående, nästa och sista alternativen, tillsammans med sidnummer som inte startar från 1, utan i stället omger den aktuella sidan användaren är på (så, till exempel, om användaren tittar på sidan 10, kanske sidnummer 8 visas 9, 10, 11 och 12).

Systemet kommer att vara tillräckligt flexibelt för att antalet synliga sidnummer ska kunna anges i en global variabel.

Systemet kommer att behandla de vänstra och höger-mest sidnummer knappar som speciella, vilket innebär att de kommer att utlösa ändra intervallet sidnummer visas. Om sidnummer 8, 9, 10, 11 och 12 visas och användaren klickar på 8 ändras sidnumren till 6, 7, 8, 9 och 10. Och det finns en liknande förskjutning till höger om de valt 12.

### <a name="add-paging-fields-to-the-model"></a>Lägga till växlingsfält i modellen

Öppna den grundläggande söksidans lösning.

1. Öppna SearchData.cs modellfilen.

2. Lägg först till några globala variabler. I MVC deklareras globala variabler i sin egen statiska klass. **I ResultsPerPage** anges antalet resultat per sida. **MaxPageRange** bestämmer antalet synliga sidnummer i vyn. **PageRangeDelta** bestämmer hur många sidor som är till vänster eller höger om sidintervallet ska flyttas när det vänstra eller högra sidnumret är markerat. Vanligtvis är det senare talet ungefär hälften av **MaxPageRange**. Lägg till följande kod i namnområdet.

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >Om du kör det här projektet på en enhet med en mindre skärm, till exempel en bärbar dator, bör du överväga att ändra **ResultsPerPage** till 2.

3. Lägg till växlingsegenskaper i klassen **SearchData,** till exempel efter egenskapen **searchText.**

    ```cs
        // The current page being displayed.
        public int currentPage { get; set; }

        // The total number of pages of results.
        public int pageCount { get; set; }

        // The left-most page number to display.
        public int leftMostPage { get; set; }

        // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
        public int pageRange { get; set; }

        // Used when page numbers, or next or prev buttons, have been selected.
        public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>Lägga till en tabell med växlingsalternativ i vyn

1. Öppna filen index.cshtml och lägg till följande &lt;kod&gt; precis före taggen stängning /brödtext. Den här nya koden visar en tabell med växlingsalternativ: först, föregående, 1, 2, 3, 4, 5, nästa, sista.

    ```cs
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
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

            <td>
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
                <td>
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

            <td>
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

            <td>
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
    ```

    Vi använder en HTML-tabell för att justera saker snyggt. Men alla åtgärder kommer @Html.ActionLink från satser, varje anropar styrenheten med en **ny** modell som skapats med olika poster till **växlingsegenskapen** vi lagt till tidigare.

    Första och sista sidan alternativ inte skicka strängar som "första" och "sista", utan i stället skicka rätt sidnummer.

2. Lägg till några växlingsklasser i listan över HTML-format i filen hotels.css. Klassen **PageSelected** är till för att identifiera den sida som användaren för närvarande visar (genom att vrida talet fetstilt) i listan med sidnummer.

    ```html
        .pageButton {
            border: none;
            color: darkblue;
            font-weight: normal;
            width: 50px;
        }

        .pageSelected {
            border: none;
            color: black;
            font-weight: bold;
            width: 50px;
        }

        .pageButtonDisabled {
            border: none;
            color: lightgray;
            font-weight: bold;
            width: 50px;
        }
    ```

### <a name="add-a-page-action-to-the-controller"></a>Lägga till en sidåtgärd i handkontrollen

1. Öppna HomeController.cs-filen och lägg till **Page** sidåtgärden. Den här åtgärden svarar på något av de valda sidalternativen.

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

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

                // Recover the search text and search for the data for the new page.
                model.searchText = TempData["searchfor"].ToString();

                await RunQueryAsync(model, page, leftMostPage);

                // Ensure Temp data is stored for next call, as TempData only stored for one call.
                TempData["page"] = (object)page;
                TempData["searchfor"] = model.searchText;
                TempData["leftMostPage"] = model.leftMostPage;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

    **Metoden RunQueryAsync** visar nu ett syntaxfel på grund av den tredje parametern, som vi kommer att komma till om en stund.

    > [!Note]
    > **TempData-anropen** lagrar ett värde (ett **objekt)** i tillfällig lagring, även om den här lagringen kvarstår för _endast_ ett anrop. Om vi lagrar något i tillfälliga data, kommer det att vara tillgänglig för nästa samtal till en controller åtgärd, men kommer definitivt att vara borta av samtalet efter det! På grund av denna korta livslängd lagrar vi söktexten och personsökningsegenskaperna tillbaka i tillfällig lagring varje samtal till **sidan.**

2. Åtgärden **Index(modell)** måste uppdateras för att lagra de temporära variablerna och för att lägga till den vänstra sidparametern i **RunQueryAsync-anropet.**

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
                await RunQueryAsync(model, 0, 0);

                // Ensure temporary data is stored for the next call.
                TempData["page"] = 0;
                TempData["leftMostPage"] = 0;
                TempData["searchfor"] = model.searchText;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. **Metoden RunQueryAsync** behöver uppdateras avsevärt. Vi använder fälten **Hoppa över,** **överkant**och **IncludeTotalResultCount** i klassen **SearchParameters** för att begära endast en sida värde av resultat, med början vid **skip-inställningen.** Vi måste också beräkna personsökningsvariablerna för vår uppfattning. Ersätt hela metoden med följande kod.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                   // Enter Hotel property names into this list so only these values will be returned.
                   // If Select is empty, all values will be returned, which can be inefficient.
                   Select = new[] { "HotelName", "Description" },
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
            model.pageCount = ( (int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

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

            return View("Index", model);
        }
    ```

4. Slutligen måste vi göra en liten förändring av synen. **VariabelresultatetList.Results.Count** kommer nu att innehålla antalet resultat som returneras på en sida (3 i vårt exempel), inte det totala antalet. Eftersom vi ställer in **IncludeTotalResultCount** till true innehåller **variabelresultatetList.Count** nu det totala antalet resultat. Så leta upp var antalet resultat visas i vyn och ändra det till följande kod.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Det finns en prestandaträff, men vanligtvis inte mycket av en, genom att ställa **in IncludeTotalResultCount** till true, eftersom den här summan måste beräknas av Azure Cognitive Search. Med komplexa datauppsättningar finns det en varning om att det returnerade värdet är en _approximation_. För våra hotelldata kommer det att vara korrekt.

### <a name="compile-and-run-the-app"></a>Kompilera och köra appen

Välj nu **Starta utan felsökning** (eller tryck på F5).

1. Sök på lite text som ger gott om resultat (till exempel "wifi"). Kan du bläddra snyggt igenom resultaten?

    ![Numrerad personsökning via "pool"-resultat](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Prova att klicka på höger och senare sidnummer till vänster. Justeras sidnumren på rätt sätt för att centrera sidan du befinner dig på?

3. Är alternativen "första" och "sista" användbara? Vissa populära webbsökningar använder dessa alternativ, och andra inte.

4. Gå till den sista resultatsidan. Den sista sidan är den enda sida som kan innehålla mindre än **ResultsPerPage-resultat.**

    ![Undersöka sista sidan av "wifi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Skriv in "stad", och klicka på sök. Inga växlingsalternativ visas om resultatet är mindre än en sida.

    ![Söker efter "stad"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Nu spara bort detta projekt och låt oss prova ett alternativ till denna form av personsökning.

## <a name="extend-your-app-with-infinite-scrolling"></a>Utöka appen med oändlig rullning

Oändlig rullning utlöses när en användare rullar en lodrät rullningslist till det sista av resultaten som visas. I det här fallet görs ett anrop till servern för nästa resultatsida. Om det inte finns några fler resultat returneras ingenting och den lodräta rullningslisten ändras inte. Om det finns fler resultat läggs de till på den aktuella sidan och rullningslisten ändras för att visa att fler resultat är tillgängliga.

Den viktiga punkten här är att sidan som visas inte ersätts, utan läggs till med de nya resultaten. En användare kan alltid rulla tillbaka upp till de första resultaten av sökningen.

Om du vill implementera oändlig rullning ska vi börja med projektet innan något av rullningselementen för sidnummer lades till. Så, om du behöver, göra en annan kopia av den grundläggande söksidan från GitHub: [Skapa första app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Lägga till växlingsfält i modellen

1. Lägg först till en **växlingsegenskap** i klassen **SearchData** (i SearchData.cs modellfilen).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Den här variabeln är en sträng som innehåller "nästa" om nästa resultatsida ska skickas eller vara null för den första sidan i en sökning.

2. Lägg till en global variabelklass med en egenskap i samma fil och inom namnområdet. I MVC deklareras globala variabler i sin egen statiska klass. **I ResultsPerPage** anges antalet resultat per sida. 

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Lägga till en lodrät rullningslist i vyn

1. Leta reda på avsnittet i filen index.cshtml som visar resultaten (den börjar med ** @if (Modell != null).**

2. Ersätt avsnittet med koden nedan. Det ** &lt;nya&gt; div-avsnittet** är runt det område som ska vara rullningsbart och lägger till både ett **spill-y-attribut** och ett anrop till en **onscroll-funktion** som kallas "scrolled()", så här.

    ```cs
        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>

            <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    // Display the hotel name and description.
                    @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. Direkt under slingan, &lt;efter&gt; /div-taggen, lägger du till den **rullningsade** funktionen.

    ```javascript
        <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 2) {
                                div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                            }
                        });
                    }
                }
        </script>
    ```

    **If-satsen** i skriptet ovan testar för att se om användaren har rullat längst ned i den lodräta rullningslisten. Om de har, ett samtal till **hemkontrollanten** görs till en åtgärd som kallas **Nästa**. Ingen annan information behövs av den registeransvarige, den kommer att returnera nästa sida med data. Dessa data formateras sedan med identiska HTML-format som den ursprungliga sidan. Om inga resultat returneras, ingenting läggs till och saker och ting förbli som de är.

### <a name="handle-the-next-action"></a>Hantera nästa åtgärd

Det finns bara tre åtgärder som behöver skickas till styrenheten: den första körningen av appen, som anropar **Index()**, den första sökningen av användaren, som anropar **Index(modell)** och sedan efterföljande kräver mer resultat via **Next(model)**.

1. Öppna filen hemkontrollant och ta bort metoden **RunQueryAsync** från den ursprungliga självstudien.

2. Ersätt åtgärden **Index(modell)** med följande kod. Det hanterar nu **växlingsfältet** när det är null, eller inställt på "nästa", och hanterar anropet till Azure Cognitive Search.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First call. Check for valid text input.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    page = 0;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Enter Hotel property names into this list so only these values will be returned.
                    // If Select is empty, all values will be returned, which can be inefficient.
                    Select = new[] { "HotelName", "Description" },
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

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    I likhet med den numrerade växlingsmetoden använder vi sökinställningarna **Hoppa över** och **överst** för att begära just de data vi behöver returneras.

3. Lägg till åtgärden **Nästa** i hemkontrollanten. Observera hur den returnerar en lista, varje hotell lägger till två element i listan: ett hotellnamn och en hotellbeskrivning. Det här formatet är inställt på att matcha den **rullande** funktionens användning av returnerade data i vyn.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel name, then description, to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Om du får ett syntaxfel på **liststrängen&lt;&gt;** lägger du sedan till följande **med hjälp** av direktivet i styrenhetsfilens huvud.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Kompilera och driva projektet

Välj nu **Starta utan felsökning** (eller tryck på F5).

1. Ange en term som ger gott om resultat (till exempel "pool") och testa sedan den lodräta rullningslisten. Utlöser det en ny sida med resultat?

    ![Oändlig rullning genom "pool"-resultat](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Om du vill vara medveten om att en rullningslist visas på den första sidan måste den första resultatsidan vara något högre än höjden på det område som de visas i. I vårt exempel **.box1** har en höjd på 30 pixlar, **.box2** har en höjd på 100 pixlar _och_ en nederkant på 24 pixlar. Så varje post använder 154 pixlar. Tre poster tar upp 3 x 154 = 462 pixlar. För att säkerställa att en lodrät rullningslist visas måste en höjd till visningsområdet ställas in som är mindre än 462 pixlar, till och med 461 verk. Det här problemet uppstår bara på den första sidan, efter att en rullningslist är säker på att visas. Raden som ska uppdateras är: ** &lt;div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()"&gt;**.

2. Bläddra nedåt hela vägen till botten av resultatet. Lägg märke till hur all information nu finns på en vysida. Du kan rulla hela vägen tillbaka till toppen utan att utlösa några serversamtal.

Mer sofistikerade oändliga rullningssystem kan använda mushjulet, eller liknande annan mekanism, för att utlösa inläsning av en ny sida med resultat. Vi kommer inte att ta oändlig rullning längre i dessa tutorials, men det har en viss charm till det eftersom det undviker extra musklick, och du kanske vill undersöka andra alternativ ytterligare!

## <a name="takeaways"></a>Lärdomar

Tänk på följande takeaways från det här projektet:

* Numrerad personsökning är bra för sökningar där ordningen på resultaten är något godtycklig, vilket innebär att det mycket väl kan finnas något av intresse för dina användare på de senare sidorna.
* Oändlig rullning är bra när resultatordningen är särskilt viktig. Om resultaten till exempel beställs på avståndet från mitten av en destinationsstad.
* Numrerad personsökning möjliggör lite bättre navigering. En användare kan till exempel komma ihåg att ett intressant resultat fanns på sidan 6, medan det inte finns någon sådan enkel referens i oändlig rullning.
* Oändlig rullning har en enkel överklagande, rulla upp och ner utan kinkig sidnummer att klicka på.
* En viktig funktion i oändlig rullning är att resultaten läggs till på en befintlig sida, inte ersätter den sidan, vilket är effektivt.
* Tillfällig lagring kvarstår för endast ett samtal och måste återställas för att överleva ytterligare anrop.


## <a name="next-steps"></a>Nästa steg

Personsökning är grundläggande för sökningar på Internet. Med växling väl täckt, är nästa steg att förbättra användarupplevelsen ytterligare, genom att lägga till typ-ahead sökningar.

> [!div class="nextstepaction"]
> [C# Självstudiekurs: Lägg till automatisk komplettering och förslag - Azure Cognitive Search](tutorial-csharp-type-ahead-and-suggestions.md)
