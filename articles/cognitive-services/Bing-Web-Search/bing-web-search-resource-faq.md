---
title: Vanliga frågor (FAQ) om Bing webbsökning API i Azure | Microsoft Docs
description: Få svar på vanliga frågor om Microsoft kognitiva Services Bing Web Sök API på Azure.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: 321f571c48f2231d1ced43848cdefd17adaa1a08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351672"
---
# <a name="frequently-asked-questions-faq-about-bing-web-search-api-cognitive-services"></a>Vanliga frågor (FAQ) om sökningen Bing webb-API (kognitiva Services)
 
 Få svar på vanliga frågor om begrepp och koden scenarion för Bing Web Sök API för Microsoft kognitiva Services på Azure.

## <a name="response-headers-in-javascript"></a>I JavaScript-svarshuvuden

Följande huvuden kan uppstå i svar från Bing webb-API för sökning.

|||
|-|-|
|`X-MSEdge-ClientID`|Unikt ID som har tilldelats användaren i Bing|
|`BingAPIs-Market`|Marknaden som användes för att uppfylla begäran|
|`BingAPIs-TraceId`|Loggposten på Bing-API-servern för denna begäran (stöd)|

Det är särskilt viktigt att bevara klient-ID och returnera med efterföljande förfrågningar. När du gör det använda tidigare kontexten i rangordning sökresultat sökningen och även ge en konsekvent användarupplevelse.

Dock när du anropar Bing webb-API för sökning från JavaScript, kan webbläsarens inbyggda säkerhetsfunktionerna (CORS) hindra dig från att få åtkomst till värdena för dessa huvuden.

För att få åtkomst till rubrikerna, kan du göra sökningen Bing webb-API-begäran via en proxyserver för CORS. Svaret från en sådan proxy har en `Access-Control-Expose-Headers` huvud som whitelists svarshuvuden och gör dem tillgängliga för JavaScript.

Det är lätt att installera en CORS-proxy för att tillåta vår [självstudiekursen app](tutorial-bing-web-search-single-page-app.md) åtkomst till de valfria klientcertifikat huvudena. Första, om du inte redan har det, [installera Node.js](https://nodejs.org/en/download/). Ange sedan följande kommando vid en kommandotolk.

    npm install -g cors-proxy-server

Ändra Bing Web Sök API-slutpunkt i HTML-filen:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Slutligen starta CORS-proxy med följande kommando:

    cors-proxy-server

Lämna Kommandotolken öppen när du använder appen självstudiekursen; stänger fönstret stoppar proxyn. I avsnittet nedan sökresultaten utbyggbara HTTP-huvuden, kan du nu se den `X-MSEdge-ClientID` huvud (bland andra) och kontrollera att det är samma för varje begäran.

## <a name="response-headers-in-production"></a>Svarsrubriker i produktion

CORS proxy metod som beskrivs i föregående svaret är lämplig för utveckling, testning och utbildning. 

I en produktionsmiljö, men du bör vara värd för ett skript på servern på samma domän som den webbsida som använder Bing webb-API för sökning. Det här skriptet bör faktiskt göra API-anrop på begäran från webbsidan JavaScript och skicka alla resultat, inklusive sidhuvud, tillbaka till klienten. Eftersom två resurser (sidan eller skript) delar ett ursprung, kommer inte CORS i play och särskilda huvuden är acessible för JavaScript på webbsidan. 

Den här metoden skyddar också API-nyckel exponerats för allmänheten, eftersom endast skriptet på servern behöver den. Skriptet kan använda en annan metod för att kontrollera att begäran är auktoriserad.

## <a name="next-steps"></a>Nästa steg

Är en fråga om en funktion eller funktionalitet som saknas? Överväg att begära eller röstning för den vår [User Voice-webbplats](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Se också

 [Stackspill: Kognitiva tjänster](http://stackoverflow.com/questions/tagged/bing-api)