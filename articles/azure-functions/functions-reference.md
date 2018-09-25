---
title: Vägledning för att utveckla Azure Functions | Microsoft Docs
description: Läs om Azure Functions-koncept och tekniker som du behöver för att utveckla funktioner i Azure, i alla programmeringsspråk och bindningar.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Developer guide, azure functions, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 38d73f38a5e04a42ee15c9206ce760936e3e10c9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980312"
---
# <a name="azure-functions-developers-guide"></a>Azure Functions-guide för utvecklare
I Azure Functions dela några tekniska nyckelkoncept och komponenter, oavsett språk eller bindning som du använder specifika funktioner. Innan du sätter igång till learning information som gäller för ett visst språk eller en bindning, bör du läsa igenom den här översikten som gäller för alla.

Den här artikeln förutsätter att du redan har läst den [översikt över Azure Functions](functions-overview.md) och är bekant med [WebJobs SDK-begrepp som utlösare och bindningar JobHost runtime](https://github.com/Azure/azure-webjobs-sdk/wiki). Azure Functions bygger på WebJobs SDK. 

## <a name="function-code"></a>Funktionskoden
En *funktionen* är det primära konceptet i Azure Functions. Du skriver kod för en funktion i ett valfritt språk och spara filerna kod och konfiguration i samma mapp. Konfigurationen har namnet `function.json`, som innehåller JSON-konfigurationsdata. Olika språk som stöds och var och en har en något annorlunda upplevelse som optimerats för att fungerar bäst för det språket. 

Filen function.json definierar bindningarna som funktionen och andra konfigurationsinställningar. Körningen använder den här filen för att hitta händelser att övervaka och hur du överför data till och returnera data från körning av funktion. Följande är en exempelfil function.json.

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

Ange den `disabled` egenskap `true` att förhindra att funktionen som körs.

Den `bindings` egenskapen är där du konfigurerar både utlösare och bindningar. Varje bindning beskriver några vanliga inställningar och vissa inställningar, som är specifika för en viss typ av bindning. Alla bindningar kräver följande inställningar:

| Egenskap  | Värdetyper / | Kommentarer |
| --- | --- | --- |
| `type` |sträng |Bindningstyp. Till exempel `queueTrigger`. |
| `direction` |”i”, ”ut” |Anger om bindningen för mottagning av data i funktionen eller skickar data från funktionen. |
| `name` |sträng |Namnet som används för bundna data i funktionen. Detta är ett argument-name; för C# för JavaScript är det nyckeln i en nyckel/värde-lista. |

## <a name="function-app"></a>Funktionsapp
En funktionsappen är en körningskontexten i Azure där dina funktioner körs. En funktionsapp består av en eller flera enskilda funktioner som hanteras tillsammans med Azure App Service. Alla funktioner i en funktionsapp delar samma prisplanen, kontinuerlig distribution och runtime-versionen. Tänk på en funktionsapp som ett sätt att ordna och hantera dina funktioner gemensamt. 

> [!NOTE]
> Från och med [version 2.x](functions-versions.md) av Azure Functions-runtime alla funktioner i en funktionsapp måste skrivas i samma språk.

## <a name="runtime"></a>Körmiljö
Azure Functions-körningen eller skriptvärden, är den underliggande värden som lyssnar efter händelser, samlar in och skickar data och slutligen Kör koden. Det här samma värd används av WebJobs SDK.

Det finns också en värd som hanterar begäranden för HTTP-utlösare för körningen. Med två värdar avslutas bidrar till att isolera runtime framifrån trafik som hanteras av webbvärd.

## <a name="folder-structure"></a>mappstruktur
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

När inställningen upp ett projekt för distribution av funktioner till en funktionsapp i Azure, kan du hantera den här mappstrukturen som din kod. Vi rekommenderar att du använder [paketera distribution](deployment-zip-push.md) att distribuera projektet till din funktionsapp i Azure. Du kan också använda befintliga verktyg som [kontinuerlig integrering och distribution](functions-continuous-deployment.md) och Azure DevOps.

> [!NOTE]
> Se till att distribuera din `host.json` filen och fungerar mappar direkt till den `wwwroot` mapp. Ta inte med den `wwwroot` mapp i dina distributioner. Annars kan du få `wwwroot\wwwroot` mappar.

## <a id="fileupdate"></a> Så här uppdaterar du funktionen app-filer
Funktionen redigeraren inbyggda i Azure-portalen kan du uppdatera den *function.json* fil- och kodfilen för en funktion. Överföra eller andra uppdateringsfiler som *package.json* eller *project.json* eller beroenden, som du behöver använda andra metoder för distribution.

Funktionsappar bygger på App Service, så att alla de [tillgängliga distributionsalternativen till standard web apps](../app-service/app-service-deploy-local-git.md) kan också användas för funktionsappar. Här följer några metoder som du kan använda för att överföra eller uppdatera funktionen app-filer. 

#### <a name="use-local-tools-and-publishing"></a>Använd lokala verktyg och publicering
Funktionsappar kan skapas och publicerade med hjälp av olika verktyg, bland annat [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md), och [Azure Functions Core Tools](./functions-develop-local.md). Mer information finns i [kod och testa Azure Functions lokalt](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

#### <a name="continuous-deployment"></a>Kontinuerlig distribution
Följ anvisningarna i avsnittet [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Parallell körning
När flera utlösande händelser inträffar snabbare än en single-threaded funktionskörningen kan bearbeta dem, kan körningen anropa funktionen flera gånger i parallellt.  Om en funktionsapp med hjälp av den [Förbrukningsvärdplanen](functions-scale.md#how-the-consumption-plan-works), funktionsappen genom att skala upp automatiskt.  Varje instans av function-app om appen körs på förbrukningen som är värd för plan eller en vanlig [App Service-värdplanen](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), kan bearbeta samtidiga funktionsanrop parallellt med flera trådar.  Det maximala antalet samtidiga funktionsanrop i varje funktionen app-instansen varierar beroende på vilken typ av utlösare som används, samt de resurser som används av andra funktioner i funktionsappen.

## <a name="functions-runtime-versioning"></a>Functions runtime versionshantering

Du kan konfigurera version av Functions runtime med den `FUNCTIONS_EXTENSION_VERSION` appinställningen. Till exempel anger värdet ”~ 2” att Funktionsappen ska använda 2.x enligt dess huvudversionen. Funktionsappar uppgraderas till varje ny delversion när de blir tillgängliga. Läs mer om, inklusive hur du visar den exakta versionen av din funktionsapp [hur du Azure Functions runtime versioner](set-runtime-version.md).

## <a name="repositories"></a>Centrallager
Koden för Azure Functions är öppen källkod och lagrats i GitHub-databaser:

* [Azure Functions-körning](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Azure Functions-portalen](https://github.com/projectkudu/AzureFunctionsPortal)
* [Azure Functions-mallar](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs-SDK-tillägg](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Bindningar
Här är en tabell med alla bindningar som stöds.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Har du problem med fel som kommer från bindningarna? Granska den [bindning felkoder i Azure Functions](functions-bindings-error-pages.md) dokumentation.

## <a name="reporting-issues"></a>Rapporteringsproblem
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions C#-utvecklarreferens](functions-reference-csharp.md)
* [Azure Functions F #-utvecklarreferens](functions-reference-fsharp.md)
* [Azure Functions NodeJS-utvecklare](functions-reference-node.md)
* [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)
* [Azure Functions: Vägen](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) på Azure App Service-teamets blogg. En historik över hur Azure Functions har utvecklats.

