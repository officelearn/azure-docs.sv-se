---
title: C#Självstudiekurs om kommandofönstret Slutför automatiskt och förslag – Azure Search
description: Den här självstudien bygger på ”sökresultat sidbrytning – Azure Search”-projektet, att lägga till kommandofönstret Slutför automatiskt och förslag. Målet är en bättre användarupplevelse. Lär dig hur du kombinerar en nedrullningsbar listruta med förslag med infogad kommandofönstret Slutför automatiskt.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: d1722d98b594c8a317fa782eab223a754fc578fe
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166808"
---
# <a name="c-tutorial-add-autocompletion-and-suggestions---azure-search"></a>C#Självstudie: Lägg till kommandofönstret Slutför automatiskt och förslag – Azure Search

Lär dig hur du implementerar kommandofönstret Slutför automatiskt (frågeifyllningsförslag och förslag) när en användare startar att skriva i sökrutan. I de här självstudierna kommer vi visa frågeifyllningsförslag resultat och förslag resultat separat och sedan visa en metod för att kombinera dem för att skapa en bättre användarupplevelse. En användare kan bara behöver skriva två eller tre nycklar för att hitta alla resultat som är tillgängliga. Den här självstudien bygger på sidindelning projektet har skapats i den [ C# självstudien: Sökresultat sidbrytning – Azure Search](tutorial-csharp-paging.md) självstudien.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Lägg till förslag
> * Lägga till markering till förslag på
> * Lägg till kommandofönstret Slutför automatiskt
> * Kombinera kommandofönstret Slutför automatiskt och förslag

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

Har den [ C# självstudien: Sökresultat sidbrytning – Azure Search](tutorial-csharp-paging.md) projekt och drift. Det här projektet kan vara din egen version som du har slutfört i den tidigare självstudiekursen, eller installera det från GitHub: [Skapa första app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="add-suggestions"></a>Lägg till förslag

Låt oss börja med det enklaste fallet av erbjudandet upp alternativ till användaren: en nedrullningsbar listruta med förslag.

1. I filen index.cshtml ändrar den **TextBoxFor** instruktionen till följande.

```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
```

Den här nyckeln är att vi har ställt in ID för sökrutan för att **azureautosuggest**.

2. Efter den här instruktionen efter avslutande  **&lt;/div&gt;** , ange det här skriptet.

```cs
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

Vi har anslutit det här skriptet till sökrutan via samma ID. Dessutom minst två tecken som krävs för att utlösa sökningen och vi kallar den **föreslå** åtgärden i kontrollanten hem med två frågeparametrar: **visar** och **fuzzy**, både inställt på false i den här instansen.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Lägg till referenser till jquery skript till vyn

Funktionen Komplettera automatiskt i skriptet ovan är inte något vi behöver skriva själva eftersom den är tillgänglig i jquery-biblioteket. 

1. Du kommer åt jquery-biblioteket genom att ändra den &lt;head&gt; i filen vyn till följande kod.

```cs
<head>
    <meta charset="utf-8">
    <title>Autocomplete demo</title>
    <link href="https://code.jquery.com/ui/1.10.4/themes/ui-lightness/jquery-ui.css"
          rel="stylesheet">
    <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
    <script src="https://code.jquery.com/ui/1.10.4/jquery-ui.js"></script>
    <link rel="stylesheet" href="~/css/hotels.css" />
</head>
```

2. Vi också behöva ta bort eller kommentera ut, en rad som refererar till jquery i filen _Layout.cshtml (i den **vyer/delade** mapp). Leta upp följande rader och kommentera ut den första raden i skriptet som visas. Den här ändringen undviker vars referenser till jquery.

```cs
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
```

Nu kan vi använda de fördefinierade automatisk komplettering jquery-funktionerna.

### <a name="add-the-suggest-action-to-the-controller"></a>Lägg till instruktionen föreslå kontrollanten

1. I kontrollanten home lägger du till den **föreslå** åtgärd (exempelvis efter den **sidan** åtgärd).

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

Den **upp** parametern anger hur många resultat för att returnera (om inget anges standardvärdet är 5). En _förslagsställare_ har angetts på Azure indexet, vilket görs när data har installerats, och inte av en klientapp som den här självstudien. I det här fallet förslagsställare kallas ”sg”, och den söker efter den **HotelName** fält - inget annat. 

Partiell matchning tillåter ”near missar” som ska ingå i utdata. Om den **visar** parametern anges till true, och sedan fetstil HTML-taggar läggs till i utdata. Vi kommer att ange dessa två parametrar till true i nästa avsnitt.

2. Du kan få några syntaxfel. I så fall lägger du till följande två **med** uttryck längst upp i filen.

```cs
using System.Collections.Generic;
using System.Linq;
```

3. Kör appen. Får du en rad alternativ när du anger ”inköpsorder”, till exempel? Prova nu ”pa”.

    ![Att skriva ”po” visar två förslag](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

Lägg märke till att bokstäverna som du anger _måste_ starta ett ord och inte bara ingå i ordet.

4. I vyn skriptet, ställer du in **& fuzzy** true och kör appen igen. Ange nu ”po”. Observera att sökningen förutsätter att du har fått en bokstav fel!
 
    ![Att skriva ”pa” med fuzzy inställd till true](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

Om du är intresserad av, den [Lucene-frågesyntax i Azure Search](https://docs.microsoft.com/azure/search/query-lucene-syntax) beskriver den logik som används i fuzzy sökningar i detalj.

## <a name="add-highlighting-to-the-suggestions"></a>Lägga till markering till förslag på

Vi kan förbättra utseendet på förslag för användaren en aning genom att ange den **visar** parametern till true. Men måste vi först lägga till kod i vyn ska visas i fetstil.

1. I vyn (index.cshtml), lägger du till följande skript efter den **azureautosuggest** skript som du angav ovan.

```cs
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

2. Ändra nu ID för textrutan så att det står på följande sätt.

```cs
@Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
```

3. Kör appen igen och du bör se den angivna texten med fetstil i förslagen. T.ex, försök att skriva ”pa”.
 
    ![Att skriva ”pa” med markering](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. Den logik som används i fokus skriptet ovan är inte felsäker. Om du anger en term som visas två gånger i samma namn, med fetstil resultatet är inte riktigt vad du vill ha. Försök att skriva ”månad”.

En av en utvecklare måste svara på frågorna är när är ett skript som arbetar ”också tillräckligt med”, och när bör dess paketberoenden kan hanteras. Vi kommer inte tar markering någon ytterligare i den här självstudien, men att söka efter en exakt algoritm är något att tänka på om tar markering ytterligare.

## <a name="add-autocompletion"></a>Lägg till kommandofönstret Slutför automatiskt

En annan variant som skiljer sig från förslag, är kommandofönstret Slutför automatiskt (kallas ibland ”frågeifyllningsförslag”). Igen, kommer vi börjar med den enklaste implementeringen, innan du flyttar till att förbättra användarupplevelsen.

1. Ange följande skript i vyn efter föregående skript.

```cs
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

2. Ändra nu ID för textrutan, så att det står på följande sätt.

```cs
@Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
```

3. I kontrollanten home vi måste du ange den **automatisk komplettering** åtgärd, exempelvis nedan den **föreslå** åtgärd.

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

Observera att vi använder samma *förslagsställare* funktion som kallas ”sg” i sökningen automatisk komplettering som vi gjorde förslag (så att vi endast försöker automatisk komplettering hotell-namn).

Det finns en mängd olika **AutocompleteMode** inställningar, och vi använder **OneTermWithContext**. Referera till [Azure automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete) en beskrivning av olika alternativ här.

4. Kör appen. Observera hur olika alternativ som visas i den nedrullningsbara listan är enstaka ord. Skriv in ord som börjar med ”sv”. Observera hur antalet alternativ minskar eftersom eller flera bokstäver har skrivits.

    ![Att skriva med grundläggande kommandofönstret Slutför automatiskt](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

Den nuvarande förslag-skriptet som du körde tidigare är förmodligen mer användbara än det här kommandofönstret Slutför automatiskt-skriptet. Om du vill göra kommandofönstret Slutför automatiskt mer användarvänlig läggs bäst till search förslag.

## <a name="combine-autocompletion-and-suggestions"></a>Kombinera kommandofönstret Slutför automatiskt och förslag

Kombinera kommandofönstret Slutför automatiskt och förslag är den mest avancerade alternativen och ger förmodligen den bästa användarupplevelsen. Vad vi vill är att visa, infogade med den text som skrivs, första valet av Azure Search för autocompleting texten. Vi också ett antal förslag som en nedrullningsbar listruta.

Det finns bibliotek som erbjuder den här funktionen - kallas ofta ”infogade kommandofönstret Slutför automatiskt” eller ett liknande namn. Men kommer vi att implementera den här funktionen, internt så kan du se vad som händer. Det dags att börja arbeta på styrenheten först i det här exemplet.

1. Vi måste lägga till en åtgärd till den domänkontrollant som returnerar bara ett resultat i kommandofönstret Slutför automatiskt, tillsammans med ett angivet antal förslag. Vi kallar den här åtgärden **AutocompleteAndSuggest**. Lägg till följande åtgärd efter din nya åtgärder i kontrollanten hem.

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

Ett alternativ i kommandofönstret Slutför automatiskt returneras överst i den **resultat** listan, följt av alla förslag.

2. I vyn först implementera vi en knep så att ett ljust grå kommandofönstret Slutför automatiskt ord återges höger under bolder text som anges av användaren. HTML omfattar relativ placering för detta ändamål. Ändra den **TextBoxFor** instruktionen (och dess omgivande &lt;div&gt; instruktioner) nedan, loggas som en andra sökruta identifieras som **under** är direkt under vår Normal sökrutan genom att hämta sökningen box 39 bildpunkter på dess ursprungliga plats!

```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
```

Obs ändrar vi ID: T igen, för att **azureautocomplete** i det här fallet.

3. Även i vyn, anger du följande skript, när alla de skript som du har angett. Det är ganska mycket.

```cs
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

Observera smarta användningen av den **intervall** att båda rensar du underliggande texten när den inte längre matchar vad användaren skriver och även att ange samma fall (övre eller nedre) som användaren skriver (som ”pa” matchar ”PA”, ”pA”, ”Pa” när söker), så att putsade texten är snygga.

Läs igenom kommentarerna i skriptet för att få en mer fullständig förståelse.

4. Slutligen måste vi göra en mindre justering till två HTML-klassen så att de blir transparent. Lägg till följande rad i den **searchBoxForm** och **searchBox** klasser i filen hotels.css.

```cs
        background: rgba(0,0,0,0);
```

5. Nu ska du köra appen. Ange ”pa” i sökrutan. Får du ”palace” som automatisk kompletteringsförslag, tillsammans med två hotell som innehåller ”pa”?

    ![Att skriva med infogad automatisk komplettering och förslag](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Tabbar att acceptera kompletteringsförslag för automatisk och försök med att markera förslag med hjälp av piltangenterna och TABB-tangenten och försök igen med hjälp av musen och ett enda klick. Kontrollera att skriptet hanterar sådana situationer snyggt.

Du kan välja att det är enklare att läsa in i ett bibliotek som erbjuder den här funktionen för dig, men nu vet du minst ett sätt att infogade kommandofönstret Slutför automatiskt att sätta igång!

## <a name="takeaways"></a>Lärdomar

Överväg följande takeaways från det här projektet:

* Kommandofönstret Slutför automatiskt (även känt som ”frågeifyllningsförslag”) och förslag kan låta användarna ange bara ett par nycklar för att hitta exakt det de vill ha.
* Kommandofönstret Slutför automatiskt och förslag som arbetar tillsammans kan ge bästa möjliga.
* Testa alltid kommandofönstret Slutför automatiskt fungerar med alla typer av indata.
* Med hjälp av den **setInterval** funktionen kan vara användbart att kontrollera och korrigera UI-element.

## <a name="next-steps"></a>Nästa steg

Du har slutfört den här serien med C# kurser, du bör ha fått värdefull kunskap om Azure Search-API: er.

Ytterligare referens och självstudier kan du bläddra [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure), eller andra självstudier i den [dokumentation om Azure Search](https://docs.microsoft.com/azure/search/).
