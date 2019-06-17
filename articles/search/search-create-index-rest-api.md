---
title: 'Snabbstart: PowerShell och REST API: er – Azure Search'
description: Skapa, läsa in och fråga ett index med hjälp av PowerShell-Invoke-RestMethod och Azure Search REST API.
ms.date: 06/10/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 7121bfceb177a7dc06d1c2a65b7c3edfca1d8c31
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063655"
---
# <a name="quickstart-create-an-azure-search-index-using-powershell"></a>Snabbstart: Skapa ett Azure Search-index med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-create-index-portal.md)
> 

Den här artikeln vägleder dig genom processen att skapa, läsa in och fråga ett Azure Search [index](search-what-is-an-index.md) med hjälp av PowerShell och [Azure Search Service REST API: er](https://docs.microsoft.com/rest/api/searchservice/). Definition av index och sökbart innehåll finns i begärandetexten som välformulerad JSON-innehåll.

Om du inte har en Azure-prenumeration kan du innan du börjar först skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) och sedan [registrera dig för Azure Search](search-create-service-portal.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande tjänster och verktyg som används i den här snabbstarten. 

+ [PowerShell 5.1 eller senare](https://github.com/PowerShell/PowerShell)med hjälp av [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) sekventiella och interaktiva anvisningar.

+ [Skapa en Azure Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här snabbstarten. 

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL: en

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

1. [Logga in på Azure-portalen](https://portal.azure.com/), och i din söktjänst **översikt** sidan, hämta URL: en. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

2. I **inställningar** > **nycklar**, hämta en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara administratörsnycklar, som angetts för kontinuitet för företag om du behöver förnya ett. Du kan använda antingen den primära eller sekundära nyckeln för förfrågningar för att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-fiddler/get-url-key.png "får en HTTP-slutpunkt och åtkomstnyckel")

Alla begäranden som kräver en api-nyckel för varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="connect-to-azure-search"></a>Anslut till Azure Search

1. I PowerShell, skapar en **$headers** objekt för att lagra innehållstyp och API-nyckel. Ersätt administrations-API-nyckeln (din-ADMIN-API-nyckel) med en nyckel som är giltig för din söktjänst. Du behöver bara ange den här rubriken en gång för hela sessionen, men du ska lägga till varje begäran. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Skapa en **$url** objekt som anger tjänstens indexerar samling. Ersätt namnet på tjänsten (din-SEARCH-SERVICE-NAME) med en giltig söktjänst.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06"
    ```

3. Kör **Invoke-RestMethod** att skicka en GET-begäran till tjänsten och kontrollera anslutningen. Lägg till **ConvertTo-Json** så att du kan se de svar som skickas tillbaka från tjänsten.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Om tjänsten är tom och har inga index, liknar resultat följande exempel. Annars ser du en JSON-representation av indexet.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 – Skapa ett index

Om du inte använder portalen, måste ett index finnas på tjänsten innan du kan läsa in data. Det här steget definierar indexet och skickas till tjänsten. Den [skapa Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) används för det här steget.

Obligatoriska elementen för ett index är ett namn och en samling fält. Fältsamlingen definierar strukturen för en *dokumentet*. Varje fält har ett namn, typ och attribut som avgör hur den används (till exempel om det är fulltext sökbar, filtrerbar eller hämtningsbara i sökresultat). I ett index, ett fält av typen `Edm.String` måste anges som den *nyckel* för dokumentet identitet.

Det här indexet har namnet ”hotels-quickstart” och fältdefinitioner som du ser nedan. Det är en del av ett större [Hotels indexet](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) används i andra genomgångar. Vi tas bort den i den här snabbstarten av utrymmesskäl.

1. Klistra in det här exemplet i PowerShell för att skapa en **$body** objekt som innehåller indexschemat.

    ```powershell
    $body = @"
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
    "@
    ```

2. Ange URI: N till samlingen index på din tjänst och *hotels-quickstart* index.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. Kör kommandot med **$url**, **$headers**, och **$body** att skapa indexet på tjänsten. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Resultatet bör se ut ungefär så här (trunkeras till de två första fälten kortfattat):

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> För verifiering, kan du också kontrollera listan index i portalen.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – läsa in dokument

Använd en HTTP POST-begäran till ditt index URL-slutpunkt för att skicka dokument. REST-API: et för den här uppgiften är [Lägg till, uppdatera eller ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

1. Klistra in det här exemplet i PowerShell för att skapa en **$body** objekt som innehåller de dokument som du vill ladda upp. 

    Denna begäran innehåller två fullständig och en partiell post. Partiell posten visar att du kan ladda upp ofullständig dokument. Den `@search.action` parametern anger hur indexering görs. Giltiga värden är ladda upp, sammanfoga, mergeOrUpload och ta bort. Beteendet mergeOrUpload antingen skapar ett nytt dokument för hotelId = 3 eller uppdaterar innehållet om det redan finns.

    ```powershell
    $body = @"
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
    "@
    ```

1. Ange slutpunkten den *hotels-quickstart* docs-samling och inkludera indexåtgärden (index/hotels-quickstart/docs/index).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. Kör kommandot med **$url**, **$headers**, och **$body** att läsa in dokument i indexet hotels-quickstart.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Resultatet bör likna följande exempel. Du bör se en [statuskod 201](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
        "value":  [
                    {
                        "key":  "1",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "2",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "3",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3 – Söka i ett index

Det här steget visar hur man frågar ett index med hjälp av den [API för webbsökning dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Var noga med att använda enkla citattecken på Sök $urls. Frågesträngar inkluderar **$** tecken, och du kan utelämna behöva föregås om hela strängen omges av enkla citattecken...

1. In den *hotels-quickstart* docs-samling och lägga till en **search** parametern för att skicka in en frågesträng. 
  
   Den här strängen kör en tom sökning (search = *), returnerar en unranked lista (Sök poäng = 1.0) av godtycklig dokument. Som standard returnerar Azure Search 50 träffar i taget. Den här frågan returnerar en struktur för hela dokumentet och värden som strukturerade. Lägg till **$count = true** att hämta en uppräkning av alla dokument i resultaten.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$count=true'
    ```

1. Kör kommando för att skicka den **$url** till tjänsten.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Resultatet bör likna följande utdata.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

Testa några andra fråga exempel för att få en bild av syntaxen. Du kan göra en sträng-sökning, ordagrant $filter frågor, begränsa resultatmängd, omfång sökningen till specifika fält och mycket mer.

```powershell
# Query example 1
# Search the entire index for the terms 'hotels' and 'wifi'
# Return only the HotelName and HotelId fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=hotels wifi&$count=true&$select=HotelName,HotelId'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelId and Description. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'

# Query example 3
# Take the top two results, and show only HotelId,HotelName,Description in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'

# Query example 4
# Sort by a specific field (`lastRenovationDate`) in descending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
```
## <a name="clean-up"></a>Rensa 

Du bör ta bort indexet om du inte längre behöver den. En kostnadsfri tjänst är begränsad till tre index. Du kanske vill ta bort alla index som du inte aktivt använder så att du kan gå igenom andra självstudier.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>Nästa steg

Försök att lägga till franska beskrivningar i indexet. I följande exempel innehåller franska strängar och visar ytterligare sökåtgärder. Använd mergeOrUpload för att skapa eller lägga till befintliga fält. Följande strängar måste vara UTF-8-kodat.

```json
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "1",
            "description_fr": "Meilleur hôtel en ville"
        },
        {
            "@search.action": "merge",
            "hotelId": "2",
            "description_fr": "Hôtel le moins cher en ville"
        }
    ]
}
```
