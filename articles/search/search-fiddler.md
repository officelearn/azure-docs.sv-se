---
title: 'Snabbstart: Utforska REST API: er i Postman - Azure Search'
description: Hur du använder Postman för att skicka HTTP-begäranden och REST API-anrop till Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 04/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 0e14131ce45d20b99c1b5d5885cb1eb24c975d03
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269120"
---
# <a name="quickstart-explore-azure-search-rest-apis-using-postman"></a>Snabbstart: Utforska Azure Search REST API: er med Postman
> [!div class="op_single_selector"]
> * [Postman](search-fiddler.md)
> * [C#](search-create-index-dotnet.md)
> * [Portalen](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

En av de enklaste sätten att utforska den [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice) använder Postman eller en annan webbplats som testar verktyget för att formulera HTTP-begäranden och granska svaren. Med hjälp av rätt verktyg och de här instruktionerna kan du skicka begäranden och visa svar innan du skriver någon kod.

> [!div class="checklist"]
> * Ladda ned ett testverktyg för webb-API
> * Hämta en nyckel och URL: en för din söktjänst
> * Anslut till Azure Search
> * Skapa ett index
> * Läs in ett index
> * Sök i ett index

Om du inte har en Azure-prenumeration kan du innan du börjar först skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) och sedan [registrera dig för Azure Search](search-create-service-portal.md).

## <a name="prerequisites"></a>Förutsättningar

Följande tjänster och verktyg som används i den här snabbstarten. 

[Skapa en Azure Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här snabbstarten. 

[Skrivbordsappen postman](https://www.getpostman.com/) eller [Telerik Fiddler](https://www.telerik.com/fiddler) används för att skicka begäranden till Azure Search.

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL: en

För att kunna göra REST-anrop behöver du tjänstens webbadress och en åtkomstnyckel för varje begäran. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

1. [Logga in på Azure-portalen](https://portal.azure.com/), och i din söktjänst **översikt** sidan, hämta URL: en. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **inställningar** > **nycklar**, hämta en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara administratörsnycklar, som angetts för kontinuitet för företag om du behöver förnya ett. Du kan använda antingen den primära eller sekundära nyckeln för förfrågningar för att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-fiddler/get-url-key.png "får en HTTP-slutpunkt och åtkomstnyckel")

Alla begäranden som kräver en api-nyckel för varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="connect-to-azure-search"></a>Anslut till Azure Search

I det här avsnittet använder du din webb-verktyget för att konfigurera anslutningar till Azure Search. Varje verktyg håller kvar begärandehuvuden för sessionen, vilket innebär att du bara behöver ange api-nyckel och innehållstyp en gång.

För båda verktygen behöver du att välja ett kommando (GET, POST, PUT och så vidare), ange en URL-slutpunkt och för vissa åtgärder, anger du JSON i brödtexten i begäran. En fullständig URL ser ut ungefär så här:

    https://<placeholder-for-your-service-name>.search.windows.net/indexes?api-version=2017-11-11

Lägg märke till HTTPS-prefixet, namnet på tjänsten, namnet på ett objekt (i det här fallet, index-samling), och [api-versionen](search-api-versions.md). Api-versionen är en obligatorisk, gemener strängen som anges som ”? api-version = 2017-11-11” för den aktuella versionen. API-versioner uppdateras regelbundet. När du inkluderar API-versionen för varje begäran får du fullständig kontroll över vilken version som används.  

Begärandehuvudet består av två element, innehållstyp samt api-nyckeln som används för att autentisera till Azure Search:

    api-key: <placeholder-api-key-for-your-service>
    Content-Type: application/json

I Postman, formulera en begäran som ser ut som följande skärmbild. Välj **hämta** som verb, anger URL: en och klickar på **skicka**. Det här kommandot ansluter till Azure Search, läser samlingen index och returnera HTTP-statuskod 200 en lyckad anslutning. Om tjänsten har redan index, omfattar även index definitioner i svaret.

![Begärandehuvud i Postman][6]

## <a name="1---create-an-index"></a>1 – Skapa ett index

I Azure Search kan skapa du vanligtvis indexet innan de läses in med data. Den [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) REST-API används för den här uppgiften. 

URL: en har utökats för att inkludera den `hotel` Indexnamnet.

Att göra detta i Postman:

1. Byt till verbet **PLACERA**
2. Kopiera i den här URL: en `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotel?api-version=2017-11-11`
3. Ange indexdefinitionen (se nedan) i brödtexten i begäran
4. Klicka på **skicka**

![Begärandetext i Postman][8]

### <a name="index-definition"></a>Indexdefinition

Fältsamlingen definierar strukturen för dokumentet. Varje dokument måste ha de här fälten och varje fält måste ha en datatyp. Strängfälten används vid fulltextsökningar, så det kan vara en bra idé att konvertera numeriska data till strängar om du vill att innehållet ska vara sökbart.

Vilka åtgärder som tillåts fastställs av fältattributen. Med REST API:er kan du använda standardåtgärder i stor utsträckning. Alla strängar blir till exempel sökbara, hämtningsbara, filtrerbara och fasettbara som standard. Du behöver ofta bara ange attribut när du vill inaktivera ett beteende.

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }

När du skickar denna begäran får du ett HTTP 201-svar som anger att indexet har skapats. Du kan kontrollera detta i portalen, men observera att portalsidan uppdateras med bestämda intervall, så det kan dröja någon minut eller två innan informationen uppdateras.

> [!TIP]
> Om HTTP 504 returneras kontrollerar du att HTTPS används i URL:en. Om HTTP 400 eller 404 returneras kontrollerar du att alla fält har kopierats och klistrats in korrekt i begärandetexten. HTTP 403 tyder vanligen på problem med API-nyckeln (antingen en ogiltig nyckel eller ett syntaxproblem när API-nyckeln angavs).

## <a name="2---load-documents"></a>2 – läsa in dokument

Att skapa ett index och att fylla det, är två separata steg. I Azure Search innehåller indexet alla sökbara data som kan användas som JSON-dokument. Den [Lägg till, uppdatera eller ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) REST-API används för den här uppgiften. 

URL: en har utökats för att inkludera den `docs` samlingar och `index` igen.

Att göra detta i Postman:

1. Byt till verbet **INLÄGG**
2. Kopiera i den här URL: en `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11`
3. Ange JSON-dokument (se nedan) i brödtexten i begäran
4. Klicka på **skicka**

![Nyttolast för begäran i Postman][10]

### <a name="json-documents-to-load-into-the-index"></a>JSON-dokument för att läsa in till indexet

Begärandetexten innehåller fyra dokument som ska läggas till i hotellindexet.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "description_fr": "Meilleur hôtel en ville",
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
             "description_fr": "Hôtel le moins cher en ville",
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
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

Efter några sekunder bör du se ett 200 HTTP-svar i sessionslistan. Detta anger att dokumenten har skapats. 

Om ett 207-svar returneras misslyckades uppladdningen av minst ett dokument. Om ett 404-svar returneras beror det på ett syntaxfel i begärandehuvudet eller i begärandetexten. Kontrollera att du har ändrat slutpunkten så att den inkluderar följande: `/docs/index`.

> [!Tip]
> För vissa utvalda datakällor kan du välja en alternativ *indexerare* för att förenkla och minska mängden kod som krävs för indexering. Mer information finns i [Indexer operations](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) (Indexeringsåtgärder).


## <a name="3---search-an-index"></a>3 – Söka i ett index

Nu när ett index och dokument har lästs in kan du skicka frågor mot dem med hjälp av [söka efter dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents) REST API.

URL: en har utökats för att inkludera en frågesträng som anges med hjälp av Sök-operator.

Att göra detta i Postman:

+ Byt till verbet **hämta**
+ Kopiera i den här URL: en `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2017-11-11`
+ Klicka på **skicka**

Den här frågan söker på termen ”motel” (motell) och returnerar antalet dokument i sökresultaten. Begäran och svar bör se ut som skärmbilden från Postman nedan efter att du klickat på **Skicka**. Statuskoden ska vara 200.

 ![Frågesvar för Postman][11]


## <a name="get-index-properties"></a>Hämta egenskaper för frågeindex
Du kan också avfråga systeminformationen för att visa antalet dokument och lagringsanvändningen: `https://mydemo.search.windows.net/indexes/hotels/stats?api-version=2017-11-11`

Din begäran i Postman borde se ut som på bilden nedan. Svaret innehåller ett dokumentantal och det diskutrymme som används uttryckt i byte.

 ![Systemfråga i Postman][12]

Observera att syntaxen för API-versionen ser annorlunda ut här. Denna begäran använder `?` för att lägga till API-versionen. Den `?` skiljer URL-sökvägen från frågesträngen, medan tecknet & separerar varje ”name = värde-par i frågesträngen. I den här frågan är API-versionen det första och enda objektet i frågesträngen.

Mer information om det här API:t finns i [Get Index Statistics (REST)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) (Hitta indexstatistik (REST)).


## <a name="use-fiddler"></a>Med hjälp av Fiddler

Det här avsnittet motsvarar föregående avsnitt, endast med Fiddler skärmbilder och instruktioner

### <a name="connect-to-azure-search"></a>Anslut till Azure Search

Formulera en begäran som ser ut som i följande skärmbild. Välj **hämta** som verb. Fiddler lägger till `User-Agent=Fiddler`. Du kan klistra in två ytterligare begärandehuvuden på de nya raderna under. Inkludera innehållstypen och tjänstens API-nyckel med hjälp av tjänstens administratörsnyckel.

Kopiera i en modifierad version av den här URL: en för target och: `https://<placeholder-for-your-service-name>.search.windows.net/indexes?api-version=2017-11-11`

![Begärandehuvud i Fiddler][1]

> [!Tip]
> Stänga av Webbtrafiken att dölja drabbas av onödiga och orelaterade HTTP-aktivitet. I Fiddler's **filen** menyn inaktivera **Capture Traffic**. 

### <a name="1---create-an-index"></a>1 – Skapa ett index

Byt till verbet **PLACERA**. Kopiera en modifierad version av den här URL: `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotel?api-version=2017-11-11`. Kopiera indexdefinitionen ovan till begärandetexten. Sidan bör likna följande skärmbild. Klicka på **kör** uppe till höger för att skicka din färdiga begäran.

![Begärandetext i Fiddler][7]

### <a name="2---load-documents"></a>2 – läsa in dokument

Byt till verbet **POST**. Ändra webbadressen så att den även inkluderar `/docs/index`. Kopiera dokumenten till begärandetexten, ungefär som på skärmbilden nedan, och kör sedan begäran.

![Nyttolast för begäran i Fiddler][9]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Tips för att köra exempelfrågor i Fiddler

Följande exempelfråga kommer från artikeln om [sökindexåtgärder (Azure Search-API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Många av exempelfrågorna i den här artikeln innehåller blanksteg, som inte är tillåtna i Fiddler. Ersätt varje blanksteg med ett plustecken (+) innan du klistrar in frågesträngen innan du försöker köra frågan i Fiddler.

**Innan blankstegen ersätts (i lastRenovationDate desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11

**När blankstegen har ersatts med + (i lastRenovationDate + desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2017-11-11

### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Tips för att visa indexstatistik i Fiddler

Klicka på fliken **Inspectors** (Inspektörer) i Fiddler, klicka på fliken **Headers** (Huvuden) och välj sedan JSON-formatet. Du bör se antalet dokument och lagringsstorleken (i kB).

## <a name="next-steps"></a>Nästa steg

REST-klienterna är ovärderliga vid improviserade undersökningar, men nu när du vet hur REST-API:er fungerar kan du fortsätta med kod. Nästföljande steg finns i följande länkar:

+ [Snabbstart: Skapa ett index med .NET SDK](search-create-index-dotnet.md)
+ [Snabbstart: Skapa ett index (REST) med hjälp av PowerShell](search-create-index-rest-api.md)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png