---
title: Komplettera automatiskt och förslag
titleSuffix: Azure Cognitive Search
description: Den här självstudien visar komplettera automatiskt och förslag som ett sätt att samla in söktermen från användare med hjälp av listrutan. Det bygger på ett befintligt hotellprojekt.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 6b74c3bbb811c122950fd969a8797e87f8f77f86
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641076"
---
# <a name="c-tutorial-add-autocomplete-and-suggestions---azure-cognitive-search"></a>C# självstudiekurs: Lägg till komplettera automatiskt och förslag - Azure Cognitive Search

Lär dig hur du implementerar komplettera automatiskt (typeahead-frågor och föreslagna dokument) när en användare börjar skriva i en sökruta. I den här självstudien visar vi automatiskt adematerade frågor och förslagsresultat separat och sedan tillsammans. En användare kanske bara behöver skriva två eller tre tecken för att hitta alla tillgängliga resultat.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Lägga till förslag
> * Lägga till markering i förslagen
> * Lägg till komplettera automatiskt
> * Kombinera komplettera automatiskt och förslag

## <a name="prerequisites"></a>Krav

Den här självstudien är en del av en serie och bygger på det växlingsprojekt som skapats i [C# Tutorial: Sökresultat sidnumreringen - Azure Cognitive Search](tutorial-csharp-paging.md).

Alternativt kan du ladda ner och köra lösningen för den här specifika självstudien: [3-tillägg-typeahead](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead).

## <a name="add-suggestions"></a>Lägga till förslag

Låt oss börja med det enklaste fallet att erbjuda upp alternativ till användaren: en rullgardinslista med förslag.

1. I filen index.cshtml `@id` ändrar du **TextBoxFor-satsen** till **azureautosuggest**.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

2. Ange det här skriptet efter den här satsen efter den avslutande ** &lt;&gt;/diven.** Det här skriptet utnyttjar [widgeten Komplettera automatiskt](https://api.jqueryui.com/autocomplete/) från det jQuery UI-biblioteket med öppen källkod för att visa listrutan med föreslagna resultat. 

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/Suggest?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    ID"azureautosuggest" ansluter ovanstående skript till sökrutan. Källalternativet för widgeten är inställt på en Föreslå-metod som anropar föreslå API med två frågeparametrar: **högdagrar** och **suddiga**, båda inställda på false i den här instansen. Dessutom behövs minst två tecken för att utlösa sökningen.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Lägga till referenser till jQuery-skript i vyn

1. Om du vill komma åt &lt;biblioteket&gt; ändrar du huvudavsnittet i vyfilen till följande kod:

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Eftersom vi introducerar en ny jQuery-referens måste vi också ta bort, eller kommentera ut, standardreferensen i filen _Layout.cshtml (i mappen **Vyer/Delad).** Leta reda på följande rader och kommentera den första skriptraden som visas. Den här ändringen undviker att kollidera med referenser till jQuery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Nu kan vi använda de fördefinierade Autocomplete jQuery-funktionerna.

### <a name="add-the-suggest-action-to-the-controller"></a>Lägg till åtgärden Föreslå i styrenheten

1. Lägg till åtgärden **Föreslå** (t.ex. efter sidåtgärden) i hemkontrollanten. **Page**

    ```cs
        public async Task<ActionResult> Suggest(bool highlights, bool fuzzy, string term)
        {
            InitSearch();

            // Setup the suggest parameters.
            var parameters = new SuggestParameters()
            {
                UseFuzzyMatching = fuzzy,
                Top = 8,
            };

            if (highlights)
            {
                parameters.HighlightPreTag = "<b>";
                parameters.HighlightPostTag = "</b>";
            }

            // Only one suggester can be specified per index. It is defined in the index schema.
            // The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    Parametern **Top** anger hur många resultat som ska returneras (om det inte anges är standardvärdet 5). En _förslagsman_ anges på Azure-indexet, vilket görs när data ställs in, och inte av en klientapp som den här självstudien. I det här fallet kallas förslagsfören "sg", och den söker i **HotelName-fältet** - inget annat. 

    Fuzzy matchning tillåter "nära missar" som ska ingå i utdata, upp till en redigering avstånd. Om parametern **Highlights** är true läggs fet html-taggar till i utdata. Vi kommer att ställa in dessa två parametrar till sant i nästa avsnitt.

2. Du kan få några syntaxfel. Om så är fallet lägger du till följande två **med hjälp** av satser överst i filen.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Kör appen. Får du en rad alternativ när du anger "po", till exempel? Försök nu "pa".

    ![Skriva "po" avslöjar två förslag](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Observera att de bokstäver du anger _måste_ starta ett ord, och inte bara ingå i ordet.

4. I vyskriptet anger du **&luddig till** true och kör appen igen. Skriv nu in "po". Observera att sökningen förutsätter att du fick ett brev fel!
 
    ![Skriva "pa" med luddig inställd på true](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Om du är intresserad beskriver [Lucene-frågesyntaxen i Azure Cognitive Search](https://docs.microsoft.com/azure/search/query-lucene-syntax) logiken som används vid luddiga sökningar i detalj.

## <a name="add-highlighting-to-the-suggestions"></a>Lägga till markering i förslagen

Vi kan förbättra utseendet på förslagen till användaren genom att ställa in **parametern Highlights** till true. Men först måste vi lägga till lite kod i vyn för att visa fet text.

1. Lägg till följande skript efter **azureautosuggest-skriptet** som du angav ovan i vyn (index.cshtml).

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

2. Nu ändra ID för textrutan så att den lyder som följer.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Kör appen igen och du bör se din angivna text fetstilt i förslagen. Säg, prova att skriva "pa".
 
    ![Skriva "pa" med markering](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. Logiken som används i markeringsskriptet ovan är inte idiotsäker. Om du anger en term som visas två gånger med samma namn är de fetstilt resultaten inte riktigt vad du vill ha. Prova att skriva "mo".

    En av de frågor en utvecklare måste svara på är, när är ett skript som fungerar "tillräckligt bra", och när bör dess egenheter åtgärdas. Vi kommer inte att ta belysa längre i den här guiden, men att hitta en exakt algoritm är något att överväga om markera inte är effektivt för dina data. Mer information finns i [Tryckmarkering](search-pagination-page-layout.md#hit-highlighting).

## <a name="add-autocomplete"></a>Lägg till komplettera automatiskt

En annan variant, som skiljer sig något från förslag, är automatisk komplettering (kallas ibland "type-ahead") som slutför en frågeterm. Återigen kommer vi att börja med den enklaste implementeringen, innan vi förbättrar användarupplevelsen.

1. Ange följande skript i vyn, efter dina tidigare skript.

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

2. Nu ändra ID för textrutan, så det står som följer.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. I hemkontrollanten måste vi ange åtgärden **Komplettera automatiskt,** säg, under **åtgärden Föreslå.**

    ```cs
        public async Task<ActionResult> AutoComplete(string term)
        {
            InitSearch();

            // Setup the autocomplete parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 6
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Convert the results to a list that can be displayed in the client.
            List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();

            // Return the list.
            return new JsonResult(autocomplete);
        }
    ```

    Observera att vi använder samma *förslagsfunktion,* som kallas "sg", i autocomplete-sökningen som vi gjorde för förslag (så vi försöker bara komplettera hotellnamnen automatiskt).

    Det finns en rad **inställningar för Komplettera automatisktmode,** och vi använder **OneTermWithContext**. Se [Api för automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete) för en beskrivning av ytterligare alternativ.

4. Kör appen. Lägg märke till hur det alternativintervall som visas i listrutan är enstaka ord. Prova att skriva ord som börjar med "re". Lägg märke till hur antalet alternativ minskar när fler bokstäver skrivs in.

    ![Skriva med grundläggande automatisk komplettering](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    Som det ser ut, de förslag skript du körde tidigare är förmodligen mer användbart än denna automatisk komplettering skript. Om du vill göra automatisk komplettering mer användarvänligt läggs det bäst till i förslagssökningen.

## <a name="combine-autocompletion-and-suggestions"></a>Kombinera komplettera automatiskt och förslag

Att kombinera komplettera automatiskt och förslag är det mest komplexa av våra alternativ, och ger förmodligen den bästa användarupplevelsen. Vad vi vill är att visa, infogad med den text som skrivs, är det första valet av Azure Cognitive Search för automatisk komplettering av texten. Dessutom vill vi ha en rad förslag som en rullgardinslista.

Det finns bibliotek som erbjuder den här funktionen - ofta kallad "inline autocompletion" eller ett liknande namn. Men vi kommer att implementera den här funktionen, så att du kan se vad som händer. Vi kommer att börja arbeta med den registeransvarige först i detta exempel.

1. Vi måste lägga till en åtgärd till styrenheten som returnerar bara ett resultat för automatisk komplettering, tillsammans med ett visst antal förslag. Vi kommer att kalla denna åtgärd **Komplettera automatisktAndSuggest**. Lägg till följande åtgärd i hemkontrollanten och följer dina andra nya åtgärder.

    ```cs
        public async Task<ActionResult> AutocompleteAndSuggest(string term)
        {
            InitSearch();

            // Setup the type-ahead search parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 1,
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Setup the suggest search parameters.
            var sp = new SuggestParameters()
            {
                Top = 8,
            };

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and it searches only the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", sp);

            // Create an empty list.
            var results = new List<string>();

            if (autocompleteResult.Results.Count > 0)
            {
                // Add the top result for type-ahead.
                results.Add(autocompleteResult.Results[0].Text);
            }
            else
            {
                // There were no type-ahead suggestions, so add an empty string.
                results.Add("");
            }
            for (int n = 0; n < suggestResult.Results.Count; n++)
            {
                // Now add the suggestions.
                results.Add(suggestResult.Results[n].Text);
            }

            // Return the list.
            return new JsonResult(results);
        }
    ```

    Ett alternativ för automatisk komplettering returneras högst upp i **resultatlistan,** följt av alla förslag.

2. I vyn implementerar vi först ett trick så att ett ljusgrått ord för automatisk komplettering återges rätt under fetare text som anges av användaren. HTML innehåller relativ positionering för detta ändamål. Ändra **TextBoxFor-satsen** (och dess omgivande &lt;div-satser)&gt; till följande, och notera att en andra sökruta som identifieras som **under** är precis under vår normala sökruta, genom att dra den här sökrutan 39 pixlar bort från sin standardplats!

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Observera att vi ändrar ID igen, till **azureautocomplete** i det här fallet.

3. Också i vyn, ange följande skript, efter alla skript som du har angett hittills. Det är ganska mycket till det.

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggest?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    Lägg märke till den smarta användningen av **intervallfunktionen** för att både rensa den underliggande texten när den inte längre matchar vad användaren skriver, och även för att ställa in samma fall (övre eller nedre) som användaren skriver (som "pa" matchar "PA", "pA", "Pa" när du söker), så att den överlagrade texten är snygg.

    Läs igenom kommentarerna i skriptet för att få en mer fullständig förståelse.

4. Slutligen måste vi göra en mindre justering av två HTML-klass för att göra dem transparenta. Lägg till följande rad i **klassen searchBoxForm** och **searchBox** i filen hotels.css.

    ```html
        background: rgba(0,0,0,0);
    ```

5. Kör nu appen. Skriv in "pa" i sökrutan. Får du "palats" som komplettera automatiskt förslag, tillsammans med två hotell som innehåller "pa"?

    ![Skriva med infogad komplettera automatiskt och förslag](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Försök tabbing för att acceptera förslaget komplettera automatiskt och försök att välja förslag med piltangenterna och tabbtangenten och försök igen med musen och ett enda klick. Kontrollera att skriptet hanterar alla dessa situationer snyggt.

    Du kanske bestämmer dig för att det är enklare att ladda i ett bibliotek som erbjuder den här funktionen för dig, men nu vet du minst ett sätt att få inline automatisk komplettering att fungera!

## <a name="takeaways"></a>Lärdomar

Tänk på följande takeaways från det här projektet:

* Komplettera automatiskt (kallas även "type-ahead") och förslag kan göra det möjligt för användaren att skriva bara några nycklar för att hitta exakt vad de vill.
* Komplettera automatiskt och förslag som arbetar tillsammans kan ge en rik användarupplevelse.
* Testa alltid automatisk kompletteringsfunktioner med alla former av indata.
* Använda **setInterval-funktionen** kan vara användbart för att verifiera och korrigera gränssnittselement.

## <a name="next-steps"></a>Nästa steg

I nästa handledning har vi en titt på ett annat sätt att förbättra användarupplevelsen, med hjälp av aspekter för att begränsa sökningar med ett enda klick.

> [!div class="nextstepaction"]
> [C# Självstudiekurs: Använd aspekter för att underlätta navigering - Azure Cognitive Search](tutorial-csharp-facets.md)


