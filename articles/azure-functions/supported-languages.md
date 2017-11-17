---
title: "Språk som stöds i Azure Functions"
description: "Lär dig vilka språk som stöds (GA) och som är experiment eller i förhandsgranskningen."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: tdykstra
ms.openlocfilehash: 5786a206b258cfe7c48f52ead9b5a4cceb64cd5f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="supported-languages-in-azure-functions"></a>Språk som stöds i Azure Functions

Den här artikeln förklarar nivåer av stöd erbjuds för språk som du kan använda med Azure Functions.

## <a name="levels-of-support"></a>Stöd

Det finns tre nivåer för support:

* **Allmänt tillgänglig (GA)** – fullständigt stöd för och godkänts för produktion.
* **Förhandsgranska** - har ännu inte stöds men förväntas komma GA status i framtiden.
* **Försök** - stöds inte och kan överges i framtiden; ingen garanti eventuell Förhandsgranska eller GA status.

## <a name="languages-in-runtime-1x-and-2x"></a>Språk i runtime 1.x och 2.x

[Två versioner av Azure Functions-runtime](functions-versions.md) är tillgängliga. 1.x-runtime är GA. Det är bara körningsmiljön som godkänts för program i produktion. 2.x-runtime är för närvarande under förhandsgranskning, så de språk som stöds finns i förhandsgranskningen. I följande tabell visas vilka språk som stöds i varje version av körningsmiljön.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Försök språk

Försök språk i 1.x skalas inte efter era behov och har stöd inte för alla bindningar. Till exempel Python är långsam eftersom Functions-runtime körs *python.exe* med varje funktionsanrop. Och medan Python stöder HTTP-bindningar, kan inte öppna request-objektet.

Försök stöd för PowerShell är begränsad till version 4.0 eftersom det är vad som är installerat på virtuella datorer som funktionen appar som körs på. Om du vill köra PowerShell-skript kan du överväga att [Azure Automation](https://azure.microsoft.com/services/automation/).

2.x-körning stöder inte experiment språk. I 2.x, kommer vi lägga till stöd för ett språk endast när det väl skalas och har stöd för avancerade utlösare.

Om du vill använda ett av de språk som endast är tillgängliga i 1.x kvar på 1.x-körningsmiljön. Men inte använder experiment språk för sådant som du förlita dig på, eftersom det finns inget officiella stöd för dessa. Du kan begära hjälp av [skapar GitHub problem](https://github.com/Azure/azure-webjobs-sdk-script/issues), men Supportfall bör inte öppnas för problem med experiment språk. 

### <a name="language-extensibility"></a>Språk utökningsbarhet

2.x-körningsmiljön har utformats för att erbjuda [språk utökningsbarhet](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Modellen är bland de första språk ska baseras på den här utökningsbarhet Java som är en förhandsversion i 2.x.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder ett av språken GA eller preview i Azure Functions, finns i följande resurser:

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)