---
title: 'Snabbstart: Skapa ett sökindex i Python med REST-API:er'
titleSuffix: Azure Cognitive Search
description: I artikeln beskrivs hur du skapar ett index, läser in data och kör frågor med Python, Jupyter-anteckningsböcker och AZURE Cognitive Search REST API.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: 93fb9ec735de1abf89eb217d0f4096fcfc0afe94
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78227104"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>Snabbstart: Skapa ett Azure Cognitive Search-index i Python med Jupyter-anteckningsböcker

> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Brevbärare (REST)](search-get-started-postman.md)
> * [Portal](search-create-index-portal.md)
> 

Skapa en Jupyter-anteckningsbok som skapar, läser in och frågar efter ett Azure Cognitive Search-index med Python och [Azure Cognitive Search REST API:er](https://docs.microsoft.com/rest/api/searchservice/). I den här artikeln beskrivs hur du skapar en anteckningsbok steg för steg. Alternativt kan du [ladda ner och köra en färdig Jupyter Python notebook](https://github.com/Azure-Samples/azure-search-python-samples).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Följande tjänster och verktyg krävs för den här snabbstarten. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), ger Python 3.x och Jupyter bärbara datorer.

+ [Skapa en Azure Cognitive Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda den kostnadsfria nivån för den här snabbstarten. 

## <a name="get-a-key-and-url"></a>Hämta en nyckel och webbadress

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En söktjänst skapas med båda, så om du har lagt till Azure Cognitive Search i din prenumeration följer du dessa steg för att få nödvändig information:

1. [Logga in på Azure-portalen](https://portal.azure.com/)och hämta webbadressen i söktjänstens **översiktssida.** Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningsnycklar** > **Keys**får du en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara admin nycklar, som tillhandahålls för kontinuitet i verksamheten om du behöver rulla en över. Du kan använda antingen primär- eller sekundärnyckeln på begäranden om att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-get-started-postman/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomstnyckel")

Alla begäranden kräver en api-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="connect-to-azure-cognitive-search"></a>Ansluta till Azure Cognitive Search

Starta en Jupyter-anteckningsbok i den här uppgiften och kontrollera att du kan ansluta till Azure Cognitive Search. Du gör detta genom att begära en lista med index från din tjänst. I Windows med Anaconda3 kan du använda Anaconda Navigator för att starta en anteckningsbok.

1. Skapa en ny Python3-anteckningsbok.

1. I den första cellen läser du in de bibliotek som används för att arbeta med JSON och formulera HTTP-begäranden.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. I den andra cellen anger du de begärandeelement som kommer att vara konstanter på varje begäran. Ersätt söktjänstnamnet (YOUR-SEARCH-SERVICE-NAME) och admin API-nyckeln (YOUR-ADMIN-API-KEY) med giltiga värden. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   Om du får ConnectionError `"Failed to establish a new connection"`kontrollerar du att api-nyckeln är en primär eller sekundär`?` `/`administratörsnyckel och att alla inledande och avslutande tecken ( och ) är på plats.

1. Formulera begäran i den tredje cellen. Den här GET-begäran är inriktad på indexsamlingen för söktjänsten och väljer namnegenskapen för befintliga index.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Kör varje steg. Om det finns index innehåller svaret en lista med indexnamn. I skärmbilden nedan har tjänsten redan ett azureblob-index och ett realestate-us-sample-index.

   ![Python-skript i Jupyter-anteckningsbok med HTTP-begäranden till Azure Cognitive Search](media/search-get-started-python/connect-azure-search.png "Python-skript i Jupyter-anteckningsbok med HTTP-begäranden till Azure Cognitive Search")

   En tom indexsamling returnerar däremot det här svaret:`{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 – Skapa ett index

Om du inte använder portalen måste det finnas ett index på tjänsten innan du kan läsa in data. I det här steget används [REST-API:et Skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index) för att skicka ett indexschema till tjänsten.

Obligatoriska element i ett index inkluderar ett namn, en fältsamling och en nyckel. Fältsamlingen definierar strukturen för ett *dokument*. Varje fält har ett namn, en typ och ett attribut som avgör hur fältet används (till exempel om det är fulltextsökbart, filterbart eller hämtat i sökresultaten). I ett index måste ett `Edm.String` av fälten av typen anges som *nyckel* för dokumentidentitet.

Det här indexet heter "hotels-quickstart" och har de fältdefinitioner som visas nedan. Det är en delmängd av ett större [Hotels-index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) som används i andra genomgångar. Vi trimmade den i denna snabbstart för korthet.

1. Klistra in följande exempel i en cell i nästa cell för att tillhandahålla schemat. 

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

2. Formulera begäran i en annan cell. Den här POST-begäran är inriktad på indexsamlingen för söktjänsten och skapar ett index baserat på indexschemat som du angav i föregående cell.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Kör varje steg.

   Svaret innehåller JSON-representationen av schemat. Följande skärmdump visar bara en del av svaret.

    ![Begäran om att skapa ett index](media/search-get-started-python/create-index.png "Begäran om att skapa ett index")

> [!Tip]
> Ett annat sätt att verifiera att index skapas är att kontrollera indexlistan i portalen.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Ladda dokument

Om du vill skicka dokument använder du en HTTP POST-begäran till indexets URL-slutpunkt. REST API är [Lägga till, uppdatera eller ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Dokument kommer från [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) på GitHub.

1. I en ny cell anger du fyra dokument som överensstämmer med indexschemat. Ange en överföringsåtgärd för varje dokument.

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

2. Formulera begäran i en annan cell. Den här POST-begäran riktar sig till docs-samlingen av hotels-quickstart-indexet och skickar dokumenten som angavs i föregående steg.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Kör varje steg för att skicka dokumenten till ett index i söktjänsten. Resultaten bör se ut ungefär som i följande exempel. 

    ![Skicka dokument till ett index](media/search-get-started-python/load-index.png "Skicka dokument till ett index")

## <a name="3---search-an-index"></a>3 – Söka i ett index

I det här steget visas hur du frågar ett index med [REST API FÖR sökdokument](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. I en cell anger du ett frågeuttryck som kör en tom sökning (search=*), som returnerar en oranerad lista (sökpoäng = 1,0) av godtyckliga dokument. Som standard returnerar Azure Cognitive Search 50 matchningar åt gången. Som strukturerad returnerar den här frågan en hel dokumentstruktur och alla värden. Lägg till $count=true för att få räkna alla dokument i resultatet.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. I en ny cell, ge följande exempel för att söka på termerna "hotell" och "wifi". Lägg till $select för att ange vilka fält som ska inkluderas i sökresultaten.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. Formulera en begäran i en annan cell. Den här GET-begäran riktar sig till dokumentsamlingen för snabbstartsindex för hotell och kopplar den fråga som du angav i föregående steg.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Kör varje steg. Resultaten bör se ut ungefär som följande utdata. 

    ![Sök i ett index](media/search-get-started-python/search-index.png "Sök i ett index")

1. Prova några andra frågeexempel för att få en känsla för syntaxen. Du kan `searchstring` ersätta med följande exempel och sedan köra sökbegäran igen. 

   Använda ett filter: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Ta de två bästa resultaten:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Order efter ett visst fält:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Rensa

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken **Alla resurser** eller **Resursgrupper** i fönstret för vänsternavigering.

Om du använder en kostnadsfri tjänst bör du komma ihåg att du är begränsad till tre index, indexerare och datakällor. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Som en förenkling använder denna snabbstart en förkortad version av Hotels index. Du kan skapa den fullständiga versionen för att prova mer intressanta frågor. Om du vill hämta den fullständiga versionen och alla 50 dokument kör du guiden **Importera data** och väljer exempel *på hotell* från de inbyggda exempeldatakällorna.

> [!div class="nextstepaction"]
> [Snabbstart: Skapa ett index i Azure-portalen](search-get-started-portal.md)
