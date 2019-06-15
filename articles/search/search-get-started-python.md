---
title: 'Snabbstart: Python- och REST API: er – Azure Search'
description: Skapa, läsa in och fråga ett index med hjälp av Python, Jupyter-anteckningsböcker och Azure Search REST API.
ms.date: 06/11/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: c519cbd151ac3008593e3309930db4e9a9414e51
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056634"
---
# <a name="quickstart-create-an-azure-search-index-using-jupyter-python-notebooks"></a>Snabbstart: Skapa ett Azure Search-index med Python i Jupyter-anteckningsböcker
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [Portal](search-create-index-portal.md)
> 

Skapa en Jupyter-anteckningsbok som skapar, läser in och frågar en Azure Search-index med hjälp av Python och [Azure Search REST API: er](https://docs.microsoft.com/rest/api/searchservice/). Den här artikeln förklarar hur du bygger en anteckningsbok steg för steg, med början från grunden. Du kan också köra en färdig anteckningsbok. Du kan hämta en kopia går du till [lagringsplatsen för Azure Search-python-exempel](https://github.com/Azure-Samples/azure-search-python-samples).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande tjänster och verktyg som används i den här snabbstarten. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), vilket ger Python 3.x och Jupyter Notebooks.

+ [Skapa en Azure Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda den kostnadsfria nivån för den här snabbstarten. 

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL: en

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

1. [Logga in på Azure-portalen](https://portal.azure.com/), och i din söktjänst **översikt** sidan, hämta URL: en. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **inställningar** > **nycklar**, hämta en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara administratörsnycklar, som angetts för kontinuitet för företag om du behöver förnya ett. Du kan använda antingen den primära eller sekundära nyckeln för förfrågningar för att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-fiddler/get-url-key.png "får en HTTP-slutpunkt och åtkomstnyckel")

Alla begäranden som kräver en api-nyckel för varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="connect-to-azure-search"></a>Anslut till Azure Search

I den här uppgiften ska starta en Jupyter notebook och kontrollera att du kan ansluta till Azure Search. Det gör du genom att begära en lista över index från din tjänst. Du kan använda Anaconda Navigator på Windows med Anaconda3 för att starta en anteckningsbok.

1. Skapa en ny Python3 anteckningsbok.

1. Läsa in de bibliotek som används för att arbeta med JSON och utformningen av HTTP-begäranden i den första cellen.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. Ange begäran-element som ska vara konstanter för varje begäran i den andra cellen. Ersätt söktjänstnamnet (din-SEARCH-SERVICE-NAME) och admin API-nyckel (din-ADMIN-API-nyckel) med giltiga värden. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. Formulera begäran i cellen tredje. Den här GET-begäran riktar sig mot samlingen index för search-tjänsten och väljer name-egenskapen för befintliga index.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Kör varje steg. Om index finns innehåller svaret en lista över namn på index. I skärmbilden nedan är har tjänsten redan en azureblob-index och en realestate-us-sample-index.

   ![Python-skriptet i Jupyter-anteckningsbok med HTTP-begäranden till Azure Search](media/search-get-started-python/connect-azure-search.png "Python-skriptet i Jupyter-anteckningsbok med HTTP-begäranden till Azure Search")

   Däremot returnerar en tom index samling svaret: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 – Skapa ett index

Om du inte använder portalen, måste ett index finnas på tjänsten innan du kan läsa in data. Det här steget använder den [skapa Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) att skicka ett indexschema till tjänsten.

Obligatoriska elementen för ett index är ett namn, en samling fält och en nyckel. Fältsamlingen definierar strukturen för en *dokumentet*. Varje fält har ett namn, typ och attribut som avgör hur fältet används (till exempel om det är fulltext sökbar, filtrerbar eller hämtningsbara i sökresultat). I ett index, ett fält av typen `Edm.String` måste anges som den *nyckel* för dokumentet identitet.

Det här indexet har namnet ”hotels-quickstart” och fältdefinitioner som du ser nedan. Det är en del av ett större [Hotels indexet](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) används i andra genomgångar. Vi tas bort den i den här snabbstarten av utrymmesskäl.

1. I nästa cell, klistrar du in i följande exempel i en cell att tillhandahålla schemat. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. Formulera begäran i en annan cell. Den här PUT begäran riktar sig mot samlingen index för search-tjänsten och skapar ett index baserat på indexschema som du angav i föregående cell.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Kör varje steg.

   Svaret innehåller JSON-representation av schemat. Följande skärmbild visar bara en del av svaret.

    ![Begäran om att skapa ett index](media/search-get-started-python/create-index.png "begäran om att skapa ett index")

> [!Tip]
> Ett annat sätt att kontrollera skapandet av index är att kontrollera listan index i portalen.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – läsa in dokument

Använd en HTTP POST-begäran till ditt index URL-slutpunkt för att skicka dokument. REST-API: et är [Lägg till, uppdatera eller ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Dokument som kommer från [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) på GitHub.

1. En ny cell innehåller fyra dokument som överensstämmer med indexschemat. Ange en uppladdning åtgärd för varje dokument.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
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
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
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

2. Formulera begäran i en annan cell. Denna POST-begäran riktar sig mot samlingen docs indexets hotels-quickstart och skickar de dokument som anges i föregående steg.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Kör varje steg för att skicka dokument till ett index i din söktjänst. Resultatet bör likna följande exempel. 

    ![Skicka dokument till ett index](media/search-get-started-python/load-index.png "skicka dokument till ett index")

## <a name="3---search-an-index"></a>3 – Söka i ett index

Det här steget visar hur man frågar ett index med hjälp av den [REST-API för Search-dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. Ange ett frågeuttryck som kör en tom sökning i en cell (search = *), returnerar en unranked lista (Sök poäng = 1.0) av valfria dokument. Som standard returnerar Azure Search 50 träffar i taget. Den här frågan returnerar en struktur för hela dokumentet och värden som strukturerade. Lägg till $count = true för att få en uppräkning av alla dokument i resultaten.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. Ange i följande exempel för att söka på villkoren ”hotels” och ”wifi” i en ny cell. Lägg till $select om du vill ange vilka fält som ska ingå i sökresultaten.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. Formulera en begäran i en annan cell. Den här GET-begäran riktar sig mot samlingen docs indexets hotels-quickstart och bifogar den fråga som du angav i föregående steg.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Kör varje steg. Resultatet bör likna följande utdata. 

    ![Söka i ett index](media/search-get-started-python/search-index.png "söka i ett index")

1. Testa några andra fråga exempel för att få en bild av syntaxen. Du kan ersätta söksträngen med följande exempel och kör sedan sökbegäran. 

   Ett filter: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Ta de främsta två resultat:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Ordna efter ett visst fält:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Rensa 

Du bör ta bort indexet om du inte längre behöver den. En kostnadsfri tjänst är begränsad till tre index. Du bör ta bort alla index som du inte aktivt använder för att göra plats för andra självstudier.

Det enklaste sättet att ta bort objekt är via portalen, men eftersom detta är en Python-Snabbstart följande syntax ger samma resultat:

   ```python
  url = endpoint + "indexes/hotels-quickstart" + api_version
  response  = requests.delete(url, headers=headers)
   ```

Du kan kontrollera indexet tas bort genom att begära en lista över befintliga index. Om hotels-quickstart är borta, vet du din begäran har slutförts.

```python
url = endpoint + "indexes" + api_version + "&$select=name"

response  = requests.get(url, headers=headers)
index_list = response.json()
pprint(index_list)
```

## <a name="next-steps"></a>Nästa steg

Den här snabbstarten används en förkortad version av hotellindexet som en förenkling av distribution. Du kan skapa den fullständiga versionen du prova att använda mer intressanta frågor. För att få den fullständiga versionen och alla 50 dokument, köra den **dataimport** guiden välja *hotels-sample* från inbyggda exempel datakällor.

> [!div class="nextstepaction"]
> [Snabbstart: Skapa ett index i Azure portal](search-get-started-portal.md)
