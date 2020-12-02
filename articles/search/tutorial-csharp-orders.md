---
title: C#-självstudie om att beställa resultat
titleSuffix: Azure Cognitive Search
description: Den här C#-självstudien visar hur du beställer Sök resultat. Det bygger på ett tidigare hotell projekt, sorterar efter primär egenskap, sekundär egenskap och innehåller en bedömnings profil för att lägga till förstärknings kriterier.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/02/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 5a55a330f6f4fefb86f2c056cd0ca3b2ba5f4b29
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499602"
---
# <a name="tutorial-order-search-results-using-the-net-sdk"></a>Självstudie: Beställ Sök resultat med hjälp av .NET SDK

I den här själv studie serien har resultaten returnerats och visas i en [standard ordning](index-add-scoring-profiles.md#what-is-default-scoring). I den här självstudien kommer du att lägga till primära och sekundära sorterings kriterier. Som ett alternativ till att sortera baserat på numeriska värden visar det sista exemplet hur du rangordnar resultat baserat på en anpassad bedömnings profil. Vi kommer också att gå djupare in i visningen av _komplexa typer_.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Ordna resultat baserat på en egenskap
> * Ordna resultat baserat på flera egenskaper
> * Filtrera resultat baserat på ett avstånd från en geografisk punkt
> * Beställ resultat baserat på en bedömnings profil

## <a name="overview"></a>Översikt

Den här självstudien utökar det oändligt rullande projekt som skapas i självstudien [Lägg till växling i Sök Resultat](tutorial-csharp-paging.md) .

En färdig version av koden i den här självstudien finns i följande projekt:

* [5-order-resultat (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/5-order-results)

## <a name="prerequisites"></a>Förutsättningar

* [2b – Lägg till oändlig rullning (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll) lösning. Projektet kan antingen vara din egen version som skapats från den föregående själv studie kursen eller en kopia från GitHub.

Den här självstudien har uppdaterats med [Azure.Search.Documents-paketet (version 11)](https://www.nuget.org/packages/Azure.Search.Documents/) . En tidigare version av .NET SDK finns i [kod exemplet Microsoft. Azure. search (version 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="order-results-based-on-one-property"></a>Ordna resultat baserat på en egenskap

När du beställer resultat baserat på en egenskap, t. ex. hotell betyg, vill vi inte bara ha det beställda resultatet, vi vill även bekräfta att beställningen är korrekt. Genom att lägga till fältet klassificering i resultatet kan vi bekräfta att resultaten sorteras korrekt.

I den här övningen kommer vi också att lägga till lite mer för att visa resultat: billigaste rums hastighet och den mest dyra rums taxan för varje hotell.

Du behöver inte ändra någon av modellerna för att möjliggöra sortering. Endast vyn och kontroll enheten kräver uppdateringar. Börja med att öppna Start styrenheten.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Lägga till egenskapen OrderBy i Sök parametrarna

1. Lägg till alternativet **OrderBy** i namnet på egenskapen. I metoden **index (SearchData Model)** lägger du till följande rad i Sök parametrarna.

    ```cs
    OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > Standard ordningen är stigande, men du kan lägga till **ASC** i egenskapen för att göra detta tydligt. Fallande ordning anges genom att lägga till **DESC**.

1. Kör nu appen och ange en vanlig sökterm. Resultatet kanske inte är i rätt ordning, eftersom inget som utvecklare, inte användaren, har något enkelt sätt att verifiera resultaten.

1. Nu ska vi ta bort resultaten för klassificering. Ersätt först **box1** -och **box2** -klasserna i filen Hotels. CSS med följande klasser (dessa klasser är de nya som vi behöver för den här kursen).

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

    > [!Tip]
    > Webbläsare cachelagrar vanligt vis CSS-filer och det kan leda till att en gammal CSS-fil används och redigeringarna ignoreras. Ett bra sätt att avrunda detta är att lägga till en frågesträng med en versions parameter till länken. Exempel:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    > Uppdatera versions numret om du tror att en gammal CSS-fil används av webbläsaren.

1. Lägg till egenskapen **klassificering** i **Select** -parametern i metoden **index (SearchData Model)** .

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

1. Öppna vyn (index. cshtml) och ersätt åter givnings slingan (**&lt; !--Visa hotell data.-- &gt;**) med följande kod.

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

1. Omdömet måste vara tillgängligt både på den första sidan som visas och på efterföljande sidor som anropas via den oändliga rullningen. För en senare av dessa två situationer måste vi uppdatera både **Nästa** åtgärd i kontrollanten och den **rullnings bara** funktionen i vyn. Börja med kontrollanten och ändra **Nästa** Metod till följande kod. Den här koden skapar och förmedlar klassificerings texten.

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

1. Nu ska du uppdatera den **rullnings bara** funktionen i vyn för att Visa betygs texten.

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

1. Kör nu appen igen. Sök på valfri gemensam term, till exempel "WiFi", och kontrol lera att resultaten sorteras efter fallande ordning på hotell betyget.

    ![Ordning baserat på klassificering](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Du ser att flera hotell har samma betyg, och att deras utseende visas i den ordning som data hittas, vilket är godtyckligt.

    Innan vi tittar på att lägga till en andra nivå av beställning ska vi lägga till en kod för att Visa intervallet med rums frekvens. Vi lägger till den här koden för att både Visa extrahering av data från en _komplex typ_ och så att vi kan diskutera beställnings resultat baserat på pris (billigaste First kanske).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Lägg till intervallet för rums taxa i vyn

1. Lägg till egenskaper som innehåller billigaste och mest kostsame rums taxa till Hotel.cs-modellen.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

1. Beräkna Room-priserna i slutet av index-åtgärden **(SearchData Model)** i Start styrenheten. Lägg till beräkningarna efter lagring av temporära data.

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

1. Lägg till egenskapen **rumss** i **Select** -parametern i metoden **index (SearchData Model)** för kontrollanten.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

1. Ändra åter givnings slingan i vyn om du vill visa hastighets intervallet för den första resultat sidan.

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

1. Ändra **Nästa** Metod i Start styrenheten för att kommunicera med hastighets intervallet, för efterföljande resultat sidor.

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

1. Uppdatera den **rullnings bara** funktionen i vyn för att hantera rums frekvenss texten.

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

1. Kör appen och kontrol lera att rums intervallet visas.

    ![Visar intervall för rums intervall](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

Egenskapen **OrderBy** för Sök parametrarna godtar inte någon post, till exempel **rummen. BaseRate** för att tillhandahålla billigaste rums frekvens, även om rummen redan har sorterats efter pris. I det här fallet sorteras inte rummen efter pris. För att kunna visa hotell i exempel data uppsättningen, sorterat efter rums frekvens, skulle du behöva sortera resultaten i din start kontroll och skicka dessa resultat till vyn i önskad ordning.

## <a name="order-results-based-on-multiple-values"></a>Ordna resultat baserat på flera värden

Frågan är nu att skilja mellan hotell med samma betyg. Ett bra sätt är att beställa på grund val av den senaste gången hotellet var renovated. Med andra ord var det senaste hotellet-renovated, desto högre blir hotellet i resultatet.

1. Om du vill lägga till en andra sorterings nivå ändrar du **OrderBy** -och **Select** -egenskaperna i **index-metoden (SearchData Model)** för att ta med egenskapen **LastRenovationDate** .

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Valfritt antal egenskaper kan anges i listan **OrderBy** . Om Hotels hade samma betygs-och renoverings datum, kan en tredje egenskap anges för att skilja dem åt.

1. Återigen måste vi se renoverings datumet i vyn, bara för att vara säker på att ordningen är korrekt. För sådant som en renovering är det vanligt vis bara året som krävs. Ändra åter givnings slingan i vyn till följande kod.

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

1. Ändra **Nästa** Metod i hem styrenheten för att vidarebefordra års komponenten för senaste renoverings datum.

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

1. Ändra den **rullnings bara** funktionen i vyn för att Visa renoverings texten.

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

1. Kör appen. Sök på en gemensam term, till exempel "pool" eller "View" och kontrol lera att hotell med samma betyg nu visas i fallande ordning efter renoverings datum.

    ![Beställning på renoverings datum](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Filtrera resultat baserat på ett avstånd från en geografisk punkt

Klassificering och renoverings datum är exempel på egenskaper som bäst visas i en fallande ordning. En alfabetisk lista är ett exempel på en bra användning av stigande ordning (till exempel om det bara fanns en **OrderBy** -egenskap, och den var inställd på **HotelName** visas en alfabetisk ordning). Men för våra exempel data skulle avstånd från en geografisk punkt vara mer lämpligt.

För att visa resultat baserat på geografisk avstånd krävs flera steg.

1. Filtrera ut alla hotell som ligger utanför en angiven radie från den givna punkten genom att ange ett filter med longitud-, latitud-och RADIUS-parametrar. Longitud anges först till punkt-funktionen. Radien är i kilo meter.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

1. Filtret ovan ordnar _inte_ resultatet utifrån avståndet, utan tar bort avvikande värden. Ordna resultaten genom att ange en **OrderBy** -inställning som anger den omnära metoden.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

1. Även om resultatet returnerades av Azure Kognitiv sökning med ett avstånds filter, returneras _inte_ det beräknade avståndet mellan data och den angivna punkten. Beräkna om värdet i vyn eller kontroll enheten om du vill visa det i resultatet.

    Följande kod beräknar avståndet mellan två Lat/Lon-punkter.

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

1. Nu måste du knyta dessa begrepp till varandra. De här kodfragmenten är i mån av den här själv studie kursen, och du får hjälp att skapa en mappad app som en övning för läsaren. Om du vill göra det här exemplet ytterligare, kan du antingen ange ett Orts namn med en radie eller hitta en punkt på en karta och välja en radie. Information om hur du undersöker dessa alternativ finns i följande resurser:

* [Azure Maps-dokumentation](../azure-maps/index.yml)
* [Hitta en adress med hjälp av Azure Maps Search-tjänsten](../azure-maps/how-to-search-for-address.md)

## <a name="order-results-based-on-a-scoring-profile"></a>Beställ resultat baserat på en bedömnings profil

I exemplen i självstudien visar vi hur man beställer numeriska värden (klassificering, renoverings datum, geografiskt avstånd), vilket ger en _exakt_ beställning av ordning. Vissa sökningar och vissa data lånar dock inte ut till en enkel jämförelse mellan två data element. I Azure Kognitiv sökning ingår begreppet _resultat_. _Bedömnings profiler_ kan anges för en uppsättning data som kan användas för att tillhandahålla mer komplexa och kvalitativa jämförelser, som bör vara mest värdefulla när du antar textbaserade data för att bestämma vilka som ska visas först.

Bedömnings profiler definieras inte av användare, men vanligt vis av administratörer för en data uppsättning. Flera bedömnings profiler har kon figurer ATS på hotell data. Nu ska vi titta på hur en bedömnings profil definieras och sedan försöka skriva kod för att söka efter dem.

### <a name="how-scoring-profiles-are-defined"></a>Så här definieras bedömnings profiler

Nu ska vi titta på tre exempel på bedömnings profiler och fundera över hur var och en _ska_ påverka resultat ordningen. Som app-utvecklare skriver du inte dessa profiler, de skrivs av data administratören, men det är bra att titta på syntaxen.

1. Detta är standard bedömnings profilen för hotell data uppsättningen, som används när du inte anger någon **OrderBy** -eller **ScoringProfile** -parameter. Den här profilen ökar _poängen_ för ett hotell om Sök texten finns i hotell namn, beskrivning eller lista med taggar (bekvämligheterna). Observera hur vikterna i poängen prioriterar vissa fält. Om Sök texten visas i ett annat fält, inte i listan nedan, kommer den att ha en vikt på 1. Självklart desto högre poäng visas det tidigare resultatet i vyn.

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

1. Följande bedömnings profil ökar poängen markant, om en angiven parameter innehåller en eller flera av taggarna (som vi anropar "bekvämligheterna"). Nyckel punkten för den här profilen är att en parameter _måste_ anges, vilket innehåller text. Om parametern är tom eller inte anges kommer ett fel att genereras.
 
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

1. I det här tredje exemplet ger klassificeringen en betydande ökning av poängen. Det senaste renovated-datumet ökar också poängen, men endast om dessa data hamnar inom 730 dagar (2 år) av det aktuella datumet.

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

    Nu ska vi se om profilerna fungerar som vi tror att de bör!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Lägg till kod i vyn för att jämföra profiler

1. Öppna filen index. cshtml och Ersätt &lt; Body- &gt; avsnittet med följande kod.

    ```html
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

1. Öppna filen SearchData.cs och Ersätt **SearchData** -klassen med följande kod.

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

1. Öppna filen Hotels. CSS och Lägg till följande HTML-klasser.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Lägg till kod i kontrollanten för att ange en bedömnings profil

1. Öppna filen med hem styrenheten. Lägg till följande **using** -instruktion (för att hjälpa till med att skapa listor).

    ```cs
    using System.Linq;
    ```

1. I det här exemplet behöver vi det första anropet till **index** för att göra lite mer än att bara returnera den inledande vyn. Metoden söker nu efter upp till 20 bekvämligheterna som ska visas i vyn.

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

1. Vi behöver två privata metoder för att spara ansikte till tillfällig lagring och för att återställa dem från tillfällig lagring och fylla en modell.

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

1. Vi måste ange **OrderBy** -och **ScoringProfile** -parametrarna vid behov. Ersätt den befintliga **index-metoden (SearchData Model)** med följande.

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

    Läs igenom kommentarerna för varje **växel** val.

5. Vi behöver inte göra några ändringar i **Nästa** åtgärd, om du har slutfört ytterligare kod för föregående avsnitt på beställning baserat på flera egenskaper.

### <a name="run-and-test-the-app"></a>Kör och testa appen

1. Kör appen. Du bör se en fullständig uppsättning bekvämligheterna i vyn.

1. Om du väljer "med numerisk klassificering" får du den numeriska ordning som du redan har implementerat i den här självstudien, med renoverings datum som ska bestämmas mellan hotell med samma betyg.

   ![Sortera "strand" baserat på klassificering](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

1. Testa nu profilen "av bekvämligheterna". Gör olika val av bekvämligheterna och kontrol lera att hotell med dessa bekvämligheterna befordras i resultat listan.

   ![Sortera "strand" baserat på profil](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

1. Prova "av Renovated-datum/betygs profil" för att se om du får det du förväntar dig. Endast nyligen renovated hotell bör få en _aktualitets_ ökning.

### <a name="resources"></a>Resurser

Mer information finns i följande [lägga till bedömnings profiler i ett Azure kognitiv sökning-index](./index-add-scoring-profiles.md).

## <a name="takeaways"></a>Lärdomar

Tänk på följande takeaways från det här projektet:

* Användarna förväntar sig att Sök resultat ska beställas, mest relevant först.
* Data måste vara strukturerade så att ordningen är enkel. Vi kunde inte sortera på "billigaste", eftersom data inte är strukturerade för att göra det möjligt att göra beställningar utan ytterligare kod.
* Det kan finnas flera nivåer att ordna, för att skilja mellan resultat som har samma värde på en högre nivå av ordning.
* Den är naturlig för vissa resultat som ska beställas i stigande ordning (t. ex. avstånd från en punkt) och vissa i fallande ordning (t. ex. gästens omdöme).
* Bedömnings profiler kan definieras när numeriska jämförelser inte är tillgängliga eller inte tillräckligt smart för en data uppsättning. Du kan se varje resultat för att ordna och visa resultaten intelligentt.

## <a name="next-steps"></a>Nästa steg

Du har slutfört den här serien med C#-självstudier – du bör ha fått värdefull kunskap om Azure Kognitiv sökning API: er.

Om du vill ha mer information och själv studie kurser kan du läsa [Microsoft Learn](/learn/browse/?products=azure)eller de andra självstudierna i [Azure kognitiv sökning-dokumentationen](./index.yml).