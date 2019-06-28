---
title: C#självstudie om att ordna resultaten – Azure Search
description: Den här självstudien bygger på ”sökresultat sidbrytning – Azure Search”-projektet, att lägga till sorteringen i sökresultaten. Lär dig att ordna resultaten på en primär egenskap och resultat som har samma primära egenskap, beställa resultat på en sekundär egenskap. Slutligen lär dig att ordna resultat baserat på en bedömningsprofil.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/21/2019
ms.openlocfilehash: 32e253b4e131d753ab6937d0aa2a49bda471e091
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466580"
---
# <a name="c-tutorial-order-the-results---azure-search"></a>C#självstudie: Sortera – Azure Search

Fram till den här punkten i vår serie självstudier resultat returneras och visas i en standardordning. Detta kan vara ordningen där data finns, eller möjligen en standard _bedömningsprofil_ har definierats som ska användas när inga skrivordning parametrar anges. I den här självstudien ska vi gå in beställa resultat baserat på en primär egenskap och sedan resultatet som har samma primära egenskap, beställa det valet på en sekundär egenskap. Som ett alternativ till ordning baserat på numeriska värden, det sista exemplet visar hur du ordning baserat på en anpassad bedömningsprofil. Vi kommer också gå lite djupare in visningen av _komplexa typer_.

