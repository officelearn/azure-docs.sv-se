---
title: Vanliga frågor (FAQ) om Azure Autosuggest API | Microsoft Docs
description: Få svar på vanliga frågor om Azure kognitiva Services Autosuggest API på Azure.
services: cognitive-services
author: HeidiSteen
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 00b91728bcfec52ff30697f080d5c2619bab79a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354474"
---
# <a name="frequently-asked-questions-faq-about-autosuggest-api-cognitive-services"></a>Vanliga frågor (FAQ) om Autosuggest API (kognitiva Services)
 
 Få svar på vanliga frågor om begrepp och koden scenarion för Autosuggest-API för kognitiva Azure-tjänster.

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>Hur skaffar jag valfria klientcertifikat huvuden vid anrop av Bing Autosuggest API: et från JavaScript?

Följande huvuden är valfria, men vi rekommenderar att du behandlar dem efter behov. Dessa huvuden med hjälp av Bing Autosuggest API: N returnerar mer exakta resultat.

- X sökplats
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Men när du anropar Bing Autosuggest API: et från JavaScript kan inbyggda säkerhetsfunktionerna i din webbläsare hindra dig från att få åtkomst till värdena för dessa huvuden.

Du kan göra Bing Autosuggest API-begäran via en proxyserver för CORS för att lösa problemet. Svaret från en sådan proxy har en `Access-Control-Expose-Headers` huvud som whitelists svarshuvuden och gör dem tillgängliga för JavaScript.

Det är lätt att installera en CORS-proxy för att tillåta vår [självstudiekursen app](tutorials/autosuggest.md) åtkomst till de valfria klientcertifikat huvudena. Första, om du inte redan har det, [installera Node.js](https://nodejs.org/en/download/). Ange sedan följande kommando vid en kommandotolk.

    npm install -g cors-proxy-server

Ändra Bing Autosuggest API-slutpunkt i HTML-filen:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Slutligen starta CORS-proxy med följande kommando:

    cors-proxy-server

Lämna Kommandotolken öppen när du använder appen självstudiekursen; stänger fönstret stoppar proxyn. I avsnittet nedan sökresultaten utbyggbara HTTP-huvuden, kan du nu se den `X-MSEdge-ClientID` huvud (bland andra) och kontrollera att det är samma för varje begäran.

## <a name="next-steps"></a>Nästa steg

Är en fråga om en funktion eller funktionalitet som saknas? Överväg att begära eller röstning för den vår [User Voice-webbplats](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Se också

- [Stackspill: Kognitiva tjänster](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
