---
title: Skapa en serverlösa API med hjälp av Azure Functions | Microsoft Docs
description: Så här skapar du en serverlösa API med hjälp av Azure-funktioner
services: functions
author: mattchenderson
manager: cfowler
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 7c3933210c01c81077b594abb8c3183d6e3c58a0
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
ms.locfileid: "24811608"
---
# <a name="create-a-serverless-api-using-azure-functions"></a>Skapa en serverlösa API med hjälp av Azure-funktioner

I kursen får du lära dig hur Azure Functions kan du skapa skalbara API: er. Azure Functions levereras med en uppsättning inbyggda HTTP-utlösare och bindningar som gör det enkelt att skapa en slutpunkt på flera olika språk, inklusive Node.JS, C# och mycket mer. I den här självstudiekursen kommer du anpassa en HTTP-utlösare för att hantera specifika åtgärder i utformningen av din API. Dessutom förbereder du för växande din API genom att integrera med Azure Functions proxyservrar och ställa in fingerad API: er. Allt detta sker ovanpå funktioner serverlösa beräkning miljö, så att du inte behöver bry dig om att skala resurser – du kan bara fokusera på din API-logik.

## <a name="prerequisites"></a>Krav 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Funktionen används för resten av den här kursen.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna Azure-portalen. Om du vill göra det loggar du in på [https://portal.azure.com](https://portal.azure.com) med ditt Azure-konto.

## <a name="customize-your-http-function"></a>Anpassa din HTTP-funktion

Som standard konfigureras din HTTP-utlösta funktion för att godkänna HTTP-metoden. Det finns också en standard-URL i formatet `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`. Om du har följt Snabbstart, sedan `<funcname>` förmodligen ser ut ungefär så ”HttpTriggerJS1”. I det här avsnittet ska du ändra funktionen så att endast GET-begäranden mot `/api/hello` dirigera i stället. 

1. Gå till din funktion i Azure-portalen. Välj **integrera** i det vänstra navigeringsfönstret.

    ![Anpassa en HTTP-funktion](./media/functions-create-serverless-api/customizing-http.png)

1. Använd HTTP-inställningarna som anges i tabellen.

    | Fält | Exempelvärde | Beskrivning |
    |---|---|---|
    | Tillåtna HTTP-metoder | Valda metoder | Avgör vilka HTTP-metoder kan användas för att anropa den här funktionen |
    | Den valda http-metoder | HÄMTA | Tillåter endast valda http-metoder som används för att anropa den här funktionen |
    | Flödesmallen | Sverige | Avgör vilken väg som används för att anropa den här funktionen |
    | Åtkomstnivå | Anonym | Valfritt: Gör din funktion tillgänglig utan en API-nyckel |

    > [!NOTE] 
    > Observera att du inte innehöll den `/api` basera sökväg prefix i väg-mall som hanteras av en global inställning.

1. Klicka på **Spara**.

