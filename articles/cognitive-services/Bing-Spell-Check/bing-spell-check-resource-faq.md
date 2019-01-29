---
title: Vanliga frågor om Bing-stavningskontroll kontrollera API
titlesuffix: Azure Cognitive Services
description: Få svar på vanliga frågor om den stavningskontroll i Bing på Azure.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 90acc80b19058c2a7eb963f9e423883846519b2c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164936"
---
# <a name="frequently-asked-questions-about-the-bing-spell-check-api"></a>Vanliga frågor om Bing-stavningskontroll kontrollera API

 Hitta svar på vanliga frågor om koncept, kod och scenarier som rör den stavningskontroll i Bing för Microsoft Cognitive Services på Azure.

## <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-spell-check-api-from-javascript"></a>Hur får jag valfria klientcertifikat rubriker när du anropar den stavningskontroll i Bing från JavaScript?

Följande huvuden är valfria, men vi rekommenderar att du ska hantera dem efter behov. Dessa huvuden kan stavningskontroll i Bing returnera mer exakta resultat.

- X-Sök-plats
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Men när du anropar den stavningskontroll i Bing från JavaScript kanske i webbläsaren inbyggda säkerhetsfunktioner hindrar dig från att komma åt värdena för dessa rubriker.

Du kan göra stavningskontroll i Bing-begäran via en CORS-proxy för att lösa problemet. Svaret från sådan proxy har en `Access-Control-Expose-Headers` rubrik som vitlistor svarshuvuden och gör dem tillgängliga för JavaScript.

Det är enkelt att installera en CORS-proxy för att tillåta den [självstudieappen](tutorials/spellcheck.md) att komma åt valfria klientcertifikat-huvuden. [Installera Node.js](https://nodejs.org/en/download/) om du inte redan har det. Ange sedan följande kommando i Kommandotolken.

    npm install -g cors-proxy-server

Ändra slutpunkten för stavningskontroll i Bing i HTML-filen:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Slutligen startar du CORS-proxyn med följande kommando:

    cors-proxy-server

Lämna kommandofönstret öppet medan du använder självstudieappen. Om du stänger fönstret stoppas proxyn. I avsnittet nedan sökresultaten utbyggbara HTTP-huvuden, kan du nu se den `X-MSEdge-ClientID` rubrik (bland annat) och kontrollera att det är samma för varje begäran.

## <a name="next-steps"></a>Nästa steg

Är en fråga om en funktion eller funktionalitet som saknas? Överväg att begära eller rösta fram den den [UserVoice-webbplats](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Se också

 [StackOverflow: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
