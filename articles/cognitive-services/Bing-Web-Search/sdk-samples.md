---
title: SDK-exempel för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: Använd SDK:n för webbsökning i Bing om du vill lägga till sökfunktioner i ditt Python-, Node.js-, C#- eller Java-program.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: sample
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: f13250bb2d6829cf7f1bf3dd0417974b810a36fe
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350440"
---
# <a name="bing-web-search-sdk-samples"></a>SDK-exempel för webbsökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

SDK:n för webbsökning i Bing är tillgänglig i Python, Node.js, C# och Java. Kodexempel, krav och versionsanvisningar finns på GitHub. Följande scenarion omfattas:

* Fråga ett enstaka ord och skriv ut namn och webbadresser för det första resultatet för webbsidor, bilder, artiklar och videor.
* Fråga en fras, kontrollera antalet resultat och skriv ut namn och webbadress för det första resultatet.
* Fråga en sökterm med svarsfilter inställda på `news` och skriv ut informationen om nyhetsresultaten.
* Fråga en sökterm med `answerCount` och `promote` parametrar och skriv sedan ut information om resultaten.

## <a name="sdks-and-libraries"></a>SDK:er och bibliotek

Använd dessa länkar om du vill få åtkomst till SDK: för det språk du föredrar.

* Kom igång med [Python-exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)
  * Mer information om definitioner och beroenden finns i [Python-bibliotek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-websearch).
* Kom igång med [Node.js exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
  * Se även avsnittet om [Node.js-webbsökning](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesWebSearch).
* Kom igång med [.NET-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
  * [NuGet-paket](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
  * Mer information om definitioner och beroenden finns i [.NET-bibliotek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingWebSearch).
* Kom igång med [Java-exempel](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
  * Mer information om definitioner och beroenden finns i [Java-bibliotek](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch).