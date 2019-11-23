---
title: 'Quickstart: Create a search index in Postman using REST APIs'
titleSuffix: Azure Cognitive Search
description: In this REST API quickstart, learn how to call the Azure Cognitive Search REST APIs using Postman and sample data and definitions.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/04/2019
ms.openlocfilehash: 9014a83596a3eee71f43d12a73188c1c43b832af
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406946"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-postman-using-rest-apis"></a>Quickstart: Create an Azure Cognitive Search index in Postman using REST APIs
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portalen](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

One of the easiest ways to explore the [Azure Cognitive Search REST APIs](https://docs.microsoft.com/rest/api/searchservice) is using Postman or another web testing tool to formulate HTTP requests and inspect the responses. Med hjälp av rätt verktyg och de här instruktionerna kan du skicka begäranden och visa svar innan du skriver någon kod.

This article explains how to formulate requests interactively. Alternatively, you can [download and import a Postman collection](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) to use predefined requests.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

The following services and tools are required for this quickstart. 

+ [Postman desktop app](https://www.getpostman.com/) is used for sending requests to Azure Cognitive Search.

+ [Create an Azure Cognitive Search service](search-create-service-portal.md) or [find an existing service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under your current subscription. You can use a free service for this quickstart. 

## <a name="get-a-key-and-url"></a>Get a key and URL

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. A search service is created with both, so if you added Azure Cognitive Search to your subscription, follow these steps to get the necessary information:

1. [Sign in to the Azure portal](https://portal.azure.com/), and in your search service **Overview** page, get the URL. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. In **Settings** > **Keys**, get an admin key for full rights on the service. There are two interchangeable admin keys, provided for business continuity in case you need to roll one over. You can use either the primary or secondary key on requests for adding, modifying, and deleting objects.

![Get an HTTP endpoint and access key](media/search-get-started-postman/get-url-key.png "Get an HTTP endpoint and access key")

All requests require an api-key on every request sent to your service. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="connect-to-azure-cognitive-search"></a>Connect to Azure Cognitive Search

In this section, use your web tool of choice to set up connections to Azure Cognitive Search. Each tool persists request header information for the session, which means you only have to enter the api-key and Content-Type once.

For either tool, you need to choose a command (GET, POST, PUT, and so forth), provide a URL endpoint, and for some tasks, provide JSON in the body of the request. Replace the search service name (YOUR-SEARCH-SERVICE-NAME)  with a valid value. Add `$select=name` to return just the name of each index. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name

Notice the HTTPS prefix, the name of the service, the name of an object (in this case, the indexes collection), and the [api-version](search-api-versions.md). The api-version is a required, lowercase string specified as `?api-version=2019-05-06` for the current version. API versions are updated regularly. När du inkluderar API-versionen för varje begäran får du fullständig kontroll över vilken version som används.  

Request header composition includes two elements, content type, plus the api-key used to authenticate to Azure Cognitive Search. Replace the admin API key (YOUR-AZURE-SEARCH-ADMIN-API-KEY) with a valid value. 

    api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
    Content-Type: application/json

In Postman, formulate a request that looks like the following screenshot. Choose **GET** as the verb, provide the URL, and click **Send**. This command connects to Azure Cognitive Search, reads the indexes collection, and returns HTTP status code 200 on a successful connection. If your service has indexes already, the response will also include index definitions.

![Postman request URL and header](media/search-get-started-postman/postman-url.png "Postman request URL and header")

## <a name="1---create-an-index"></a>1 – Skapa ett index

In Azure Cognitive Search, you usually create the index before loading it with data. The [Create Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) is used for this task. 

The URL is extended to include the `hotels` index name.

To do this in Postman:

1. Change the verb to **PUT**.

2. Copy in this URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06`.

3. Provide the index definition (copy-ready code is provided below) in the body of the request.

4. Klicka på **Skicka**.

![Index JSON document in request body](media/search-get-started-postman/postman-request.png "Index JSON document in request body")

### <a name="index-definition"></a>Indexdefinition

The fields collection defines document structure. Each document must have these fields, and each field must have a data type. Strängfälten används vid fulltextsökningar, så det kan vara en bra idé att konvertera numeriska data till strängar om du vill att innehållet ska vara sökbart.

Vilka åtgärder som tillåts fastställs av fältattributen. Med REST API:er kan du använda standardåtgärder i stor utsträckning. Alla strängar blir till exempel sökbara, hämtningsbara, filtrerbara och fasettbara som standard. Often, you only have to set attributes when you need to turn off a behavior.

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

## <a name="2---load-documents"></a>2 - Load documents

Att skapa ett index och att fylla det, är två separata steg. In Azure Cognitive Search, the index contains all searchable data, which you can provide as JSON documents. The [Add, Update, or Delete Documents REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) is used for this task. 

The URL is extended to include the `docs` collections and `index` operation.

To do this in Postman:

1. Byt till verbet **POST**.

2. Copy in this URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06`.

3. Provide the JSON documents (copy-ready code is below) in the body of the request.

4. Klicka på **Skicka**.

![JSON documents in request body](media/search-get-started-postman/postman-docs.png "JSON documents in request body")

### <a name="json-documents-to-load-into-the-index"></a>JSON documents to load into the index

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

In a few seconds, you should see an HTTP 201 response in the session list. Detta anger att dokumenten har skapats. 

Om ett 207-svar returneras misslyckades uppladdningen av minst ett dokument. Om ett 404-svar returneras beror det på ett syntaxfel i begärandehuvudet eller i begärandetexten. Kontrollera att du har ändrat slutpunkten så att den inkluderar följande: `/docs/index`.

> [!Tip]
> För vissa utvalda datakällor kan du välja en alternativ *indexerare* för att förenkla och minska mängden kod som krävs för indexering. Mer information finns i [Indexer operations](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) (Indexeringsåtgärder).


## <a name="3---search-an-index"></a>3 – Söka i ett index

Now that an index and documents are loaded, you can issue queries against them using [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

The URL is extended to include a query expression, specified using the search operator.

To do this in Postman:

1. Change the verb to **GET**.

2. Copy in this URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2019-05-06`.

3. Klicka på **Skicka**.

This query is an empty and returns a count of the documents in the search results. Begäran och svar bör se ut som skärmbilden från Postman nedan efter att du klickat på **Skicka**. Statuskoden ska vara 200.

 ![GET with search string on the URL](media/search-get-started-postman/postman-query.png "GET with search string on the URL")

Try a few other query examples to get a feel for the syntax. You can do a string search, verbatim $filter queries, limit the results set, scope the search to specific fields, and more.

Swap out the current URL with the ones below, clicking **Send** each time to view the results.

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

## <a name="get-index-properties"></a>Get index properties
You can also use [Get Statistics](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) to query for document counts and index size: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2019-05-06
```

Adding `/stats` to your URL returns index information. Din begäran i Postman borde se ut som på bilden nedan. Svaret innehåller ett dokumentantal och det diskutrymme som används uttryckt i byte.

 ![Get index information](media/search-get-started-postman/postman-system-query.png "Get index information")

Observera att syntaxen för API-versionen ser annorlunda ut här. Denna begäran använder `?` för att lägga till API-versionen. The `?` separates the URL path from the query string, while & separates each 'name=value' pair in the query string. I den här frågan är API-versionen det första och enda objektet i frågesträngen.

## <a name="clean-up-resources"></a>Rensa resurser

When you're working in your own subscription, it's a good idea at the end of a project to identify whether you still need the resources you created. Resources left running can cost you money. You can delete resources individually or delete the resource group to delete the entire set of resources.

You can find and manage resources in the portal, using the **All resources** or **Resource groups** link in the left-navigation pane.

If you are using a free service, remember that you are limited to three indexes, indexers, and data sources. You can delete individual items in the portal to stay under the limit. 

## <a name="next-steps"></a>Nästa steg

Now that you know how to perform core tasks, you can move forward with additional REST API calls for more advanced features, such as indexers or [setting up a cognitive search pipeline](cognitive-search-tutorial-blob.md). For your next step, we recommend the following link:

> [!div class="nextstepaction"]
> [REST Tutorial: Index and search semi-structured data (JSON blobs) in Azure Cognitive Search](search-semi-structured-data.md)
