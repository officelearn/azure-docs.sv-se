---
title: Vanliga frågor om Bing Stavningskontrolls kontroll-API - kognitiva Azure-tjänster | Microsoft Docs
description: Få svar på vanliga frågor om Bing stavningskontroll kontrollera API på Azure.
services: cognitive-services
author: HeidiSteen
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 87b1f3ed3e0aaa9f3c3c804dc9eac3ee60b4a565
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354948"
---
# <a name="frequently-asked-questions-about-the-bing-spell-check-api"></a>Vanliga frågor om Bing stavningskontroll kontrollera API

 Få svar på vanliga frågor om begrepp och koden scenarion för Bing stavningskontroll kontrollera API för Microsoft kognitiva Services på Azure.

## <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-spell-check-api-from-javascript"></a>Hur skaffar jag valfria klientcertifikat huvuden vid anrop av Bing stavningskontroll kontrollera API från JavaScript?

Följande huvuden är valfria, men vi rekommenderar att du behandlar dem efter behov. Dessa huvuden med hjälp av Bing stavningskontroll kontrollera API returnerade mer korrekta resultat.

- X sökplats
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Men när du anropar Bing stavningskontroll kontrollera API från JavaScript kan inbyggda säkerhetsfunktionerna i din webbläsare hindra dig från att få åtkomst till värdena för dessa huvuden.

Du kan göra Bing stavningskontroll kontrollera API-begäran via en proxyserver för CORS för att lösa problemet. Svaret från en sådan proxy har en `Access-Control-Expose-Headers` huvud som whitelists svarshuvuden och gör dem tillgängliga för JavaScript.

Det är lätt att installera en proxy för CORS så att den [självstudiekursen app](tutorials/spellcheck.md) åtkomst till de valfria klientcertifikat huvudena. Första, om du inte redan har det, [installera Node.js](https://nodejs.org/en/download/). Ange sedan följande kommando vid en kommandotolk.

    npm install -g cors-proxy-server

Ändra Bing stavningskontroll kontrollera API-slutpunkt i HTML-filen:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Slutligen starta CORS-proxy med följande kommando:

    cors-proxy-server

Lämna Kommandotolken öppen när du använder appen självstudiekursen; stänger fönstret stoppar proxyn. I avsnittet nedan sökresultaten utbyggbara HTTP-huvuden, kan du nu se den `X-MSEdge-ClientID` huvud (bland andra) och kontrollera att det är samma för varje begäran.

## <a name="next-steps"></a>Nästa steg

Är en fråga om en funktion eller funktionalitet som saknas? Överväg att begära eller röstning för den den [UserVoice-webbplatsen](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Se också

 [StackOverflow: Kognitiva tjänster](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
