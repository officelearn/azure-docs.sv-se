---
title: C#självstudie om sökning resulterar sidbrytning – Azure Search
description: Den här självstudien bygger på ”Skapa ditt första program – Azure Search”-projektet med valet av två typer av sidindelning. Först använder olika knappar för sidnummer, samt först, sedan tidigare, och senast sidan knappar. Andra används sidindelning oändlig rullning, utlöses genom att flytta en lodrät rullningslist till dess nedre gräns.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: 7e6c433168b73c6b58d13d4698bed55d7c18ec58
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434634"
---
# <a name="c-tutorial-search-results-pagination---azure-search"></a>C#självstudie: Sökresultat sidbrytning – Azure Search

Lär dig hur du implementerar två olika sidindelning system, först utifrån sidnummer och andra på oändlig rullning. Båda systemen för sidindelning används mycket och att välja rätt beror på användarens upplevelse som du vill ha med resultaten. Den här självstudien bygger sidindelning system i projektet har skapats i den [ C# självstudien: Skapa ditt första program – Azure Search](tutorial-csharp-create-first-app.md) självstudien.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Utöka din app med numrerade växling
> * Utöka din app med oändlig rullning

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

Har den [ C# självstudien: Skapa ditt första program – Azure Search](tutorial-csharp-create-first-app.md) projekt och drift. Det här projektet kan vara din egen version eller installera det från GitHub: [Skapa första app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Utöka din app med numrerade växling

Numrerade växling är sidindelning-system med val av de huvudsakliga sökmotorer på internet och de flesta andra search-webbplatser. Numrerade sidindelning innehåller vanligtvis ett ”nästa” och ”tidigare” alternativ utöver en sidintervallet faktiska. Även kan en ”första sidan” och ”sista sidan” alternativet också vara tillgängliga. Dessa alternativ ger däremot en användarkontroll över gå igenom sidan-baserade resultat.

Vi lägger till ett system som innehåller första, föregående, nästa och senast alternativ, tillsammans med sidnummer som inte startar från 1, men i stället omge den aktuella sidan användaren finns på (så, till exempel om du tittar på sidan 10, kanske sidnummer 8 9, 10, 11 och 12 visas).

Systemet blir tillräckligt flexibelt för att tillåta antal synliga sidnummer anges i en global variabel.

Systemet behandlar på vänster och höger sida siffrorna som särskilda, vilket innebär att de utlöser ändring av intervallet för sidnummer visas. Till exempel om sidnummer 8, 9, 10, 11 och 12 visas och användaren klickar på 8, visas sedan sidintervallet ändringar för 6, 7, 8, 9 och 10. Och det finns en liknande skifte till höger om de markerade 12.

### <a name="add-paging-fields-to-the-model"></a>Lägga till växling fält i modellen

Har den grundläggande söklösningen på sidan öppna.

1. Öppna modellfilen SearchData.cs.

2. Först ska du lägga till vissa globala variabler. Globala variabler deklareras i sin egen statisk klass i MVC dvs. **ResultsPerPage** anger antalet resultat per sida. **MaxPageRange** anger antalet synliga sidnummer för vyn. **PageRangeDelta** avgör hur många sidor åt vänster eller höger de sidor som ska flyttas när vänster eller höger sidnumret väljs. Numret senare är vanligtvis runt hälften av **MaxPageRange**. Lägg till följande kod i namnområdet.

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
    >Om du kör det här projektet på en enhet med en mindre skärm, till exempel en bärbar dator, bör du ändra **ResultsPerPage** till 2.

3. Lägga till växling egenskaper så att den **SearchData** klassen exempelvis efter den **searchText** egenskapen.

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

### <a name="add-a-table-of-paging-options-to-the-view"></a>Lägg till en tabell med växlingsalternativ i vyn

1. Öppna filen index.cshtml och Lägg till följande kod direkt före avslutande &lt;/body&gt; tagg. Den här nya koden visar en tabell med växlingsalternativ: första, föregående, 1, 2, 3, 4, 5, sedan senaste.

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

    Vi använder en HTML-tabell för att justera saker snyggt. Men alla åtgärder som kommer från den @Html.ActionLink instruktioner, som anropar kontrollanten med en **nya** modellen som skapats med olika poster till den **växlingsfiler** egenskapen som vi lade till tidigare.

    De första och sista sidan alternativ strängar, till exempel ”” och ”efternamn” Skicka inte, men i stället skicka rätt sidnummer.

2. Lägga till vissa sidindelning klasser i listan över HTML-format i filen hotels.css. Den **pageSelected** klassen finns tillgängliga för att identifiera den sida som användaren för närvarande (genom att stänga många fetstil) i listan över sidnummer.

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

### <a name="add-a-page-action-to-the-controller"></a>Lägg till en sida-åtgärd till styrenhet

1. Öppna filen homecontroller.CS och Lägg till den **sidan** åtgärd. Den här åtgärden svarar på något av sidan alternativ som valts.

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

    Den **RunQueryAsync** metod visas nu ett syntaxfel på grund av den tredje parametern, som vi kommer att ta i en stund.

    > [!Note]
    > Den **TempData** anrop spara ett-värde (en **objekt**) i tillfällig lagring, men den här lagringen kvarstår efter _endast_ ett anrop. Om vi sparar något i tillfälliga data blir tillgängliga för din nästa uppmaning till en domänkontrollant, men virtuella tas bort av anropet efter det! På grund av den här korta livslängd Vi lagrar söktexten och tillbaka sidindelning egenskaper i tillfällig lagring för varje anrop till **sidan**.

2. Den **Index(model)** åtgärd måste uppdateras för att lagra tillfällig variablerna och lägga till parametern vänster sida för att den **RunQueryAsync** anropa.

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

3. Den **RunQueryAsync** metod måste uppdateras avsevärt. Vi använder den **hoppa över**, **upp**, och **IncludeTotalResultCount** fälten i den **SearchParameters** klassen för att begära plats på endast en sida av resultat, med början vid den **hoppa över** inställningen. Vi också behöva beräkna sidindelning variabler för vår vy. Ersätt hela metoden med följande kod.

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

4. Slutligen måste vi göra små ändringar i vyn. Variabeln **resultsList.Results.Count** innehåller nu antalet resultat som returneras i en sida (3 i vårt exempel), inte det totala antalet. Eftersom vi anger den **IncludeTotalResultCount** true, variabeln **resultsList.Count** innehåller nu det totala antalet resultat. Så Leta upp där antalet resultat som visas i vyn och ändra den till följande kod.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Det är en träff prestanda, men vanligtvis inte mycket av en, genom att ange **IncludeTotalResultCount** true, eftersom denna summa måste beräknas av Azure Search. Med komplexa datamängder som det finns en varning om att det returnerade värdet är en _uppskattning_. För våra Hotelldata är korrekt.

### <a name="compile-and-run-the-app"></a>Kompilera och kör appen

Välj nu **starta utan felsökning** (eller tryck på F5).

1. Sök på text som ger tillräckligt med resultat (till exempel ”wifi”). Kan du bläddra snyggt igenom resultaten?

    ![Numrerade bläddring genom ”pool” resultat](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Försök att klicka på de högra eller senare, vänster sidnummer. Numren justerar på lämpligt sätt för att centrera den sida du är på?

3. Är alternativen ”” och ”efternamn” användbara? Använd dessa alternativ för några populära webbsökningar och andra inte.

4. Gå till den sista sidan i resultaten. Den sista sidan är den enda sidan som kan innehålla mindre än **ResultsPerPage** resultat.

    ![Undersöka sista sidan i ”wifi”](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Skriv ”stad” och klicka på Sök. Inga växlingsalternativ visas om det finns plats på mindre än en sida med resultat.

    ![Söka efter ”stad”](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Spara nu av det här projektet och prova med ett alternativ till den här typen av sidindelning.

## <a name="extend-your-app-with-infinite-scrolling"></a>Utöka din app med oändlig rullning

Oändlig rullning utlöses när användaren rullar en lodrät rullningslist till den senaste av resultaten som visas. I så fall görs ett anrop till servern för nästa sida i resultatet. Om det finns inga fler resultat, inget returneras och den lodräta rullningslisten ändras inte. Om det finns fler resultat, läggs de till den aktuella sidan och rulla fält ändras och visar att det finns fler resultat.

Det viktiga här är att sidan visas inte ersätts, men tillagda till med de nya resultat. En användare kan alltid gå tillbaka till de första resultaten av sökningen.

Om du vill implementera oändlig rullning, låt oss börja med projektet innan någon av de numeriska rullande sidelement lades till. Så om du behöver göra en kopia av sidan för enkel sökning från GitHub: [Skapa första app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Lägga till växling fält i modellen

1. Lägg först till en **växlingsfiler** egenskap enligt den **SearchData** klassen (i modellfilen SearchData.cs).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Den här variabeln är en sträng som innehåller ”nästa” om nästa sida i resultatet ska skickas, eller vara null för den första sidan i en sökning.

2. Lägg till en global variabel klass med en egenskap i samma fil och i ett namnområde. Globala variabler deklareras i sin egen statisk klass i MVC dvs. **ResultsPerPage** anger antalet resultat per sida. 

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

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Lägg till en lodrät rullningslist till vyn

1. Leta upp avsnittet av filen index.cshtml som visar resultatet (det börjar med den  **@if (modell! = null)** ).

2. Ersätt avsnittet med koden nedan. Den nya **&lt;div&gt;** avsnittet är runt det område som ska vara bläddringsbara och lägger till både en **spill y** attribut och ett anrop till en **onscroll**funktion som kallas ”scrolled()”, t.ex.

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

3. Direkt under loopen, när den &lt;/div&gt; tagga, lägga till den **rullas** funktion.

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

    Den **om** instruktionen i skriptet ovan tester för att se om användaren har försvunnit längst ned på den lodräta rullningslisten. Om de har ett anrop till den **Start** controller görs för att en åtgärd som kallas **nästa**. Ingen annan information som krävs av controller, returnerar den nästa sida i data. Dessa data formateras sedan med identiska HTML-format som den ursprungliga sidan. Om inga resultat returneras inget läggs och Förbered dig inför saker som de är.

### <a name="handle-the-next-action"></a>Hantera nästa åtgärd

Det finns endast tre åtgärder som ska skickas till kontrollanten: första körningen av en app, som anropar **Index()** , den första sökningen av användaren, som anropar **Index(model)** , och sedan efterföljande anropar för fler resultat via **Next(model)** .

1. Öppna filen home domänkontrollant och ta bort den **RunQueryAsync** metod från den ursprungliga handboken.

2. Ersätt den **Index(model)** åtgärd med följande kod. Den hanterar nu den **växlingsfiler** när det är null eller inställd på ”Nästa” och hanterar anropet till Azure Search.

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

    Liknar metoden numrerade växling, som vi använder den **hoppa över** och **upp** sökinställningar att begära precis de data som vi behöver returneras.

3. Lägg till den **nästa** att kontrollanten hem. Observera hur den returnerar en lista, varje hotell som lägger till två element i listan: ett hotellnamn och en beskrivning för hotell. Det här formatet är inställt på att matcha den **rullas** funktionens användning av data som returneras i vyn.

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

4. Om du får ett syntaxfel **lista&lt;sträng&gt;** , Lägg till följande **med** direktivet i sidhuvudet för styrenheten.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Kompilera och köra projektet

Välj nu **starta utan felsökning** (eller tryck på F5).

1. Ange en term som ger tillräckligt med resultat (till exempel ”pool”) och sedan testa den lodräta rullningslisten. Det utlösa en ny sida med resultat?

    ![Oändlig rulla igenom ”pool” resultat](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > För att säkerställa att en rullningslist visas på första sidan, överskrida första sidan i resultatet något höjden på området för de som visas i. I vårt exempel **.box1** har en höjd på 30 bildpunkter **.box2** har en höjd på 100 bildpunkter _och_ en nedre marginalen på 24 bildpunkter. Så använder varje post 154 bildpunkter. Tre poster tar upp 3 x 154 = 462 bildpunkter. För att säkerställa att en lodrät rullningslist, en höjden visningsområdet måste anges som är mindre än 462 bildpunkter, även 461 fungerar. Det här problemet inträffar bara på första sidan efter en rullningslist är säker på att visas. The line to update is: **&lt;div id="myDiv" style="width: 800 bpt; Höjd: 450px; Spill y: Bläddra ”; onscroll="scrolled() ”&gt;** .

2. Rulla nedåt ända till slutet av resultaten. Observera hur all information är nu på sidan en vy. Du kan rulla ända tillbaka på sidan utan att alla serveranrop.

Mer avancerade oändlig rullande system kan använda mushjulet, eller liknande andra mekanism för att utlösa inläsningen av en ny sida med resultat. Vi kommer inte ta oändlig rullning sådan ytterligare i de här självstudierna, men den har en viss snabbknappen till den eftersom de undviker extra musklick, och du kanske vill undersöka andra alternativ för ytterligare!

## <a name="takeaways"></a>Lärdomar

Överväg följande takeaways från det här projektet:

* Numrerade växling är bra för sökningar där ordningen på resultaten är något godtyckliga, vilket innebär kan också vara något av intresse för dina användare på sidorna senare.
* Oändlig rullning är bra när resultatet är särskilt viktigt. Till exempel, om resultaten ordnas avståndet från mitten av en mål-stad.
* Numrerade växling kan vissa bättre navigering. Exempelvis kan en användare kan komma ihåg att det var ett intressanta resultat på sidan 6, medan ingen enkel referens finns i oändlig rullning.
* Oändlig rullning har en enkel ändring, rulla uppåt och nedåt utan krångligt sidnummer Klicka på.
* En nyckelfunktion i oändlig rullning är att resultatet läggs till på en befintlig sida, inte ersätta sidan, vilket är effektivt.
* Temporär lagring kvarstår för bara ett anrop och måste återställas för att överleva ytterligare anrop.


## <a name="next-steps"></a>Nästa steg

Växling är grundläggande för internet-sökningar. Med växling väl skyddad, är nästa steg att förbättra användarupplevelsen ytterligare, genom att lägga till frågeifyllningsförslag sökningar.

> [!div class="nextstepaction"]
> [C#Självstudie: Lägg till kommandofönstret Slutför automatiskt och förslag – Azure Search](tutorial-csharp-type-ahead-and-suggestions.md)
