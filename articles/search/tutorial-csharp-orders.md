---
title: C# handledning om att beställa resultat
titleSuffix: Azure Cognitive Search
description: Den här självstudien visar hur du beställer sökresultat. Den bygger på ett tidigare hotellprojekt, som beställs efter primär egendom, sekundär egendom och innehåller en bedömningsprofil för att lägga till ökande kriterier.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 812085a5a4b3e8d1233f19c947d2fd5e433f6ab7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121558"
---
# <a name="c-tutorial-order-the-results---azure-cognitive-search"></a>C# självstudiekurs: Beställ resultaten - Azure Cognitive Search

Fram till denna punkt i vår serie av tutorials, resultaten returneras och visas i en standard ordning. Detta kan vara den ordning i vilken data finns, eller möjligen en _standardbedömningsprofil_ har definierats, som kommer att användas när inga orderparametrar anges. I den här självstudien går vi in på hur du beställer resultat baserat på en primär egenskap och sedan för resultat som har samma primära egenskap, hur du beställer det valet på en sekundär egenskap. Som ett alternativ till beställning baserat på numeriska värden visar det slutliga exemplet hur du beställer baserat på en anpassad bedömningsprofil. Vi kommer också att gå lite djupare in i visningen av _komplexa typer._

För att enkelt kunna jämföra returnerade resultat bygger det här projektet på det oändliga rullningsprojektet som skapats i [C# Tutorial: Sökresultat sidnumreringen - Azure Cognitive](tutorial-csharp-paging.md) Search-självstudien.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Orderresultat baserat på en egenskap
> * Orderresultat baserat på flera egenskaper
> * Filtrera resultat baserat på ett avstånd från en geografisk punkt
> * Beställ resultat baserat på en poängprofil

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

Ha den oändliga rullande versionen av [C# Tutorial: Sökresultat sidnumrering - Azure Cognitive Search](tutorial-csharp-paging.md) projektet igång. Det här projektet kan antingen vara din egen version eller installera den från GitHub: [Skapa den första appen](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Orderresultat baserat på en egenskap

När vi beställer resultat baserat på en fastighet, säger hotellbetyg, vill vi inte bara ha de beställda resultaten, vi vill också ha en bekräftelse på att beställningen är korrekt. Med andra ord, om vi beställer på betyg, bör vi visa betyget i vyn.

I den här guiden kommer vi också att lägga till lite mer till visningen av resultat, det billigaste rumspriset och det dyraste rumspriset för varje hotell. När vi gräver i beställning, kommer vi också att lägga till värden för att se till att det vi beställer på också visas i vyn.

Det finns ingen anledning att ändra någon av modellerna för att möjliggöra beställning. Vyn och styrenheten behöver uppdateras. Börja med att öppna hemkontrollanten.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Lägg till egenskapen OrderBy i sökparametrarna

1. Allt som krävs för att beställa resultat baserat på en enda numerisk egenskap är att ställa in parametern **OrderBy** till namnet på egenskapen. Lägg till följande rad i sökparametrarna i metoden **Index(SearchData-modell).**

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > Standardordningen är stigande, men du kan lägga till **asc** till egenskapen för att göra detta klart. Fallande ordning anges genom att lägga till **desc**.

2. Kör nu appen och ange en gemensam sökterm. Resultaten kan eller inte kan vara i rätt ordning, eftersom varken du som utvecklare, inte användaren, har något enkelt sätt att kontrollera resultaten!

3. Låt oss klargöra att resultaten är beställda på betyg. Först ersätta **box1** och **box2** klasser i hotels.css filen med följande klasser (dessa klasser är alla de nya vi behöver för den här guiden).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    >[!Tip]
    >Webbläsare cachelagrar vanligtvis CSS-filer, och detta kan leda till att en gammal css-fil används, och dina redigeringar ignoreras. Ett bra sätt runt detta är att lägga till en frågesträng med en versionsparameter till länken. Ett exempel:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Uppdatera versionsnumret om du tror att en gammal css-fil används av din webbläsare.

4. Lägg till egenskapen **Klassificering** i parametern **Select** i metoden **Index(SearchData model).**

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Öppna vyn (index.cshtml) och ersätt renderingsloopen**&lt;(!-- Visa&gt;hotelldata. --**) med följande kod.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

6. Klassificeringen måste vara tillgänglig både på den första visade sidan och på de efterföljande sidorna som anropas via den oändliga rullningen. För den senare av dessa två situationer måste vi uppdatera både **nästa** åtgärd i styrenheten och den **rullade** funktionen i vyn. Börja med styrenheten och ändra metoden **Nästa** till följande kod. Den här koden skapar och kommunicerar klassificeringstexten.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

7. Uppdatera nu den **rullade** funktionen i vyn för att visa klassificeringstexten.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 3) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                            }
                        });
                    }
                }
            </script>

    ```

8. Kör nu appen igen. Sök på en vanlig term, till exempel "wifi", och kontrollera att resultaten sorteras efter fallande ordning på hotellbetyg.

    ![Beställning baserad på betyg](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Du kommer att märka att flera hotell har en identisk rating, och så deras utseende i displayen är återigen den ordning i vilken data hittas, vilket är godtyckligt.

    Innan vi tittar på att lägga till en andra beställningsnivå, låt oss lägga till lite kod för att visa intervallet för rumspriser. Vi lägger till denna kod för att både visa extrahera data från en _komplex typ,_ och även så att vi kan diskutera beställningsresultat baserat på pris (billigaste först kanske).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Lägg till intervall av rumspriser i vyn

1. Lägg till egenskaper som innehåller det billigaste och dyraste rumspriset till den Hotel.cs modellen.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Beräkna rumspriserna i slutet av åtgärden **Index(SearchData-modell)** i hemkontrollanten. Lägg till beräkningarna efter lagring av tillfälliga data.

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
    ```

