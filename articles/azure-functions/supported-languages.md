---
title: Språk som stöds i Azure Functions
description: Lär dig vilka språk som stöds (GA) och vilka som är experimentella eller i för hands versionen.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: eac67c43915edb0aed273df40e7b08ca846062bf
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802466"
---
# <a name="supported-languages-in-azure-functions"></a>Språk som stöds i Azure Functions

I den här artikeln beskrivs de support nivåer som erbjuds för språk som du kan använda med Azure Functions.

## <a name="levels-of-support"></a>Support nivåer

Det finns tre support nivåer:

* **Allmänt tillgänglig (ga)** – fullständigt stöd och godkänd för produktions användning.
* För **hands version** – stöds ännu inte, men förväntas komma att uppnå GA-status i framtiden.
* **Experimentell** – stöds inte och kan överges i framtiden. ingen garanti för eventuell för hands version eller GA-status.

## <a name="languages-by-runtime-version"></a>Språk efter körnings version 

[Det finns två versioner av Azure Functions runtime](functions-versions.md) . I följande tabell visas vilka språk som stöds i varje körnings version.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Experimentella språk

Experiment språken i version 1. x skalar inte bra och har inte stöd för alla bindningar.

Använd inte experimentella funktioner för något som du förlitar dig på, eftersom det inte finns något offentligt stöd för dem. Support ärenden bör inte öppnas för problem med experimentella språk. 

Version 2. x-körningsmiljön stöder inte experiment språk. Stöd för nya språk läggs bara till om språket kan användas i produktionen. 

### <a name="language-extensibility"></a>Språk utökning

Från och med version 2. x är körnings miljön utformad för att erbjuda [språk utöknings barhet](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Java Script-och Java-språken i 2. x-körningsmiljön har skapats med den här utökningen.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar funktioner i de språk som stöds finns i följande resurser:

+ [C#referens för klass biblioteks utvecklare](functions-dotnet-class-library.md)
+ [C#referens för skript utvecklare](functions-reference-csharp.md)
+ [Referens för Java-utvecklare](functions-reference-java.md)
+ [JavaScript-referens för utvecklare](functions-reference-node.md)
+ [Referens för PowerShell-utvecklare](functions-reference-powershell.md)
+ [Python-referens för utvecklare](functions-reference-python.md)
+ [TypeScript Developer-referens](functions-reference-node.md#typescript)
