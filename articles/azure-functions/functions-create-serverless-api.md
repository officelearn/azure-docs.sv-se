---
title: Skapa ett API utan server med Azure Functions | Microsoft Docs
description: Skapa ett API utan server med Azure Functions
services: functions
author: mattchenderson
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: f6a678e03818f1e1f2182b3b0dfab221d415dc72
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55698281"
---
# <a name="create-a-serverless-api-using-azure-functions"></a>Skapa ett API utan server med Azure Functions

I den här självstudien lär du dig att skapa skalbara API:er med Azure Functions. Azure Functions levereras med en uppsättning inbyggda HTTP-utlösare och -bindningar vilket gör det enkelt att skapa en slutpunkt på en mängd olika språk, bland andra Node.JS och C#. I den här självstudien anpassar du en HTTP-utlösare för att hantera specifika åtgärder i din API-design. Du förbereder även för att utöka ditt API genom att integrera det i Azure Functions Proxies och konfigurera fingerade API:er. Detta görs i Functions serverlösa beräkningsmiljö, så att du inte behöver bekymra dig om resursskalning – du kan fokusera på din API-logik.

## <a name="prerequisites"></a>Nödvändiga komponenter 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Funktionen används i resten av den här självstudien.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna Azure Portal. Detta gör du genom att logga in på [https://portal.azure.com](https://portal.azure.com) med ditt Azure-konto.

## <a name="customize-your-http-function"></a>Anpassa din HTTP-funktion

Som standard konfigureras din HTTP-utlösta funktion för att godkänna alla HTTP-metoder. Det finns också en standard-URL i formatet `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`. Om du följde snabbstarten ser `<funcname>` förmodligen ut ungefär så här: ”HttpTriggerJS1”. I det här avsnittet ändrar du funktionen så att den endast svarar på GET-begäranden mot `/api/hello`-flödet i stället. 

1. Gå till din funktion i Azure-portalen. Välj **Integrate** (Integrera) i det vänstra navigeringsfältet.

    ![Anpassa en HTTP-funktion](./media/functions-create-serverless-api/customizing-http.png)

1. Använd HTTP-utlösarinställningarna som anges i tabellen.

    | Fält | Exempelvärde | Beskrivning |
    |---|---|---|
    | Tillåtna HTTP-metoder | Valda metoder | Avgör vilka HTTP-metoder som kan användas för att anropa den här funktionen |
    | Valda HTTP-metoder | HÄMTA | Tillåter endast de valda HTTP-metoderna att användas för att anropa den här funktionen |
    | Flödesmall | /hello | Avgör vilket flöde som används för att anropa den här funktionen |
    | Auktorisationsnivå | Anonym | Valfritt: Gör din funktion tillgänglig utan en API-nyckel |

    > [!NOTE] 
    > Observera att du har inte tog med bassökvägsprefixet `/api` i flödesmallen, eftersom det styrs av en global inställning.

1. Klicka på **Spara**.

Du kan läsa mer om att anpassa HTTP-funktioner i [Azure Functions HTTP-bindningar](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook).

### <a name="test-your-api"></a>Testa ditt API

Testa din funktion så du ser hur den fungerar med den nya API-ytan.
1. Gå tillbaka till utvecklingssidan genom att klicka på funktionsnamnet i det vänstra navigeringsfönstret.
1. Klicka på **Hämta funktionswebbadress** och kopiera webbadressen. Nu bör du kunna se att den använder `/api/hello`-flödet.
1. Kopiera webbadressen till en ny webbläsarflik eller den REST-klient som du föredrar. Webbläsare använder GET som standard.
1. Lägg till parametrar i frågesträngen i din webbadress, t.ex. `/api/hello/?name=John`
1. Tryck på Retur för att bekräfta att det fungerar. Du bör se svaret ”*Hello John*”
1. Du kan även försöka anropa slutpunkten med en annan HTTP-metod för att bekräfta att funktionen inte körs. För detta använder du en REST-klient som cURL, Postman eller Fiddler.

## <a name="proxies-overview"></a>Översikt över proxyservrar

I nästa avsnitt använder du ditt API via en proxy. Med Azure Functions Proxies kan du vidarebefordra begäranden till andra resurser. Du definierar en HTTP-slutpunkt precis som med HTTP-utlösare, men i stället för att skriva kod som ska köras när slutpunkten anropas anger du en webbadress till en fjärrimplementering. På så sätt kan du skapa flera API-källor i en enda API-yta som är enkel för klienter att använda. Detta är särskilt användbart om du vill skapa ditt API som mikrotjänster.

