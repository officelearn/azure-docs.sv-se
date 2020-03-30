---
title: 'Snabbstart: Skapa ett sökindex i Node.js med REST-API:er'
titleSuffix: Azure Cognitive Search
description: 'I den här nod.js snabbstart, lära sig hur du skapar ett index, läsa in data och köra frågor på Azure Cognitive Search med JavaScript och REST API: er.'
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.devlang: nodejs
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/25/2020
ms.openlocfilehash: cbef6029b93f134f95ee54aa87ce0dd65bcdf50d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77623999"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-nodejs-using-rest-apis"></a>Snabbstart: Skapa ett Azure Cognitive Search-index i Node.js med REST-API:er
> [!div class="op_single_selector"]
> * [Javascript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [Powershell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Skapa ett Node.js-program som skapar, läser in och frågar ett Azure Cognitive Search-index. Den här artikeln visar hur du skapar programmet steg för steg. Du kan också [hämta källkoden och data och](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/) köra programmet från kommandoraden.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Vi använde följande programvara och tjänster för att bygga och testa denna snabbstart:

+ [Node.js](https://nodejs.org)

+ [NPM](https://www.npmjs.com) ska installeras av Node.js

+ Ett exempel på indexstruktur och matchande dokument finns i den här artikeln, eller från [ **snabbstartskatalogen** för](https://github.com/Azure-Samples/azure-search-javascript-samples/)

+ [Skapa en Azure Cognitive Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här snabbstarten.

Rekommenderat:

* [Visual Studio-kod](https://code.visualstudio.com)

* [Snyggare](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) och [ESLint-tillägg](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) för VSCode.

<a name="get-service-info"></a>

## <a name="get-keys-and-urls"></a>Hämta nycklar och webbadresser

Anrop till tjänsten kräver en URL-slutpunkt och en åtkomstnyckel på varje begäran. En söktjänst skapas med båda, så om du har lagt till Azure Cognitive Search i din prenumeration följer du dessa steg för att få nödvändig information:

1. [Logga in på Azure-portalen](https://portal.azure.com/)och på **sidan** Översikt för söktjänsten får du namnet på söktjänsten. Du kan bekräfta ditt tjänstnamn genom att granska slutpunktsadressen. Om slutpunktsadressen var `https://mydemo.search.windows.net`skulle `mydemo`tjänstnamnet vara .

2. I **Inställningsnycklar** > **Keys**får du en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara admin nycklar, som tillhandahålls för kontinuitet i verksamheten om du behöver rulla en över. Du kan använda antingen primär- eller sekundärnyckeln på begäranden om att lägga till, ändra och ta bort objekt.

    Hämta frågenyckeln också. Det är en bra idé att utfärda frågebegäranden med skrivskyddad åtkomst.

![Hämta tjänstens namn och administratörs- och frågenycklar](media/search-get-started-nodejs/service-name-and-keys.png)

Alla begäranden kräver en api-nyckel i huvudet på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, per begäran, mellan programmet som skickar begäran och den tjänst som hanterar den.

## <a name="set-up-your-environment"></a>Konfigurera din miljö

Börja med att öppna en Powershell-konsol eller annan miljö där du har installerat Node.js.

1. Skapa en utvecklingskatalog och `quickstart` ge den namnet:

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. Initiera ett tomt projekt med `npm init`NPM genom att köra . Acceptera standardvärdena, med undantag för licensen, som du bör ange till "MIT". 

1. Lägg till paket som kommer att vara beroende av koden och stöd i utvecklingen:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Bekräfta att du har konfigurerat projekten och dess beroenden genom att kontrollera att **filen package.json** liknar följande:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Azure_Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "nconf": "^0.10.0",
        "node-fetch": "^2.6.0"
      },
      "devDependencies": {
        "eslint": "^6.1.0",
        "eslint-config-airbnb-base": "^13.2.0",
        "eslint-config-prettier": "^6.0.0",
        "eslint-plugin-import": "^2.18.2",
        "prettier": "^1.18.2"
      }
    }
    ```

5. Skapa en fil **azure_search_config.json** för att lagra söktjänstdata:

    ```json
    {
        "serviceName" : "[SEARCH_SERVICE_NAME]",
        "adminKey" : "[ADMIN_KEY]",
        "queryKey" : "[QUERY_KEY]",
        "indexName" : "hotels-quickstart"
    }
    ```

Ersätt `[SERVICE_NAME]` värdet med namnet på söktjänsten. Ersätt `[ADMIN_KEY]` `[QUERY_KEY]` och med de nyckelvärden som du spelade in tidigare. 

## <a name="1---create-index"></a>1 - Skapa index 

Skapa en fil **hotels_quickstart_index.json**.  Den här filen definierar hur Azure Cognitive Search fungerar med de dokument som du ska läsa in i nästa steg. Varje fält identifieras av `name` a och `type`har en angiven . Varje fält har också en serie indexattribut som anger om Azure Cognitive Search kan söka, filtrera, sortera och aspekt på fältet. De flesta fälten är enkla datatyper, men vissa, till exempel, `AddressType` är komplexa typer som gör att du kan skapa omfattande datastrukturer i indexet.  Du kan läsa mer om [datatyper](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) och [indexattribut](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes)som stöds . 

Lägg till följande **i hotels_quickstart_index.json** eller [hämta filen](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels_quickstart_index.json). 

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
    

Det är god praxis att skilja detaljerna i ett visst scenario från kod som kommer att vara allmänt tillämpliga. Klassen `AzureSearchClient` som definieras i filen **AzureSearchClient.js** vet hur du konstruerar url:er för begäran, gör en begäran med hjälp av Hämta API och reagerar på statuskoden för svaret.

Börja arbeta med **AzureSearchClient.js** genom att importera **nod-fetch-paketet** och skapa en enkel klass. Isolera de utbytbara delarna av `AzureSearchClient` klassen genom att till konstruktorn skicka de olika konfigurationsvärdena till konstruktorn:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    constructor(searchServiceName, adminKey, queryKey, indexName) {
        this.searchServiceName = searchServiceName;
        this.adminKey = adminKey;
        // The query key is used for read-only requests and so can be distributed with less risk of abuse.
        this.queryKey = queryKey;
        this.indexName = indexName;
        this.apiVersion = '2019-05-06';
    }

    // All methods go inside class body here!
}

module.exports = AzureSearchClient;
```

Klassens första ansvar är att veta hur webbadresser ska skapas som du kan skicka de olika begärandena till. Skapa dessa webbadresser med instansmetoder som använder konfigurationsdata som skickas till klasskonstruktorn. Observera att url:en som de konstruerar är specifik för en API-version och måste ha ett argument som anger den versionen (i det här programmet). `2019-05-06` 

Den första av dessa metoder returnerar URL:en för själva indexet. Lägg till följande metod i klasstexten:

```javascript
getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

Nästa ansvar `AzureSearchClient` är att göra en asynkron begäran med Hämta API. Den asynkrona statiska metoden `request` tar en URL, en sträng som anger HTTP-metoden ("GET", "PUT", "POST", "DELETE"), nyckeln som ska användas i begäran och ett valfritt JSON-objekt. Variabeln `headers` mappar `queryKey` (oavsett om administratörsnyckeln eller skrivskyddad frågenyckeln) till HTTP-begäranden "api-key". Alternativen för begäran `method` innehåller alltid de `headers`som ska användas och . Om `bodyJson` det `null`inte är , är brödtexten i `bodyJson`HTTP-begäran inställd på strängrepresentationen av . Metoden `request` returnerar Hämta API:s löfte att köra HTTP-begäran.

```javascript
static async request(url, method, apiKey, bodyJson = null) {
    // Uncomment the following for request details:
    /*
    console.log(`\n${method} ${url}`);
    console.log(`\nKey ${apiKey}`);
    if (bodyJson !== null) {
        console.log(`\ncontent: ${JSON.stringify(bodyJson, null, 4)}`);
    }
    */

    const headers = {
        'content-type' : 'application/json',
        'api-key' : apiKey
    };
    const init = bodyJson === null ?
        { 
            method, 
            headers
        }
        : 
        {
            method, 
            headers,
            body : JSON.stringify(bodyJson)
        };
    return fetch(url, init);
}
```

I demosyfte, bara kasta ett undantag om HTTP-begäran inte är en framgång. I ett riktigt program skulle du förmodligen göra en del loggning och diagnos av HTTP-statuskoden i `response` från söktjänstbegäran. 
    
```javascript
static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
}
```

Lägg slutligen till metoder för att identifiera, ta bort och skapa Azure Cognitive Search-indexet. Dessa metoder har alla samma struktur:

* Hämta slutpunkten som begäran ska göras till.
* Generera begäran med lämplig slutpunkt, HTTP-verb, API-nyckel och, om det är lämpligt, en JSON-brödtext. `indexExistsAsync()`och `deleteIndexAsync()` inte har en JSON `createIndexAsync(definition)` kropp, men gör.
* `await`svaret på begäran.  
* Agera på statuskoden för svaret.
* Returnera ett löfte om ett lämpligt `this`värde (ett booleskt eller frågeresultat). 

```javascript
async indexExistsAsync() { 
    console.log("\n Checking if index exists...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "GET", this.adminKey);
    // Success has a few likely status codes: 200 or 204 (No Content), but accept all in 200 range...
    const exists = response.status >= 200 && response.status < 300;
    return exists;
}

async deleteIndexAsync() {
    console.log("\n Deleting existing index...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "DELETE", this.adminKey);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}

async createIndexAsync(definition) {
    console.log("\n Creating index...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "PUT", this.adminKey, definition);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

Bekräfta att dina metoder finns i klassen och att du exporterar klassen. **AzureSearchClient.js** yttersta omfattning bör vara:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

En objektorienterad klass var ett bra val för den potentiellt återanvändbara **AzureSearchClient.js-modulen,** men är inte nödvändig för huvudprogrammet, som du bör placera i en fil som heter **index.js**. 

Skapa **index.js** och börja med att föra in:

* **Nconf-paketet,** som ger dig flexibilitet att ange konfigurationen med JSON, miljövariabler eller kommandoradsargument.
* Data från **filen hotels_quickstart_index.json.**
* `AzureSearchClient`-modulen.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

Med [ **nconf-paketet** ](https://github.com/indexzero/nconf) kan du ange konfigurationsdata i en mängd olika format, till exempel miljövariabler eller kommandoraden. I det här exemplet används **nconf** på ett grundläggande sätt för att läsa filen **azure_search_config.json** och returnera filens innehåll som en ordlista. Med **nconf's** `get(key)` funktion kan du göra en snabb kontroll av att konfigurationsinformationen har anpassats korrekt. Slutligen returnerar funktionen konfigurationen:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME]' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

Funktionen `sleep` skapar en `Promise` som matchar efter en angiven tid. Med den här funktionen kan appen pausas i väntan på att asynkrona indexåtgärder ska slutföras och bli tillgängliga. Att lägga till en sådan fördröjning är vanligtvis bara nödvändigt i demonstrationer, tester och exempelprogram.

```javascript
function sleep(ms) {
    return(
        new Promise(function(resolve, reject) {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Slutligen anger och anropar den huvudsakliga asynkrona `run` funktionen. Den här funktionen anropar de andra funktionerna `Promise`i ordning, i väntan på vad som behövs för att lösa s.

* Hämta konfigurationen `getAzureConfiguration()` med den du skrev tidigare
* Skapa en `AzureSearchClient` ny instans som skickar in värden från konfigurationen
* Kontrollera om indexet finns och, om det gör det, ta bort det
* Skapa ett index `indexDefinition` med hjälp av inlästa från **hotels_quickstart_index.json**

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
    } catch (x) {
        console.log(x);
    }
}

run();
```

Glöm inte det sista `run()`samtalet till! Det är ingångspunkten till ditt `node index.js` program när du kör i nästa steg.

Lägg `AzureSearchClient.indexExistsAsync()` märke `AzureSearchClient.deleteIndexAsync()` till det och ta inte parametrar. Dessa funktioner `AzureSearchClient.request()` anlökar utan `bodyJson` argument. Inom `AzureSearchClient.request()`, `bodyJson === null` `true`sedan `init` är, är strukturen inställd på att vara `indexExistsAsync()` bara HTTP-verbet ("GET" för och "DELETE" för `deleteIndexAsync()`) och rubrikerna, som anger begäranden.  

Metoden `AzureSearchClient.createIndexAsync(indexDefinition)` _tar_ däremot en parameter. Funktionen `run` i `index.js`, skickar innehållet i filen **hotels_quickstart_index.json** `AzureSearchClient.createIndexAsync(indexDefinition)` till metoden. Metoden `createIndexAsync()` skickar den `AzureSearchClient.request()`här definitionen till . I `AzureSearchClient.request()`, `bodyJson === null` sedan `false`är `init` nu innehåller strukturen inte bara HTTP-verbet ("PUT") och rubrikerna, utan anger `body` indexdefinitionsdata.

### <a name="prepare-and-run-the-sample"></a>Förbereda och köra provet

Använd ett terminalfönster för följande kommandon.

1. Navigera till mappen som innehåller **filen package.json** och resten av koden.
1. Installera paketen för provet med `npm install`.  Det här kommandot hämtar de paket som koden är beroende av.
1. Kör programmet `node index.js`med .

Du bör se en serie meddelanden som beskriver de åtgärder som vidtas av programmet. Om du vill se mer information om begäranden kan du avkommentera [raderna i början av `AzureSearchClient.request()` metoden]https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/AzureSearchClient.js#L21-L27) i **AzureSearchClient.js**. 

Öppna **översikten över** söktjänsten i Azure-portalen. Välj fliken **Index.** Du bör se något liknande följande:

![Skärmbild av Azure Portal, fliken Översikt över söktjänsten, fliken Index](media/search-get-started-nodejs/create-index-no-data.png)

I nästa steg ska du lägga till data i index. 

## <a name="2---load-documents"></a>2 - Ladda dokument 

I Azure Cognitive Search är dokument datastrukturer som både är indata till indexering och utdata från frågor. Du måste publicera sådana data till indexet. Detta använder en annan slutpunkt än de åtgärder som gjordes i föregående steg. Öppna **AzureSearchClient.js** och lägg `getIndexUrl()`till följande metod efter:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Liksom `AzureSearchClient.createIndexAsync(definition)`, du behöver `AzureSearchClient.request()` en funktion som samtal och passerar i hotellets data för att vara dess kropp. I **AzureSearchClient.js** lägg till `postDataAsync(hotelsData)` efter `createIndexAsync(definition)`:

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 Dokumentindata kan vara rader i en databas, blobbar i Blob-lagring eller, som i det här exemplet, JSON-dokument på disken. Du kan antingen ladda ner [hotels.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels.json) eller skapa din egen **hotels.json-fil** med följande innehåll:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}

```

Om du vill läsa in dessa data i programmet ändrar `hotelData` du **index.js** genom att lägga till raden som refererar till längst upp:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Ändra nu `run()` funktionen i **index.js**. Det kan ta några sekunder för indexet att bli tillgängliga, så `AzureSearchClient.postDataAsync(hotelData)`lägg till en 2-sekunders paus innan du ringer:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
    } catch (x) {
        console.log(x);
    }
}
```

Kör programmet igen `node index.js`med . Du bör se en något annorlunda uppsättning meddelanden från dem du såg i steg 1. Den här gången _finns_ indexet och du bör se meddelande om att ta bort det innan appen skapar det nya indexet och publicerar data till den. 

## <a name="3---search-an-index"></a>3 – Söka i ett index

Gå tillbaka till fliken **Index** i **översikten över** söktjänsten på Azure-portalen. Indexet innehåller nu fyra dokument och förbrukar en viss mängd lagringsutrymme (det kan ta några minuter för användargränssnittet att korrekt återspegla indexets underliggande tillstånd). Klicka på det indexnamn som ska tas till **Sökutforskaren**. På den här sidan kan du experimentera med datafrågor. Prova att söka på `*&$count=true` en frågesträng och du bör få tillbaka alla dina dokument och antalet resultat. Prova med frågesträngen `historic&highlight=Description&$filter=Rating gt 4` och du bör få tillbaka ett enda dokument, med ordet "historiska" insvept i `<em></em>` taggar. Läs mer om [hur du skriver en fråga i Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-query-overview). 

Reproducera dessa frågor i kod genom att öppna **index.js** och lägga till den här koden högst upp:

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

Skriv `doQueriesAsync()` funktionen som visas nedan i samma **index.js-fil.** Den här `AzureSearchClient` funktionen tar `AzureSearchClient.queryAsync` ett objekt och använder `queries` metoden på vart och ett av värdena i matrisen. Den använder `Promise.all()` funktionen för `Promise` att returnera en enda som bara löser när alla frågor har lösts. Anropet `JSON.stringify(body, null, 4)` för att formatera frågeresultatet blir mer läsbart.

```javascript
async function doQueriesAsync(client) {
    return Promise.all(
        queries.map( async query => {
            const result = await client.queryAsync(query);
            const body = await result.json();
            const str = JSON.stringify( body, null, 4);
            console.log(`Query: ${query} \n ${str}`);
        })
    );
}
```

Ändra `run()` funktionen så att den pausar tillräckligt länge för `doQueriesAsync(client)` att indexeraren ska fungera och anropa funktionen:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
        // Data availability can take a few seconds
        await sleep(5000);
        await doQueriesAsync(client);
    } catch (x) {
        console.log(x);
    }
}
```

Om `AzureSearchClient.queryAsync(query)`du vill implementera redigera du filen **AzureSearchClient.js**. Sökning kräver en annan slutpunkt och söktermerna blir `getSearchUrl(searchTerm)` URL-argument, så lägg till funktionen tillsammans med de `getIndexUrl()` metoder och `getPostDataUrl()` metoder som du redan har skrivit.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

Funktionen `queryAsync(searchTerm)` går också i **AzureSearchClient.js** och följer `postDataAsync(data)` samma struktur som och andra frågefunktioner: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

Sökningen görs med "GET"-verbet och ingen kropp, eftersom söktermen är en del av webbadressen. Observera `queryAsync(searchTerm)` att `this.queryKey`använder , till skillnad från de andra funktioner som använde administratörsnyckeln. Frågenycklar, som namnet antyder, kan bara användas för att fråga om indexet och kan inte användas för att ändra indexet på något sätt. Frågenycklar är därför säkrare att distribuera till klientprogram.

Kör programmet `node index.js`med . Nu, förutom de tidigare stegen, kommer frågorna att skickas och resultaten skrivna till konsolen.

### <a name="about-the-sample"></a>Om exemplet

Exemplet använder en liten mängd hotelldata som är tillräckligt för att visa grunderna för att skapa och fråga ett Azure Cognitive Search-index.

Klassen **AzureSearchClient** kapslar in konfiguration, webbadresser och grundläggande HTTP-begäranden för söktjänsten. Filen **index.js** läser in konfigurationsdata för Azure Cognitive Search-tjänsten, hotelldata som ska överföras för indexering och, i sin `run` funktion, order och kör de olika åtgärderna.

Det övergripande beteendet för `run` funktionen är att ta bort Azure Cognitive Search index om det finns, skapa index, lägga till vissa data och utföra vissa frågor.  

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken **Alla resurser** eller **Resursgrupper** i fönstret för vänsternavigering.

Om du använder en kostnadsfri tjänst bör du komma ihåg att du är begränsad till tre index, indexerare och datakällor. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

I den här nod.js-snabbstarten har du arbetat igenom en serie aktiviteter för att skapa ett index, läsa in det med dokument och köra frågor. Vi gjorde vissa steg, till exempel att läsa konfigurationen och definiera frågorna, på enklast möjliga sätt. I en verklig ansökan, skulle du vilja lägga dessa frågor i separata moduler som skulle ge flexibilitet och inkapsling. 
 
Om du redan har en bakgrund i Azure Cognitive Search kan du använda det här exemplet som en språngbräda för att försöka föreslå (type-ahead eller komplettera frågor automatiskt), filter och fakulterad navigering. Om du inte har använt Azure Cognitive Search tidigare rekommenderar vi att du provar andra självstudier för att utveckla en förståelse för vad du kan skapa. Vår [dokumentationssida](https://azure.microsoft.com/documentation/services/search/) innehåller fler resurser. 

> [!div class="nextstepaction"]
> [Anropa Azure Cognitive Search från en webbsida med Javascript](https://github.com/liamca/azure-search-javascript-samples)
