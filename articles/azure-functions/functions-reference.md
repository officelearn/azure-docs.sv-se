---
title: "Riktlinjer för att utveckla Azure Functions | Microsoft Docs"
description: "Läs om Azure Functions koncept och tekniker som du behöver för att utveckla funktioner i Azure, för alla programming språk och bindningar."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "utvecklare guide, azure-funktion, funktioner, händelsebearbetning, webhooks, dynamiska beräkning, serverlösa arkitektur"
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/12/2017
ms.author: tdykstra
ms.openlocfilehash: 80996c8bc6e40665201057ed185700ddaeea170a
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="azure-functions-developers-guide"></a>Azure Functions-guide för utvecklare
I Azure Functions dela funktioner några grundläggande tekniska begrepp och komponenter, oavsett språk eller bindning som du använder. Innan du går till learning information som är specifika för ett visst språk eller bindning bör du läsa igenom den här översikten som gäller för alla.

Den här artikeln förutsätter att du redan har läst den [översikt över Azure Functions](functions-overview.md) och är bekant med [WebJobs SDK begrepp som utlösare och bindningar JobHost runtime](https://github.com/Azure/azure-webjobs-sdk/wiki). Azure Functions baseras på WebJobs-SDK. 

## <a name="function-code"></a>Funktionskoden
En *funktionen* är det primära begreppet i Azure Functions. Du skriva kod för en funktion på ett annat språk och spara filerna koden och konfigurationen i samma mapp. Konfigurationen har namnet `function.json`, som innehåller JSON-konfigurationsdata. Olika språk som stöds och var och en har ett något annorlunda optimerad för att fungerar bäst för det språket. 

Filen function.json definierar bindningarna som funktionen och andra konfigurationsinställningar. Körningsmiljön använder den här filen för att fastställa händelser att övervaka och hur du överför data till och returnera data från funktionen körning. Följande är ett exempel function.json-fil.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Ange den `disabled` egenskapen `true` att förhindra att funktionen utförs.

Den `bindings` egenskapen är där du kan konfigurera både utlösare och bindningar. Varje bindning delar några vanliga inställningar och vissa inställningar som är specifika för en viss typ av bindningen. Alla bindningar kräver följande inställningar:

| Egenskap | Värdetyper / | Kommentarer |
| --- | --- | --- |
| `type` |Sträng |Bindningstyp. Till exempel `queueTrigger`. |
| `direction` |'i', 'out' |Anger om bindningen för mottagning av data i funktionen eller skicka data från funktionen. |
| `name` |Sträng |Namnet som används för det bundna data i funktionen. Detta är ett argumentnamn; för C# JavaScript är det nyckeln i en lista med nyckel/värde. |

## <a name="function-app"></a>Funktionsapp
En funktionsapp består av en eller flera enskilda funktioner som hanteras tillsammans i Azure App Service. Alla funktioner i en funktionsapp delar samma prisavtal, kontinuerlig distribution och versionen av körningsmiljön. Funktioner på flera språk kan dela appen med samma funktion. Se en funktionsapp som ett sätt att ordna och hantera dina funktioner tillsammans. 

## <a name="runtime-script-host-and-web-host"></a>Runtime (skriptvärden och webbvärd)
Runtime eller skriptvärden, är den underliggande WebJobs SDK-värden som lyssnar efter händelser, samlar in och skickar data och slutligen körs din kod. 

För att underlätta http-utlösare, finns det också en värd som är avsedd att sitta framför skriptvärden i produktion scenarier. Med två värdar avslutas bidrar till att isolera skriptvärden framifrån trafik som hanteras av Webbvärden.

## <a name="folder-structure"></a>Mappstruktur
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

När inställningen upp ett projekt för distribution av funktioner på en funktionsapp i Azure App Service, kan du behandla den här mappstrukturen som din Platskod. Du kan använda befintliga verktyg kontinuerlig integrering och distribution eller distribution av anpassade skript för detta distribuera tid installationen eller code transpilation.

> [!NOTE]
> Se till att distribuera din `host.json` filen och fungerar mappar direkt till den `wwwroot` mapp. Ta inte med den `wwwroot` mappen i dina distributioner. Annars kan du få `wwwroot\wwwroot` mappar. 
> 
> 

## <a id="fileupdate"></a>Så här uppdaterar du funktionen app-filer
Funktionen redigeraren inbyggda i Azure-portalen kan du uppdatera den *function.json* fil- och kodfilen för en funktion. Att hämta eller uppdatera andra filer som *package.json* eller *project.json* beroenden, måste du använda andra metoder för distribution.

Funktionen appar bygger på Apptjänst, så alla de [distributionsalternativ för standard web apps](../app-service/app-service-deploy-local-git.md) finns också tillgängliga för funktionen appar. Här följer några metoder som du kan använda för att hämta eller uppdatera funktionen programfilerna. 

#### <a name="to-use-app-service-editor"></a>Du använder App Service Editor
1. I Azure Functions-portalen klickar du på **plattformsfunktioner**.
2. I den **UTVECKLINGSVERKTYG** klickar du på **App Service Editor**.   
   När App Service Editor har lästs in, visas den *host.json* fil- och mappar under *wwwroot*. 
5. Öppna filer att redigera dem, eller dra och släppa från utvecklingsdatorn att överföra filer.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>Att använda funktionen appens SCM (Kudu) slutpunkt
1. Gå till: `https://<function_app_name>.scm.azurewebsites.net`.
2. Klicka på **Debug konsolen > CMD**.
3. Gå till `D:\home\site\wwwroot\` att uppdatera *host.json* eller `D:\home\site\wwwroot\<function_name>` att uppdatera filer för en funktion.
4. Dra och släpp en fil som du vill ladda upp till rätt mapp i rutnätet filen. Det finns två områden i rutnätet filen där du kan släppa en fil. För *.zip* filer, visas en ruta med etiketten ”dra hit om du vill ladda upp och packa”. Ta bort i rutnätet filen men utanför rutan ”packa” för andra filtyper.

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

#### <a name="to-use-continuous-deployment"></a>Att använda kontinuerlig distribution
Följ anvisningarna i avsnittet [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Parallell körning
När flera utlösande händelser inträffar snabbare än en enkeltrådig funktionen runtime kan bearbeta dem, kan körningen anropa funktionen flera gånger i parallellt.  Om en funktionsapp använder den [förbrukning som värd för planen](functions-scale.md#how-the-consumption-plan-works), funktionen appen kan byggas ut automatiskt.  Varje instans av funktionsapp om appen körs på förbrukningen värd plan eller en vanlig [Apptjänst som är värd för planen](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), kan bearbeta samtidiga funktionsanrop parallellt med flera trådar.  Det maximala antalet samtidiga funktionsanrop i varje funktionen app-instansen varierar beroende på vilken typ av utlösare som används samt de resurser som används av andra funktioner i appen funktion.

## <a name="functions-runtime-versioning"></a>Funktioner runtime versionshantering

Du kan konfigurera version av körningsmiljön funktioner med den `FUNCTIONS_EXTENSION_VERSION` appinställningen. Till exempel anger värdet ”1 ~” att appen funktionen ska använda 1 enligt dess huvudversion. Funktionen appar uppgraderas till varje ny delversion när de blir tillgängliga. Mer information, inklusive hur du visar den exakta versionen av appen funktionen finns [så avsedda för Azure Functions-runtime versioner](functions-versions.md).

## <a name="repositories"></a>Centrallager
Koden för Azure Functions är öppen källkod och lagras i GitHub-databaser:

* [Azure Functions-runtime](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Azure Functions-portalen](https://github.com/projectkudu/AzureFunctionsPortal)
* [Azure Functions-mallar](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs-SDK-tillägg](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Bindningar
Här är en tabell med alla bindningar som stöds.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

## <a name="reporting-issues"></a>Rapportera problem
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions C# för utvecklare](functions-reference-csharp.md)
* [Azure Functions F # för utvecklare](functions-reference-fsharp.md)
* [Azure Functions NodeJS för utvecklare](functions-reference-node.md)
* [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)
* [Azure Functions: Transporten](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) på Azure App Service-teamets blogg. En historik över hur Azure Functions utvecklades.

