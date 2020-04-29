---
title: C#-självstudie om sid brytning för Sök Resultat
titleSuffix: Azure Cognitive Search
description: Den här självstudien visar hur du växlar Sök resultat. Det bygger på ett befintligt hotell projekt med sid indelning efter första, nästa, föregående, senaste och numrerade knappar. Ett andra växlings system använder oändlig rullning som utlöses genom att flytta en lodrät rullnings list till dess nedre gräns.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 9abfeb54be6e22885b8e973034a6d89df8272146
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "77121519"
---
# <a name="c-tutorial-search-results-pagination---azure-cognitive-search"></a>C#-självstudie: sid brytning för Sök Resultat – Azure Kognitiv sökning

Lär dig hur du implementerar två olika växlings system, det första baserat på sid nummer och det andra vid oändlig rullning. Båda växlings systemen används ofta och valet av rätt är beroende av den användar upplevelse som du vill ha med resultatet. I den här självstudien skapas växlings systemen i projektet som skapats i [C#-själv studie kursen: skapa din första app – Azure kognitiv sökning-](tutorial-csharp-create-first-app.md) självstudie.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Utöka din app med numrerad sid indelning
> * Utöka din app med oändlig rullning

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

Ha [C#-själv studie kursen: skapa din första app – Azure kognitiv sökning](tutorial-csharp-create-first-app.md) Project och köra. Projektet kan antingen vara din egen version eller installeras från GitHub: [skapa första app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Utöka din app med numrerad sid indelning

Numrerad växling är växlings systemet för val av de huvudsakliga sökmotorer för Internet sökmotorer och de flesta andra Sök webbplatser. Numrerad sid indelning innehåller vanligt vis alternativen "Nästa" och "föregående" utöver ett intervall av faktiska sid nummer. Dessutom kan alternativet "första sidan" och "sista sidan" också vara tillgängligt. De här alternativen ger en användar kontroll för att navigera genom sidbaserade resultat.

Vi lägger till ett system som omfattar första, föregående, nästa och sista alternativ, tillsammans med sid nummer som inte börjar från 1, utan omger den aktuella sidan som användaren är på (till exempel om användaren tittar på sidan 10, kanske sid nummer 8, 9, 10, 11 och 12 visas).

Systemet är tillräckligt flexibelt för att tillåta att antalet synliga sid nummer anges i en global variabel.

Systemet kommer att hantera knapparna längst till vänster och höger-högst sidor som särskilda, vilket innebär att de utlöser ändrings intervallet för sid nummer som visas. Exempel: om sid nummer 8, 9, 10, 11 och 12 visas och användaren klickar på 8, visas sid numren som ändras till 6, 7, 8, 9 och 10. Och det finns ett liknande Skift till höger om de markerade 12.

### <a name="add-paging-fields-to-the-model"></a>Lägg till växlings fält i modellen

Ha den grundläggande Sök sid lösningen öppen.

1. Öppna SearchData.cs-modell filen.

2. Lägg först till några globala variabler. I MVC deklareras globala variabler i sin egen statiska klass. **ResultsPerPage** anger antalet resultat per sida. **MaxPageRange** bestämmer antalet synliga sid nummer i vyn. **PageRangeDelta** anger hur många sidor vänster eller höger som sid intervallet ska flyttas till när sidan längst till vänster eller längst till höger är markerad. Det sistnämnda talet är vanligt vis cirka hälften av **MaxPageRange**. Lägg till följande kod i namn området.

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
    >Om du kör projektet på en enhet med en mindre skärm, till exempel en bärbar dator, kan du överväga att ändra **ResultsPerPage** till 2.

3. Lägg till sid egenskaper i **SearchData** -klassen, till exempel efter egenskapen **searchText** .

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

### <a name="add-a-table-of-paging-options-to-the-view"></a>Lägg till en tabell med sid växlings alternativ i vyn

1. Öppna filen index. cshtml och Lägg till följande kod direkt före den avslutande &lt;/Body&gt; -taggen. Den här nya koden visar en tabell med sid alternativ: First, Previous, 1, 2, 3, 4, 5, nästa, sist.

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

    Vi använder en HTML-tabell för att justera saker på ett snyggt sätt. Alla åtgärder kommer dock från @Html.ActionLink instruktionerna, varje anrop av kontrollanten med en **ny** modell som skapats med olika poster till egenskapen **växling** som vi lade till tidigare.

    De första och sista sid alternativen skickar inte strängar som "First" och "Last", utan skickar istället rätt sid nummer.

2. Lägg till några växlings klasser i listan med HTML-format i filen Hotels. CSS. **PageSelected** -klassen finns där för att identifiera sidan som användaren visar (genom att aktivera talet fetstil) i listan med sid nummer.

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

### <a name="add-a-page-action-to-the-controller"></a>Lägg till en sid åtgärd för kontrollanten

1. Öppna filen HomeController.cs och Lägg till åtgärden **sida** . Den här åtgärden svarar på något av de valda sid alternativen.

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

    **RunQueryAsync** -metoden visar nu ett syntaxfel på grund av den tredje parametern som vi kommer att komma till i en bit.

    > [!Note]
    > **TempData** anropar ett värde (ett **objekt**) i ett tillfälligt lagrings utrymme, men den här lagringen sparas _bara_ för ett anrop. Om vi lagrar något i temporära data, kommer det att vara tillgängligt för nästa anrop till en kontroll av styrenheten, men kommer oftast att vara borta av anropet. På grund av den här korta livs längd lagrar vi söktext och växlings egenskaper tillbaka i tillfällig lagring varje och varje anrop till **sida**.

2. **Index (modell)-** åtgärden måste uppdateras för att lagra de tillfälliga variablerna och för att lägga till den vänstra sidan i **RunQueryAsync** -anropet.

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

3. **RunQueryAsync** -metoden måste uppdateras markant. Vi använder fälten **Skip**, **Top**och **IncludeTotalResultCount** i klassen **SearchParameters** för att endast begära en sida med resultat, med början vid **Skip** -inställningen. Vi måste också beräkna växlings variablerna för vår vy. Ersätt hela metoden med följande kod.

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

4. Slutligen måste vi göra en liten ändring i vyn. Variabeln **resultsList. Results. Count** kommer nu att innehålla antalet resultat som returneras på en sida (3 i vårt exempel), inte det totala antalet. Eftersom vi anger **IncludeTotalResultCount** till True innehåller variabeln **resultsList. Count** nu det totala antalet resultat. Leta reda på var antalet resultat visas i vyn och ändra det till följande kod.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Det uppstår en prestanda träff, även om det inte oftast är mycket av en, genom att ange **IncludeTotalResultCount** till true, eftersom den här summan måste beräknas av Azure kognitiv sökning. Med komplexa data uppsättningar finns det en varning om att det returnerade värdet är en _uppskattning_. För våra hotell data är det bättre.

### <a name="compile-and-run-the-app"></a>Kompilera och köra appen

Välj nu **starta utan fel sökning** (eller tryck på tangenten F5).

1. Sök efter text som ger massor av resultat (till exempel "WiFi"). Kan du enkelt bläddra igenom resultaten?

    ![Numrerad växling via "pool"-resultat](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Försök att klicka på längst till höger och sedan längst till vänster på sidan. Justerar sid numren på rätt sätt för att centrera sidan som du är på?

3. Är alternativen "First" och "sista" användbara? Vissa populära Webbs ökningar använder dessa alternativ och andra inte.

4. Gå till sista resultat sidan. Den sista sidan är den enda sidan som kan innehålla färre än **ResultsPerPage** resultat.

    ![Undersöker den sista sidan i "WiFi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Skriv "stad" och klicka på Sök. Inga sid alternativ visas om det finns mindre än en sida med resultat.

    ![Söker efter "stad"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Spara nu det här projektet och prova med ett alternativ till den här typen av växling.

## <a name="extend-your-app-with-infinite-scrolling"></a>Utöka din app med oändlig rullning

Oändlig rullning utlöses när en användare rullar en lodrät rullnings list till det sista av resultaten som visas. I detta fall görs ett anrop till servern för nästa resultat sida. Om det inte finns några fler resultat returneras ingenting och den lodräta rullnings listen ändras inte. Om det finns fler resultat läggs de till på den aktuella sidan och rullnings listen ändras för att visa att fler resultat är tillgängliga.

Den viktiga punkten här är att den sida som visas inte ersätts, men läggs till i med det nya resultatet. En användare kan alltid rulla tillbaka till det första resultatet av sökningen.

Om du vill implementera oändlig rullning börjar vi med projektet innan något av de rullande elementen för sid nummer har lagts till. Så om du behöver göra en kopia av den grundläggande Sök sidan från GitHub: [skapa första app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Lägg till växlings fält i modellen

1. Lägg först till en **växlings** egenskap i **SearchData** -klassen (i SearchData.cs-modell filen).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Den här variabeln är en sträng som innehåller "Nästa" om nästa sida med resultat ska skickas, eller vara null för den första sidan i en sökning.

2. I samma fil och i namn området lägger du till en global variabel klass med en egenskap. I MVC deklareras globala variabler i sin egen statiska klass. **ResultsPerPage** anger antalet resultat per sida. 

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

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Lägg till en lodrät rullnings List i vyn

1. Leta upp avsnittet i filen index. cshtml som visar resultatet (det börjar med ** @if (modell! = null)**).

2. Ersätt avsnittet med koden nedan. Det nya ** &lt;div&gt; ** -avsnittet är runt området som ska kunna rullas och lägger till både ett **overflow-y-** attribut och ett anrop till en **onscroll** -funktion som kallas "rullnings Bart ()", som så.

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

3. Direkt under slingan, efter taggen &lt;/div&gt; , lägger du till den **rullnings bara** funktionen.

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

    Instruktionen **IF** i skriptet ovan testar för att se om användaren har rullat längst ned i den lodräta rullnings listen. Om de har det, görs ett anrop till **hem** styrenheten till en åtgärd som kallas **Nästa**. Ingen annan information behövs av kontrollanten, den kommer att returnera nästa sida med data. Dessa data formateras sedan med identiska HTML-format som den ursprungliga sidan. Om inga resultat returneras, läggs inget till och behålls som de är.

### <a name="handle-the-next-action"></a>Hanterar nästa åtgärd

Det finns bara tre åtgärder som måste skickas till kontrollanten: den första körningen av appen, som anropar **index ()**, den första sökningen av användaren, som anropar **index (modell)** och sedan de efterföljande anropen för fler resultat via **Next (modell)**.

1. Öppna start kontroll filen och ta bort **RunQueryAsync** -metoden från den ursprungliga självstudien.

2. Ersätt **index-åtgärden (modell)** med följande kod. Nu hanterar det **växlings** fältet när det är null, eller anges till "Next", och hanterar anropet till Azure kognitiv sökning.

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

    Precis som med den numrerade växlings metoden använder vi Sök inställningarna **Skip** och **Top** för att begära att bara de data vi behöver returneras.

3. Lägg till **Nästa** åtgärd till Start styrenheten. Observera hur den returnerar en lista, varje hotell som lägger till två element i listan: ett hotell namn och en hotell beskrivning. Det här formatet är inställt på att matcha den **rullnings bara** funktionens användning av returnerade data i vyn.

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

4. Om du får ett syntaxfel i **list&lt;strängen&gt;** lägger du till följande **med hjälp av** direktiv i enhets filens huvud.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Kompilera och köra ditt projekt

Välj nu **starta utan fel sökning** (eller tryck på tangenten F5).

1. Ange en term som ger massor av resultat (till exempel "pool") och testa den lodräta rullnings listen. Utlöses en ny resultat sida?

    ![Oändlig rullning genom "pool"-resultat](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > För att se till att en rullnings List visas på den första sidan måste den första sidan med resultat skilja sig från höjden på det utrymme som de visas i. I vårt exempel **. box1** har höjden 30 bild punkter, **. box2** har en höjd på 100 bild punkter _och_ en botten marginal på 24 bild punkter. Varje post använder alltså 154 bild punkter. Tre poster tar upp 3 x 154 = 462 bild punkter. För att säkerställa att en lodrät rullnings List visas måste en höjd till visnings utrymmet vara inställd på mindre än 462 bild punkter, även 461 fungerar. Det här problemet inträffar bara på den första sidan, efter att en rullnings List är säker på att den ska visas. Raden som ska uppdateras är: ** &lt;div ID = "myDiv" Style = "width: 800px; height: 450px; spill-y: Scroll;" onscroll = "rullad ()"&gt;**.

2. Bläddra nedåt längst ned i resultaten. Observera hur all information finns nu på sidan med en vy. Du kan bläddra hela vägen överst utan att utlösa några Server anrop.

Mer avancerade oändliga rullnings bara system kan använda mus hjulet eller liknande andra mekanism för att utlösa inläsningen av en ny resultat sida. Vi kommer inte att ta bort oändlig bläddring i de här självstudierna, men det har en särskild snabb knapp som du kan använda för att undvika extra musklick och du kanske vill undersöka andra alternativ ytterligare!

## <a name="takeaways"></a>Lärdomar

Tänk på följande takeaways från det här projektet:

* Numrerad sid indelning är bra för sökningar där ordningen på resultaten är något godtycklig, vilket innebär att det kan vara något intressant för användarna på de senare sidorna.
* Oändlig rullning är bra när resultat ordningen är särskilt viktig. Om resultatet till exempel beställs på avståndet från mitten av en ort i destinationen.
* Med numrerad sid indelning kan du få bättre navigering. En användare kan till exempel komma ihåg att ett intressant resultat var på sidan 6, men det finns ingen sådan enkel referens i oändlig rullning.
* Oändlig rullning har ett enkelt sätt att rulla upp och ned utan att behöva sid nummer för att klicka på.
* En viktig funktion för oändlig rullning är att resultat läggs till på en befintlig sida, inte att ersätta sidan, vilket är effektivt.
* Temporär lagring behålls bara för ett samtal och måste återställas för att klara ytterligare samtal.


## <a name="next-steps"></a>Nästa steg

Sid indelning är grundläggande för Internets ökningar. Med rätt sid indelning är nästa steg att förbättra användar upplevelsen ytterligare genom att lägga till typ söknings sökningar.

> [!div class="nextstepaction"]
> [C#-självstudie: Lägg till komplettering och förslag – Azure Kognitiv sökning](tutorial-csharp-type-ahead-and-suggestions.md)
