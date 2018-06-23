---
title: Bing Search SDK | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Bing Search SDK för program som söker på webben.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: 4a40ea665e153536d2322706b455598902ce41eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355269"
---
# <a name="bing-search-sdk"></a>Bing Search SDK
Bing News Sök API-exempel finns scenarier som:
1. Fråga Nyheter för sökvillkoren med `market` och `count` parametrar, kontrollera antalet resultat och skriva ut `totalEstimatedMatches`, namn, URL, beskrivning, publicerade tid och namnet på det första resultatet nyheter-providern.
2. Hämta de senaste nyheterna sökord med `freshness` och `sortBy` parametrar, kontrollera antalet resultat och skriva ut `totalEstimatedMatches`, URL, beskrivning, publicerade tid och namnet på det första resultatet nyheter-providern.
3. Fråga kategori Nyheter för `movie` och `TV entertainment` kontrollera antalet resultat med säker sökning och skriva ut kategori, namn, URL, beskrivning, publicerade tid och namnet på det första resultatet nyheter-providern.
4. Frågan trender Nyheter i Bing, kontrollera antalet resultat och skriva ut namn, texten i frågan, `webSearchUrl`, `newsSearchUrl` och bild-URL för det första resultatet nyheter.

SDK: er för Bing Search göra sökfunktionen web åtkomlig i följande programmeringsspråk:
* Kom igång med [.NET-exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
    * [NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0)
    * Se även [.NET-biblioteken](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch) för definitioner och beroenden.
* Kom igång med [Node.js-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Se även [Node.js-bibliotek](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch) för definitioner och beroenden.
* Kom igång med [Java-exempel](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Se även [Java-bibliotek](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch) för definitioner och beroenden.
* Kom igång med [Python-exempel](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Se även [Python-bibliotek](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch) för definitioner och beroenden.

SDK-exempel för varje språk som innehåller en Viktigt-fil med information om krav och installera/körs exemplen.