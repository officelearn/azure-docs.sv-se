---
title: Azure Functions runtime versioner-översikt
description: Azure Functions har stöd för flera versioner av körningen. Läs om skillnaderna mellan dem och hur du väljer det alternativ som passar dig.
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 8ba12f21d76d8deded047f40489c46657c9380b8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38602125"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions runtime versioner-översikt

 Det finns två större versioner av Azure Functions-körningen: 1.x och 2.x. Endast 1.x är godkänd för användning i produktion. Den här artikeln förklarar vad är nytt i 2.x som förhandsversion.

| Körmiljö | Status |
|---------|---------|
|1.x|Allmänt tillgänglig (GA)|
|2.x|Förhandsversion|

> [!NOTE] 
> Den här artikeln handlar om Molntjänsten Azure Functions. Läs om hur produkten som kan du köra Azure Functions lokalt på [översikt över Azure Functions Runtime](functions-runtime-overview.md).

## <a name="cross-platform-development"></a>Plattformsoberoende utveckling

Runtime 1.x har stöd för utveckling och som är värd för endast i portalen eller på Windows. Runtime 2.x körs på .NET Core, vilket innebär att den kan köras på alla plattformar som stöds av .NET Core, inklusive macOS och Linux. Det möjliggör plattformsoberoende utveckling och värdbaserade scenarier som inte är möjligt med 1.x.

## <a name="languages"></a>Språk

Runtime 2.x använder en ny modell för utökningsbarhet språk. Inledningsvis, utnyttjar JavaScript och Java den nya modellen. Azure Functions 1.x experimentella språk har inte uppdaterats för att använda den nya modellen så att de inte stöds i 2.x. Följande tabell visar vilka programmeringsspråk som stöds i varje runtime-versionen.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Mer information finns i [språk som stöds](supported-languages.md).

## <a name="bindings"></a>Bindningar 

Runtime 2.x använder en ny [bindning modell för utökningsbarhet](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) som ger följande fördelar:

* Stöd för tillägg av bindning från tredje part.
* Frikoppling av runtime och bindningar. På så sätt kan tillägg av bindning vara versionsnummer och utgivna oberoende av varandra. Du kan till exempel välja för att uppgradera till en version av ett tillägg som är beroende av en nyare version av en underliggande SDK.
* En ljusare körningsmiljö där endast bindningarna som används är kända och lästs in av körningen.

Alla inbyggda Azure Functions-bindningar har antagit den här modellen och ingår inte längre som standard, förutom den Timer som utlösaren och HTTP-utlösaren. Dessa tillägg installeras automatiskt när du skapar funktioner via verktyg som Visual Studio eller via portalen.

Följande tabell visar vilka stöds i varje runtime-versionen.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>Kända problem i 2.x

Läs mer om stödet för bindningar och andra funktionella gapet i 2.x [Runtime 2.0 kända problem](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Koda och testa Azure Functions lokalt](functions-run-local.md)
* [Hur du Azure Functions runtime versioner](set-runtime-version.md)
* [Viktig information](https://github.com/Azure/azure-functions-host/releases)