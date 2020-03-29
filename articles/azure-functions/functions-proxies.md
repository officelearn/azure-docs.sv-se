---
title: Arbeta med proxyservrar i Azure Functions
description: Översikt över hur du använder Azure Functions Proxies
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: 09e4616bc7cbb4361ad067ed64984ed95e9a20c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849198"
---
# <a name="work-with-azure-functions-proxies"></a>Arbeta med Azure Functions-proxyservrar

I den här artikeln beskrivs hur du konfigurerar och arbetar med Azure Functions Proxies. Med den här funktionen kan du ange slutpunkter på funktionsappen som implementeras av en annan resurs. Du kan använda dessa proxyservrar för att dela upp ett stort API i flera funktionsappar (som i en mikrotjänstarkitektur), samtidigt som du presenterar en enda API-yta för klienter.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Standardfunktioner fakturering gäller för proxykörningar. Mer information finns i [prissättning för Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-proxy"></a><a name="create"></a>Skapa en proxy

I det här avsnittet visas hur du skapar en proxy i funktionsportalen.

1. Öppna [Azure-portalen]och gå sedan till din funktionsapp.
2. Välj **Ny proxy**i den vänstra rutan .
3. Ange ett namn för din proxy.
4. Konfigurera slutpunkten som visas i den här funktionsappen genom att ange **vägmallen** och **HTTP-metoderna**. Dessa parametrar fungerar enligt reglerna för [HTTP-utlösare].
5. Ange **backend-URL:en** till en annan slutpunkt. Den här slutpunkten kan vara en funktion i en annan funktionsapp eller vara något annat API. Värdet behöver inte vara statiskt och det kan referera till [programinställningar] och [parametrar från den ursprungliga klientbegäran].
6. Klicka på **Skapa**.

Din proxy finns nu som en ny slutpunkt i funktionsappen. Ur ett klientperspektiv motsvarar det en HttpTrigger i Azure Functions. Du kan prova din nya proxy genom att kopiera proxy-URL:en och testa den med din favorit HTTP-klient.

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>Ändra begäranden och svaren

Med Azure Functions Proxies kan du ändra begäranden till och svar från backend. Dessa omvandlingar kan använda variabler enligt definitionen i [Använd variabler].

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>Ändra backend-begäran

Som standard initieras backend-begäran som en kopia av den ursprungliga begäran. Förutom att ange backend-URL:en kan du göra ändringar i PARAMETRARNA HTTP,headers och query string. De ändrade värdena kan referera till [programinställningar] och [parametrar från den ursprungliga klientbegäran].

Backend-begäranden kan ändras i portalen genom att expandera avsnittet *för åsidosättning* av begäran på proxyinformationssidan. 

### <a name="modify-the-response"></a><a name="modify-response"></a>Ändra svaret

Som standard initieras klientsvaret som en kopia av backend-svaret. Du kan göra ändringar i svarets statuskod, orsaksfras, rubriker och brödtext. De ändrade värdena kan referera till [programinställningar,] [parametrar från den ursprungliga klientbegäran]och parametrar från [backend-svaret].

Backend-begäranden kan ändras i portalen genom att utöka avsnittet för åsidosättning av *svarsförståelse* på proxyinformationssidan. 

## <a name="use-variables"></a><a name="using-variables"></a>Använda variabler

Konfigurationen för en proxy behöver inte vara statisk. Du kan villkora att den använder variabler från den ursprungliga klientbegäran, backend-svaret eller programinställningarna.

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>Referera till lokala funktioner
Du kan `localhost` använda för att referera till en funktion i samma funktionsapp direkt, utan en proxybegäran för rundturer.

`"backendurl": "https://localhost/api/httptriggerC#1"`refererar till en lokal HTTP-utlöst funktion vid rutten`/api/httptriggerC#1`

 
>[!Note]  
>Om funktionen använder *funktions-, administratörs- eller* sysauktoriseringsnivåer måste du ange koden och clientId enligt den ursprungliga funktionsadressen. I det här fallet skulle `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` referensen se ut: Vi rekommenderar att du lagrar dessa nycklar i [programinställningarna] och refererar till dem i dina proxyservrar. På så sätt undviker du att lagra hemligheter i källkoden. 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>Parametrar för referensbegäran

Du kan använda begärandeparametrar som indata till backend-URL-egenskapen eller som en del av att ändra begäranden och svar. Vissa parametrar kan vara bundna från den flödesmall som anges i basproxykonfigurationen, och andra kan komma från egenskaper för den inkommande begäran.

#### <a name="route-template-parameters"></a>Parametrar för flödesmall
Parametrar som används i flödesmallen kan refereras med namn. Parameternamnen omges av klammerparenteser ({}).

Om en proxy till exempel har en `/pets/{petId}`flödesmall, till exempel, `{petId}`kan backend-URL:en innehålla värdet för , som i `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Om flödesmallen avslutas i ett `/api/{*restOfPath}`jokertecken, till exempel , är värdet `{restOfPath}` en strängrepresentation av de återstående sökvägssegmenten från den inkommande begäran.

#### <a name="additional-request-parameters"></a>Ytterligare parametrar för begäran
Förutom parametrarna för flödesmallen kan följande värden användas i konfigurationsvärden:

* **{request.method}**: DEN HTTP-metod som används på den ursprungliga begäran.
* **{request.headers.\< HeaderName\>}**: Ett sidhuvud som kan läsas från den ursprungliga begäran. Ersätt * \<HeaderName\> * med namnet på det sidhuvud som du vill läsa. Om huvudet inte inkluderas på begäran blir värdet den tomma strängen.
* **{request.querystring.\< ParameterName\>}**: En frågesträngparameter som kan läsas från den ursprungliga begäran. Ersätt * \<ParameterName\> * med namnet på den parameter som du vill läsa. Om parametern inte ingår i begäran blir värdet den tomma strängen.

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>Referensparametrar för backend-svar

Svarsparametrar kan användas som en del av att ändra svaret på klienten. Följande värden kan användas i config-värden:

* **{backend.response.statusCode}**: DEN HTTP-statuskod som returneras på backend-svaret.
* **{backend.response.statusReason}**: HTTP-orsaksfrasen som returneras på backend-svaret.
* **{backend.response.headers.\< HeaderName\>}**: Ett sidhuvud som kan läsas från backend-svaret. Ersätt * \<HeaderName\> * med namnet på det sidhuvud som du vill läsa. Om huvudet inte ingår i svaret blir värdet den tomma strängen.

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>Referensprograminställningar

Du kan också referera till [programinställningar som definierats för funktionsappen](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) genom att omge inställningsnamnet med procenttecken (%).

En backend-URL för *https://%ORDER_PROCESSING_HOST%/api/orders* skulle till exempel ha %ORDER_PROCESSING_HOST%" ersatt med värdet för inställningen ORDER_PROCESSING_HOST.

> [!TIP] 
> Använd programinställningar för backend-värdar när du har flera distributioner eller testmiljöer. På så sätt kan du se till att du alltid pratar med rätt back-end för den miljön.

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>Felsöka proxyservrar

Genom att `"debug":true` lägga till flaggan `proxies.json` i någon proxy i din du kommer att möjliggöra felsökning loggning. Loggar lagras i `D:\home\LogFiles\Application\Proxies\DetailedTrace` och nås via de avancerade verktygen (kudu). Alla HTTP-svar innehåller `Proxy-Trace-Location` också ett huvud med en URL för att komma åt loggfilen.

Du kan felsöka en proxy från `Proxy-Trace-Enabled` klientsidan `true`genom att lägga till en rubrikuppsättning i . Detta kommer också att logga en spårning till filsystemet och returnera spårnings-URL:en som ett huvud i svaret.

### <a name="block-proxy-traces"></a>Blockera proxyspårningar

Av säkerhetsskäl kanske du inte vill tillåta att alla som ringer din tjänst genererar en spårning. De kommer inte att kunna komma åt spårningsinnehållet utan dina inloggningsuppgifter, men generera spårning förbrukar resurser och exponerar att du använder Funktion Proxyservrar.

Inaktivera spår helt och `"debug":false` hållet genom att `proxies.json`lägga till en viss proxy i din .

## <a name="advanced-configuration"></a>Avancerad konfiguration

Proxyservrarna som du konfigurerar lagras i en *proxyxies.json-fil,* som finns i roten till en funktionsappkatalog. Du kan redigera filen manuellt och distribuera den som en del av appen när du använder någon av de [distributionsmetoder](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) som Functions stöder. 

> [!TIP] 
> Om du inte har ställt in någon av distributionsmetoderna kan du också arbeta med *filen proxies.json* i portalen. Gå till din funktionsapp, välj **Plattformsfunktioner**och välj sedan **App Service Editor**. På så sätt kan du visa hela filstrukturen i funktionsappen och sedan göra ändringar.

*Proxies.json* definieras av ett proxyobjekt, som består av namngivna proxyservrar och deras definitioner. Om redigeraren stöder det kan du referera till ett [JSON-schema](http://json.schemastore.org/proxies) för kodkomplettering. En exempelfil kan se ut så här:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Varje proxy har ett eget namn, till exempel *proxy1* i föregående exempel. Motsvarande proxydefinitionsobjekt definieras av följande egenskaper:

* **matchCondition**: Obligatoriskt - ett objekt som definierar de begäranden som utlöser körningen av den här proxyn. Den innehåller två egenskaper som delas med [HTTP-utlösare:]
    * _metoder_: En matris med HTTP-metoder som proxyn svarar på. Om det inte anges svarar proxyn på alla HTTP-metoder på rutten.
    * _rutt_: Obligatoriskt – definierar flödesmallen och styr vilka url:er som proxyn svarar på. Till skillnad från i HTTP-utlösare finns det inget standardvärde.
* **backendUri**: URL:en för backend-resursen som begäran ska skickas till. Det här värdet kan referera till programinställningar och parametrar från den ursprungliga klientbegäran. Om den här egenskapen inte ingår svarar Azure Functions med en HTTP 200 OK.
* **requestOverrides**: Ett objekt som definierar omvandlingar till backend-begäran. Se [Definiera ett requestOverrides-objekt].
* **responseOverrides**: Ett objekt som definierar omvandlingar till klientsvaret. Se [Definiera ett responseOverrides-objekt].

> [!NOTE] 
> Vägegenskapen i Azure Functions Proxies respekterar inte egenskapen *routePrefix* för funktionsappens värdkonfiguration. *route* Om du vill inkludera ett `/api`prefix, till exempel, måste det inkluderas i flödesegenskapen. *route*

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a>Inaktivera enskilda proxyservrar

Du kan inaktivera enskilda proxyservrar genom att lägga `"disabled": true` till proxyn i `proxies.json` filen. Detta medför att alla begäranden som matchar villkoret returnerar 404.
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="application-settings"></a><a name="applicationSettings"></a>Programinställningar

Proxybeteendet kan styras av flera appinställningar. De beskrivs alla i [referensen För funktionsappinställningar](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a>Reserverade tecken (strängformatering)

Proxyservrar läser alla strängar ur en JSON-fil med \ som en escape-symbol. Fullmakter tolkar också klammerparenteser. Se en fullständig uppsättning exempel nedan.

|Tecken|Rymt tecken|Exempel|
|-|-|-|
|{ eller }|{{ eller }}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>Definiera ett requestReride-objekt

Objektet requestOverrides definierar ändringar som gjorts i begäran när backend-resursen anropas. Objektet definieras av följande egenskaper:

* **backend.request.method**: DEN HTTP-metod som används för att anropa backend.
* **backend.request.querystring. ParameterName\>: En frågesträngparameter som kan ställas in för anropet till backend. \<** Ersätt * \<ParameterName\> * med namnet på den parameter som du vill ange. Observera att om den tomma strängen tillhandahålls, är parametern fortfarande inkluderad på backend-begäran.
* **backend.request.headers. HeaderName\>: Ett sidhuvud som kan ställas in för anropet till backend. \<** Ersätt * \<HeaderName\> * med namnet på det huvud som du vill ange. Om du anger den tomma strängen inkluderas inte huvudet på backend-begäran.

Värden kan referera till programinställningar och parametrar från den ursprungliga klientbegäran.

En exempelkonfiguration kan se ut så här:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>Definiera ett responseOverrides-objekt

Objektet requestOverrides definierar ändringar som görs i svaret som skickas tillbaka till klienten. Objektet definieras av följande egenskaper:

* **response.statusCode**: DEN HTTP-statuskod som ska returneras till klienten.
* **response.statusReason**: HTTP-orsaksfrasen som ska returneras till klienten.
* **response.body**: Strängrepresentationen av det organ som ska returneras till klienten.
* **response.headers. HeaderName\>: Ett huvud som kan ställas in för svaret på klienten. \<** Ersätt * \<HeaderName\> * med namnet på det huvud som du vill ange. Om du anger den tomma strängen inkluderas inte huvudet i svaret.

Värden kan referera till programinställningar, parametrar från den ursprungliga klientbegäran och parametrar från backend-svaret.

En exempelkonfiguration kan se ut så här:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> I det här exemplet anges svarstexten direkt, så ingen `backendUri` egenskap behövs. Exemplet visar hur du kan använda Azure Functions Proxies för att håna API:er.

[Azure-portal]: https://portal.azure.com
[HTTP-utlösare]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definiera ett requestReride-objekt]: #requestOverrides
[Definiera ett responseOverrides-objekt]: #responseOverrides
[programinställningar]: #use-appsettings
[Använda variabler]: #using-variables
[parametrar från den ursprungliga klientbegäran]: #request-parameters
[parametrar från backend-svaret]: #response-parameters
