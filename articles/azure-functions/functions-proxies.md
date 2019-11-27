---
title: Arbeta med proxyservrar i Azure Functions
description: Översikt över hur du använder Azure Functions Proxies
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: alkarche
ms.openlocfilehash: dffdffdfa80d940c4a50d0a6630c665164f24d5c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230460"
---
# <a name="work-with-azure-functions-proxies"></a>Arbeta med Azure Functions Proxies

Den här artikeln beskriver hur du konfigurerar och arbetar med Azure Functions-proxyservrar. Med den här funktionen kan du ange slutpunkter på funktionsappen som implementeras av en annan resurs. Du kan använda dessa proxyservrar för att dela ett stort API i flera funktionsappar (som i en mikrotjänstarkitektur), samtidigt som en enda API-yta för klienter.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> Standardfunktioner fakturering gäller proxy körningar. Mer information finns i [prissättning för Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create"></a>Skapa en proxy

Det här avsnittet visar hur du skapar en proxy i Functions-portalen.

1. Öppna [Azure Portal]och gå sedan till din Function-app.
2. I den vänstra rutan väljer du **ny proxy**.
3. Ange ett namn för proxyservern.
4. Konfigurera den slut punkt som exponeras för den här Function-appen genom att ange en **vägfil** och **http-metoder**. Dessa parametrar fungerar enligt reglerna för [http-utlösare].
5. Ange **backend-URL:** en till en annan slut punkt. Den här slutpunkten kan vara en funktion i en annan funktionsapp eller det bero på att alla andra API: er. Värdet behöver inte vara statiskt och det kan referera till [program inställningar] och [parametrar från den ursprungliga klientbegäran].
6. Klicka på **Skapa**.

Proxyservern finns nu som en ny slutpunkt på din funktionsapp. Från en klientperspektiv motsvarar den en HttpTrigger i Azure Functions. Du kan testa din nya proxyserverkonfigurationen genom att kopiera URL: en för Proxy och testa det med dina favoritverktyg HTTP-klienten.

## <a name="modify-requests-responses"></a>Ändra förfrågningar och svar

Du kan ändra begäranden till och -svar från backend-server med Azure Functions Proxies. Dessa omvandlingar kan använda variabler som definieras i [använda variabler].

### <a name="modify-backend-request"></a>Ändra backend-begäran

Som standard initieras backend-begäran som en kopia av den ursprungliga begäran. Förutom att URL: en för backend-server kan du ändra till HTTP-metoden, rubriker och frågesträngsparametrarna. De ändrade värdena kan referera till [program inställningar] och [parametrar från den ursprungliga klientbegäran].

Backend-begäranden kan ändras i portalen genom att expandera avsnittet *begär åsidosättning* på sidan information om proxyserver. 

### <a name="modify-response"></a>Ändra svaret

Som standard initieras klienten svaret som en kopia av backend-svaret. Du kan ändra Svarets statuskod, orsaksfras, rubriker och brödtext. De ändrade värdena kan referera till [program inställningar], [parametrar från den ursprungliga klientbegäran]och [parametrar från Server delens svar].

Server dels begär Anden kan ändras i portalen genom att avsnittet om *åsidosättning av svar* expanderas på sidan information om proxyserver. 

## <a name="using-variables"></a>Använda variabler

Konfigurationen för en proxyserver behöver inte vara statisk. Du kan ange ett villkor för det för att använda variabler i ursprungliga klientbegäran, backend-svar eller programinställningar.

### <a name="reference-localhost"></a>Referera till lokala funktioner
Du kan använda `localhost` för att referera till en funktion inuti samma Function-app direkt, utan en tur-proxy-begäran.

`"backendurl": "https://localhost/api/httptriggerC#1"` kommer att referera till en lokal HTTP-utlöst funktion i väg `/api/httptriggerC#1`

 
>[!Note]  
>Om din funktion använder auktoriseringsregler *-, admin-eller sys* -nivåer måste du ange koden och clientId, enligt den ursprungliga funktions webb adressen. I det här fallet skulle referensen se ut så här: `"backendurl": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"` vi rekommenderar att du lagrar dessa nycklar i [program inställningar] och refererar till dem i dina proxyservrar. På så sätt undviker du att lagra hemligheter i käll koden. 

### <a name="request-parameters"></a>Parametrar för referens förfrågan

Du kan använda parametrarna som indata till backend-URL: en egenskap eller som en del av ändra begäranden och svar. Vissa parametrar kan vara kopplat från flödesmallen som anges i den grundläggande proxykonfigurationen och andra kan komma från egenskaperna för den inkommande begäranden.

#### <a name="route-template-parameters"></a>Dirigera mallparametrar
Parametrar som används i flödesmallen är tillgängliga för att referera till efter namn. Parameter namnen omges av klammerparenteser ({}).

Om en proxyserver till exempel har en vägfil, till exempel `/pets/{petId}`, kan URL: en för Server delen innehålla värdet för `{petId}`, som i `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Om väg mal len avslutas i ett jokertecken, till exempel `/api/{*restOfPath}`, är värdet `{restOfPath}` en sträng representation av återstående Sök vägs segment från den inkommande begäran.

#### <a name="additional-request-parameters"></a>Om ytterligare begäranparametrar
Följande värden kan användas i konfigurationsvärden förutom mallparametrar väg:

* **{Request. Method}** : http-metoden som används på den ursprungliga begäran.
* **{Request. headers.\<huvud\>}** : en rubrik som kan läsas från den ursprungliga begäran. Ersätt *\<huvud\>* med namnet på rubriken som du vill läsa. Om sidhuvudet inte finns med på begäran, kommer värdet vara en tom sträng.
* **{Request. QueryString.\<ParameterName\>}** : en frågesträngparametern som kan läsas från den ursprungliga begäran. Ersätt *\<ParameterName\>* med namnet på den parameter som du vill läsa. Om parametern inte finns med på begäran, kommer värdet vara en tom sträng.

### <a name="response-parameters"></a>Referera till parametrar för backend-svar

Svarsparametrar kan användas som en del av ändra svaret till klienten. Följande värden kan användas i konfigurationsvärden:

* **{Server del. Response. StatusCode}** : den HTTP-statuskod som returneras av backend-svaret.
* **{Server del. Response. statusReason}** : http-orsaks frasen som returneras i Server delens svar.
* **{Server del. Response. headers.\<huvud\>}** : en rubrik som kan läsas från Server delens svar. Ersätt *\<huvud\>* med namnet på rubriken som du vill läsa. Om rubriken inte ingår i svaret, kommer värdet vara en tom sträng.

### <a name="use-appsettings"></a>Referens program inställningar

Du kan också referera till [program inställningar som definierats för Function-appen](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings) genom att omgivande inställnings namnet med procent tecken (%).

Till exempel skulle en backend-URL för *https://%ORDER_PROCESSING_HOST%/api/orders* ha% ORDER_PROCESSING_HOST% ersatts med värdet för ORDER_PROCESSING_HOST inställningen.

> [!TIP] 
> Använd programinställningar för backend-värdar när du har flera distributioner eller testmiljöer. På så sätt kan du kan se till att du alltid pratar med rätt serverdelen för den miljön.

## <a name="debugProxies"></a>Felsöka proxyservrar

Genom att lägga till flaggan `"debug":true` till en proxy i `proxies.json` aktiverar du fel söknings loggning. Loggarna lagras i `D:\home\LogFiles\Application\Proxies\DetailedTrace` och kan nås via avancerade verktyg (kudu). Alla HTTP-svar kommer också att innehålla ett `Proxy-Trace-Location` huvud med en URL för att få åtkomst till logg filen.

Du kan felsöka en proxyserver från klient sidan genom att lägga till en `Proxy-Trace-Enabled` huvud uppsättning till `true`. Detta kommer också logga en spårning på filsystemet och returnera trace-URL: en som en rubrik i svaret.

### <a name="block-proxy-traces"></a>Blockera proxy spårningar

Av säkerhetsskäl kan du inte vill att alla anropar din tjänst för att generera en spårning. De kommer inte att kunna få åtkomst till trace innehållet utan att dina inloggningsuppgifter, men genererar spårningen förbrukar resurser och visar att du använder Funktionsproxy.

Inaktivera spår helt genom att lägga till `"debug":false` till en viss proxy i `proxies.json`.

## <a name="advanced-configuration"></a>Avancerad konfiguration

De proxyservrar som du konfigurerar lagras i en *proxy. JSON* -fil som finns i roten i en Function app-katalog. Du kan redigera den här filen manuellt och distribuera den som en del av din app när du använder någon av de [distributions metoder](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) som stöds av functions. 

> [!TIP] 
> Om du inte har konfigurerat någon av distributions metoderna kan du också arbeta med filen *proxys. JSON* i portalen. Gå till din Function-app, Välj **plattforms funktioner**och välj sedan **App Service Editor**. Då kan du visa hela filen strukturen för din funktionsapp och göra ändringar.

*Proxys. JSON* definieras av ett objekt i proxyn, som består av namngivna proxyservrar och deras definitioner. Om Redigeraren stöder det kan du också referera till ett JSON- [schema](http://json.schemastore.org/proxies) för kod komplettering. En exempelfil kan se ut så här:

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

Varje proxy har ett eget namn, till exempel *Proxy1* i föregående exempel. Objekt för rättighetsdefinition av motsvarande proxy definieras av följande egenskaper:

* **matchCondition**: krävs--ett objekt som definierar de begär Anden som utlöser körningen av den här proxyn. Den innehåller två egenskaper som delas med [http-utlösare]:
    * _metoder_: en matris med de http-metoder som proxyservern svarar på. Om det inte anges svarar proxyn på alla HTTP-metoder på vägen.
    * _Route_: required--definierar väg mal len, vilket styr vilka URL: er för begäran som proxyservern svarar på. Till skillnad från i HTTP-utlösare finns inget standardvärde.
* ryggs ryggi **: URL**: en till den backend-resurs som begäran ska skickas till via proxy. Det här värdet kan referera till programinställningar och parametrar från den ursprungliga klientbegäran. Om den här egenskapen inte finns, svarar Azure Functions med en HTTP 200 OK.
* **requestOverrides**: ett objekt som definierar omvandlingar till backend-begäran. Se [definiera ett requestOverrides-objekt].
* **responseOverrides**: ett objekt som definierar omvandlingar till klient svaret. Se [definiera ett responseOverrides-objekt].

> [!NOTE] 
> Egenskapen *Route* i Azure Functions-proxyservrar följer inte egenskapen *routePrefix* för Funktionsapp-värd konfigurationen. Om du vill inkludera ett prefix som `/api`, måste det ingå i egenskapen *Route* .

### <a name="disableProxies"></a>Inaktivera enskilda proxyservrar

Du kan inaktivera enskilda proxyservrar genom att lägga till `"disabled": true` till proxyn i `proxies.json`s filen. Detta gör att alla begär Anden som uppfyller matchCondition kan returnera 404.
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

### <a name="applicationSettings"></a>Program inställningar

Beteendet proxy kan styras av flera appinställningar. De beskrivs i avsnittet [Functions App Settings Reference](./functions-app-settings.md)

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reservedChars"></a>Reserverade tecken (sträng formatering)

Proxyservrar läser alla strängar från en JSON-fil med hjälp av \ som en Escape-symbol. Proxyservrar tolkar också klammerparenteser. Se en fullständig uppsättning av exempel nedan.

|Tecken|Undantagstecknet|Exempel|
|-|-|-|
|{eller}|{{eller}}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="requestOverrides"></a>Definiera ett requestOverrides-objekt

Objektet requestOverrides definierar ändringar som gjorts på begäran när resursen backend-anropas. Objektet definieras av följande egenskaper:

* **Server del. Request.-metod**: http-metoden som används för att anropa Server delen.
* **Server del. Request. QueryString.\<ParameterName\>** : en frågesträngparametern som kan anges för anropet till Server delen. Ersätt *\<ParameterName\>* med namnet på den parameter som du vill ange. Om den tomma strängen anges, ingår inte parametern på backend-begäran.
* **backend. Request. headers.\<huvud\>** : en rubrik som kan anges för anropet till Server delen. Ersätt *\<huvud\>* med namnet på rubriken som du vill ange. Om du anger den tomma strängen kan ingår inte rubriken på backend-begäran.

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

### <a name="responseOverrides"></a>Definiera ett responseOverrides-objekt

Objektet requestOverrides definierar ändringar som görs i ett svar som skickas tillbaka till klienten. Objektet definieras av följande egenskaper:

* **Response. StatusCode**: den HTTP-statuskod som ska returneras till klienten.
* **Response. statusReason**: den http-orsaks fras som ska returneras till klienten.
* **Response. Body**: sträng representationen för bröd texten som ska returneras till klienten.
* **Response. headers.\<huvud\>** : en rubrik som kan anges för svaret på klienten. Ersätt *\<huvud\>* med namnet på rubriken som du vill ange. Om du anger den tomma strängen kan ingår inte rubriken på ett svar.

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
> I det här exemplet anges svars texten direkt, så ingen `backendUri` egenskap krävs. Exemplet visar hur du kan använda Azure Functions-proxyservrar för simulerade API: er.

[Azure Portal]: https://portal.azure.com
[HTTP-utlösare]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[Definiera ett requestOverrides-objekt]: #requestOverrides
[Definiera ett responseOverrides-objekt]: #responseOverrides
[program inställningar]: #use-appsettings
[Använda variabler]: #using-variables
[parametrar från den ursprungliga klientbegäran]: #request-parameters
[parametrar från Server delens svar]: #response-parameters
