---
title: Språk som stöds i Azure Functions
description: Lär dig vilka språk som stöds (GA) och vilka som är experimentella eller i för hands versionen.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 029ea753439dca3093bf214a5adfb6d58a1fe567
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74942264"
---
# <a name="supported-languages-in-azure-functions"></a>Språk som stöds i Azure Functions

I den här artikeln beskrivs de support nivåer som erbjuds för språk som du kan använda med Azure Functions.

## <a name="levels-of-support"></a>Support nivåer

Det finns tre support nivåer:

* **Allmänt tillgänglig (ga)** – fullständigt stöd och godkänd för produktions användning.
* För **hands version** – stöds ännu inte, men förväntas komma att uppnå GA-status i framtiden.
* **Experimentell** – stöds inte och kan överges i framtiden. ingen garanti för eventuell för hands version eller GA-status.

## <a name="languages-by-runtime-version"></a>Språk efter körnings version 

[Det finns tre versioner av Azure Functions runtime](functions-versions.md) . I följande tabell visas vilka språk som stöds i varje körnings version.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Experimentella språk

Experiment språken i version 1. x skalar inte bra och har inte stöd för alla bindningar.

Använd inte experimentella funktioner för något som du förlitar dig på, eftersom det inte finns något offentligt stöd för dem. Support ärenden bör inte öppnas för problem med experimentella språk. 

Senare körnings versioner stöder inte experiment språk. Stöd för nya språk läggs bara till om språket kan användas i produktionen. 

### <a name="language-extensibility"></a>Språk utökning

Från och med version 2. x är körnings miljön utformad för att erbjuda [språk utöknings barhet](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Java Script-och Java-språken i 2. x-körningsmiljön har skapats med den här utökningen.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar funktioner i de språk som stöds finns i följande resurser:

+ [C#-referens för klass bibliotek i C#](functions-dotnet-class-library.md)
+ [C#-referens för skript utvecklare](functions-reference-csharp.md)
+ [Referens för Java-utvecklare](functions-reference-java.md)
+ [JavaScript-referens för utvecklare](functions-reference-node.md)
+ [Referens för PowerShell-utvecklare](functions-reference-powershell.md)
+ [Python-referens för utvecklare](functions-reference-python.md)
+ [TypeScript Developer-referens](functions-reference-node.md#typescript)
