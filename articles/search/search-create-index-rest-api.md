---
title: Skapa ett index i kod med hjälp av PowerShell och REST-API – Azure Search
description: Skapa ett sökbart fulltextindex i kod med hjälp av HTTP-begäranden och Azure Search REST API.
ms.date: 03/15/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 0524bd224e3da3e6a9b18a4225c88e9c43d07606
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223418"
---
# <a name="quickstart-create-an-azure-search-index-using-powershell-and-the-rest-api"></a>Snabbstart: Skapa ett Azure Search-index med PowerShell och REST API
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Portal](search-create-index-portal.md)
> 

Den här artikeln vägleder dig genom processen att skapa, läsa in och fråga ett Azure Search [index](search-what-is-an-index.md) med hjälp av PowerShell och [Azure Search Service REST API](https://docs.microsoft.com/rest/api/searchservice/). Definition av index och sökbart innehåll tillhandahålls som välformulerad JSON-innehåll i begärandetexten.

## <a name="prerequisites"></a>Förutsättningar

[Skapa en Azure Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här snabbstarten. Andra förutsättningar inkluderar följande.

[PowerShell 5.1 eller senare](https://github.com/PowerShell/PowerShell)med hjälp av [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) sekventiella och interaktiva anvisningar.

En URL-slutpunkt och admin api-nyckeln för search-tjänsten. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

1. Azure-portalen i din söktjänst **översikt** sidan, hämta URL: en. En exempel-slutpunkt kan se ut https:\//my-service-name.search.windows.net.

2. I **inställningar** > **nycklar**, hämta en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara administratörsnycklar, som angetts för kontinuitet för företag om du behöver förnya ett. Du kan använda antingen den primära eller sekundära nyckeln för förfrågningar för att lägga till, ändra och ta bort objekt.

   ![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-fiddler/get-url-key.png "får en HTTP-slutpunkt och åtkomstnyckel")

   Alla begäranden som kräver en api-nyckel för varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="connect-to-azure-search"></a>Anslut till Azure Search

I PowerShell, skapar en **$headers** objekt för att lagra innehållstyp och API-nyckel. Du behöver bara ange den här rubriken en gång för hela sessionen, men du ska lägga till varje begäran. 

```powershell
$headers = @{
   'api-key' = '<your-admin-api-key>'
   'Content-Type' = 'application/json' 
   'Accept' = 'application/json' }
```

Skapa en **$url** objekt som anger tjänstens indexerar samling. Den `mydemo` tjänstnamnet är avsedd som en platshållare. Ersätt den med en giltig söktjänst i någon aktuell prenumeration under hela det här exemplet.

```powershell
$url = "https://mydemo.search.windows.net/indexes?api-version=2017-11-11"
```

Kör **Invoke-RestMethod** att skicka en GET-begäran till tjänsten och kontrollera anslutningen. Lägg till **ConvertTo-Json** så att du kan se de svar som skickas tillbaka från tjänsten.

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

## <a name="1---create-an-index"></a>1 – skapa ett index

Om du inte använder portalen, måste ett index finnas på tjänsten innan du kan läsa in data. Det här steget definierar indexet och skickas till tjänsten. Den [Create Index (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) används för det här steget.

Obligatoriska elementen för ett index är ett namn och en samling fält. Fältsamlingen definierar strukturen för en *dokumentet*. Varje fält har ett namn, typ och attribut som avgör hur den används (till exempel om det är fulltext sökbar, filtrerbar eller hämtningsbara i sökresultat). I ett index, ett fält av typen `Edm.String` måste anges som den *nyckel* för dokumentet identitet.

Det här indexet har namnet ”hotels” och fältdefinitioner som du ser nedan. Indexdefinitionen anger en [språkanalysverktyg](index-add-language-analyzers.md) för den `description_fr` fältet eftersom det ska lagra fransk text som vi lägger till i ett senare exempel.

Klistra in det här exemplet i PowerShell för att skapa en **$body** objekt som innehåller indexschemat.

```powershell
$body = @"
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
"@
```

Ange URI: N till samlingen index på din tjänst och *hotels* index.

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11"
```

Kör kommandot med **$url**, **$headers**, och **$body** att skapa indexet på tjänsten. 

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
```
Resultatet bör se ut ungefär så här (trunkeras till de två första fälten kortfattat):

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag":  "\"0x8D6A99E2DED96B0\"",
    "name":  "hotels",
    "defaultScoringProfile":  null,
    "fields":  [
                   {
                       "name":  "hotelId",
                       "type":  "Edm.String",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  false,
                       "facetable":  false,
                       "key":  true,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
                   {
                       "name":  "baseRate",
                       "type":  "Edm.Double",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  true,
                       "facetable":  true,
                       "key":  false,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
. . .
```

> [!Tip]
> För verifiering, du kan också kontrollera listan index i portalen eller kör kommandot används för att verifiera tjänstanslutning till för att se den *hotels* index som anges i index-samlingen.

## <a name="2---load-documents"></a>2 – läsa in dokument

Använd en HTTP POST-begäran till ditt index URL-slutpunkt för att skicka dokument. REST-API: et för den här uppgiften är [Lägg till, uppdatera eller ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

Klistra in det här exemplet i PowerShell för att skapa en **$body** objekt som innehåller de dokument som du vill ladda upp. 

Denna begäran innehåller två fullständig och en partiell post. Partiell posten visar att du kan ladda upp ofullständig dokument. Den `@search.action` parametern anger hur indexering görs. Giltiga värden är ladda upp, sammanfoga, mergeOrUpload och ta bort. Beteendet mergeOrUpload antingen skapar ett nytt dokument för hotelId = 3 eller uppdaterar innehållet om det redan finns.

```powershell
$body = @"
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        }
    ]
}
"@
```

Ange slutpunkten den *hotels* docs-samling och inkludera indexåtgärden (index/hotels/docs/index).

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11"
```

Kör kommandot med **$url**, **$headers**, och **$body** att läsa in dokument till i hotellindexet.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
```
Resultatet bör likna följande exempel. Du bör se statuskod 201. En beskrivning av alla statuskoder finns i [HTTP-statuskoder (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2017_11_11.IndexResult)",
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
                  }
              ]
}
```

## <a name="3---search-an-index"></a>3 – söka i ett index

Det här steget visar hur man frågar ett index med hjälp av den [API för webbsökning dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents).

In den *hotels* docs-samling och lägga till en **search** parametern att inkludera frågesträngar. Strängen är en tom sökning och den returnerar en unranked lista över alla dokument.

```powershell
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*'
```

Kör kommando för att skicka den **$url** till tjänsten.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

Resultatet bör likna följande utdata.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#docs(*)",
    "value":  [
                  {
                      "@search.score":  1.0,
                      "hotelId":  "1",
                      "baseRate":  199.0,
                      "description":  "Best hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Fancy Stay",
                      "category":  "Luxury",
                      "tags":  "pool view wifi concierge",
                      "parkingIncluded":  false,
                      "smokingAllowed":  false,
                      "lastRenovationDate":  "2010-06-27T00:00:00Z",
                      "rating":  5,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "2",
                      "baseRate":  79.99,
                      "description":  "Cheapest hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Roach Motel",
                      "category":  "Budget",
                      "tags":  "motel budget",
                      "parkingIncluded":  true,
                      "smokingAllowed":  true,
                      "lastRenovationDate":  "1982-04-28T00:00:00Z",
                      "rating":  1,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "3",
                      "baseRate":  129.99,
                      "description":  "Close to town hall and the river",
                      "description_fr":  null,
                      "hotelName":  null,
                      "category":  null,
                      "tags":  "",
                      "parkingIncluded":  null,
                      "smokingAllowed":  null,
                      "lastRenovationDate":  null,
                      "rating":  null,
                      "location":  null
                  }
              ]
}
```

Testa några andra fråga exempel för att få en bild av syntaxen. Du kan göra en sträng-sökning, ordagrant $filter frågor, begränsa resultatmängd, omfång sökningen till specifika fält och mycket mer.

```powershell
# Query example 1
# Search the entire index for the term 'budget'
# Return only the `hotelName` field, "Roach hotel"
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=budget&$select=hotelName'

# Query example 2 
# Apply a filter to the index to find hotels cheaper than $150 per night
# Returns the `hotelId` and `description`. Two documents match.
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$filter=baseRate lt 150&$select=hotelId,description'

# Query example 3
# Search the entire index, order by a specific field (`lastRenovationDate`) in descending order
# Take the top two results, and show only `hotelName` and `lastRenovationDate`
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate'
```
## <a name="clean-up"></a>Rensa 

Du bör ta bort indexet om du inte längre behöver den. En kostnadsfri tjänst är begränsad till tre index. Du kanske vill ta bort alla index som du inte aktivt använder så att du kan gå igenom andra självstudier.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11'

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
            "description_fr": "Hôtel le moins cher en ville",
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```
