---
title: Om Text till tal | Microsoft Docs
description: En översikt över funktionerna i Text till tal.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 84baf03c83bb63883b80982056cdf6e1e25b3fb7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355632"
---
# <a name="about-the-text-to-speech-api"></a>Om Text till tal-API

Den **Text till tal** (TTS) API för tjänsten tal konverterar-indata till låta naturliga tal (kallas även *tal sammanfattande*).

Om du vill generera tal, skickar programmet HTTP POST-förfrågningar till tal-tjänst. Det, text syntetiskt till mänskliga låta tal och returneras som en ljudfil. En mängd olika röster och språk som stöds.

I vilken tal sammanfattande som antas scenarier är:

* *Förbättra tillgängligheten:* **Text till tal** tekniken möjliggör innehållsägare och utgivare att svara på olika sätt personer interagera med deras innehåll. Personer med visual försämring eller läsa svårigheter uppskattar att kunna använda innehåll aurally. Röst utdata också gör det enklare för personer som ska få textinnehåll, till exempel tidningar eller bloggar på mobila enheter när pendling eller utöva.

* *Svara i scenarier med samtidig körning:* **Text till tal** gör det möjligt att snabbt och lätt upptar viktig information vid körning eller på annat sätt läsa miljö utanför en praktisk. Navigering är en vanlig tillämpning i det här området. 

* *Utöka learning med flera lägen:* olika personer veta bäst på olika sätt. Onlineinlärning experter har visat att tillhandahåller röst- och tillsammans hjälper gör lättare att lära sig och behålla informationen.

* *Leverera intuitiva robotar eller assistenter:* möjligheten att prata kan vara en integrerad del av en intelligent chatt bot eller en virtuell Installationsassistenten. Fler och fler företag utvecklar chatt robotar för att ge spännande customer service upplevelser för sina kunder. Röst lägger till en annan dimension genom att låta den bot svar som tas emot aurally (till exempel av phone).

## <a name="voice-support"></a>Röst-support

Microsoft **text till tal** tjänst erbjuder mer än 75 röster i mer än 45 språk och nationella inställningar. Om du vill använda dessa standard ”röst teckensnitt” behöver du bara ange röst-namn med några andra parametrar när du anropar tjänstens REST API. Mer information om röster som stöds finns i [språk som stöds](supported-languages.md). 

Om du vill använda en unik röst för ditt program kan du skapa [anpassade röst teckensnitt](how-to-customize-voice-font.md) från tal exempel.

## <a name="next-steps"></a>Nästa steg

* [Hämta din utvärderingsprenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du identifierar tal i C#](quickstart-csharp-windows.md)
