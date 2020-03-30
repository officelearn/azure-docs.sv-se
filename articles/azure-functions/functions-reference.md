---
title: Vägledning för utveckling av Azure-funktioner
description: Lär dig de Azure Functions-begrepp och tekniker som du behöver för att utveckla funktioner i Azure, över alla programmeringsspråk och bindningar.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 7dd7ef3c4833fb9ffa3781f06faba4f40cd40cfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276626"
---
# <a name="azure-functions-developers-guide"></a>Guide för Azure Functions-utvecklare
I Azure Functions delar specifika funktioner några grundläggande tekniska koncept och komponenter, oavsett vilket språk eller vilket bindning du använder. Innan du börjar lära dig information som är specifik för ett visst språk eller en viss bindning måste du läsa igenom den här översikten som gäller för dem alla.

Den här artikeln förutsätter att du redan har läst [översikten över Azure Functions](functions-overview.md).

## <a name="function-code"></a>Funktionskod
En *funktion* är det primära konceptet i Azure Functions. En funktion innehåller två viktiga bitar - din kod, som kan skrivas på en mängd olika språk, och några config, function.json filen. För kompilerade språk genereras den här konfigurationsfilen automatiskt från anteckningar i koden. För skriptspråk måste du själv ange config-filen.

Filen function.json definierar funktionens utlösare, bindningar och andra konfigurationsinställningar. Varje funktion har endast en utlösare. Körningen använder den här konfigurationsfilen för att bestämma vilka händelser som ska övervakas och hur data ska överföras till och returnera data från en funktionskörning. Följande är en exempelfunktion.json-fil.

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

Mer information finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

Egenskapen `bindings` är där du konfigurerar både utlösare och bindningar. Varje bindning delar några vanliga inställningar och vissa inställningar som är specifika för en viss typ av bindning. Varje bindning kräver följande inställningar:

| Egenskap | Värden/typer | Kommentarer |
| --- | --- | --- |
| `type` |sträng |Bindningstyp. Till exempel `queueTrigger`. |
| `direction` |"in", "ut" |Anger om bindningen är för att ta emot data till funktionen eller skicka data från funktionen. |
| `name` |sträng |Namnet som används för bundna data i funktionen. För C#är detta ett argumentnamn. för JavaScript är det nyckeln i en nyckel/värdelista. |

## <a name="function-app"></a>Funktionsapp
En funktionsapp tillhandahåller en körningskontext i Azure där dina funktioner körs. Som sådan är det enheten för distribution och hantering för dina funktioner. En funktionsapp består av en eller flera enskilda funktioner som hanteras, distribueras och skalas tillsammans. Alla funktioner i en funktionsapp delar samma prisplan, distributionsmetod och körningsversion. Tänk på en funktionsapp som ett sätt att organisera och kollektivt hantera dina funktioner. Mer information finns i [Så här hanterar du en funktionsapp](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Alla funktioner i en funktionsapp måste vara författade på samma språk. I [tidigare versioner](functions-versions.md) av Azure Functions-körningen var detta inte nödvändigt.

## <a name="folder-structure"></a>Mappstrukturen
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Ovanstående är standardmappstrukturen (och rekommenderad) för en funktionsapp. Om du vill ändra filplatsen för en funktions `scriptFile` kod ändrar du avsnittet i _filen function.json._ Vi rekommenderar också att du använder [paketdistribution](deployment-zip-push.md) för att distribuera ditt projekt till din funktionsapp i Azure. Du kan också använda befintliga verktyg som [kontinuerlig integrering och distribution](functions-continuous-deployment.md) och Azure DevOps.

> [!NOTE]
> Om du distribuerar ett paket manuellt måste du distribuera filen _host.json-filen_ och funktionsmapparna direkt till `wwwroot` mappen. Ta inte `wwwroot` med mappen i distributionerna. Annars hamnar du `wwwroot\wwwroot` med mappar.

#### <a name="use-local-tools-and-publishing"></a>Använda lokala verktyg och publicering
Funktionsappar kan skapas och publiceras med hjälp av en mängd olika verktyg, inklusive [Visual Studio,](./functions-develop-vs.md) [Visual Studio Code,](functions-create-first-function-vs-code.md) [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md)och Azure Functions [Core Tools](./functions-develop-local.md). Mer information finns i [Kod och testa Azure Functions lokalt](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a>Redigera funktioner i Azure-portalen
Med Functions-redigeraren som är inbyggd i Azure-portalen kan du uppdatera koden och filen *function.json* direkt infogad. Detta rekommenderas endast för små ändringar eller proofs of concept - bästa praxis är att använda ett lokalt utvecklingsverktyg som VS Code.

## <a name="parallel-execution"></a>Parallell körning
När flera utlösande händelser inträffar snabbare än en entrådig funktionskörning kan bearbeta dem, kan körningen anropa funktionen flera gånger parallellt.  Om en funktionsapp använder [abonnemanget För förbrukning](functions-scale.md#how-the-consumption-and-premium-plans-work)kan funktionsappen skalas ut automatiskt.  Varje instans av funktionsappen, oavsett om appen körs på förbrukningsvärdplanen eller en vanlig [App Service-värdplan,](../app-service/overview-hosting-plans.md)kan bearbeta samtidiga funktionsinrop parallellt med flera trådar.  Det maximala antalet samtidiga funktionsanrop i varje funktionsappinstans varierar beroende på vilken typ av utlösare som används samt de resurser som används av andra funktioner i funktionsappen.

## <a name="functions-runtime-versioning"></a>Funktioner körningsversion

Du kan konfigurera versionen av funktionskörningen med hjälp av appinställningen. `FUNCTIONS_EXTENSION_VERSION` Värdet "~3" anger till exempel att funktionsappen kommer att använda 3.x som huvudversion. Funktionsappar uppgraderas till varje ny delversion när de släpps. Mer information, inklusive hur du visar den exakta versionen av funktionsappen, finns i [Så här inriktar du dig på Azure Functions runtime-versioner](set-runtime-version.md).

## <a name="repositories"></a>Centrallager
Koden för Azure Functions är öppen källkod och lagras i GitHub-databaser:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Värd för Azure Functions](https://github.com/Azure/azure-functions-host/)
* [Portalen Azure Functions](https://github.com/azure/azure-functions-ux)
* [Azure Functions-mallar](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK-tillägg](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Bindningar
Här är en tabell över alla bindningar som stöds.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Har du problem med fel som kommer från bindningarna? Granska dokumentationen för [Azure Functions Binding Error Codes.](functions-bindings-error-pages.md)

## <a name="reporting-issues"></a>Rapportera ärenden
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)
* [Koda och testa Azure Functions lokalt](./functions-develop-local.md)
* [Metodtips för Azure-funktioner](functions-best-practices.md)
* [Azure Functions C#-utvecklarreferens](functions-dotnet-class-library.md)
* [Utvecklarreferens för Azure Functions Node.js](functions-reference-node.md)
