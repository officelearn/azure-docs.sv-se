---
title: Om Text till tal
description: En översikt över funktionerna i Text till tal.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: eebe41ea879ad34defadd08ce3095f7f6edca4d8
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719808"
---
# <a name="about-the-text-to-speech-api"></a>Om Text till tal-API

Den **Text till tal** (text till tal-) API: et för Speech-tjänsten konverterar indata text till tal för naturlig standardrösttyper (kallas även *talsyntes*).

För att generera tal, skickar programmet HTTP POST-förfrågningar till tal-tjänst. Där texten är syntetiskt till mänskliga standardrösttyper tal och returneras som en ljudfil. En mängd olika röster och språk som stöds.

Scenarier i vilka tal syntes används är:

* *Förbättra tillgängligheten:* **Text till tal** teknik kan innehållsägare och utgivare att svara på de olika sätt interagera med sitt innehåll. Personer med visual nedskrivningar eller läsa svårigheter uppskattar att kunna använda innehåll aurally. Röst utdata också gör det enklare för personer som gillar textinnehåll, till exempel newspapers eller bloggar på mobila enheter när du roamar eller utöva.

* *Svara i scenarier för flerprogramskörning:* **Text till tal** gör det möjligt för personer att absorbera viktig information snabbt och bekvämt vid körning eller annars utanför en praktisk läsning av miljö. Navigering är en vanlig tillämpning i det här området. 

* *Förbättra inlärning med flera lägen:* olika personer Lär dig bästa på olika sätt. Utbildning online experter har visat att tillsammans att tillhandahålla röst- och text kan underlätta information att lära sig och behålla.

* *Leverera intuitiva robotar eller assistenter:* möjligheten att prata kan vara en del av en intelligent chattrobot eller virtuella assistenter. Fler och fler företag utvecklar chatt robotar för att tillhandahålla engagerade service upplevelser för sina kunder. Röst lägger till en annan dimension genom att låta robotens svar tas emot aurally (till exempel av telefon).

## <a name="voice-support"></a>Stöd för röst

Microsofts **text till tal** tjänsten erbjuder mer än 75 röster i mer än 45 språk och nationella inställningar. Om du vill använda dessa standard ”rösttyper”, behöver du bara ange voice-namn med några andra parametrar när du anropar tjänstens REST API. Information om röster som stöds, se [språk som stöds](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#text-to-speech). 

Om du vill ha en unik röst för ditt program, kan du skapa [anpassade rösttyper](how-to-customize-voice-font.md) från din egen tal-exempel.

## <a name="api-capabilities"></a>API-funktioner

Många av funktionerna i den **Text till tal** API - särskilt när det gäller anpassning - är tillgängliga via REST. I följande tabell sammanfattas funktionerna för varje metod för att komma åt API: et. För en fullständig lista över funktioner och API: et. detaljer finns [Swagger](https://swagger/service/11ed9226-335e-4d08-a623-4547014ba2cc#/)

| Användningsfall | REST | SDK:er |
|-----|-----|-----|----|
| Ladda upp datauppsättningar för röst-anpassning | Ja | Nej |
| Skapa och hantera modeller för röst-teckensnitt | Ja | Nej |
| Skapa och hantera distributioner av röst teckensnitt | Ja | Nej |
| Skapa och hantera röst teckensnitt tester| Ja | Nej |
| Hantera prenumerationer | Ja | Nej |

> [!NOTE]
> API: et implementerar de nätverksbegränsningar API-begäranden till 25 per 5 sekunder. Meddelandet hearders informerar begränsningar.

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du syntetisera tal via REST API](how-to-text-to-speech.md)