För att kunna jämföra returnerade resultat enkelt, skapar det här projektet till oändlig rullande projektet har skapats i den [ C# självstudien: Sökresultat sidbrytning – Azure Search](tutorial-csharp-paging.md) självstudien.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Ordning resultat baserat på en egenskap
> * Ordning resultat baserat på flera egenskaper
> * Filtrera resultatet baserat på ett avstånd från en geografisk plats
> * Ordning resultat baserat på en bedömningsprofil

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

Har oändlig rullande version av den [ C# självstudien: Sökresultat sidbrytning – Azure Search](tutorial-csharp-paging.md) projekt och drift. Det här projektet kan vara din egen version eller installera det från GitHub: [Skapa första app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Ordning resultat baserat på en egenskap

När vi beställer resultat baserat på en egenskap, exempelvis hotell betyg, vi vill inte bara sorterade resultat, vi också vill vara säker på att ordningen är korrekt. Med andra ord, om vi beställer på klassificeringen ska vi visa omdömet i vyn.

I den här självstudien, vi kommer också lägga till lite mer visning av resultaten och den billigaste rummet hastigheten dyraste rummet avgiften, för varje hotell. Eftersom vi fördjupar dig i ordning, vi kommer även att lägga till värden för att kontrollera vad vi beställer på visas också i vyn.

Det finns inget behov att ändra modeller för att aktivera ordningsföljd. Vyn och domänkontrollant behöver uppdateras. Starta genom att öppna kontrollanten hem.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Lägga till egenskapen OrderBy i sökparametrarna

1. Allt det tar att ordning resultat baserat på en enskild numeriska egenskap är att ställa in den **OrderBy** parameter med namnet på egenskapen. I den **Index (SearchData modellen)** metod, lägga till följande rad i sökparametrarna.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > Ordning är stigande, men du kan lägga till **asc** i egenskapen att göra detta klart. Fallande ordning anges genom att lägga till **desc**.

2. Nu köra appen och ange några vanliga sökterm. Resultatet kanske eller kanske inte i rätt ordning, eftersom du som utvecklare, inte användaren heller något enkelt sätt med att verifiera resultaten!

3. Låt oss göra det tydligt resultaten ordnas på klassificeringen. Ersätt först, den **Ruta1** och **Ruta2** klasser i filen hotels.css med följande klasser (de här klasserna är alla nya som vi behöver för den här kursen).

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
    >Webbläsare cachelagra vanligtvis css-filer och detta kan leda till en gammal css-fil som används och redigeringarna ignoreras. Ett bra sätt avrunda det här är att lägga till en frågesträng med en versionsparameter för länken. Exempel:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Uppdatera versionsnumret om du anser att en gammal css-fil som används av din webbläsare.

4. Lägg till den **omdöme** egenskap enligt den **Välj** parametern i den **Index (SearchData modellen)** metoden.

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Öppna vyn (index.cshtml) och Ersätt återgivning-loop ( **&lt;!--visar den hotell.--&gt;** ) med följande kod.

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

6. Omdömet måste vara tillgänglig både i den första sidan och i de efterföljande sidorna som anropas via oändlig Bläddra. I det senare av dessa två situationer som vi behöver uppdatera både den **nästa** åtgärd på Kontrollpanelen och **rullas** funktion i vyn. Från och med registeransvarige, ändra den **nästa** metod till följande kod. Den här koden skapar och kommunicerar klassificering texten.

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

7. Uppdatera nu den **rullas** funktion i vyn för att visa texten klassificering.

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

8. Kör appen igen. Sök på några vanliga period, till exempel ”wifi”, och kontrollera att resultaten ordnas efter fallande ordning efter hotell klassificering.

    ![Ordning baserat på klassificeringen](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Du ser att flera hotels har en identisk klassificering och så att de ser ut i visningen är igen den ordning i vilken data som hittas, vilket är valfri.

    Lägg till kod för att visa intervall med rummet värden innan vi ser till att lägga till en andra nivå av sortering. Vi lägger till den här koden till båda show extrahera data från en _komplex typ_, och även så att vi kan diskutera ordning resultat baserat på priset (billigaste första kanske).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Lägg till intervall med rummet värden i vyn

1. Lägg till egenskaper som innehåller den billigaste och dyraste rummet hastigheten i Hotel.cs-modellen.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Beräkna rummet betalning i slutet av den **Index (SearchData modellen)** åtgärden i kontrollanten hem. Lägga till beräkningar när lagring av tillfälliga data.

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

3. Lägg till den **Rooms** egenskap till den **Välj** parametern i den **Index (SearchData modellen)** åtgärdsmetod för kontrollenheten.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Ändra rendering loop i vy för att visa rate-intervallet för den första sidan i resultaten.

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

5. Ändra den **nästa** -metod i kontrollanten home kommunicera rate-intervallet för kommande sidor i resultaten.

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

6. Uppdatera den **rullas** funktion i vyn för att hantera rummet bedömer text.

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

7. Kör appen och verifiera rummet rate intervall visas.

    ![Visa rummet rate-intervall](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

Den **OrderBy** egenskapen för sökparametrarna accepterar en post som **Rooms.BaseRate** att tillhandahålla den billigaste rummet hastigheten, även om lokalerna som sorterats på pris (som de inte är). För att kunna visa hotell i provdatauppsättning, Beställt rummet pris, skulle du behöva sortera resultaten i din startsida styrenhet och skicka de här resultaten returneras till vyn i önskad ordning.

## <a name="order-results-based-on-multiple-values"></a>Ordning resultat baserat på flera värden

Fråga nu är att skilja mellan hotels med samma klassificering. Ett bra sätt är att ordningen på grundval av hotellet har renoverade senast. Med andra ord, desto mer nyligen hotellet har renoverade, desto högre hotellet visas i resultatet.

1. Om du vill lägga till en andra nivå av sortering, ändra den **OrderBy** och **Välj** egenskaper i den **Index (SearchData modellen)** metoden och ta den  **LastRenovationDate** egenskapen.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Valfritt antal egenskaper kan anges i den **OrderBy** lista. Om hotels hade samma klassificering och renovering datum, kan en tredje egenskap anges för att skilja mellan dem.

2. Vi behöver igen och se renovering datumet i vyn för säkerhets skull sorteringen är korrekt. För sådana en sak som en renovering krävs antagligen bara året. Ändra rendering loop i vyn till följande kod.

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

3. Ändra den **nästa** -metod i kontrollanten home att vidarebefordra årskomponenten för det sista datumet i renovering.

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

4. Ändra den **rullas** funktion i vyn för att visa renovering texten.

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

5. Kör appen. Sök på en vanlig term, till exempel ”pool” eller ”view”, och kontrollera hotels med samma klassificering visas nu i fallande ordning efter renovering datum.

    ![Sortering av renovering datum](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Filtrera resultatet baserat på ett avstånd från en geografisk plats

Klassificering och renovering datum är exempel på egenskaper som visas bäst i fallande ordning. En alfabetisk lista är ett exempel på med stigande ordning (till exempel om det har bara en **OrderBy** egenskap och det var inställd **HotelName** och sedan visas en alfabetisk ordning ). Men för våra exempeldata skulle avståndet från en geografisk plats vara lämpligare.

Om du vill visa resultat baserat på geografisk avståndet krävs flera steg.

1. Filtrera ut alla hotell som är utanför en angiven radie från given plats genom att ange ett filter med longitud, latitud och RADIUS-parametrar. Longitud anges först för punkt-funktionen. RADIUS är i kilometer.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. Ovanstående filter har _inte_ order resultatet baserat på avstånd, det tar bara bort avvikare. Om du vill sortera resultaten, ange ett **OrderBy** inställning som anger metoden som geoDistance.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Även om resultaten returnerades av Azure Search med hjälp av ett filter för avståndet, är beräknade avståndet mellan data och den angivna punkten _inte_ returneras. Beräknar om det här värdet i vyn eller styrenhet, om du vill visa i resultatet.

    Följande kod beräknar avståndet mellan två lat/celligt punkter.

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

4. Nu har du att knyta samman dessa begrepp. Dessa fragment är dock som våra självstudier går, att skapa en kartabaserad app lämnas som en övning för läsaren. Överväg att ange namnet på en stad med en radius eller försök att hitta en plats på en karta och att välja en radius för att få det här exemplet ytterligare är. Om du vill undersöka dessa ytterligare alternativ finns i följande resurser:

* [Azure Maps-dokumentation](https://docs.microsoft.com/azure/azure-maps/)
* [Hitta en adress med hjälp av Azure Maps search-tjänst](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Ordning resultat baserat på en bedömningsprofil

Om exemplen i självstudien hittills visar hur du beställer på numeriska värden (betyg, renovering datum, geografiska avstånd), vilket ger en _exakta_ bearbeta beställningen. Men lämpar vissa sökningar och vissa data inte sig för en enkel jämförelse mellan två dataelement. Azure Search innehåller konceptet _bedömning_. _Poängprofiler_ kan anges för en uppsättning data som kan användas för att ge mer komplexa och kvalitativ jämförelser, som ska vara mest värdefulla när, exempelvis jämföra textbaserade data och Bestäm vilket ska vara visas först.

Bedömningsprofiler har inte definierats av användare, men normalt administratörer av en uppsättning data. Flera bedömningsprofiler har ställts in på hotels-data. Nu ska vi titta på hur en bedömningsprofil definieras och försök skriva kod för att söka i dem.

### <a name="how-scoring-profiles-are-defined"></a>Hur bedömning profiler definieras

Nu ska vi titta på tre exempel på bedömningsprofiler och Överväg hur varje _bör_ påverkar resultaten. Som apputvecklare, att skriva inte de här profilerna, de skrivs av dataadministratören, men det kan vara bra att titta på syntaxen.

1. Detta är standardinställningen bedömningsprofil för datauppsättningen hotels används när du inte anger någon **OrderBy** eller **ScoringProfile** parametern. Den här profilen ökar den _poäng_ för ett hotell om söktexten finns i hotellnamn, beskrivning eller lista över taggar (bekvämligheterna). Observera hur vikterna av poängsättningen prioriterar vissa fält. Om söktexten visas i ett annat fält inte visas i listan nedan har en vikt på 1. Naturligtvis är ju högre poäng, den tidigare ett resultat visas i vyn.

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

2. Följande bedömningsprofilen ökar poängen mycket, om den angivna parametern innehåller ett eller flera av en lista över taggar (som vi anropar ”bekvämligheterna”). En viktig aspekt av den här profilen är som en parameter _måste_ anges, som innehåller text. Om parametern är tom eller har inte angetts, genereras ett fel.
 
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

3. I detta tredje exempel ger klassificeringen en betydande boost till poängen. Datum för senaste renoverade förbättras också poäng, men endast om dessa data inte är inom 730 dagar (2 år) för det aktuella datumet.

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

    Nu kan vi se om de här profilerna fungerar som vi tror att de ska!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Lägg till kod i vyn för att jämföra profiler

1. Öppna filen index.cshtml och Ersätt den &lt;brödtext&gt; avsnittet med följande kod.

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

2. Öppna filen SearchData.cs och Ersätt den **SearchData** klassen med följande kod.

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

3. Öppna filen hotels.css och Lägg till följande HTML-klasser.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Lägg till kod i kontrollenheten för att ange en bedömningsprofil

1. Öppna filen home controller. Lägg till följande **med** instruktionen (som hjälper till med att skapa listor).

    ```cs
    using System.Linq;
    ```

2.  I det här exemplet behöver vi första anropet till **Index** till lite mer än att bara returnera den ursprungliga vyn. Metoden söker nu upp till 20 bekvämligheterna ska visas i vyn.

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

3. Vi behöver två privata metoder för att spara fasetterna till ett tillfälligt lagringsutrymme och för att återställa dem från temporär lagring och fylla i en modell.

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

4. Vi måste ange den **OrderBy** och **ScoringProfile** parametrarna efter behov. Ersätt de befintliga **Index (SearchData modellen)** metoden med följande.

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

    Läs igenom kommentarerna för var och en av de **växla** val.

5. Vi behöver inte göra några ändringar i den **nästa** åtgärd, om du har slutfört ytterligare koden för föregående avsnitt i ordning baserat på flera egenskaper.

### <a name="run-and-test-the-app"></a>Kör och testa appen

1. Kör appen. Du bör se en fullständig uppsättning bekvämligheterna i vyn.

2. För ordning ger att välja ”av numeriska klassificering” dig den numeriska ordning som du redan har implementerat i den här självstudien med renovering datum välja bland hotell i samma klassificering.

![Sorteringen ”stranden” baserat på klassificeringen](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Prova nu ”av bekvämligheterna”-profil. Göra olika val av bekvämligheterna och kontrollera att hotels med dessa bekvämligheterna lyfts upp listan med resultat.

![Sorteringen ”stranden” bygger på profilen](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Prova den ”av Renovated datum/klassificering profil” att se om du får du förväntat dig. Endast nyligen renoverade hotels bör få en _färskhet_ boost.

### <a name="resources"></a>Resurser

Mer information finns i följande [lägga till bedömningsprofiler i en Azure Search-index](https://docs.microsoft.com/azure/search/index-add-scoring-profiles).

## <a name="takeaways"></a>Lärdomar

Överväg följande takeaways från det här projektet:

* Användarna förväntar sökresultaten till att sortera, mest relevanta först.
* Data som behöver strukturerad så att sorteringen är enkelt. Det gick inte att sortera efter ”billigaste” först enkelt, eftersom data inte rätt struktur för att aktivera ordningsföljd som ska utföras utan ytterligare kod.
* Det kan finnas många nivåer ordning, att skilja mellan resultat som har samma värde på en högre nivå av ordning.
* Det känns naturligt innan vissa resultaten sorteras i stigande ordning (exempelvis avstånd från en tidpunkt) och vissa i fallande ordning (exempelvis Gäst omdöme).
* Poängprofiler kan definieras när numeriska jämförelser är inte tillgängliga eller inte för smartkort, för en datauppsättning. Bedömning av varje resultat att ordning och visa resultatet smart.

## <a name="next-steps"></a>Nästa steg

Du har slutfört den här serien med C# kurser, du bör ha fått värdefull kunskap om Azure Search-API: er.

Ytterligare referens och självstudier kan du bläddra [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure), eller andra självstudier i den [dokumentation om Azure Search](https://docs.microsoft.com/azure/search/).
