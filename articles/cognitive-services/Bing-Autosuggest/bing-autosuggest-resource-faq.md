---
title: Vanliga frågor (FAQ) - automatiska förslag för Bing
titlesuffix: Azure Cognitive Services
description: Få svar på vanliga frågor om automatiska förslag i Bing.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: b0ec10bbf03e8a8d005eece4b6496b74b2943233
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173266"
---
# <a name="frequently-asked-questions-faq-about-bing-autosuggest-api"></a>Vanliga frågor (och svar FAQ) om Bing för automatiska förslag i
 
 Hitta svar på vanliga frågor om koncept, kod och scenarier som rör API: et för automatiska förslag för Azure Cognitive Services.

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>Hur får jag valfria klientcertifikat rubriker när du anropar den automatiska förslag i Bing från JavaScript?

Följande huvuden är valfria, men vi rekommenderar att du ska hantera dem efter behov. Dessa huvuden kan den automatiska förslag i Bing returnera mer exakta resultat.

- X-Sök-plats
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Men när du anropar den automatiska förslag i Bing från JavaScript kanske i webbläsaren inbyggda säkerhetsfunktioner hindrar dig från att komma åt värdena för dessa rubriker.

Du kan göra automatiska förslag i Bing-begäran via en CORS-proxy för att lösa problemet. Svaret från sådan proxy har en `Access-Control-Expose-Headers` rubrik som vitlistor svarshuvuden och gör dem tillgängliga för JavaScript.

Det är enkelt att installera en proxy för CORS så att våra [självstudieappen](tutorials/autosuggest.md) att komma åt valfria klientcertifikat-huvuden. [Installera Node.js](https://nodejs.org/en/download/) om du inte redan har det. Ange sedan följande kommando i Kommandotolken.

    npm install -g cors-proxy-server

Sedan ändra den automatiska förslag i Bing-slutpunkten i HTML-filen:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Slutligen startar du CORS-proxyn med följande kommando:

    cors-proxy-server

Lämna kommandofönstret öppet medan du använder självstudieappen. Om du stänger fönstret stoppas proxyn. I det expanderbara avsnittet om HTTP-huvuden nedan kan du nu se `X-MSEdge-ClientID`-huvudet (bland annat) under sökresultatet och du kan kontrollera att det är samma för varje begäran.

## <a name="next-steps"></a>Nästa steg

Är en fråga om en funktion eller funktionalitet som saknas? Överväg att begära eller röstat vår [User Voice-webbplatsen](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Se också

- [Stack Overflow: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
