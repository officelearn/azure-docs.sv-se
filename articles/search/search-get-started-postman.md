---
title: 'Snabbstart: Skapa ett sökindex i Postman med REST-API:er'
titleSuffix: Azure Cognitive Search
description: I den här REST API-snabbstarten får du lära dig hur du anropar Azure Cognitive Search REST API:er med Hjälp av Postman- och exempeldata och definitioner.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: c502886aac9d13f7a470a9b83f1fc12334913beb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121644"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-postman-using-rest-apis"></a>Snabbstart: Skapa ett Azure Cognitive Search-index i Postman med REST-API:er
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> * [Powershell](search-howto-dotnet-sdk.md)
>*

Ett av de enklaste sätten att utforska [Azure Cognitive Search REST API:er](https://docs.microsoft.com/rest/api/searchservice) är att använda Postman eller ett annat webbtestverktyg för att formulera HTTP-begäranden och granska svaren. Med hjälp av rätt verktyg och de här instruktionerna kan du skicka begäranden och visa svar innan du skriver någon kod.

I den här artikeln beskrivs hur du formulerar begäranden interaktivt. Du kan också [hämta och importera en Postman-samling](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) för att använda fördefinierade begäranden.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Följande tjänster och verktyg krävs för den här snabbstarten. 

+ [Postman-skrivbordsapp](https://www.getpostman.com/) används för att skicka begäranden till Azure Cognitive Search.

+ [Skapa en Azure Cognitive Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här snabbstarten. 

## <a name="get-a-key-and-url"></a>Hämta en nyckel och webbadress

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En söktjänst skapas med båda, så om du har lagt till Azure Cognitive Search i din prenumeration följer du dessa steg för att få nödvändig information:

1. [Logga in på Azure-portalen](https://portal.azure.com/)och hämta webbadressen i söktjänstens **översiktssida.** Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningsnycklar** > **Keys**får du en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara admin nycklar, som tillhandahålls för kontinuitet i verksamheten om du behöver rulla en över. Du kan använda antingen primär- eller sekundärnyckeln på begäranden om att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-get-started-postman/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomstnyckel")

Alla begäranden kräver en api-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="connect-to-azure-cognitive-search"></a>Ansluta till Azure Cognitive Search

I det här avsnittet använder du ditt webbverktyg för att konfigurera anslutningar till Azure Cognitive Search. Varje verktyg behåller information om begäran om huvud för sessionen, vilket innebär att du bara behöver ange api-tangenten och Content-Type en gång.

För båda verktygen måste du välja ett kommando (GET, POST, PUT och så vidare), tillhandahålla en URL-slutpunkt och för vissa uppgifter ange JSON i brödtexten för begäran. Ersätt söktjänstnamnet (DITT-SÖK-TJÄNSTNAMN) med ett giltigt värde. Lägg `$select=name` till för att bara returnera namnet på varje index. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name

Lägg märke till HTTPS-prefixet, namnet på tjänsten, namnet på ett objekt (i det här fallet indexsamlingen) och [api-versionen](search-api-versions.md). Api-versionen är en obligatorisk, gemen `?api-version=2019-05-06` sträng som anges som för den aktuella versionen. API-versionerna uppdateras regelbundet. När du inkluderar API-versionen för varje begäran får du fullständig kontroll över vilken version som används.  

Begär rubrikkomposition innehåller två element, innehållstyp, plus api-nyckeln som används för att autentisera till Azure Cognitive Search. Ersätt administratörs-API-nyckeln (YOUR-AZURE-SEARCH-ADMIN-API-KEY) med ett giltigt värde. 

    api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
    Content-Type: application/json

I Postman formulerar du en begäran som ser ut som följande skärmbild. Välj **HÄMTA** som verb, ange webbadressen och klicka på **Skicka**. Det här kommandot ansluter till Azure Cognitive Search, läser indexsamlingen och returnerar HTTP-statuskod 200 på en lyckad anslutning. Om tjänsten redan har index innehåller svaret även indexdefinitioner.

![URL och rubrik för postmanbegäran](media/search-get-started-postman/postman-url.png "URL och rubrik för postmanbegäran")

## <a name="1---create-an-index"></a>1 – Skapa ett index

I Azure Cognitive Search skapar du vanligtvis indexet innan du läser in det med data. [REST-API:et](https://docs.microsoft.com/rest/api/searchservice/create-index) skapa index används för den här uppgiften. 

URL:en utökas `hotels` så att indexnamnet ingår.

Så här gör du i Brevbäraren:

1. Ändra verbet till **PUT**.

2. Kopiera i `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06`den här webbadressen .

3. Ange indexdefinitionen (kopieringsklar kod finns nedan) i brödtexten för begäran.

4. Klicka på **Skicka**.

![Index JSON-dokument i förfrågelstexten](media/search-get-started-postman/postman-request.png "Index JSON-dokument i förfrågelstexten")

### <a name="index-definition"></a>Indexdefinition

Fältsamlingen definierar dokumentstrukturen. Varje dokument måste ha dessa fält och varje fält måste ha en datatyp. Strängfälten används vid fulltextsökningar, så det kan vara en bra idé att konvertera numeriska data till strängar om du vill att innehållet ska vara sökbart.

Vilka åtgärder som tillåts fastställs av fältattributen. Med REST API:er kan du använda standardåtgärder i stor utsträckning. Alla strängar blir till exempel sökbara, hämtningsbara, filtrerbara och fasettbara som standard. Ofta behöver du bara ange attribut när du behöver inaktivera ett beteende.

```json
{
    "name": "hotels-quickstart",  
    "fields": [
        {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
        {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
        {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
        {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
        {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
        {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
        {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
        {"name": "Address", "type": "Edm.ComplexType", 
        "fields": [
        {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
        {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
        ]
     }
  ]
}
```

När du skickar denna begäran får du ett HTTP 201-svar som anger att indexet har skapats. Du kan kontrollera detta i portalen, men observera att portalsidan uppdateras med bestämda intervall, så det kan dröja någon minut eller två innan informationen dyker upp.

> [!TIP]
> Om HTTP 504 returneras kontrollerar du att HTTPS används i URL:en. Om HTTP 400 eller 404 returneras kontrollerar du att alla fält har kopierats och klistrats in korrekt i begärandetexten. HTTP 403 tyder vanligen på problem med API-nyckeln (antingen en ogiltig nyckel eller ett syntaxproblem när API-nyckeln angavs).

## <a name="2---load-documents"></a>2 - Ladda dokument

Att skapa ett index och att fylla det, är två separata steg. I Azure Cognitive Search innehåller indexet alla sökbara data som du kan tillhandahålla som JSON-dokument. [REST-API:et för Lägg till, uppdatera eller ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) används för den här uppgiften. 

URL:en utökas `docs` till `index` att omfatta samlingar och åtgärder.

Så här gör du i Brevbäraren:

1. Byt till verbet **POST**.

2. Kopiera i `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06`den här webbadressen .

3. Ange JSON-dokumenten (kopieringsklar kod finns nedan) i brödtexten i begäran.

4. Klicka på **Skicka**.

![JSON dokument i begäran organ](media/search-get-started-postman/postman-docs.png "JSON dokument i begäran organ")

### <a name="json-documents-to-load-into-the-index"></a>JSON dokument för att ladda in i indexet

Begärandetexten innehåller fyra dokument som ska läggas till i hotellindexet.

```json
{
    "value": [
    {
    "@search.action": "upload",
    "HotelId": "1",
    "HotelName": "Secret Point Motel",
    "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
    "Category": "Boutique",
    "Tags": [ "pool", "air conditioning", "concierge" ],
    "ParkingIncluded": false,
    "LastRenovationDate": "1970-01-18T00:00:00Z",
    "Rating": 3.60,
    "Address": 
        {
        "StreetAddress": "677 5th Ave",
        "City": "New York",
        "StateProvince": "NY",
        "PostalCode": "10022",
        "Country": "USA"
        } 
    },
    {
    "@search.action": "upload",
    "HotelId": "2",
    "HotelName": "Twin Dome Motel",
    "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
    "Category": "Boutique",
    "Tags": [ "pool", "free wifi", "concierge" ],
    "ParkingIncluded": false,
    "LastRenovationDate": "1979-02-18T00:00:00Z",
    "Rating": 3.60,
    "Address": 
        {
        "StreetAddress": "140 University Town Center Dr",
        "City": "Sarasota",
        "StateProvince": "FL",
        "PostalCode": "34243",
        "Country": "USA"
        } 
    },
    {
    "@search.action": "upload",
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Category": "Resort and Spa",
    "Tags": [ "air conditioning", "bar", "continental breakfast" ],
    "ParkingIncluded": true,
    "LastRenovationDate": "2015-09-20T00:00:00Z",
    "Rating": 4.80,
    "Address": 
        {
        "StreetAddress": "3393 Peachtree Rd",
        "City": "Atlanta",
        "StateProvince": "GA",
        "PostalCode": "30326",
        "Country": "USA"
        } 
    },
    {
    "@search.action": "upload",
    "HotelId": "4",
    "HotelName": "Sublime Cliff Hotel",
    "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
    "Category": "Boutique",
    "Tags": [ "concierge", "view", "24-hour front desk service" ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1960-02-06T00:00:00Z",
    "Rating": 4.60,
    "Address": 
        {
        "StreetAddress": "7400 San Pedro Ave",
        "City": "San Antonio",
        "StateProvince": "TX",
        "PostalCode": "78216",
        "Country": "USA"
        }
    }
  ]
}
```

Om några sekunder bör du se ett HTTP 201-svar i sessionslistan. Detta anger att dokumenten har skapats. 

Om ett 207-svar returneras misslyckades uppladdningen av minst ett dokument. Om ett 404-svar returneras beror det på ett syntaxfel i begärandehuvudet eller i begärandetexten. Kontrollera att du har ändrat slutpunkten så att den inkluderar följande: `/docs/index`.

> [!Tip]
> För vissa utvalda datakällor kan du välja en alternativ *indexerare* för att förenkla och minska mängden kod som krävs för indexering. Mer information finns i [Indexer operations](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) (Indexeringsåtgärder).


## <a name="3---search-an-index"></a>3 – Söka i ett index

Nu när ett index och dokument läses in kan du utfärda frågor mot dem med REST [API för sökdokument](https://docs.microsoft.com/rest/api/searchservice/search-documents).

URL:en utökas till att omfatta ett frågeuttryck som anges med hjälp av sökoperatorn.

Så här gör du i Brevbäraren:

1. Ändra verbet till **GET**.

2. Kopiera i `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2019-05-06`den här webbadressen .

3. Klicka på **Skicka**.

Den här frågan är tom och returnerar antalet dokument i sökresultaten. Begäran och svar bör se ut som skärmbilden från Postman nedan efter att du klickat på **Skicka**. Statuskoden ska vara 200.

 ![FÅ med söksträngen på webbadressen](media/search-get-started-postman/postman-query.png "FÅ med söksträngen på webbadressen")

Prova några andra frågeexempel för att få en känsla för syntaxen. Du kan göra en strängsökning, fullständiga $filter frågor, begränsa resultatuppsättningen, begränsa sökningen till specifika fält med mera.

Byt ut den aktuella webbadressen med de nedan, klicka på **Skicka** varje gång för att visa resultaten.

```
# Query example 1 - Search on restaurant and wifi
# Return only the HotelName, Description, and Tags fields
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=restaurant wifi&$count=true&$select=HotelName,Description,Tags&api-version=2019-05-06

# Query example 2 - Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$filter=Rating gt 4&$select=HotelName,Rating&api-version=2019-05-06

# Query example 3 - Take the top two results, and show only HotelName and Category in the results
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=boutique&$top=2&$select=HotelName,Category&api-version=2019-05-06

# Query example 4 - Sort by a specific field (Address/City) in ascending order
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating&api-version=2019-05-06
```

## <a name="get-index-properties"></a>Hämta indexegenskaper
Du kan också använda [Hämta statistik](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) för att fråga efter antal dokument och indexstorlek: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2019-05-06
```

Om `/stats` du lägger till i webbadressen returneras indexinformation. Din begäran i Postman borde se ut som på bilden nedan. Svaret innehåller ett dokumentantal och det diskutrymme som används uttryckt i byte.

 ![Hämta indexinformation](media/search-get-started-postman/postman-system-query.png "Hämta indexinformation")

Observera att syntaxen för API-versionen ser annorlunda ut här. Denna begäran använder `?` för att lägga till API-versionen. Url-sökvägen `?` separeras från frågesträngen, medan & separerar varje namn=värde-par i frågesträngen. I den här frågan är API-versionen det första och enda objektet i frågesträngen.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken **Alla resurser** eller **Resursgrupper** i fönstret för vänsternavigering.

Om du använder en kostnadsfri tjänst bör du komma ihåg att du är begränsad till tre index, indexerare och datakällor. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du utför kärnuppgifter kan du gå vidare med ytterligare REST API-anrop för mer avancerade funktioner, till exempel indexerare eller [konfigurera en kognitiv sökpipeline](cognitive-search-tutorial-blob.md). För nästa steg rekommenderar vi följande länk:

> [!div class="nextstepaction"]
> [REST-självstudiekurs: Indexera och sök semistrukturerade data (JSON-blobbar) i Azure Cognitive Search](search-semi-structured-data.md)
