---
title: Vanliga frågor och svar (FAQ) - API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: Få svar på vanliga frågor om hur du använder Bing Web Search API.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: troubleshooting
ms.date: 10/06/2017
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: d11165b22665eec09e5025a4daa5ecd1171b7d80
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159037"
---
# <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

 Hitta svar på vanliga frågor om koncept, kod och scenarier som är relaterat till Bing Web Search API för Microsoft Cognitive Services på Azure.

## <a name="response-headers-in-javascript"></a>Svarshuvuden i JavaScript

Följande huvuden kan uppstå i svar från Bing Web Search API.

|||
|-|-|
|`X-MSEdge-ClientID`|Unikt ID som Bing har tilldelat till användaren|
|`BingAPIs-Market`|På marknaden som användes för att uppfylla begäran|
|`BingAPIs-TraceId`|Loggposten på Bing API-servern för den här förfrågan (för stöd)|

Det är särskilt viktigt att bevara klient-ID och lämna tillbaka med efterföljande förfrågningar. När du gör detta sökningen använder de senaste kontexten i rangordning sökresultat och också ge en konsekvent användarupplevelse.

Men när du anropar API för webbsökning i Bing från JavaScript kanske i webbläsaren inbyggda säkerhetsfunktioner (CORS) hindrar dig från att komma åt värdena för dessa rubriker.

För att få åtkomst till rubrikerna du Bing Web Search API-begäran via en CORS-proxy. Svaret från en sådan proxy har ett `Access-Control-Expose-Headers`-huvud som vitlistar svarshuvuden och gör dem tillgängliga för JavaScript.

Det är enkelt att installera en proxy för CORS så att våra [självstudieappen](tutorial-bing-web-search-single-page-app.md) att komma åt valfria klientcertifikat-huvuden. [Installera Node.js](https://nodejs.org/en/download/) om du inte redan har det. Ange sedan följande kommando i Kommandotolken.

    npm install -g cors-proxy-server

Sedan ändra Bing Web Search API-slutpunkten i HTML-filen:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Slutligen startar du CORS-proxyn med följande kommando:

    cors-proxy-server

Lämna kommandofönstret öppet medan du använder självstudieappen. Om du stänger fönstret stoppas proxyn. I det expanderbara avsnittet om HTTP-huvuden nedan kan du nu se `X-MSEdge-ClientID`-huvudet (bland annat) under sökresultatet och du kan kontrollera att det är samma för varje begäran.

## <a name="response-headers-in-production"></a>Svarshuvuden i produktion

CORS-proxy-metoden som beskrivs i föregående svar är lämplig för utveckling, testning och utbildning.

I en produktionsmiljö bör du ha ett skript på servern på samma domän som den webbsida som använder Bing Web Search API. Det här skriptet ska faktiskt göra API-anrop på begäran från JavaScript-webbsida och skicka alla resultat, inklusive rubriker, tillbaka till klienten. Eftersom de två resurserna (sidan eller skript) dela ett ursprung, CORS kommer inte betydelse och de särskilda rubrikerna är tillgängliga för JavaScript på webbsidan.

Den här metoden skyddar även din API-nyckel från exponeringen för allmänheten, eftersom endast serverskriptet måste den. Skriptet kan använda en annan metod för att se till att begäran har behörighet.

## <a name="next-steps"></a>Nästa steg

Är en fråga om en funktion eller funktionalitet som saknas? Överväg att begära eller röstat vår [User Voice-webbplatsen](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Se också

 [Stack Overflow: Cognitive Services](http://stackoverflow.com/questions/tagged/bing-api)