Du kan lära dig mer om hur du anpassar http-funktioner i [Azure Functions HTTP och webhook bindningar](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#customizing-the-http-endpoint).

### <a name="test-your-api"></a>Testa ditt API

Sedan testa funktionen om du vill se den arbetar med nya API-ytan.
1. Gå tillbaka till sidan utveckling genom att klicka på funktionsnamnet i det vänstra navigeringsfönstret.
1. Klicka på **få funktionen URL** och kopiera Webbadressen. Du bör se till att den använder den `/api/hello` vidarebefordra nu.
1. Kopiera URL till en ny webbläsarflik eller önskade REST-klient. Webbläsare använder GET som standard.
1. Lägg till parametrar i frågesträngen i URL: en t.ex.`/api/hello/?name=John`
1. Klicka på Ange för att bekräfta att den fungerar. Du bör se svaret ”*Hello John*”
1. Du kan också försöka anropar slutpunkten med ett annat HTTP-metoden för att bekräfta att funktionen inte körs. För att göra detta behöver du använda en REST-klient, till exempel cURL, Postman eller Fiddler.

## <a name="proxies-overview"></a>Översikt över proxyservrar

I nästa avsnitt ska du ansluta din API via en proxyserver. Azure Functions proxyservrar kan du vidarebefordrar begäranden till andra resurser. Du definierar en HTTP-slutpunkt precis som med HTTP-utlösare, men i stället för att skriva kod för att köra när denna slutpunkt anropas kan du ange en Webbadress till en fjärr-implementering. På så sätt kan du skapa flera API källor i en enda API-yta som är enkelt för klienter att använda. Detta är särskilt användbart om du vill skapa din API som mikrotjänster.

En proxy kan peka till en HTTP-resurs som:
- Azure Functions 
- API apps i [Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-overview)
- Docker-behållare i [Apptjänst på Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)
- Värdbaserade API

Mer information om proxyservrar finns [arbeta med Azure Functions proxyservrar].

## <a name="create-your-first-proxy"></a>Skapa din första proxy

I det här avsnittet skapar du en ny proxy som fungerar som en klientdel till din övergripande API. 

### <a name="setting-up-the-frontend-environment"></a>Ställa in miljön för klientdel

Upprepa stegen för att [skapa en funktionsapp](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) att skapa en ny funktionsapp där du skapar din proxyserver. URL för den här nya appen fungerar som klientdelen för vårt API och funktionsapp du redigerade tidigare fungerar som en serverdel.

1. Gå till din nya klientdel funktionsapp i portalen.
1. Välj **plattformsfunktioner** och välj **programinställningar**.
1. Rulla ned till **programinställningar** där nyckel/värde-par lagras och skapa en ny inställning med nyckeln ”HELLO_HOST”. Ange värdet till värden för appen backend-funktion som `<YourBackendApp>.azurewebsites.net`. Detta är en del av den URL som du kopierade tidigare när du testar din HTTP-funktion. Du måste referera till den här inställningen i konfigurationen senare.

    > [!NOTE] 
    > App-inställningar rekommenderas för Värdkonfiguration att förhindra att ett hårdkodat miljö beroende för proxy. Med hjälp av appinställningar innebär att du kan flytta proxykonfigurationen mellan miljöer och miljö-specifik app-inställningar tillämpas.

1. Klicka på **Spara**.

### <a name="creating-a-proxy-on-the-frontend"></a>Att skapa en proxy på klientdelen

1. Gå tillbaka till din klientdel funktionsapp i portalen.
1. I det vänstra navigeringsfönstret, klicka på plustecknet '+' bredvid ”proxy”.
    ![Att skapa en proxy](./media/functions-create-serverless-api/creating-proxy.png)
1. Använda proxy-inställningar som anges i tabellen. 

    | Fält | Exempelvärde | Beskrivning |
    |---|---|---|
    | Namn | HelloProxy | Ett eget namn som används endast för hantering |
    | Flödesmallen | / api/hello | Avgör vilken väg som används för att anropa denna proxy |
    | Backend-URL | https://%HELLO_HOST%/API/hello | Anger den slutpunkt som begäran ska vara via proxy |
    
1. Observera att proxyservrar inte tillhandahåller den `/api` bassökväg prefix och måste tas med i flödesmallen.
1. Den `%HELLO_HOST%` syntax refererar appinställningen som du skapade tidigare. Matcha URL: en pekar på din ursprungliga funktion.
1. Klicka på **Skapa**.
1. Du kan testa den nya proxyserverkonfigurationen genom att kopiera URL: en för Proxy och testa det i webbläsaren eller med din favorit HTTP-klienten.
    1. Vid användning av en anonym funktion:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?name="Proxies"`
    1. För en funktion med auktorisering använder:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/hello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>Skapa en fingerad API

Sedan kommer du använder en proxyserver för att skapa en fingerad API för din lösning. På så sätt kan klienten utvecklingsmiljö till pågår, utan att behöva serverdelen fullt ut. Senare i utveckling, kan du skapa en ny funktionsapp som stöder denna logik och omdirigera proxyservern till den.

Om du vill skapa den här fingerad API vi skapar en ny proxy tid med den [App Service Editor](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Kom igång genom att navigera till appen funktionen i portalen. Välj **plattformsfunktioner** och under **utvecklingsverktyg** hitta **App Service Editor**. Klicka på det här öppnas redigeraren App Service i en ny flik.

Välj `proxies.json` i det vänstra navigeringsfönstret. Detta är den fil som lagrar konfigurationen för alla dina proxyservrar. Om du använder en av de [fungerar distributionsmetoder](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment), detta är den fil som du ska behålla i källkontroll. Mer information om den här filen finns [proxyservrar avancerad konfiguration](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Om du har följt hittills, din proxies.json bör se ut ungefär så här:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Härnäst ska du lägga till din fingerad API. Ersätt filen proxies.json med följande:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Detta lägger till en ny proxy, ”GetUserByName” utan backendUri-egenskap. I stället för att anropa en annan resurs, ändrar den Standardsvar från proxyservrar med en åsidosättning för svar. Förfrågan och svar åsidosättningar kan också användas tillsammans med en backend-URL. Detta är särskilt användbart när via proxy till ett äldre system där du kan behöva ändra sidhuvud, frågar parametrar, osv. Läs mer om begäran och svar åsidosättningar i [ändra begäranden och -svar i proxyservrar](https://docs.microsoft.com/azure/azure-functions/functions-proxies#a-namemodify-requests-responsesamodifying-requests-and-responses).

Testa din fingerad API genom att anropa den `<YourProxyApp>.azurewebsites.net/api/users/{username}` slutpunkten med hjälp av en webbläsare eller ditt favoritprogram REST-klient. Se till att ersätta _{username}_ med ett strängvärde som representerar ett användarnamn.

## <a name="next-steps"></a>Nästa steg

I kursen får du har lärt dig hur du skapar och anpassar en API på Azure Functions. Du också fått lära dig hur du hanterar flera API: er, inklusive mocks, tillsammans som en enhetlig API-ytan. Du kan använda dessa metoder för att bygga ut API: er för alla komplexitet alla när du kör på serverlösa beräknings-modell som tillhandahålls av Azure Functions.

I följande referenser kan vara till hjälp när du utvecklar dina API ytterligare:

- [Azure Functions HTTP och webhook bindningar](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [arbeta med Azure Functions proxyservrar]
- [Dokumentera Azure Functions API (förhandsgranskning)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[arbeta med Azure Functions proxyservrar]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