3. Lägg till egenskapen **Rum** i parametern **Select** i åtgärdsmetoden **Index(SearchData-modell)** för styrenheten.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Ändra renderingsloopen i vyn för att visa hastighetsintervallet för den första resultatsidan.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

5. Ändra **metoden Nästa** i hemkontrollanten för att kommunicera hastighetsintervallet för efterföljande resultatsidor.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

6. Uppdatera den **rullade** funktionen i vyn för att hantera rumspriser text.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 4) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

7. Kör appen och kontrollera att rumsprisintervallen visas.

    ![Visa rumsprisintervall](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

**Egenskapen OrderBy** för sökparametrarna accepterar inte en post som **Rooms.BaseRate** för att ange det billigaste rumspriset, även om rummen redan var sorterade efter pris. I det här fallet är rummen inte sorterade efter pris. För att kunna visa hotell i exempeldatauppsättningen, beställda på rumspris, måste du sortera resultaten i din hemkontrollant och skicka dessa resultat till vyn i önskad ordning.

## <a name="order-results-based-on-multiple-values"></a>Orderresultat baserat på flera värden

Frågan är nu hur man ska skilja mellan hotell med samma betyg. Ett bra sätt skulle vara att beställa på grundval av förra gången hotellet renoverades. Med andra ord, ju mer nyligen hotellet renoverades, desto högre hotellet visas i resultatet.

1. Om du vill lägga till en andra beställningsnivå ändrar du egenskaperna **OrderBy** och **Select** i metoden **Index(SearchData-modell)** så att egenskapen **LastRenovationDate** inkluderas.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Valfritt antal egenskaper kan anges i listan **OrderBy.** Om hotellen hade samma betyg och renoveringsdatum kunde en tredje fastighet anges för att skilja mellan dem.

2. Återigen måste vi se renoveringsdatum i sikte, bara för att vara säker på att beställningen är korrekt. För en sådan sak som en renovering, förmodligen bara året krävs. Ändra renderingsloopen i vyn till följande kod.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

3. Ändra **metoden Nästa** i hemkontrollanten för att vidarebefordra årskomponenten för det senaste renoveringsdatumet.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Ändra den **rullade** funktionen i vyn för att visa renoveringstexten.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 5) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

