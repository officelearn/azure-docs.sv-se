---
title: ta med fil
description: ta med fil
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: include
ms.custom: include file
ms.date: 11/17/2020
ms.openlocfilehash: c84a7291e342a1acc465732cfbc350c571bba74d
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714223"
---
Artikeln använder ett Visual Studio Code-tillägg (för hands version) för Azure Kognitiv sökning REST-API: er.

> [!IMPORTANT] 
> Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="prerequisites"></a>Förutsättningar

Följande tjänster och verktyg krävs för den här snabb starten. 

+ [Visual Studio Code](https://code.visualstudio.com/download)

+ [Azure Kognitiv sökning för Visual Studio Code (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)

+ [Skapa en Azure kognitiv sökning-tjänst](../search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här snabb starten. 

## <a name="copy-a-key-and-url"></a>Kopiera en nyckel och en URL

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomst nyckel](../media/search-get-started-rest/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="install-the-extension"></a>Installera tillägget

Börja med att öppna [vs Code](https://code.visualstudio.com). Välj fliken **tillägg** i aktivitets fältet och Sök sedan efter *Azure-kognitiv sökning*. Hitta tillägget i Sök resultaten och välj **Installera**.

![VS Code Extension-fönstret](../media/search-get-started-rest/download-extension.png "Laddar ned VS Code-tillägget")

Alternativt kan du installera [Azure kognitiv sökning-tillägget](https://aka.ms/vscode-search) från vs Code-marknadsplatsen i en webbläsare.

Du bör se en ny Azure-flik som visas i aktivitets fältet om du inte redan har det.

![VS Code Azure-fönstret](../media/search-get-started-rest/azure-pane.png "Azure-fönstret i VS Code")

## <a name="connect-to-your-subscription"></a>Ansluta till din prenumeration

Välj **Logga in på Azure...** och logga in på ditt Azure-konto.

Du bör se vilka prenumerationer som visas. Välj prenumerationen om du vill visa en lista över Sök tjänsterna i prenumerationen.

![VS Code Azure-prenumerationer](../media/search-get-started-rest/subscriptions.png "Prenumerationer i VS-kod")

Om du vill begränsa vilka prenumerationer som visas öppnar du kommando paletten (Ctrl + Shift + P eller cmd + Shift + P) och söker efter *Azure* eller *väljer prenumerationer*. Det finns även kommandon som är tillgängliga för att logga in och ut från ditt Azure-konto.

När du expanderar Sök tjänsten visas träd objekt för var och en av Kognitiv sökning resurser: index, data källor, indexerare, färdighetsuppsättningar och synonymer.

![VS Code Azure Search-träd](../media/search-get-started-rest/search-tree.png "VS Code Azure Search-träd")

Dessa träd objekt kan expanderas för att visa alla resurser som du har i din Sök tjänst

## <a name="1---create-an-index"></a>1 – Skapa ett index

För att komma igång med Azure Kognitiv sökning måste du först skapa ett sökindex. Detta görs med hjälp av [create Index REST API](/rest/api/searchservice/create-index). 

Med VS Code-tillägget behöver du bara bekymra dig om bröd texten i begäran. I den här snabb starten tillhandahåller vi en exempel index definition och motsvarande dokument.

### <a name="index-definition"></a>Indexdefinition

Index definitionen nedan är ett exempel schema för fiktiva hotell.

`fields`Samlingen definierar dokument strukturen i Sök indexet. Varje fält har en datatyp och ett antal ytterligare attribut som avgör hur fältet kan användas.

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

Om du vill skapa ett nytt index högerklickar du på **index** och väljer sedan **Skapa nytt index**. En redigerare med ett namn som liknar `indexes-new-28c972f661.azsindex` visas i popup-fönstret. 

Klistra in index definitionen från ovanför i fönstret. Spara filen och välj **Ladda upp** när du tillfrågas om du vill uppdatera indexet. Detta skapar indexet och det kommer att vara tillgängligt i trädvyn.

![GIF för att skapa ett index](../media/search-get-started-rest/create-index.gif)

Om det uppstår ett problem med index definitionen bör du se ett fel meddelande med popup-meddelanden som förklarar felet.

![Fel meddelande för Create index](../media/search-get-started-rest/create-index-error.png)

Om detta inträffar åtgärdar du problemet och sparar filen igen.

## <a name="2---load-documents"></a>2 Läs in dokument

Att skapa ett index och att fylla det, är två separata steg. I Azure Kognitiv sökning innehåller indexet alla sökbara data. I det här scenariot tillhandahålls data som JSON-dokument. REST API för att [lägga till, uppdatera eller ta bort dokument](/rest/api/searchservice/addupdate-or-delete-documents) används för den här uppgiften. 

Lägga till nya dokument i VS Code:

1. Expandera det `hotels-quickstart` index som du har skapat. Högerklicka på **dokument** och välj **Skapa nytt dokument**.

    ![Skapa ett dokument](../media/search-get-started-rest/create-document.png)

2. Då öppnas en JSON-redigerare som har härlett schemat för ditt index.

    ![Skapa ett dokument-JSON](../media/search-get-started-rest/create-document-2.png)

3. Klistra in i JSON nedan och spara sedan filen. En uppmaning öppnas som ber dig bekräfta dina ändringar. Klicka på **överför** för att spara ändringarna.

    ```json
    {
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
        } 
    }
    ```

4. Upprepa den här processen för de tre återstående dokumenten

    Dokument 2:
    ```json
    {
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
        } 
    }
    ```

    Dokument 3:
    ```json
    {
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
        } 
    }
    ```

    Dokument 4:
    ```json
    {
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
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
    ```

Nu bör du se alla fyra dokument som är tillgängliga i avsnittet dokument.

![status efter överföring av alla dokument](../media/search-get-started-rest/create-document-finish.png)

## <a name="3---search-an-index"></a>3 – Söka i ett index

Nu när indexet och dokument uppsättningen har lästs in kan du skicka frågor mot dem med hjälp av [Sök dokument REST API](/rest/api/searchservice/search-documents).

För att göra detta i VS Code:

1. Högerklicka på det index som du vill söka efter och välj **Sök index**. Då öppnas en redigerare med ett namn som liknar `sandbox-b946dcda48.azs` .

    ![sökvy för tillägg](../media/search-get-started-rest/search-vscode.png)

2. En enkel fråga fylls i automatiskt. Tryck på **CTRL + ALT + r** eller **cmd + Alt + R** för att skicka frågan. Resultaten visas i ett fönster till vänster.

    ![Sök resultat i tillägg](../media/search-get-started-rest/search-results.png)


### <a name="example-queries"></a>Exempelfrågor

Testa några andra exempel frågor för att få en känsla för syntaxen. Det finns fyra ytterligare frågor som du kan prova. Du kan lägga till flera frågor i samma redigerare. När du trycker på **CTRL + ALT + r** eller **cmd + Alt + R** avgör linjen vilken fråga som ska skickas.

![frågor och resultat sida vid sida](../media/search-get-started-rest/all-searches.png)

I den första frågan söker vi `boutique` endast efter `select` vissa fält. Det är bäst att `select` använda de fält som du behöver eftersom du kan lägga till svars tid för dina frågor genom att dra tillbaka onödiga data. Frågan anger också `$count=true` hur många resultat som ska returneras med Sök resultaten.

```
// Query example 1 - Search `boutique` with select and return count
search=boutique&$count=true&$select=HotelId,HotelName,Rating,Category
```

I nästa fråga anger vi Sök termen `wifi` och inkluderar även ett filter för att endast returnera resultat där tillstånd är lika med `'FL'` . Resultaten sorteras också efter Hotellets `Rating` .

```
// Query example 2 - Search with filter, orderBy, select, and count
search=wifi&$filter=Address/StateProvince eq 'FL'&$select=HotelId,HotelName,Rating&$orderby=Rating desc
```

Därefter är sökningen begränsad till ett enda sökbart fält med hjälp av `searchFields` parametern. Det här är ett bra alternativ för att göra frågan mer effektiv om du vet att du bara är intresse rad av matchningar i vissa fält.

```
// Query example 3 - Limit searchFields
search=submlime cliff&$select=HotelId,HotelName,Rating&searchFields=HotelName
```

Ett annat vanligt alternativ att inkludera i en fråga är `facets` . Med ansikte kan du bygga ut filter i användar gränssnittet så att det blir lättare för användarna att veta vilka värden de kan filtrera ned till.

```
// Query example 4 - Take the top two results, and show only HotelName and Category in the results
search=*&$select=HotelId,HotelName,Rating&searchFields=HotelName&facet=Category
```

## <a name="open-index-in-the-portal"></a>Öppna index i portalen

Om du vill visa din Sök tjänst i portalen högerklickar du på namnet på Sök tjänsten och väljer **Öppna i portalen**. Det tar dig till Sök tjänsten i Azure Portal.