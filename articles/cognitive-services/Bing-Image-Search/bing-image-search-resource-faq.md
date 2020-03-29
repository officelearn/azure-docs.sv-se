---
title: Vanliga frågor och svar (Vanliga frågor) - API för bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Hitta svar på vanliga frågor om begrepp, kod och scenarier relaterade till API:et för bildsökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 6841e573446103466e2719797da9e4161b70b5a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881701"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Vanliga frågor och svar om API:et för bildsökning av Bing

Hitta svar på vanliga frågor om begrepp, kod och scenarier relaterade till API:et för Bing-avbildningssökning för Microsoft Cognitive Services på Azure.

## <a name="response-headers-in-javascript"></a>Svarsrubriker i JavaScript

Följande rubriker kan förekomma i svar från API:et för bildsökning i Bing.

| `Attribute`         | `Description` |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |Det unika ID som Bing har tilldelat användaren |
| `BingAPIs-Market`   |Den marknad som användes för att uppfylla begäran |
| `BingAPIs-TraceId`  |Loggposten på Bing API-servern för den här begäran (för support) |

Det är särskilt viktigt att behålla klient-ID:n och returnera det med efterföljande begäranden. När du gör detta kommer sökningen att använda tidigare kontext i rankningsresultaten och ger också en konsekvent användarupplevelse.

Men när du anropar API:et för bing-bildsökning från JavaScript kan webbläsarens inbyggda säkerhetsfunktioner (CORS) hindra dig från att komma åt värdena för dessa rubriker.

För att få tillgång till rubrikerna kan du göra API-begäran om api för Bing-bildsökning via en CORS-proxy. Svaret från en sådan proxy har ett `Access-Control-Expose-Headers`-huvud som vitlistar svarshuvuden och gör dem tillgängliga för JavaScript.

Det är enkelt att installera en CORS-proxy så att vår [handledningsapp](tutorial-bing-image-search-single-page-app.md) kan komma åt de valfria klienthuvudena. [Installera Node.js](https://nodejs.org/en/download/) om du inte redan har det. Ange sedan följande kommando i en kommandotolk.

    npm install -g cors-proxy-server

Ändra sedan api-slutpunkten för Bing Image Search i HTML-filen till:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Slutligen startar du CORS-proxyn med följande kommando:

    cors-proxy-server

Lämna kommandofönstret öppet medan du använder självstudieappen. Om du stänger fönstret stoppas proxyn. I det expanderbara avsnittet om HTTP-huvuden nedan kan du nu se `X-MSEdge-ClientID`-huvudet (bland annat) under sökresultatet och du kan kontrollera att det är samma för varje begäran.

## <a name="response-headers-in-production"></a>Svarshuvuden i produktion

Cors proxy-metoden som beskrivs i föregående svar är lämplig för utveckling, testning och inlärning.

I en produktionsmiljö bör du dock vara värd för ett serverskript på samma domän som webbsidan som använder API:et för webbsökning på Bing. Det här skriptet bör faktiskt göra API-anrop på begäran från webbsidan JavaScript och skicka alla resultat, inklusive rubriker, tillbaka till klienten. Eftersom de två resurserna (sidan och skriptet) delar ett ursprung spelar CORS inte in och specialrubrikerna är tillgängliga för JavaScript på webbsidan.

Den här metoden skyddar också din API-nyckel från exponering för allmänheten, eftersom endast serverskriptet behöver det. Skriptet kan använda en annan metod (till exempel HTTP-referenden) för att kontrollera att begäran är auktoriserad.

## <a name="next-steps"></a>Nästa steg

Är din fråga om en saknad funktion? Överväg att begära eller rösta för det på vår [User Voice webbplats](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Se även

 [Stack överflöd: Kognitiva tjänster](https://stackoverflow.com/questions/tagged/bing-api)
