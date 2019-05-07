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
ms.openlocfilehash: 8839fc0fb9f19a1cfa95a4191213503dba7602c6
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148827"
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

Experimentella språk i version 1.x inte skalbarheten och stöder inte alla bindningar.

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
