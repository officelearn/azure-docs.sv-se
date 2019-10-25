---
title: C#Självstudie om autokomplettering och förslag
titleSuffix: Azure Cognitive Search
description: Den här självstudien bygger på projektet "Sök Resultat sid brytning – Azure Kognitiv sökning" för att lägga till komplettering och förslag. Målet är en rikare användar upplevelse. Lär dig hur du kombinerar en listruta med förslag med intern komplettering.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 959ae749f9ab8a025ec9c78d75640e2108868372
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786492"
---
# <a name="c-tutorial-add-autocompletion-and-suggestions---azure-cognitive-search"></a>C#Självstudie: Lägg till komplettering och förslag – Azure Kognitiv sökning

Lär dig hur du implementerar automatisk komplettering (Skriv-Ahead och förslag) när en användare börjar skriva i sökrutan. I den här självstudien visar vi resultaten för resultat och förslag separat, och visar sedan en metod för att kombinera dem för att skapa en rikare användar upplevelse. En användare behöver bara ange två eller tre nycklar för att hitta alla resultat som är tillgängliga. Den här självstudien bygger på det växlings projekt som skapades i [ C# självstudien: Sök Resultat sid brytning – Azure kognitiv sökning](tutorial-csharp-paging.md) själv studie kurs.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Lägg till förslag
> * Lägg till markering i förslagen
> * Lägg till autokomplettering
> * Kombinera autokomplettering och förslag

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

[ C# Själv studie kursen: sid brytning av Sök Resultat – Azure kognitiv sökning](tutorial-csharp-paging.md) Project up och igång. Projektet kan antingen vara din egen version, som du har slutfört i föregående självstudie eller installera det från GitHub: [skapa första app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="add-suggestions"></a>Lägg till förslag

Vi börjar med det enklaste fallet med att erbjuda alternativ till användaren: en listruta med förslag.

1. I filen index. cshtml ändrar du **TextBoxFor** -instruktionen till följande.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

    Nyckeln här är att vi har angett ID för sökrutan till **azureautosuggest**.

2. Följ den här instruktionen, efter att ha stängt **&lt;/div&gt;** , ange det här skriptet.

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

    Vi har kopplat det här skriptet till sökrutan via samma ID. Dessutom krävs minst två tecken för att utlösa sökningen och vi kallar den **föreslå** åtgärden i Start styrenheten med två frågeparametrar: **högdagrar** och **fuzzy**, båda anges som falskt i den här instansen.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Lägg till referenser till jQuery-skript i vyn

Funktionen Autoavsluta som anropades i skriptet ovan är inte något som vi behöver skriva skapar eftersom den är tillgänglig i jQuery-biblioteket. 

1. Du kommer åt jQuery-biblioteket genom att ändra &lt;Head&gt;-avsnittet i visnings filen till följande kod.

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

2. Vi måste också ta bort eller kommentera ut en rad som refererar till jQuery i filen _Layout. cshtml (i **vyer/delad** mapp). Leta upp följande rader och kommentera ut den första skript raden som visas. Den här ändringen förhindrar konflikt referenser till jQuery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Nu kan vi använda de fördefinierade jQuery-funktionerna i Komplettera automatiskt.

### <a name="add-the-suggest-action-to-the-controller"></a>Lägg till föreslå-åtgärden i kontrollanten

1. I Start styrenheten lägger du till den **föreslåde** åtgärden (t. ex. efter **sid** åtgärden).

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

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    Den **övre** parametern anger hur många resultat som ska returneras (om inget anges är standardvärdet 5). En _förslags ställare_ anges i Azure-indexet, som görs när data har kon figurer ATS och inte av en klient app, till exempel den här självstudien. I det här fallet kallas förslags texten "SG", och den söker i fältet **HotelName** – ingenting annat. 

    Med fuzzy Matching kan "nära Cachemissar" tas med i utdata. Om parametern **markeringar** har angetts till True läggs FETSTILTA HTML-taggar till i utdata. Vi kommer att ställa in dessa två parametrar på True i nästa avsnitt.

2. Du kan få några syntaxfel. I så fall lägger du till följande två **using** -uttryck högst upp i filen.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Kör appen. Får du ett utbud av alternativ när du anger "Po", till exempel? Prova nu "pa".

    ![Om du skriver "Po" påvisar vi två förslag](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Observera att bokstäverna du anger _måste_ starta ett ord och inte bara inkluderas i ordet.

4. I Visa skript, ange **& fuzzy** till true och kör appen igen. Skriv nu "Po". Observera att sökningen förutsätter att du fick en bokstav fel!
 
    ![Skriv "pa" med fuzzy-inställt på Sant](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Om du är intresse rad av [Lucene-frågesyntaxen i Azure kognitiv sökning](https://docs.microsoft.com/azure/search/query-lucene-syntax) beskrivs den logik som används i fuzzy-sökningar i detalj.

## <a name="add-highlighting-to-the-suggestions"></a>Lägg till markering i förslagen

Vi kan förbättra utseendet på förslagen till användaren en bit genom att ange parametern **markeringar** till true. Först måste vi dock lägga till en kod i vyn för att visa den fetstilta texten.

1. I vyn (index. cshtml) lägger du till följande skript efter det **azureautosuggest** -skript som du angav ovan.

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

2. Ändra nu text rutans ID så att det ser ut så här.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Kör appen igen och se att din angivna text är fet i förslagen. Anta att du skriver "pa".
 
    ![Skriv "pa" med markering](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. Logiken som används i markerings skriptet ovan är inte foolproof. Om du anger en term som visas två gånger i samma namn är de fetstilta resultaten inte riktigt vad du vill ha. Försök att skriva "Mo".

    En av frågorna som en utvecklare behöver för att svara är, när är ett skript fungerar "tillräckligt bra", och när bör dess knep tas emot. Vi kommer inte att titta närmare på några ytterligare i den här självstudien, men att hitta en exakt algoritm är något att tänka på om du tar markeringar vidare.

## <a name="add-autocompletion"></a>Lägg till autokomplettering

En annan variation, som skiljer sig något från förslag, är autokomplettering (kallas ibland "typ Ahead"). Återigen börjar vi med den enklaste implementeringen, innan du går vidare till att förbättra användar upplevelsen.

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

2. Ändra nu text rutans ID så att det kan tolkas på följande sätt.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. I Start styrenheten måste vi ange åtgärden för **automatisk komplettering** , till exempel under **föreslå** åtgärd.

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

    Observera att vi använder samma *förslags* funktion, som kallas "SG", i sökningen som vi gjorde för förslag (så att vi bara försöker komplettera om hotell namnen).

    Det finns ett antal **AutocompleteMode** -inställningar och vi använder **OneTermWithContext**. Se [Azure-kompletteraren](https://docs.microsoft.com/rest/api/searchservice/autocomplete) för att se en beskrivning av alternativen här.

4. Kör appen. Observera hur intervallet för de alternativ som visas i list rutan är enkla ord. Försök att skriva ord som börjar med "re". Observera hur antalet alternativ minskar när fler bokstäver skrivs.

    ![Skriva med grundläggande autoifyllning](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    I takt med att det finns rekommenderar vi att du har kört det här skriptet. För att göra det mer användarvänligt är det bäst att lägga till förslags sökningen.

## <a name="combine-autocompletion-and-suggestions"></a>Kombinera autokomplettering och förslag

Att kombinera autokomplettering och förslag är den mest komplexa av våra alternativ och ger förmodligen den bästa användar upplevelsen. Det vi vill ha är att visa, infoga text med den text som skrivs, det första valet av Azure-Kognitiv sökning för att komplettera texten. Vi vill också ha en rad förslag som en nedrullningsbar listruta.

Det finns bibliotek som erbjuder den här funktionen – ofta kallat "intern ifyllning" eller liknande namn. Vi kommer dock att implementera den här funktionen internt så att du kan se vad som händer. Vi kommer att börja arbeta på kontrollanten först i det här exemplet.

1. Vi måste lägga till en åtgärd i kontrollanten som bara returnerar ett resultat för autokomplettering, tillsammans med ett angivet antal förslag. Vi kommer att anropa den här åtgärden **AutocompleteAndSuggest**. I Start styrenheten lägger du till följande åtgärd, efter dina andra nya åtgärder.

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

    Ett alternativ för autokomplettering returneras överst i **resultat** listan, följt av alla förslag.

2. Först implementerar vi ett stick i vyn så att ett ljust grått ord för automatisk komplettering återges direkt under Bolder text som anges av användaren. HTML innehåller relativ placering för detta ändamål. Ändra **TextBoxFor** -instruktionen (och dess omgivande &lt;div&gt;-instruktioner) till följande, och Observera att en andra sökruta som identifieras som **nedan är rätt** under vår vanliga sökruta genom att hämta den här sökrutan 39 pixlar av dess standard plats!

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Observera att vi ändrar ID: t igen till **azureautocomplete** i det här fallet.

3. I vyn anger du även följande skript när du har angett alla skript som du har angett hittills. Det är mycket för IT.

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

    Observera att smarta använder funktionen **Interval** för att både rensa den underliggande texten när den inte längre matchar vad användaren skriver, och även för att ställa in samma Skift läge (övre eller lägre) när användaren skriver (som "pa" matchar "pa", "pa", "pa" vid sökning) , så att den överliggande texten är prydlig.

    Läs igenom kommentarerna i skriptet för att få en mer fullständig förståelse.

4. Slutligen måste vi göra en mindre justering av två HTML-klasser för att göra dem transparenta. Lägg till följande rad i klasserna **searchBoxForm** och **search** i filen Hotels. CSS.

    ```html
        background: rgba(0,0,0,0);
    ```

5. Kör nu appen. Ange "pa" i sökrutan. Får du "Palace" som förslag på Autoavsluta, tillsammans med två hotell som innehåller "pa"?

    ![Skriva med Komplettera automatiskt och förslag](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Försök att tabba för att godkänna Autoavsluta-förslaget och försök att välja förslag med piltangenterna och TABB-tangenten och försök igen med musen och med ett enkelt klick. Kontrol lera att skriptet hanterar alla dessa situationer prydligt.

    Du kan bestämma att det är enklare att läsa in ett bibliotek som erbjuder den här funktionen åt dig, men nu vet du att du kan få intern ifyllning på ett sätt som fungerar!

## <a name="takeaways"></a>Lärdomar

Tänk på följande takeaways från det här projektet:

* Autoifyllning (kallas även "typ Ahead") och förslag kan göra det möjligt för användaren att skriva några nycklar för att hitta exakt det du söker.
* Autokomplettering och förslag som fungerar tillsammans kan ge en omfattande användar upplevelse.
* Testa alltid funktioner för autokomplettering med alla former av inmatade.
* Det kan vara användbart att använda funktionen **setInterval** för att verifiera och korrigera gränssnitts element.

## <a name="next-steps"></a>Nästa steg

I nästa självstudie har vi en titt på ett annat sätt att förbättra användar upplevelsen genom att använda Faces för att begränsa sökningar med ett enda klick.

> [!div class="nextstepaction"]
> [C#Självstudie: använda Faces för att under lätta navigeringen – Azure Kognitiv sökning](tutorial-csharp-facets.md)


