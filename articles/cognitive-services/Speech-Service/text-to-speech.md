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
ms.openlocfilehash: d7ec8648a8428558264c9bfd4d923523b90cce07
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855948"
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

Microsofts **text till tal** tjänsten erbjuder mer än 75 röster i mer än 45 språk och nationella inställningar. Om du vill använda dessa standard ”rösttyper”, behöver du bara ange voice-namn med några andra parametrar när du anropar tjänstens REST API. Information om röster som stöds, se [språk som stöds](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/supported-languages#text-to-speech). 

Om du vill ha en unik röst för ditt program, kan du skapa [anpassade rösttyper](how-to-customize-voice-font.md) från din egen tal-exempel.

## <a name="next-steps"></a>Nästa steg

* [Få en kostnadsfri prenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du kan känna igen tal i C#](quickstart-csharp-windows.md)