5. Kör appen. Sök på en vanlig term, till exempel "pool" eller "visa", och kontrollera att hotell med samma betyg nu visas i fallande ordning efter renoveringsdatum.

    ![Beställning på renoveringsdatum](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Filtrera resultat baserat på ett avstånd från en geografisk punkt

Betyg och renoveringsdatum är exempel på egenskaper som bäst visas i en fallande ordning. En alfabetisk lista skulle vara ett exempel på en bra användning av stigande ordning (till exempel om det bara fanns en **OrderBy** egendom, och det var inställt på **HotelName** då en alfabetisk ordning skulle visas). För våra urvalsdata skulle dock avståndet från en geografisk punkt vara lämpligare.

För att visa resultat baserat på geografiskt avstånd krävs flera steg.

1. Filtrera bort alla hotell som ligger utanför en angiven radie från den angivna punkten genom att ange ett filter med parametrar för longitud, latitud och radie. Longitud ges först till POINT-funktionen. Radien är på kilometer.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. Ovanstående filter beställer _inte_ resultaten baserat på avstånd, det tar bara bort extremvärden. Om du vill beställa resultaten anger du en **OrderBy-inställning** som anger geoDistance-metoden.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Även om resultaten returnerades av Azure Cognitive Search med hjälp av ett avståndsfilter returneras _inte_ det beräknade avståndet mellan data och den angivna punkten. Beräkna om det här värdet i vyn eller styrenheten om du vill visa det i resultatet.

    Följande kod beräknar avståndet mellan två lat/lon-punkter.

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

4. Nu måste du knyta ihop dessa begrepp. Men dessa kodavsnitt är så långt som vår handledning går, bygga en karta-baserad app är kvar som en övning för läsaren. Om du vill ta det här exemplet längre bör du överväga att antingen ange ett stadsnamn med radie eller hitta en punkt på en karta och välja en radie. Mer information om hur du undersöker dessa alternativ ytterligare finns i följande resurser:

* [Azure Maps-dokumentation](https://docs.microsoft.com/azure/azure-maps/)
* [Hitta en adress med söktjänsten Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Beställ resultat baserat på en poängprofil

Exemplen i handledningen hittills visar hur man beställer på numeriska värden (betyg, renoveringsdatum, geografiskt avstånd), vilket ger en _exakt_ ordningsprocess. Vissa sökningar och vissa data lämpar sig dock inte för en så enkel jämförelse mellan två dataelement. Azure Cognitive Search innehåller begreppet _bedömning_. _Poängprofiler_ kan anges för en uppsättning data som kan användas för att ge mer komplexa och kvalitativa jämförelser, som bör vara mest värdefulla när, säg, jämföra textbaserade data för att avgöra vilka som ska visas först.

Bedömningsprofiler definieras inte av användare, men vanligtvis av administratörer av en datauppsättning. Flera poängprofiler har satts upp på hotelldata. Låt oss titta på hur en poängprofil definieras och sedan försöka skriva kod för att söka på dem.

### <a name="how-scoring-profiles-are-defined"></a>Så här definieras bedömningsprofiler

Låt oss titta på tre exempel på bedömningsprofiler och fundera över hur var och _en ska_ påverka resultatordningen. Som apputvecklare skriver du inte dessa profiler, de skrivs av dataadministratören, men det är bra att titta på syntaxen.

1. Det här är standardbedömningsprofilen för hotelldatauppsättningen, som används när du inte anger någon **Parameter OrderBy** eller **ScoringProfile.** Den här profilen ökar _poängen_ för ett hotell om söktexten finns i hotellets namn, beskrivning eller lista med taggar (bekvämligheter). Lägg märke till hur vikterna för poängsättningen gynnar vissa fält. Om söktexten visas i ett annat fält, som inte visas nedan, har den en vikt på 1. Självklart, ju högre poäng, desto tidigare ett resultat visas i vyn.

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

2. Följande poängsättningsprofil ökar poängen avsevärt, om en medföljande parameter innehåller en eller flera av listan med taggar (som vi kallar "bekvämligheter"). Den viktigaste punkten i den här profilen är att en parameter _måste_ anges, som innehåller text. Om parametern är tom eller inte har angetts kommer ett fel att genereras.
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

3. I det här tredje exemplet ger betyget en betydande ökning av poängen. Det senaste renoverade datumet kommer också att öka poängen, men bara om dessa uppgifter faller inom 730 dagar (2 år) från det aktuella datumet.

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    Nu ska vi se om dessa profiler fungerar som vi tycker att de borde!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Lägga till kod i vyn för att jämföra profiler

1. Öppna filen index.cshtml och &lt;ersätt&gt; brödtextavsnittet med följande kod.

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
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

2. Öppna filen SearchData.cs och ersätt klassen **SearchData** med följande kod.

    ```cs
    public class SearchData
    {
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

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

3. Öppna filen hotels.css och lägg till följande HTML-klasser.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Lägga till kod i styrenheten för att ange en bedömningsprofil

1. Öppna hemkontrollfilen. Lägg till följande **med hjälp av** utdrag (för att underlätta för att skapa listor).

    ```cs
    using System.Linq;
    ```

2.  I det här exemplet behöver vi det första anropet till **Index** för att göra lite mer än att bara returnera den ursprungliga vyn. Metoden söker nu efter upp till 20 bekvämligheter att visa i vyn.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

3. Vi behöver två privata metoder för att spara fasorna till tillfällig lagring, och för att återställa dem från tillfällig lagring och fylla en modell.

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

4. Vi måste ställa in parametrarna **OrderBy** och **ScoringProfile** efter behov. Ersätt den befintliga **metoden Index(SearchData-modell)** med följande.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Recover the facet text, and the facet check box settings.
                    RecoverFacets(model, true);

                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First search with text. 
                    // Recover the facet text, but ignore the check box settings, and use the current model settings.
                    RecoverFacets(model,false);

                    // First call. Check for valid text input, and valid scoring profile.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    if (model.scoring == null)
                    {
                        model.scoring = "Default";
                    }
                    page = 0;
                }

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
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
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Läs igenom kommentarerna för **switch** var och en av switchvalen.

5. Vi behöver inte göra några ändringar i **åtgärden Nästa,** om du har slutfört den extra koden för föregående avsnitt om beställning baserat på flera egenskaper.

### <a name="run-and-test-the-app"></a>Kör och testa appen

1. Kör appen. Du bör se en fullständig uppsättning bekvämligheter i utsikten.

2. För beställning, välja "Genom numerisk rating" ger dig den numeriska beställning du redan har genomfört i den här guiden, med renovering datum beslutar bland hotell med samma betyg.

![Beställa "strand" baserat på betyg](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Nu prova "Av bekvämligheter" profil. Gör olika val av bekvämligheter, och kontrollera att hotell med dessa bekvämligheter främjas upp resultatlistan.

![Beställa "strand" baserat på profil](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Prova "By Renovated date/Rating profile" för att se om du får vad du förväntar dig. Endast nyrenoverade hotell bör få en _friskhet_ uppsving.

### <a name="resources"></a>Resurser

Mer information finns i följande [Lägg till bedömningsprofiler i ett Azure Cognitive Search-index](https://docs.microsoft.com/azure/search/index-add-scoring-profiles).

## <a name="takeaways"></a>Lärdomar

Tänk på följande takeaways från det här projektet:

* Användarna förväntar sig att sökresultaten ska beställas, mest relevanta först.
* Data behöver struktureras så att det är enkelt att beställa. Vi kunde inte sortera på "billigaste" först lätt, eftersom uppgifterna inte är strukturerade för att möjliggöra beställning göras utan extra kod.
* Det kan finnas många nivåer att beställa, att skilja mellan resultat som har samma värde på en högre nivå av ordning.
* Det är naturligt för vissa resultat som skall beställas i stigande ordning (säg, avstånd från en punkt), och några i fallande ordning (säg, gästens betyg).
* Poängsättningsprofiler kan definieras när numeriska jämförelser inte är tillgängliga, eller inte tillräckligt smarta, för en datauppsättning. Scoring varje resultat kommer att bidra till att beställa och visa resultaten intelligent.

## <a name="next-steps"></a>Nästa steg

Du har slutfört den här serien C#-självstudier – du borde ha fått värdefull kunskap om Azure Cognitive Search API:er.

Om du vill ha ytterligare referenser och självstudier kan du läsa om [microsoft learn](https://docs.microsoft.com/learn/browse/?products=azure)eller andra självstudier i Azure Cognitive [Search Documentation](https://docs.microsoft.com/azure/search/).
