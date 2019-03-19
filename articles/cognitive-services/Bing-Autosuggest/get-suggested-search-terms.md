---
title: Vad är Automatiska förslag i Bing?
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder API för automatiska förslag i Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 669eaaf0eaf28c696a7ca368c02f5816054e5d10
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010114"
---
# <a name="what-is-bing-autosuggest"></a>Vad är Automatiska förslag i Bing?

Om programmet skickar frågor till något av API:erna för Bing-sökning, kan du använda API för automatiska förslag för Bing till att förbättra användarnas sökupplevelse. API för automatiska förslag för Bing returnerar en lista med föreslagna frågor som baseras på den partiella frågesträngen i sökrutan. Samtidigt som tecken skrivs i sökrutan kan du visa förslag i en nedrullningsbar listruta.

## <a name="bing-autosuggest-api-features"></a>API för automatiska förslag för Bing

| Funktion                                                                                                                                                                                 | Beskrivning                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Föreslå söktermer i realtid](concepts/get-suggestions.md) | Förbättra programupplevelsen med API:et för automatiska förslag. Föreslagna sökord visas medan användaren skriver. |

## <a name="workflow"></a>Arbetsflöde

API för automatiska förslag för Bing är en RESTful-webbtjänst, vilket innebär att den är enkel att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON. 

1. Skapa ett [Cognitive Services-API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med åtkomst till API:er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) kostnadsfritt.
2. Skicka en begäran till det här API:et varje gång en användare skriver ett nytt tecken i programmets sökruta.
3. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

Vanligtvis anropar du det här API:et varje gång en användare skriver ett nytt tecken i programmets sökruta. När fler tecken anges returnerar API:et mer relevanta föreslagna sökfrågor. Till exempel är de förslag som API:et returnerar för ett enskilt `s` förmodligen mindre relevanta än de för `sail`.

I följande exempel visas en nedrullningsbar sökruta med föreslagna sökord från API för automatiska förslag för Bing.

![Lista med automatiska förslag i sökrutans listruta](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

När en användare väljer ett förslag i den nedrullningsbara listan, kan du använda det till att börja sökningen med någon av API:erna för Bing-sökning eller gå direkt till Bings sökresultatsida.

## <a name="next-steps"></a>Nästa steg

Kom igång snabbt med din första begäran genom att läsa avsnittet om att [Skapa din första fråga](quickstarts/csharp.md).

Bekanta dig med referensen för [API för automatiska förslag i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference). Referensen innehåller en lista över de slutpunkter, rubriker och frågeparametrar som du använder för att begära föreslagna frågetermer samt definitionerna av svarsobjekten.

Lär dig hur du söker på webben med hjälp av [API för webbsökning i Bing](../bing-web-search/search-the-web.md).

Se till att läsa [Bing Use and Display Requirements](./useanddisplayrequirements.md) (Krav för användning och visning i Bing) så att du inte bryter mot någon av reglerna om användning av sökresultat.
