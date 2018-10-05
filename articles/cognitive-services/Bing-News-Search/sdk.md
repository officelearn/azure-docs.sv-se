---
title: Sök på Bing News SDK
titleSuffix: Azure Cognitive Services
description: Bing News Search SDK för program som söker på webben.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: b1d9eaa35416adfa11647f2116171256f82fe095
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801241"
---
# <a name="bing-search-sdk"></a>Bing Search SDK
Nyhetssökning i Bing-exempel innehåller scenarier som:
1. Fråga efter nyheter för sökvillkor med `market` och `count` parametrar, kontrollera antalet resultat och skriva ut `totalEstimatedMatches`, namn, URL: en, beskrivning, publiceringstid och namnet på leverantören av det första resultatet nyheter.
2. Fråga efter de senaste nyheterna för sökvillkor med `freshness` och `sortBy` parametrar, kontrollera antalet resultat och skriva ut `totalEstimatedMatches`, URL, beskrivning, publiceringstid och namnet på leverantören av det första resultatet nyheter.
3. Fråga efter kategori Nyheter för `movie` och `TV entertainment` kontrollera antalet resultat med säker sökning och skriva ut kategori, namn, URL: en, beskrivning, publiceringstid och namnet på leverantören av det första resultatet nyheter.
4. Fråga populära nyhetsämnen i Bing, kontrollera antalet resultat och skriva ut namn, texten för frågan, `webSearchUrl`, `newsSearchUrl` och bild-URL för det första resultatet nyheter.

SDK: er för Bing Search gör det lätt tillgängliga i följande programmeringsspråken sökfunktioner i webb:
* Kom igång med [.NET-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
    * [NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)
    * Se även [.NET-biblioteken](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch) för definitioner och beroenden.
* Kom igång med [Node.js-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Se även [Node.js-bibliotek](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch) för definitioner och beroenden.
* Kom igång med [Java-exempel](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Se även [Java-bibliotek](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch) för definitioner och beroenden.
* Kom igång med [Python-exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Se även [Python-biblioteken](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch) för definitioner och beroenden.

SDK-prov för varje språk som innehåller en Viktigt-fil med information om nödvändiga komponenter och installation/körs exemplen.