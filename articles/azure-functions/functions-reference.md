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
ms.openlocfilehash: 01fce9813299465767bdaed7f3c2939813cfab87
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "56339501"
---
# <a name="azure-functions-developers-guide"></a>Azure Functions-guide för utvecklare
I Azure Functions dela några tekniska nyckelkoncept och komponenter, oavsett språk eller bindning som du använder specifika funktioner. Innan du sätter igång till learning information som gäller för ett visst språk eller en bindning, bör du läsa igenom den här översikten som gäller för alla.

Den här artikeln förutsätter att du redan har läst den [översikt över Azure Functions](functions-overview.md).

## <a name="function-code"></a>Funktionskoden
En *funktionen* är det primära konceptet i Azure Functions. En funktion innehåller två viktiga delar – din kod, som kan skrivas på olika språk och vissa konfigurationer, function.json-filen. Den här konfigurationsfilen genereras automatiskt för kompilerade språk från kommentarer i din kod. Skriptspråk måste du ange konfigurationsfilen själv.

Filen function.json definierar funktionens utlösare och bindningar andra konfigurationsinställningar. Varje funktion har en utlösare. Körningen använder den här konfigurationsfilen för att hitta händelser att övervaka och hur du överför data till och returnera data från körning av en funktion. Följande är en exempelfil function.json.

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

Den `bindings` egenskapen är där du konfigurerar både utlösare och bindningar. Varje bindning beskriver några vanliga inställningar och vissa inställningar som är specifika för en viss typ av bindning. Alla bindningar kräver följande inställningar:

| Egenskap  | Värdetyper / | Kommentarer |
| --- | --- | --- |
| `type` |sträng |Bindningstyp. Till exempel `queueTrigger`. |
| `direction` |”i”, ”ut” |Anger om bindningen för mottagning av data i funktionen eller skickar data från funktionen. |
| `name` |sträng |Namnet som används för bundna data i funktionen. Detta är ett argument-name; för C# för JavaScript är det nyckeln i en nyckel/värde-lista. |

## <a name="function-app"></a>Funktionsapp
En funktionsappen är en körningskontexten i Azure där dina funktioner körs. En funktionsapp består av en eller flera enskilda funktioner som hanteras, distribueras och skalas tillsammans. Alla funktioner i en funktionsapp delar samma prisplanen, kontinuerlig distribution och runtime-versionen. Tänk på en funktionsapp som ett sätt att ordna och hantera dina funktioner gemensamt. 

> [!NOTE]
> Alla funktioner i en funktionsapp måste skrivas i samma språk. I [tidigare versioner](functions-versions.md) av Azure Functions-körning, det var inte krävs.

## <a name="folder-structure"></a>mappstruktur
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Ovanstående är standardinställningen (och rekommenderas) mappstrukturen för en funktionsapp. Om du vill ändra sökvägen till en funktion kod kan ändra den `scriptFile` delen av den _function.json_ fil. Vi rekommenderar också med [paketera distribution](deployment-zip-push.md) att distribuera projektet till din funktionsapp i Azure. Du kan också använda befintliga verktyg som [kontinuerlig integrering och distribution](functions-continuous-deployment.md) och Azure DevOps.

> [!NOTE]
> Om du distribuerar ett paket manuellt, se till att distribuera din _host.json_ filen och fungerar mappar direkt till den `wwwroot` mapp. Ta inte med den `wwwroot` mapp i dina distributioner. Annars kan du få `wwwroot\wwwroot` mappar.

#### <a name="use-local-tools-and-publishing"></a>Använd lokala verktyg och publicering
Funktionsappar kan skapas och publicerade med hjälp av en mängd olika verktyg, bland annat [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md), och [Azure Functions Core Tools](./functions-develop-local.md). Mer information finns i [kod och testa Azure Functions lokalt](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a id="fileupdate"></a> Så här redigerar du funktioner i Azure portal
Functions-redigeraren är inbyggda i Azure-portalen kan du uppdatera din kod och dina *function.json* filen direkt infogade. Det här rekommenderas endast för små ändringar eller koncepttester – bästa praxis är att använda ett verktyg för lokal utveckling som VS Code.

## <a name="parallel-execution"></a>Parallell körning
När flera utlösande händelser inträffar snabbare än en single-threaded funktionskörningen kan bearbeta dem, kan körningen anropa funktionen flera gånger i parallellt.  Om en funktionsapp med hjälp av den [Förbrukningsvärdplanen](functions-scale.md#how-the-consumption-plan-works), funktionsappen genom att skala upp automatiskt.  Varje instans av function-app om appen körs på förbrukningen som är värd för plan eller en vanlig [App Service-värdplanen](../app-service/overview-hosting-plans.md), kan bearbeta samtidiga funktionsanrop parallellt med flera trådar.  Det maximala antalet samtidiga funktionsanrop i varje funktionen app-instansen varierar beroende på vilken typ av utlösare som används, samt de resurser som används av andra funktioner i funktionsappen.

## <a name="functions-runtime-versioning"></a>Functions runtime versionshantering

Du kan konfigurera version av Functions runtime med den `FUNCTIONS_EXTENSION_VERSION` appinställningen. Till exempel anger värdet ”~ 2” att Funktionsappen ska använda 2.x enligt dess huvudversionen. Funktionsappar uppgraderas till varje ny delversion när de blir tillgängliga. Läs mer om, inklusive hur du visar den exakta versionen av din funktionsapp [hur du Azure Functions runtime versioner](set-runtime-version.md).

## <a name="repositories"></a>Centrallager
Koden för Azure Functions är öppen källkod och lagrats i GitHub-databaser:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions-värd](https://github.com/Azure/azure-functions-host/)
* [Azure Functions-portalen](https://github.com/azure/azure-functions-ux)
* [Azure Functions-mallar](https://github.com/azure/azure-functions-templates)
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

* [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)
* [Koda och testa Azure Functions lokalt](./functions-develop-local.md)
* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions C#-utvecklarreferens](functions-reference-csharp.md)
* [Azure Functions NodeJS-utvecklare](functions-reference-node.md)
