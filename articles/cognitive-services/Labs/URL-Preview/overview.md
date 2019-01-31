---
title: Vad är förhandsversion av projekt-URL?
titlesuffix: Azure Cognitive Services
description: Introduktion till förhandsgranskning av projekt-URL.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: overview
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 2e974acb1a7583fbd1a961eda4daa3732fbeff7c
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218181"
---
# <a name="what-is-project-url-preview"></a>Vad är förhandsversion av projekt-URL?
Slutpunkten för URL-förhandsgranskningen tar en URL-frågeparameter och returnerar ett JSON-svar med namnet på målresursen, en kort beskrivning och en länk till en bild som ska visas i en förhandsgranskning. Svaret innehåller även flaggan [isFamilyFriendly](url-preview-reference.md#query-parameters), som anger huruvida URL:en innehåller innehåll för vuxna eller piratkopierat eller annat olagligt innehåll. 

För att få resultat för URL-förhandsgranskning skickar du en GET-begäran och inkluderar huvudet *Ocp-Apim-Subscription-Key* med en giltig token:  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
Svaret: 
```
HTTP Headers:
BingAPIs-TraceId: 3CC74C94769440C0851D9DF0869FCE7F
BingAPIs-SessionId: 52219085A6364692958C9C83983A0DBA
X-MSEdge-ClientID: 13D44DC2DE946B4B0F25460CDF036AD6
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 3CC74C94769440C0851D9DF0869FCE7F Ref B: CO1EDGE0315 Ref C: 2018-04-11T18:47:40Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https:\/\/swiftkey.com\/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https:\/\/swiftkey.com\/images\/og\/default.jpg"
  }
}

```
## <a name="scenarios"></a>Scenarier 

API:et för URL-förhandsgranskning stöder korta beskrivningar av webbresurser. Utvecklare använda det för att skapa omfattande förhandsgranskningsfunktioner.  Användare kan dela eller bokmärka webbplatser, nyheter, bloggar, forum osv. Detta API kan även användas för innehållsmoderering.    

Program använder URL-förhandsgranskning för att skicka webbförfrågningar till slutpunkten med en fråga som tilldelas till den URL som ska förhandsgranskas.  JSON-svaret innehåller informationen om förhandsgranskningen: namn, beskrivning av resursen, flaggan *familyFriendly* samt länkar som ger åtkomst till en representativ bild och till den fullständiga resursen online. 

## <a name="terms-of-use"></a>Användningsvillkor
Använd endast data från Projekt-URL-förhandsgranskning för att visa förhandsgranskningar och miniatyrbilder som är hyperlänkade till sina källwebbplatser, i slutanvändarinitierad URL-delning på sociala medier, chattrobot eller liknande kanaler. Du får inte kopiera, lagra eller cachelagra data som du tar emot via Projekt-URL-förhandsgranskning. Respektera eventuella begäranden att inaktivera förhandsgranskningar som du får från ägare av webbplatser eller innehåll.

Du eller en tredje part för din räkning får inte använda, behålla, lagra, cachelagra, dela eller distribuera data från API:et för URL-förhandsgranskning i syfte att testa, utveckla, träna, distribuera eller tillgängliggöra tjänster eller funktioner som inte kommer från Microsoft. 

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg
- [Snabbstart för C#](csharp.md)
- [Snabbstart för Java](java-quickstart.md)
- [Snabbstart för JavaScript](javascript.md)
- [Snabbstart för Node](node-quickstart.md)
- [Snabbstart för Python](python-quickstart.md)
