---
title: Vanliga frågor (FAQ) - automatiska förslag för Bing
titlesuffix: Azure Cognitive Services
description: Få svar på vanliga frågor om automatiska förslag i Bing.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 84f1b0555922119e9de4addc3d51ac233e7bae65
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831372"
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

Det är enkelt att installera en proxy för CORS så att våra [självstudieappen](tutorials/autosuggest.md) att komma åt valfria klientcertifikat-huvuden. Första, om du inte redan har det, [installera Node.js](https://nodejs.org/en/download/). Ange sedan följande kommando i Kommandotolken.

    npm install -g cors-proxy-server

Sedan ändra den automatiska förslag i Bing-slutpunkten i HTML-filen:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Slutligen börja CORS-proxyn med följande kommando:

    cors-proxy-server

Lämna Kommandotolken öppen medan du använder självstudieappen; stänga fönstret stoppar proxyn. I avsnittet nedan sökresultaten utbyggbara HTTP-huvuden, kan du nu se den `X-MSEdge-ClientID` rubrik (bland annat) och kontrollera att det är samma för varje begäran.

## <a name="next-steps"></a>Nästa steg

Är en fråga om en funktion eller funktionalitet som saknas? Överväg att begära eller röstat vår [User Voice-webbplatsen](https://cognitive.uservoice.com/).

## <a name="see-also"></a>Se också

- [Stackspill: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
