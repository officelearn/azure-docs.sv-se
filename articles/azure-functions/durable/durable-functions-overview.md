---
title: Översikt över Durable Functions – Azure
description: Introduktion till Durable Functions-tillägget för Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 12/22/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: 4c7b4733d05f18d3c30e45fd08c3cf9c50354ebc
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816109"
---
# <a name="what-are-durable-functions"></a>Vad är Durable Functions?

*Durable Functions* är en utökning av [Azure Functions](../functions-overview.md) som gör att du kan skriva tillståndskänsliga funktioner i en serverlös miljö. Tillägget hanterar tillstånd, kontrollpunkter och omstarter.

## <a name="benefits"></a>Fördelar

Med det här tillägget kan du definiera tillståndskänsliga arbetsflöden med hjälp av en [*initierarfunktion*](durable-functions-types-features-overview.md#orchestrator-functions), vilket kan ge följande fördelar:

* Du kan definiera dina arbetsflöden i kod. Det krävs inga JSON-scheman eller designers.
* Andra funktioner kan anropas både synkront och asynkront. Utdata från anropade funktioner kan sparas till lokala variabler.
* Förloppet sparas automatiskt med kontrollpunkter när funktionen väntar. Det lokala tillståndet förloras aldrig när processen återanvänds eller den virtuella datorn startas om.

## <a name="application-patterns"></a>Programmönster

Det huvudsakliga användningsfallet för Durable Functions är att förenkla komplexa, tillståndskänsliga koordinationskrav i serverlösa program. Här följer några vanliga programmönster som kan dra nytta av Durable Functions:

* [Kedjekoppling](durable-functions-concepts.md#chaining)
* [Förgrening](durable-functions-concepts.md#fan-in-out)
* [Asynkrona HTTP-API:er](durable-functions-concepts.md#async-http)
* [Övervakning](durable-functions-concepts.md#monitoring)
* [Mänsklig interaktion](durable-functions-concepts.md#human)

## <a name="language-support"></a>Språk som stöds

Durable Functions stöder för närvarande följande språk:

* **C#**: både [förkompilerade klassbibliotek](../functions-dotnet-class-library.md) och [C#-skript](../functions-reference-csharp.md).
* **F#**: både förkompilerade klassbibliotek och F#-skript. F#-skriptet stöds endast för version 1.x av Azure Functions-körningen.
* **JavaScript**: stöds endast för version 2.x av Azure Functions-körningen. Kräver version 1.7.0 av Durable Functions-tillägget eller en senare version. 

Durable Functions har som mål att stödja alla [Azure Functions-språk](../supported-languages.md). I [Durable Functions-problemlistan](https://github.com/Azure/azure-functions-durable-extension/issues) finns senaste status för arbetet med att stödja ytterligare språk.

Liksom för Azure Functions finns det mallar som hjälper dig att utveckla Durable Functions med hjälp av [Visual Studio 2017](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md) och [Azure-portalen](durable-functions-create-portal.md).

## <a name="billing"></a>Fakturering

Durable Functions debiteras samma som Azure Functions. Mer information finns i [prissättning för Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="jump-right-in"></a>Kom igång snabbt

Du kan komma igång med Durable Functions på mindre än 10 minuter genom att slutföra någon av dessa språkspecifika snabbstartsguider:

* [C# med hjälp av Visual Studio 2017](durable-functions-create-first-csharp.md)
* [JavaScript med hjälp av Visual Studio Code](quickstart-js-vscode.md)

I båda snabbstarterna skapar du och testar en beständig ”hello world”-funktion lokalt. Du publicerar sedan funktionskoden till Azure. Den funktion som du skapar orkestrerar och kedjar samman anrop till andra funktioner.

## <a name="learn-more"></a>Läs mer

Följande video visar fördelarna med Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Eftersom Durable Functions är ett avancerat tillägg för [Azure Functions](../functions-overview.md) är det inte lämpligt för alla program. Läs mer om Durable Functions i [Mönster och tekniska begrepp i Durable Functions](durable-functions-concepts.md). En jämförelse med andra Azure-orkestreringstekniker finns i avsnittet om att [jämföra Azure Functions med Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mönster och tekniska begrepp i Durable Functions](durable-functions-concepts.md)
