---
title: Språk som stöds i Azure Functions
description: Lär dig vilka språk som stöds (GA) och som är experimentella versioner eller i en förhandsversion.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: 5f55122b3bf4bb7160459d524b20dd1303cc0fd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60325463"
---
# <a name="supported-languages-in-azure-functions"></a>Språk som stöds i Azure Functions

Den här artikeln beskriver supportnivåerna erbjuds för språk som du kan använda med Azure Functions.

## <a name="levels-of-support"></a>Supportnivåer

Det finns tre nivåer av support:

* **Allmänt tillgänglig (GA)** – fullständigt stöd och godkänts för användning i produktion.
* **Förhandsversion av** – har ännu inte stöds men förväntas bli allmänt tillgänglig status i framtiden.
* **Experimentella** – stöds inte och kan vara övergivna i framtiden; ingen garanti för slutlig förhandsversion eller allmän tillgänglighet status.

## <a name="languages-in-runtime-1x-and-2x"></a>Språk i runtime 1.x och 2.x

[Två versioner av Azure Functions-runtime](functions-versions.md) är tillgängliga. I följande tabell visas vilka språk som stöds i varje runtime-versionen.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Experimentella språk

Experimentella språk i version 1.x inte skalbarheten och stöder inte alla bindningar. Till exempel är experimentella stöd för PowerShell i 1.x begränsad till version 5.1, eftersom det är vad som är installerat som standard på de virtuella datorerna på vilka funktionsappar kör. Om du vill köra PowerShell-skript kan du överväga att [Azure Automation](https://azure.microsoft.com/services/automation/).

Använd inte experimentella funktioner för sådant som du litar på, eftersom det finns inget officiella stöd för dessa. Bör inte att öppna supportärenden för problem med experimentella språk. 

Version 2.x-körningen stöder inte experimentella språk. Stöd för nya språk läggs endast när språket som kan användas i produktion. 

### <a name="language-extensibility"></a>Utökningsbarhet för språk

2.x-körningen är utformad att erbjuda [språk utökningsbarhet](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). JavaScript och Java-språk i 2.x-körningen skapas med den här utökningsbarhet.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du använder ett av de allmänt tillgängliga eller preview språk i Azure Functions, finns i följande resurser:

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)

> [!div class="nextstepaction"]
> [Python](functions-reference-python.md)
