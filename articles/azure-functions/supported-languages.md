---
title: Språk som stöds i Azure Functions
description: Lär dig vilka språk som stöds (GA) och vilka som är experimentella eller i förhandsversion.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 029ea753439dca3093bf214a5adfb6d58a1fe567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74942264"
---
# <a name="supported-languages-in-azure-functions"></a>Språk som stöds i Azure Functions

I den här artikeln beskrivs de supportnivåer som erbjuds för språk som du kan använda med Azure Functions.

## <a name="levels-of-support"></a>Stödnivåer

Det finns tre nivåer av stöd:

* **Allmänt tillgänglig (GA)** - Fullt stöd och godkänd för produktionsbruk.
* **Preview** - Ännu inte stöds men förväntas nå GA-status i framtiden.
* **Experimentell** - Stöds inte och kan överges i framtiden; ingen garanti för eventuell förhandsgranskning eller GA-status.

## <a name="languages-by-runtime-version"></a>Språk efter runtime-version 

Det finns [tre versioner av Azure Functions-körningen.](functions-versions.md) I följande tabell visas vilka språk som stöds i varje körningsversion.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Experimentella språk

De experimentella språken i version 1.x skalas inte bra och stöder inte alla bindningar.

Använd inte experimentella funktioner för något som du förlitar dig på, eftersom det inte finns något officiellt stöd för dem. Stödärenden bör inte öppnas för problem med experimentella språk. 

Senare körningsversioner stöder inte experimentella språk. Stöd för nya språk läggs bara till när språket kan stödjas i produktionen. 

### <a name="language-extensibility"></a>Språkutökningsförmåga

Från och med version 2.x är körningen utformad för att erbjuda [språkutökningsbarhet](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). JavaScript- och Java-språken i 2.x-körningen är byggda med den här utökningsbarheten.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar funktioner på språken som stöds finns i följande resurser:

+ [Referens för biblioteksutvecklare i C#-klass](functions-dotnet-class-library.md)
+ [C# skriptutvecklare referens](functions-reference-csharp.md)
+ [Referens för Java-utvecklare](functions-reference-java.md)
+ [Referens för JavaScript-utvecklare](functions-reference-node.md)
+ [Referens för PowerShell-utvecklare](functions-reference-powershell.md)
+ [Referens för Python-utvecklare](functions-reference-python.md)
+ [Referens för TypeScript-utvecklare](functions-reference-node.md#typescript)
