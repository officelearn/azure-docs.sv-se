---
title: 'Snabb start: skapa ett Sök index i python med REST API: er'
titleSuffix: Azure Cognitive Search
description: Förklarar hur du skapar ett index, läser in data och kör frågor med python, Jupyter Notebooks och Azure Kognitiv sökning REST API.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 08/20/2020
ms.custom: devx-track-python
ms.openlocfilehash: dca53dc27eacc5c7e04bbf6cb5df82a8e8da0dfc
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694559"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>Snabb start: skapa ett Azure Kognitiv sökning-index i python med Jupyter-anteckningsböcker

> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](./search-get-started-powershell.md)
> * [C#](./search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Portal](search-get-started-portal.md)
> 

Skapa en Jupyter-anteckningsbok som skapar, läser in och skickar frågor till ett Azure Kognitiv sökning-index med python och [Azure KOGNITIV sökning REST-API: er](/rest/api/searchservice/). Den här artikeln förklarar hur du skapar en antecknings bok steg för steg. Du kan också [Hämta och köra en färdig Jupyter python-anteckningsbok](https://github.com/Azure-Samples/azure-search-python-samples).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Följande tjänster och verktyg krävs för den här snabb starten. 

+ [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section)som tillhandahåller python 3. x-och Jupyter-anteckningsböcker.

+ [Skapa en Azure kognitiv sökning-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda den kostnads fria nivån för den här snabb starten. 

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomst nyckel](media/search-get-started-rest/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="connect-to-azure-cognitive-search"></a>Ansluta till Azure Kognitiv sökning

I den här uppgiften startar du en Jupyter-anteckningsbok och kontrollerar att du kan ansluta till Azure Kognitiv sökning. Du gör detta genom att begära en lista över index från din tjänst. I Windows med Anaconda3 kan du använda Anaconda-navigatören för att starta en bärbar dator.

1. Skapa en ny python3 Notebook.

1. I den första cellen läser du in de bibliotek som används för att arbeta med JSON och att utforma HTTP-begäranden.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. I den andra cellen skriver du in de begär ande element som ska vara konstanter på varje begäran. Ersätt Sök tjänst namnet (ditt-SEARCH-SERVICE-NAME) och Admin API-nyckeln (din-ADMIN-API-nyckel) med giltiga värden. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2020-06-30'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   Om du får ConnectionError `"Failed to establish a new connection"` kontrollerar du att API-nyckeln är en primär eller sekundär administratörs nyckel och att alla inledande och avslutande tecken ( `?` och `/` ) är på plats.

1. Formulera begäran i den tredje cellen. Den här GET-begäran riktar in sig på index samlingen för Sök tjänsten och väljer egenskapen namn för befintliga index.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Kör varje steg. Om index finns innehåller svaret en lista över index namn. I skärm bilden nedan har tjänsten redan ett azureblob-index och ett realestate-exempel index.

   ![Python-skript i Jupyter Notebook med HTTP-begäranden till Azure Kognitiv sökning](media/search-get-started-python/connect-azure-search.png "Python-skript i Jupyter Notebook med HTTP-begäranden till Azure Kognitiv sökning")

   En tom index samling returnerar däremot följande svar: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 – Skapa ett index

Om du inte använder portalen måste det finnas ett index för tjänsten innan du kan läsa in data. I det här steget används [create Index REST API](/rest/api/searchservice/create-index) för att skicka ett index schema till tjänsten.

Obligatoriska element i ett index inkluderar ett namn, en fält samling och en nyckel. Fält samlingen definierar strukturen för ett *dokument*. Varje fält har ett namn, typ och attribut som avgör hur fältet används (till exempel om det är full text sökbar, filtrerings bar eller hämtnings bar i Sök resultaten). I ett index måste ett av fälten av typen anges `Edm.String` som *nyckel* för dokument identitet.

Det här indexet heter "Hotels-snabb start" och innehåller fält definitionerna som visas nedan. Det är en del av ett större [hotell index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) som används i andra genom gångar. Vi trimmade det i den här snabb starten för det kortfattat.

1. I nästa cell klistrar du in följande exempel i en cell för att ange schemat. 

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

2. Formulera begäran i en annan cell. Den här POST-begäran riktar in sig på index samlingen för Sök tjänsten och skapar ett index baserat på det index schema som du angav i föregående cell.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Kör varje steg.

   Svaret innehåller en JSON-representation av schemat. Följande skärm bild visar bara en del av svaret.

    ![Begäran om att skapa ett index](media/search-get-started-python/create-index.png "Begäran om att skapa ett index")

> [!Tip]
> Ett annat sätt att kontrol lera att skapa index är att kontrol lera listan index i portalen.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 Läs in dokument

Om du vill skicka dokument använder du en HTTP POST-begäran till indexets URL-slutpunkt. REST API [lägga till, uppdatera eller ta bort dokument](/rest/api/searchservice/addupdate-or-delete-documents). Dokumenten kommer från [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) på GitHub.

1. I en ny cell, ange fyra dokument som stämmer överens med index schemat. Ange en uppladdnings åtgärd för varje dokument.

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
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
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

2. Formulera begäran i en annan cell. Den här POST-begäran riktar in dokument samlingen för hotell-snabb starts indexet och skickar dokumenten som tillhandahålls i föregående steg.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Kör varje steg för att skicka dokumenten till ett index i din Sök tjänst. Resultatet bör se ut som i följande exempel. 

    ![Skicka dokument till ett index](media/search-get-started-python/load-index.png "Skicka dokument till ett index")

## <a name="3---search-an-index"></a>3 – Söka i ett index

Det här steget visar hur du frågar ett index med hjälp av [Sök dokument REST API](/rest/api/searchservice/search-documents).

1. I en cell anger du ett frågeuttryck som kör en tom sökning (search = *) och returnerar en lista med en lista med en lista (Sök poäng = 1,0) av godtyckliga dokument. Som standard returnerar Azure Kognitiv sökning 50 matchningar åt gången. Som strukturerad returnerar den här frågan en hel dokument struktur och-värden. Lägg till $count = sant för att få ett antal dokument i resultaten.

   ```python
   searchstring = '&search=*&$count=true'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. I en ny cell anger du följande exempel för att söka efter termerna "Hotels" och "WiFi". Lägg till $select för att ange vilka fält som ska ingå i Sök resultaten.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)   
   ```

   Resultaten bör likna följande utdata. 

    ![Sök i ett index](media/search-get-started-python/search-index.png "Sök i ett index")

1. Använd sedan ett $filter-uttryck som bara väljer hotell med en klassificering som är större än 4. 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)     
   ```

1. Som standard returnerar sökmotorn de översta 50 dokumenten men du kan använda Top och SKIP för att lägga till sid brytning och välja hur många dokument i varje resultat. Den här frågan returnerar två dokument i varje resultat uppsättning.

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. I det här sista exemplet använder $orderby för att sortera resultaten efter ort. Det här exemplet innehåller fält från adress samlingen.

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

## <a name="clean-up"></a>Rensa

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

Som en förenkling använder den här snabb starten en förkortad version av hotell indexet. Du kan skapa en fullständig version för att prova fler intressanta frågor. Om du vill hämta den fullständiga versionen och alla 50-dokument kör du guiden **Importera data** och väljer *hotell-exempel* från de inbyggda exempel data källorna.

> [!div class="nextstepaction"]
> [Snabb start: skapa ett index i Azure Portal](search-get-started-portal.md)