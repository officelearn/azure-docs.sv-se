---
title: 'Snabb start för Node. js: Skapa, läsa in och fråga index med Azure Search REST-API: er – Azure Search'
description: Node. js-exempel för Azure Search som demonstrerar hur du skapar, läser in data i och frågar från Java Script.
author: lobrien
manager: nitinme
tags: azure-portal
services: search
ms.service: search
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: laobri
ms.openlocfilehash: b22a6371522674895465afac88e68f556ab09bcb
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656308"
---
# <a name="quickstart-create-an-azure-search-index-in-nodejs"></a>Snabbstart: Skapa ett Azure Search-index i Node. js
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Skapa ett Node. js-program som skapar, läser in och skickar frågor till ett Azure Search-index. Den här artikeln visar hur du skapar programmet steg för steg. Alternativt kan du [Ladda ned käll koden och data](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/) och köra programmet från kommando raden.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Följande tjänster, verktyg och data används i den här snabb starten.

+ [Node.js](https://nodejs.org).
+ [NPM](https://www.npmjs.com) bör installeras av Node. js.
+ En exempel index struktur och matchande dokument finns i den här artikeln, eller från [ **snabb starts** katalogen i lagrings platsen](https://github.com/Azure-Samples/azure-search-javascript-samples/).
+ [Skapa en Azure Search tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här snabb starten.

Rekommenderas

* [Visual Studio Code](https://code.visualstudio.com).
* [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) -och [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) -tillägg för VSCode.

<a name="get-service-info"></a>
## <a name="get-keys-and-urls"></a>Hämta nycklar och URL: er

Anrop till tjänsten kräver en URL-slutpunkt och en åtkomst nyckel på varje begäran. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta namnet på din Sök tjänst på sidan **Översikt över** Sök tjänsten. Du kan bekräfta tjänst namnet genom att granska slut punkts-URL: en. Om slut punkts- `https://mydemo.search.windows.net`URL: en var, är `mydemo`tjänstens namn.

2. I **Inställningar** > **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

    Hämta även frågans nyckel. Det är en bra idé att utfärda förfrågningar med skrivskyddad åtkomst.

![Hämta tjänstens namn och administratör och fråge nycklar](media/search-get-started-nodejs/service-name-and-keys.png)

Alla begär Anden kräver en API-nyckel i rubriken för varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende per begäran mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="set-up-your-environment"></a>Konfigurera din miljö

Börja med att öppna en PowerShell-konsol eller annan miljö där du har installerat Node. js.

1. Skapa en utvecklings katalog och ge den namnet `quickstart` :

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. Initiera ett tomt projekt med NPM genom att `npm init`köra. Acceptera standardvärdena, förutom för licensen, som du bör ange till "MIT". 

1. Lägg till paket som kommer att vara beroende av koden och stöd för utveckling:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Bekräfta att du har konfigurerat projekten och dess beroenden genom att kontrol lera att **Package. JSON** -filen ser ut ungefär så här:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Search Quickstart",
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
Skapa en fil **azure_search_config. JSON** för att lagra dina Sök tjänst data:

```json
{
    "serviceName" : "[SERVICE_NAME]",
    "adminKey" : "[ADMIN_KEY]",
    "queryKey" : "[QUERY_KEY]",
    "indexName" : "hotels-quickstart"
}
```

`[SERVICE_NAME]` Ersätt värdet med namnet på Sök tjänsten. Ersätt `[ADMIN_KEY]` och`[QUERY_KEY]` med de viktiga värden som du registrerade tidigare. 

## <a name="1---create-index"></a>1 – Skapa index 

Skapa en fil **hotels_quickstart_index. JSON**.  Den här filen definierar hur Azure Search fungerar med dokumenten som du läser in i nästa steg. Varje fält identifieras av en `name` och har en angiven. `type` Varje fält har också en serie med indexfiler som anger om Azure Search kan söka, filtrera, sortera och fasett vid fältet. De flesta fält är enkla data typer, men vissa, som `AddressType` är komplexa typer, som gör att du kan skapa omfattande data strukturer i ditt index.  Du kan läsa mer om [vilka data typer](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) och [index-attribut](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes)som stöds. 

Lägg till följande i **hotels_quickstart_index. JSON** eller [Ladda ned filen](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels_quickstart_index.json). 

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
    

Det är en bra idé att separera specifika scenarier från kod som kommer att vara bred att gälla. Den `AzureSearchClient` klass som definieras i filen **AzureSearchClient. js** vet hur du konstruerar begär ande-URL: er, gör en begäran med hjälp av hämtnings-API: et och reagerar på status koden för svaret.

Börja arbeta med **AzureSearchClient. js** genom att importera det **Node Fetch-** paketet och skapa en enkel klass. Isolera ändrings bara delar av `AzureSearchClient` klassen genom att skicka till dess konstruktör de olika konfigurations värdena:

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

Det första ansvaret för klassen är att veta hur du skapar URL: er som de olika förfrågningarna ska skickas till. Bygg dessa URL: er med instans metoder som använder de konfigurations data som skickas till klassen konstruktor. Observera att den URL som de konstruerar är specifik för en API-version och måste ha ett argument som anger den versionen ( `2019-05-06`i det här programmet). 

Den första av dessa metoder kommer att returnera URL: en för själva indexet. Lägg till följande metod i klass texten:

```javascript
getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

Nästa ansvar i `AzureSearchClient` gör en asynkron begäran med hämtnings-API: et. Den asynkrona statiska `request` metoden tar en URL, en sträng som anger http-metoden ("Get", "placera", "post", "Delete"), nyckeln som ska användas i begäran och ett valfritt JSON-objekt. Variabeln`queryKey` mappar (om administratörs nyckeln eller den skrivskyddade fråge nyckeln) till http-begäran för http-begäran (API-nyckel). `headers` Alternativen för begäran innehåller alltid de `method` som ska användas `headers`och. Om `bodyJson` `bodyJson`inte `null`, anges bröd texten i http-begäran till sträng representationen av. `request` Metoden returnerar det hämtnings-API: et löfte att köra http-begäran.

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

I demonstrations syfte genererar du bara ett undantag om HTTP-begäran inte lyckas. I ett verkligt program skulle du förmodligen göra viss loggning och diagnostisering av HTTP-statuskoden i `response` från search service-begäran. 
    
```javascript
static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
}
```

Slutligen lägger du till metoderna för att identifiera, ta bort och skapa Azure Search-indexet. Dessa metoder har samma struktur:

* Hämta slut punkten som begäran görs till.
* Generera begäran med lämplig slut punkt, HTTP-verb, API-nyckel och, om det behövs, en JSON-text. `indexExistsAsync()`och `deleteIndexAsync()` har ingen JSON-text, men `createIndexAsync(definition)` gör.
* `await`svaret på begäran.  
* Arbeta med svarets status kod.
* Returnera ett löfte av ett lämpligt värde (ett booleskt `this`värde, eller frågeresultaten). 

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

Bekräfta att metoderna finns i-klassen och att du exporterar klassen. Det yttersta omfånget av **AzureSearchClient. js** ska vara:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

En objektorienterad klass var ett bra val för den potentiellt återanvändbara **AzureSearchClient. js** -modulen, men är inte nödvändig för huvud programmet, som du bör placera i en fil med namnet **index. js**. 

Skapa **index. js** och börja med att sätta igång:

* **NConf** -paketet, som ger dig flexibilitet för att ange konfigurationen med JSON-, miljövariabler eller kommando rads argument.
* Data från filen **hotels_quickstart_index. JSON** .
* `AzureSearchClient`-modulen.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

Med [ **NConf** -paketet](https://github.com/indexzero/nconf) kan du ange konfigurations data i olika format, till exempel miljövariabler eller kommando raden. I det här exemplet används **NConf** på ett grundläggande sätt för att läsa filen **azure_search_config. JSON** och returnera filens innehåll som en ord lista. Medhjälp av `get(key)` NConf-funktionen kan du göra en snabb kontroll av att konfigurations informationen har anpassats korrekt. Slutligen returnerar funktionen konfigurationen:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

Funktionen skapar en `Promise` som matchar efter en angiven tids period. `sleep` Med den här funktionen kan appen pausa i väntan på att asynkrona index åtgärder ska slutföras och bli tillgänglig. Att lägga till sådan fördröjning är vanligt vis bara nödvändigt i demonstrationer, tester och exempel program.

```javascript
function sleep(ms) {
    return(
        new Promise(function(resolve, reject) {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Slutligen anger och anropar du den viktigaste `run` asynkrona funktionen. Den här funktionen anropar de andra funktionerna i ordning, vilket väntar efter behov `Promise`för att lösa s.

* Hämta konfigurationen med den `getAzureConfiguration()` du skrev tidigare
* Skapa en ny `AzureSearchClient` instans och skicka in värden från konfigurationen
* Kontrol lera om indexet finns och ta bort det
* Skapa ett index med hjälp `indexDefinition` av inläst från **hotels_quickstart_index. JSON**

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get["serviceName"]);
        
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

Glöm inte att det sista anropet till `run()`! Det är ingångs punkten för programmet när du kör `node index.js` i nästa steg.

Observera att `AzureSearchClient.indexExistsAsync()` och `AzureSearchClient.deleteIndexAsync()` inte tar parametrar. Dessa funktioner anropar `AzureSearchClient.request()` `bodyJson` utan argument. Inom `AzureSearchClient.request()`, eftersom `bodyJson === null` `indexExistsAsync()` `deleteIndexAsync()`är, är strukturen inställd på att bara vara HTTP-verbet ("Get" för och "Delete" för) och rubrikerna som anger nyckel för begäran. `init` `true`  

`AzureSearchClient.createIndexAsync(indexDefinition)` Metoden kan däremot ta en parameter. Funktionen i `index.js`skickar innehållet i filen **hotels_quickstart_index. JSON** till `AzureSearchClient.createIndexAsync(indexDefinition)` -metoden. `run` Metoden skickar denna definition till `AzureSearchClient.request()`. `createIndexAsync()` I `AzureSearchClient.request()`, eftersom `bodyJson === null` är nu `false`, `init` innehåller strukturen inte bara HTTP-verbet ( `body` "placering") och rubrikerna, utan anger till index definitions data.

### <a name="prepare-and-run-the-sample"></a>Förbereda och köra exemplet

Använd ett terminalfönster för följande kommandon.

1. Navigera till den mapp som innehåller **Package. JSON** -filen och resten av koden.
1. Installera paketen för exemplet med `npm install`.  Det här kommandot hämtar de paket som koden är beroende av.
1. Kör programmet med `node index.js`.

Du bör se en serie meddelanden som beskriver de åtgärder som utförs av programmet. Om du vill se mer information om förfrågningarna kan du ta bort kommentaren [raderna i början av `AzureSearchClient.request()` metoden]https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/AzureSearchClient.js#L21-L27) i **AzureSearchClient. js**. 

Öppna **översikten** över Sök tjänsten i Azure Portal. Välj fliken **index** . Det ser ut ungefär så här:

![Skärm bild av Azure Portal, Search Service översikt, fliken index](media/search-get-started-nodejs/create-index-no-data.png)

I nästa steg ska du lägga till data i indexet. 

## <a name="2---load-documents"></a>2 Läs in dokument 

I Azure Search är dokument data strukturer som båda är indata för indexering och utdata från frågor. Du måste publicera sådana data till indexet. Detta använder en annan slut punkt än de åtgärder som utfördes i föregående steg. Öppna **AzureSearchClient. js** och Lägg till följande metod efter `getIndexUrl()`:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Precis `AzureSearchClient.createIndexAsync(definition)`som måste du ha en funktion som `AzureSearchClient.request()` anropar och passerar i hotellet-data för att vara dess brödtext. I **AzureSearchClient. js** lägger `postDataAsync(hotelsData)` du `createIndexAsync(definition)`till efter:

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 Dokument indata kan vara rader i en databas, blobar i Blob Storage eller, som i det här exemplet, JSON-dokument på disk. Du kan antingen hämta [Hotels. JSON](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels.json) eller skapa en egen **hotell-. JSON** -fil med följande innehåll:

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

Om du vill läsa in data i programmet ändrar du **index. js** genom att lägga till raden `hotelData` som refererar till längst upp:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Ändra `run()` nu funktionen i **index. js**. Det kan ta några sekunder innan indexet blir tillgängligt, så Lägg till en paus på 2 sekunder innan du anropar `AzureSearchClient.postDataAsync(hotelData)`:

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

Kör programmet igen med `node index.js`. Du bör se en något annorlunda uppsättning meddelanden från de som du såg i steg 1. Den här gången finns indexet och du bör se ett meddelande om att ta bort det innan appen skapar det nya indexet och publicerar data till den. 

## <a name="3---search-an-index"></a>3 – Söka i ett index

Gå tillbaka till fliken **index** i översikten för din Sök tjänst på Azure Portal. Ditt index innehåller nu fyra dokument och använder en del lagrings utrymme (det kan ta några minuter innan gränssnittet återger det underliggande indexets tillstånd korrekt). Klicka på index namnet som ska hämtas till **Sök Utforskaren**. På den här sidan kan du experimentera med data frågor. Försök att söka efter en frågesträng av `*&$count=true` och du bör få tillbaka alla dokument och antalet resultat. Försök med frågesträngen `historic&highlight=Description&$filter=Rating gt 4` och du bör komma tillbaka till ett enda dokument, med ordet "historiskt" inkapslat `<em></em>` i taggar. Läs mer om [hur du skapar en fråga i Azure Search](https://docs.microsoft.com/azure/search/search-query-overview). 

Återskapa dessa frågor i kod genom att öppna **index. js** och lägga till den här koden längst upp:

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

Skriv den `doQueriesAsync()` funktion som visas nedan i samma **index. js** -fil. Den här funktionen tar `AzureSearchClient` ett objekt och `AzureSearchClient.queryAsync` tillämpar metoden på varje värde i `queries` matrisen. `Promise.all()` Funktionen används för att returnera en enskild `Promise` som bara matchar när alla frågor har åtgärd ATS. Anropet till `JSON.stringify(body, null, 4)` formaterar frågeresultatet så att det blir lättare att läsa.

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

Ändra funktionen så att den pausas tillräckligt länge för att indexeraren ska fungera och `doQueriesAsync(client)` anropa sedan funktionen: `run()`

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

Om du `AzureSearchClient.queryAsync(query)`vill implementera redigerar du filen **AzureSearchClient. js**. Sökningen kräver en annan slut punkt och Sök termerna blir URL-argument, så Lägg till `getSearchUrl(searchTerm)` funktionen tillsammans `getIndexUrl()` `getPostDataUrl()` med de metoder som du redan har skrivit.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

Funktionen går också in i **AzureSearchClient. js** och följer samma struktur som `postDataAsync(data)` och de andra funktionerna i frågan: `queryAsync(searchTerm)` 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

Sökningen görs med verbet "GET" och ingen brödtext, eftersom Sök termen är en del av URL: en. Observera att `queryAsync(searchTerm)` använder `this.queryKey`, till skillnad från andra funktioner som använde administratörs nyckeln. Frågeinställningar, som namnet antyder, kan bara användas för att skicka frågor till indexet och kan inte användas för att ändra indexet på något sätt. Det är därför säkrare att distribuera fråge nycklar till klient program.

Kör programmet med `node index.js`. Förutom föregående steg skickas frågorna och resultaten som skrivs till-konsolen.

### <a name="about-the-sample"></a>Om exemplet

Exemplet använder en liten mängd hotell data som är tillräckliga för att demonstrera grunderna för att skapa och skicka frågor till ett Azure Search-index.

**AzureSearchClient** -klassen kapslar in konfiguration, URL: er och grundläggande HTTP-begäranden för Sök tjänsten. Filen **index. js** läser in konfigurations data för Azure Searchs tjänsten, hotell data som ska laddas upp för indexering och, i den `run` fungerar, order och kör de olika åtgärderna.

Det övergripande funktions sättet `run` är att ta bort Azure Search index om det finns, skapa indexet, lägga till data och utföra vissa frågor.  

## <a name="clean-up"></a>Rensa 

När du arbetar med din egen prenumeration är det en bra idé i slutet av ett projekt för att identifiera om du fortfarande behöver de resurser som du har skapat. Resurser som har lämnats igång kostar dig pengar. Du kan ta bort resurser individuellt eller ta bort resurs gruppen för att ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.
Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

I den här Node. js-snabb starten har du arbetat genom en serie aktiviteter för att skapa ett index, läsa in det med dokument och köra frågor. Vi har gjort vissa steg, till exempel att läsa konfigurationen och definiera frågorna på det enklaste sättet. I ett verkligt program skulle du vilja placera dessa problem i separata moduler som ger flexibilitet och inkapsling. 
 
Om du redan har viss erfarenhet av Azure Search kan du använda det här exemplet som utgångspunkt för att prova förslagsställare (frågeifyllningsförslag eller Komplettera automatiskt), filter och aspektbaserad navigering. Om du är nybörjare på Azure Search rekommenderar vi att du testar andra självstudier för att utveckla en förståelse för vad du kan skapa. Vår [dokumentationssida](https://azure.microsoft.com/documentation/services/search/) innehåller fler resurser. 

> [!div class="nextstepaction"]
> [Anropa Azure Search från en webb sida med hjälp av Java Script](https://github.com/liamca/azure-search-javascript-samples)
