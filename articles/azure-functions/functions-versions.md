---
title: "Azure Functions runtime versioner: översikt"
description: "Azure Functions stöder flera versioner av körningsmiljön. Lär dig skillnaderna mellan dem och hur du väljer det som passar dig."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 9f916aaa8032ff519709d73a1c1f51195f811686
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions runtime versioner: översikt

 Det finns två viktiga versioner av Azure Functions-runtime.: 1.x och 2.x. Den här artikeln förklarar hur du väljer högre version som ska användas.

> [!IMPORTANT] 
> Runtime 1.x är den enda versionen som godkänts för produktion.

| Körning | Status |
|---------|---------|
|1.x|Allmänt tillgänglig (GA)|
|2.x|Förhandsversion|

Information om hur du konfigurerar en funktionsapp eller din utvecklingsmiljö för en viss version finns [så avsedda för Azure Functions-runtime versioner](set-runtime-version.md) och [koden och testa Azure Functions lokalt](functions-run-local.md).

## <a name="cross-platform-development"></a>Plattformsoberoende appar

Runtime 1.x stöder utveckling och värd endast i portalen eller i Windows. Runtime 2.x körs på .NET Core, vilket betyder att det kan köras på alla plattformar som stöds av .NET Core, inklusive macOS- och Linux. Detta gör att plattformsoberoende utvecklings- och värdscenarier som inte är möjligt med 1.x.

## <a name="languages"></a>Språk

Runtime 2.x använder ett nytt språk-modellen för utökning. Från början, utnyttjar JavaScript och Java den nya modellen. Azure Functions 1.x experiment språk har inte uppdaterats för att använda den nya modellen, så att de inte stöds i 2.x. I följande tabell anger vilket programmeringsspråk som stöds i varje version av körningsmiljön.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Mer information finns i [språk som stöds](supported-languages.md).

## <a name="bindings"></a>Bindningar 

Runtime 2.x använder en ny [modellen för utökning av bindning](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) som ger följande fördelar:

* Stöd för tillägg från tredje part bindning.
* Frikoppling av körningsmiljön och bindningar. Detta gör att bindningen tillägg ska vara en ny version och oberoende släpps. Du kan till exempel välja för att uppgradera till en version av ett tillägg som förlitar sig på en nyare version av en underliggande SDK.
* En ljusare körningsmiljö, där endast bindningarna som används är kända och läses in av körningsmiljön.

Alla inbyggda Azure Functions-bindningar har antagit den här modellen och inte längre ingår som standard, förutom de Timer som utlösaren och HTTP-utlösaren. Dessa tillägg installeras automatiskt när du skapar funktioner via verktyg som Visual Studio eller via portalen.

I följande tabell anger vilka fältparameterbindningar stöds i varje version av körningsmiljön.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>Kända problem i 2.x

Mer information om stöd för bindningar och andra funktionella luckor i 2.x finns [Runtime 2.0 kända problem](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Körningsmiljön 2.0 i din lokala utvecklingsmiljö](functions-run-local.md)

> [!div class="nextstepaction"]
> [Se viktig information för runtime-versioner](https://github.com/Azure/azure-webjobs-sdk-script/releases)