---
title: Språk som stöds i Azure Functions
description: Lär dig vilka språk som stöds (GA) och som är experimentella versioner eller i en förhandsversion.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: glenga
ms.openlocfilehash: 00f291e903948bf43bc997816b6072186cf1f889
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343091"
---
# <a name="supported-languages-in-azure-functions"></a>Språk som stöds i Azure Functions

Den här artikeln beskriver supportnivåerna erbjuds för språk som du kan använda med Azure Functions.

## <a name="levels-of-support"></a>Supportnivåer

Det finns tre nivåer av support:

* **Allmänt tillgänglig (GA)** – fullständigt stöd och godkänts för användning i produktion.
* **Förhandsversion av** – har ännu inte stöds men förväntas bli allmänt tillgänglig status i framtiden.
* **Experimentella** – stöds inte och kan vara övergivna i framtiden; ingen garanti för slutlig förhandsversion eller allmän tillgänglighet status.

## <a name="languages-in-runtime-1x-and-2x"></a>Språk i runtime 1.x och 2.x

[Två versioner av Azure Functions-runtime](functions-versions.md) är tillgängliga. 1.x-körningen är GA. Det är den enda runtime som har godkänts för program i produktion. 2.x-körningen är för närvarande i förhandsversion, så att de språk som stöds finns i förhandsversion. I följande tabell visas vilka språk som stöds i varje runtime-versionen.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Experimentella språk

Experimentella språk i 1.x skalbarheten inte och stöder inte alla bindningar. Till exempel Python är långsam eftersom Functions-körningen körs *python.exe* med varje funktionsanrop. Och medan Python har stöd för HTTP-bindningar, den kan inte komma åt Begäranobjektet.

Experimentella stöd för PowerShell är begränsad till version 4.0 eftersom det är vad som är installerat på de virtuella datorerna som funktionsappar körs på. Om du vill köra PowerShell-skript kan du överväga att [Azure Automation](https://azure.microsoft.com/services/automation/).

2.x-körningen stöder inte experimentella språk. I 2.x kan vi lägga till stöd för ett språk som endast när den goda och har stöd för avancerade utlösare.

Om du vill använda en av de språk som är tillgängliga i 1.x kvar på 1.x-körningen. Men Använd inte experimentella språk för sådant som du litar på, eftersom det finns inget officiella stöd för dessa. Du kan be om hjälp genom [skapa GitHub-ärenden](https://github.com/Azure/azure-webjobs-sdk-script/issues), men bör inte att öppna supportärenden för problem med experimentella språk. 

### <a name="language-extensibility"></a>Utökningsbarhet för språk

2.x-körningen är utformad att erbjuda [språk utökningsbarhet](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Bland de första språk som ska väljas utifrån den här utökningsbarhet är modellen Java, som finns i förhandsversion i 2.x.

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