En proxy kan peka på en HTTP-resurs, till exempel:
- Azure Functions 
- API-appar i [Azure App Service](https://docs.microsoft.com/azure/app-service/overview)
- Docker-container i [App Service i Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)
- Övriga värdbaserade API:er

Läs mer om proxyservrar i [Arbeta med proxyservrar i Azure Functions].

## <a name="create-your-first-proxy"></a>Skapa din första proxy

I det här avsnittet skapar du en ny proxy som fungerar som en klientdel i ditt övergripande API. 

### <a name="setting-up-the-frontend-environment"></a>Konfigurera klientdelsmiljön

Upprepa stegen för att [Skapa en funktionsapp](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) och skapa en ny funktionsapp som du sedan skapar proxyservern i. Den nya appens webbadress fungerar som klientdel för vårt API, och funktionsappen som du redigerade tidigare fungerar som serverdel.

1. Navigera till din nya klientdelsfunktionsapp i portalen.
1. Välj **Plattformsfunktioner** och välj sedan **Programinställningar**.
1. Rulla ned till **Programinställningar** där nyckel/värde-par lagras och skapa en ny inställning med nyckeln ”HELLO_HOST”. Ange värdet till klientdelsfunktionsappens värd, till exempel `<YourBackendApp>.azurewebsites.net`. Detta är en del av den webbadress som du kopierade tidigare när du testade din HTTP-funktion. Du kan referera till den här inställningen i konfigurationen senare.

    > [!NOTE] 
    > Programinställningar rekommenderas för värdkonfigurationen för att förhindra ett hårdkodat miljöberoende för proxyn. Om du använder programinställningar kan du flytta proxykonfigurationen mellan miljöer, och de miljöspecifika programinställningarna tillämpas.

1. Klicka på **Spara**.

### <a name="creating-a-proxy-on-the-frontend"></a>Skapa en proxy på klientdelen

1. Navigera tillbaka till din nya klientdelsfunktionsapp i portalen.
1. I det vänstra navigeringsfältet klickar du på plustecknet ”+” bredvid ”Proxyservrar”.
    ![Skapa en proxy](./media/functions-create-serverless-api/creating-proxy.png)
1. Använd proxyinställningarna enligt tabellen. 

    | Fält | Exempelvärde | Beskrivning |
    |---|---|---|
    | Namn | HelloProxy | Ett eget namn som endast används för hantering |
    | Flödesmall | /api/remotehello | Avgör vilket flöde som används för att anropa den här proxyn |
    | Webbadress för serverdel | https://%HELLO_HOST%/api/hello | Anger den slutpunkt som begäran ska nå via proxy |
    
1. Observera att Proxyservrar inte ger rotsökvägsprefixet `/api`, och detta måste ingå i flödesmallen.
1. `%HELLO_HOST%`-syntaxen refererar sedan de programinställningar som du skapade tidigare. Den matchade webbadressen pekar på din ursprungliga funktion.
1. Klicka på **Skapa**.
1. Du kan testa din nya proxy genom att kopiera Proxywebbadressen och testa den i webbläsaren eller med den HTTP-klient du föredrar.
    1. För en anonym funktion använder du:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"`
    1. För en funktion med auktorisering använder du:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>Skapa ett fingerat API

Sedan använder du en proxy för att skapa ett fingerat API för din lösning. Det gör att klientutvecklingen kan fortsätta utan att serverdelen behöver implementeras fullt ut. Längre fram under utvecklingen kan du skapa en ny funktionsapp med stöd för den här logiken och omdirigera proxyn till den.

För att skapa detta fingerade API skapar vi en ny proxy, den här gången med [App Service Editor](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Börja med att navigera till din funktionsapp i portalen. Välj **Plattformsfunktioner**, gå till **Utvecklingsverktyg** och leta rätt på **App Service Editor**. Klicka på den. Då öppnas App Service Editor i en ny flik.

Välj `proxies.json` i det vänstra navigeringsfältet. Detta är den fil som innehåller konfigurationen för alla dina proxyservrar. Om du använder en av [funktionsdistributionsmetoderna](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) är detta den fil som du ska behålla i källkontrollen. Läs mer om den här filen i [Avancerad proxykonfiguration](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Om du har följt anvisningarna hittills bör din proxies.json-fil se ut så här:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Härnäst lägger du till ditt fingerade API. Ersätt din proxies.json-fil med följande:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
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

Då läggs en ny proxy till, ”GetUserByName”, utan egenskapen backendUri. Istället för att anropa en annan resurs modifierar den standardsvaret från proxyservrar med en åsidosättning av svaret. Åsidosättningar av begäranden och svar kan också användas tillsammans med en serverdels-URL. Detta är särskilt användbart när proxy används med ett äldre system där du kan behöva ändra rubriker, frågeparametrar osv. Läs mer om åsidosättningar av begäranden och svar i [Ändra begäranden och svar i proxyservrar](https://docs.microsoft.com/azure/azure-functions/functions-proxies).

Testa ditt fingerade API genom att anropa slutpunkten `<YourProxyApp>.azurewebsites.net/api/users/{username}` med en webbläsare eller den REST-klient du föredrar. Se till att ersätta _{username}_ med ett strängvärde som representerar ett användarnamn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skapa och anpassa ett API på Azure Functions. Du lärde dig också att hantera flera API:er, även fingerade, tillsammans som en enhetlig API-yta. Du kan använda de här metoderna för att bygga ut API:er i olika komplexitetsgrader medan de körs på den serverlösa beräkningsmodell som Azure Functions ger.

Följande referenser kan vara till hjälp när du utvecklar ditt API vidare:

- [HTTP-bindningar för Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [Arbeta med proxyservrar i Azure Functions]
- [Dokumentera ett Azure Functions-API (förhandsversion)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[Arbeta med proxyservrar i Azure Functions]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
