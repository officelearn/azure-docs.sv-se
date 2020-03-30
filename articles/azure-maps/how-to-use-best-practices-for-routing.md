---
title: Metodtips för Azure Maps Route Service | Microsoft Azure Maps
description: Lär dig hur du dirigerar effektivt med hjälp av Route Service från Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 85ce29d088b8fbd110988db67776d89346215e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335405"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Metodtips för Azure Maps Route-tjänst

Route Directions och Route Matrix API:er i Azure Maps [Route Service](https://docs.microsoft.com/rest/api/maps/route) kan användas för att beräkna de uppskattade ankomsttiderna (ETAs) för varje begärd rutt. Rutt-API:er beaktar faktorer som trafikinformation i realtid och historiska trafikdata, som de typiska väghastigheterna på den begärda veckodagen och tid på dagen. API:erna returnerar de kortaste eller snabbaste rutterna som är tillgängliga för flera destinationer i följd eller i optimerad ordning, baserat på tid eller avstånd. Användare kan också begära specialiserade rutter och detaljer för vandrare, cyklister och kommersiella fordon som lastbilar. I den här artikeln delar vi de bästa metoderna för att anropa Azure Maps [Route Service](https://docs.microsoft.com/rest/api/maps/route)och du får lära dig hur du gör:

* Välj mellan API:erna för vägbeskrivningar och matrisroutnings-API:et
* Begär historiska och förutsedda restider, baserat på trafikdata i realtid och historiska
* Begär ruttdetaljer, som tid och avstånd, för hela rutten och varje sträcka av rutten
* Begär rutt för ett nyttofordon, som en lastbil
* Begär trafikinformation längs en rutt, till exempel sylt och avgiftsinformation
* Begär en rutt som består av ett eller flera stopp (waypoints)
* Optimera en rutt med ett eller flera stopp för att få den bästa ordningen för att besöka varje stopp (waypoint)
* Optimera alternativa rutter med hjälp av stödpunkter. Till exempel erbjuda alternativa vägar som passerar en laddningsstation för elfordon.
* Använda [route-tjänsten](https://docs.microsoft.com/rest/api/maps/route) med Azure Maps Web SDK

## <a name="prerequisites"></a>Krav

Om du vill ringa samtal till Azure Maps API:er behöver du ett Azure Maps-konto och en nyckel. Mer information finns i [Skapa ett konto](quick-demo-map-app.md#create-an-account-with-azure-maps) och Hämta en [primärnyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account). Primärnyckeln kallas även primär prenumerationsnyckel eller prenumerationsnyckel.

Information om autentisering i Azure Maps finns [i Hantera autentisering i Azure Maps](./how-to-manage-authentication.md). Och mer information om täckningen av routetjänsten finns i [routningstäckningen](routing-coverage.md).

Den här artikeln använder [Postman-appen](https://www.postman.com/downloads/) för att skapa REST-anrop, men du kan välja vilken API-utvecklingsmiljö som helst.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Välj mellan ruttriktningar och matrisroutning

Api:erna för vägbeskrivningar returnerar instruktioner, inklusive restid och koordinater för en ruttsökväg. Med Route Matrix API kan du beräkna restiden och avstånden för en uppsättning rutter som definieras av ursprungs- och målplatser. För varje givet ursprung beräknar Matrix API kostnaden (restid och sträcka) för routning från det ursprunget till varje given destination. Alla dessa API:er gör att du kan ange parametrar som önskad avgångstid, ankomsttider och fordonstyp, som bil eller lastbil. De använder alla realtids- eller prediktiva trafikdata i enlighet med detta för att returnera de mest optimala rutterna.

Överväg att anropa Api:er för vägbeskrivningar om ditt scenario är att:

* Begär den kortaste eller snabbaste körvägen mellan två eller flera kända platser, för att få exakta ankomsttider för dina leveransfordon.
* Begär detaljerad ruttvägledning, inklusive ruttgeometri, för att visualisera rutter på kartan
* Med en lista över kundplatser, beräkna den kortaste möjliga vägen för att besöka varje kundplats och återgå till ursprunget. Det här scenariot kallas allmänt det resande säljarproblemet. Du kan skicka upp till 150 waypoints (stopp) i en begäran.
* Skicka batchar med frågor till kommandobedrifts-API:et för vägbeskrivning med bara ett enda API-anrop.

Överväg att anropa Matrix Routning API om ditt scenario är att:

* Beräkna restiden eller avståndet mellan en uppsättning ursprung och destinationer. Till exempel har du 12 förare och du måste hitta närmaste tillgängliga förare för att hämta matleveransen från restaurangen.
* Sortera potentiella rutter efter deras faktiska ressträcka eller tid. Matrix API returnerar endast restider och avstånd för varje ursprung och målkombination.
* Klusterdata baserat på restid eller avstånd. Ditt företag har till exempel 50 anställda, hitta alla anställda som bor inom 20 minuters körtid från kontoret.

Här är en jämförelse för att visa några funktioner i Route Directions och Matrix API:er:

| Azure Maps API | Max antal frågor i begäran | Undvik områden | Kördragning av lastbilar och elfordon | waypoints och Traveling Salesman optimering | Stödjande punkter |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Hämta vägbeskrivningar | 1 | | X | X | |
| Skicka vägbeskrivningar | 1 | X | X | X | X |
| Batch för bokföra vägbeskrivningar | 700 | | X | X | |
| Matris för inläggsrutt | 700 | | X | | |

Mer information om routningsfunktioner för elfordon finns i vår handledning om hur du [dirigerar elfordon med Azure Notebooks med Python](tutorial-ev-routing.md).

## <a name="request-historic-and-real-time-data"></a>Begär historiska data och realtidsdata

Som standard förutsätter route-tjänsten att reseläget är en bil och avgångstiden är nu. Den returnerar flöde baserat på trafikförhållanden i realtid om inte en begäran om flödesberäkning anger något annat. Fasta trafikbegränsningar som är beroende av tid, till exempel Vänstersvängar, är inte tillåtna mellan 16:00 och 18:00 och kommer att beaktas av routningsmotorn. Vägavstängningar, som vägarbeten, kommer att övervägas om du inte specifikt begär en rutt som ignorerar den aktuella levande trafiken. Om du vill ignorera `traffic` `false` den aktuella trafiken anger du till i din API-begäran.

Ruttberäkningen **travelTimeInSeconds-värdet** inkluderar fördröjningen på grund av trafik. Det genereras genom att utnyttja den nuvarande och historiska restidsdata, när avgångstiden är inställd på nu. Om din avgångstid är inställd i framtiden returnerar API:erna förväntade restider baserat på historiska data.

Om du inkluderar parametern **computeTravelTimeFor=all** i din begäran kommer sammanfattningselementet i svaret att ha följande ytterligare fält, inklusive historiska trafikförhållanden:

| Element | Beskrivning|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | Beräknad restid beräknad som om det inte finns några förseningar på sträckan på grund av trafikförhållanden, till exempel på grund av överbelastning |
| historiskaTrafficTravelTimeInSeconds | Beräknad restid beräknad med hjälp av tidsberoende historiska trafikdata |
| liveTrafficIncidentsTravelTimeInSeconds liveTrafficIncidentsTravelTimeInSeconds liveTrafficIncidentsTravelTimeInSeconds liveTra | Beräknad restid beräknad med hjälp av hastighetsdata i realtid |

I nästa avsnitt visas hur du ringer anrop till route-API:erna med hjälp av de diskuterade parametrarna.

### <a name="sample-query"></a>Exempelfråga

I det första exemplet nedan är avgångstiden inställd på framtiden, i skrivande stund.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

Svaret innehåller ett sammanfattande element, som det nedan. Eftersom avgångstiden är inställd på framtiden är **trafficDelayInSeconds-värdet** noll. **Värdet travelTimeInSeconds** beräknas med hjälp av tidsberoende historiska trafikdata. Så, i det här fallet, **travelTimeInSeconds** värdet är lika med den **historiskaTrafficTravelTimeInSeconds** värde.

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>Exempelfråga

I det andra exemplet nedan har vi en begäran om routning i realtid, där avgångstiden är nu. Det anges inte uttryckligen i webbadressen eftersom det är standardvärdet.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

Svaret innehåller en sammanfattning som visas nedan. På grund av överbelastning är **trafficDelaysInSeconds-värdet** större än noll. Det är också större än **historiskaTrafficTravelTimeInSeconds**.

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>Begär rutt- och beninformation

Som standard returnerar route-tjänsten en matris med koordinater. Svaret innehåller de koordinater som utgör sökvägen i en lista med namnet `points`. Ruttresponsen omfattar också avståndet från ruttens början och den beräknade förflutna tiden. Dessa värden kan användas för att beräkna medelhastigheten för hela rutten.

Följande bild visar `points` elementet.

<center>

![punktlista](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

</center>

Expandera `point` elementet om du vill visa listan över koordinater för sökvägen:

<center>

![punktlista](media/how-to-use-best-practices-for-routing/points-list-img.png)

</center>

Api:erna för vägbeskrivningar stöder olika format med instruktioner som kan användas genom att ange parametern **instructionsType.** Om du vill formatera instruktioner för enkel datorbearbetning använder du **instruktionernaTyp=kodad**. Använd **instruktionerTyp=taggade** för att visa instruktioner som text för användaren. Instruktioner kan också formateras som text där vissa delar av instruktionerna är markerade, och instruktionen presenteras med särskild formatering. Mer information finns i [listan över instruktionstyper som stöds](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype).

När instruktioner begärs returnerar svaret ett `guidance`nytt element med namnet . Elementet `guidance` innehåller två delar av information: turn-by-turn riktningar och sammanfattade instruktioner.

<center>

![Typ av instruktioner](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

</center>

Elementet `instructions` har turn-by-turn riktningar för `instructionGroups` resan, och har sammanfattat instruktioner. Varje instruktionssammanfattning täcker ett segment av resan som kan omfatta flera vägar. API:erna kan returnera information om avsnitt i en rutt. koordinatområdet för en trafikstockning eller den aktuella hastigheten på trafiken.

<center>

![Instruktioner för sväng för sväng](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

</center>

<center>

![Sammanfattade instruktioner](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

</center>

## <a name="request-a-route-for-a-commercial-vehicle"></a>Begär en rutt för ett nyttofordon

Azure Maps Routing API:er stöder routning av nyttofordon, som omfattar kommersiell lastbilsroutning. API:erna överväger angivna gränser. Såsom fordonets längd och vikt och om fordonet transporterar farlig last. Om ett fordon till exempel transporterar brandfarligt, undviker utstringsmotorn vissa tunnlar som ligger nära bostadsområden.

### <a name="sample-query"></a>Exempelfråga

Exempelbegäran nedan frågar en rutt för en kommersiell lastbil. Lastbilen transporterar klass 1 farligt avfall.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Route API returnerar riktningar som rymmer lastbilens dimensioner och det farliga avfallet. Du kan läsa flödesinstruktionerna genom att expandera elementet. `guidance`

<center>

![Lastbil med klass 1 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

</center>

### <a name="sample-query"></a>Exempelfråga

Om du ändrar den amerikanska Hazmat-klassen, från ovanstående fråga, kommer det att resultera i en annan väg för att hantera den här ändringen.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Svaret nedan gäller en lastbil som transporterar ett farligt material av klass 9, vilket är mindre farligt än ett farligt material av klass 1. När du `guidance` expanderar elementet för att läsa anvisningarna märker du att anvisningarna inte är desamma. Det finns fler ruttinstruktioner för lastbilen som transporterar farligt material av klass 1.

<center>

![Lastbil med klass 9 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)

</center>

## <a name="request-traffic-information-along-a-route"></a>Begär trafikinformation längs en rutt

Med Azure Maps Route Direction API:er kan utvecklare begära `sectionType` information för varje avsnittstyp genom att inkludera parametern i begäran. Du kan till exempel begära hastighetsinformation för varje trafikstockningssegment. Läs [listan med värden för sectionType-nyckeln](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype) om du vill veta mer om de olika detaljer som du kan begära.

### <a name="sample-query"></a>Exempelfråga

Följande fråga anger `sectionType` `traffic`till . Den begär de avsnitt som innehåller trafikinformation från Seattle till San Diego.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

Svaret innehåller de avsnitt som är lämpliga för trafik längs de angivna koordinaterna.

<center>

![trafikavsnitt](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

</center>

Det här alternativet kan användas för att färglägga avsnitten när kartan återges, som i bilden nedan: 

<center>

![trafikavsnitt](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

</center>

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Beräkna och optimera en multi-stop rutt

Azure Maps innehåller för närvarande två former av vägoptimeringar:

* Optimeringar baserade på den begärda flödestypen, utan att ändra ordningen på waypoints. Du hittar de [vägtyper som stöds här](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype)

* Traveling salesman optimering, som ändrar ordningen på waypoints för att få den bästa ordningen för att besöka varje stopp

För flerstoppsdirigering kan upp till 150 waypoints anges i en enda vägbegäran. Start- och slutkoordinatplatserna kan vara desamma, som skulle vara fallet med en tur och retur. Men du måste ange minst en ytterligare waypoint för att göra ruttberäkningen. Waypoints kan läggas till i frågan mellan ursprungs- och målkoordinaterna.

Om du vill optimera den bästa ordningen för att besöka de angivna waypoints, måste du ange **computeBestOrder = sant**. Det här scenariot kallas även det resande optimeringsproblemet för säljare.

### <a name="sample-query"></a>Exempelfråga

Följande fråga begär sökvägen för sex waypoints, med parametern `computeBestOrder` inställd `false`på . Det är också standardvärdet `computeBestOrder` för parametern.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

Svaret beskriver banlängden till 140 851 meter, och att det skulle ta 9 991 sekunder att färdas på den vägen.

<center>

![Icke-optimerad respons](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

</center>

Bilden nedan illustrerar sökvägen till följd av den här frågan. Denna väg är en möjlig väg. Det är inte den optimala vägen baserat på tid eller avstånd.

<center>

![Icke-optimerad bild](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)

</center>

Den här route waypoint-ordern är: 0, 1, 2, 3, 4, 5 och 6.

### <a name="sample-query"></a>Exempelfråga

Följande fråga begär sökvägen för samma sex waypoints, som i exemplet ovan. Den här `computeBestOrder` gången `true` är parametern inställd på (den resande försäljareoptimeringen).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

Svaret beskriver banlängden till 91 814 meter, och att det skulle ta 7 797 sekunder att färdas på den vägen. Ressträckan och restiden är båda lägre här eftersom API:et returnerade den optimerade rutten.

<center>

![Icke-optimerad respons](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

</center>

Bilden nedan illustrerar sökvägen till följd av den här frågan.

<center>

![Icke-optimerad bild](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

</center>

Den optimala rutten har följande waypointordning: 0, 5, 1, 2, 4, 3 och 6.

>[!TIP]
> Den optimerade waypointorderinformationen från routningstjänsten innehåller en uppsättning index. Dessa utesluter ursprungs- och destinationsindexen. Du måste öka dessa värden med 1 för att ta hänsyn till ursprunget. Lägg sedan till din destination i slutet för att få den fullständiga ordnade waypointlistan.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Beräkna och snedvrida alternativa rutter med hjälp av stödpunkter

Du kan ha situationer där du vill rekonstruera en rutt för att beräkna noll eller fler alternativa vägar för en referensväg. Du kanske till exempel vill visa kunderna alternativa rutter som passerar din butik. I det här fallet måste du fördom en plats med hjälp av stödpunkter. Här är stegen för att fördom en plats:

1. Beräkna en rutt som den är och hämta sökvägen från ruttsvaret
2. Använd ruttsökvägen för att hitta önskade platser längs eller nära ruttsökvägen. Du kan till exempel använda Azure Maps [Point of Interest API](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) eller fråga dina egna data i databasen.  
3. Beställ platserna baserat på avståndet från ruttens början
4. Lägg till dessa platser som stödpunkter i en ny vägbegäran till [API:et för inläggsvägbeskrivningar](https://docs.microsoft.com/rest/api/maps/route/postroutedirections). Mer information om stödpunkterna finns i [API-dokumentationen för Api för inläggsvägsvägbeskrivning](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints). 

När du [anropar API:et för inläggsvägar](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)kan du ställa in minsta avvikelsetid eller avståndsbegränsningar, tillsammans med stödpunkterna. Använd dessa parametrar om du vill erbjuda alternativa rutter, men du vill också begränsa restiden. När dessa begränsningar används följer de alternativa rutterna referensrutten från ursprungspunkten för den angivna tiden eller avståndet. Med andra ord skiljer sig de andra rutterna från referensrutten per de angivna begränsningarna.

Bilden nedan är ett exempel på att återge alternativa rutter med angivna avvikelsegränser för tiden och avståndet.

<center>

![Alternativa rutter](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

</center>

## <a name="use-the-routing-service-in-a-web-app"></a>Använda routningstjänsten i en webbapp

Azure Maps Web SDK tillhandahåller en [tjänstmodul](https://docs.microsoft.com/javascript/api/azure-maps-rest/?view=azure-maps-typescript-latest). Den här modulen är ett hjälpbibliotek som gör det enkelt att använda Azure Maps REST API:er i webb- eller nod.js-program med JavaScript eller TypeScript. Modulen Service kan användas för att återge de returnerade rutterna på kartan. Modulen avgör automatiskt vilket API som ska användas med GET- och POST-begäranden.

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

> [!div class="nextstepaction"]
> [Azure Maps Route-tjänst](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [Så här använder du servicemodulen](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [Visa rutt på kartan](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [NPM-paket för Azure Maps](https://www.npmjs.com/package/azure-maps-rest  )
