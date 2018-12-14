---
title: Om text till tal-Speech Service
titleSuffix: Azure Cognitive Services
description: 'Text till tal-API: et erbjuder mer än 75 röster i mer än 45 språk och nationella inställningar. Om du vill använda standard rösttyper, behöver du bara ange voice-namn med några andra parametrar när du anropar Speech-tjänsten.'
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b06864e08f6edf52e4c96c33c88bba9f8ef4e859
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343215"
---
# <a name="about-the-text-to-speech-api"></a>Om text till tal-API:

Den **text till tal** API för (text till tal-) konverterar indata text till tal för naturlig standardrösttyper (kallas även *talsyntes*).

Ditt program skickar HTTP POST-förfrågningar för att generera tal, text till tal-API: et. Där texten är syntetiskt till mänskliga standardrösttyper tal och returneras som en ljudfil. En mängd olika röster och språk som stöds.

Scenarier i vilka tal syntes används är:

* *Förbättra tillgängligheten:* **text till tal** teknik kan innehållsägare och utgivare att svara på de olika sätt interagera med sitt innehåll. Personer med visual nedskrivningar eller läsa svårigheter uppskattar att kunna använda innehåll aurally. Röst utdata också gör det enklare för personer som gillar textinnehåll, till exempel newspapers eller bloggar på mobila enheter när du roamar eller utöva.

* *Svara i scenarier för flerprogramskörning:* **text till tal** gör det möjligt för personer att absorbera viktig information snabbt och bekvämt vid körning eller annars utanför en praktisk läsning av miljö. Navigering är en vanlig tillämpning i det här området.

* *Förbättra learning med flera lägen:* Olika personer Lär dig bästa på olika sätt. Utbildning online experter har visat att tillsammans att tillhandahålla röst- och text kan underlätta information att lära sig och behålla.

* *Leverera intuitiva robotar eller assistenter:* Möjligheten att prata kan vara en del av en intelligent chattrobot eller virtuella assistenter. Fler och fler företag utvecklar chatt robotar för att tillhandahålla engagerade service upplevelser för sina kunder. Röst lägger till en annan dimension genom att låta robotens svar tas emot aurally (till exempel av telefon).

## <a name="voice-support"></a>Stöd för röst

Microsofts **text till tal** tjänsten erbjuder mer än 75 röster i mer än 45 språk och nationella inställningar. Om du vill använda dessa standard ”rösttyper”, behöver du bara ange voice-namn med några andra parametrar när du anropar tjänstens REST API. Mer information om språk som stöds, språk och röster finns [språk som stöds](language-support.md#text-to-speech).

### <a name="neural-voices"></a>Neural röster

Neural text till tal kan användas för att göra interaktion med chattrobotar och virtuella assistenter mer naturligt och engagerande och konverterar digitala texter, till exempel e-böcker till audiobooks och förbättra navigering i bilen system. Med människoliknande naturlig prosody och rensa rörlighet ord har Neural text till tal kraftigt sänkt lyssnande utmattning när du interagerar med AI-system. Läs mer om neural röster [språk som stöds](language-support.md#text-to-speech).

### <a name="custom-voices"></a>Anpassade röster

Rösten anpassning kan du skapa en identifierbara, en av en typ ton för ditt varumärke: en *rösttyp.* För att skapa din rösttyp du gör en studio-registrering och ladda upp de associerade skript som träningsdata. Tjänsten skapar sedan en unik röst modell koll på inspelningen. Du kan använda sin rösttyp för att syntetisera tal. Mer information finns i [anpassade rösttyper](how-to-customize-voice-font.md).

## <a name="api-capabilities"></a>API-funktioner

Många av funktionerna i den **text till tal** API, särskilt när det gäller anpassning, är tillgängliga via REST. I följande tabell sammanfattas funktionerna för varje metod för att komma åt API: et. En fullständig lista över funktioner och API-information, se [Swagger referens](https://westus.cris.ai/swagger/ui/index).

| Användningsfall | REST | SDK:er |
|-----|-----|-----|----|
| Ladda upp datauppsättningar för röst-anpassning | Ja | Nej |
| Skapa och hantera modeller för röst-teckensnitt | Ja | Nej |
| Skapa och hantera distributioner av röst teckensnitt | Ja | Nej |
| Skapa och hantera röst teckensnitt tester| Ja | Nej |
| Hantera prenumerationer | Ja | Nej |

> [!NOTE]
> API: et implementerar de nätverksbegränsningar API-begäranden till 25 per 5 sekunder. Meddelandehuvudena informerar begränsningar.

## <a name="next-steps"></a>Nästa steg

* [Skaffa en kostnadsfri Speech Services-prenumeration](https://azure.microsoft.com/try/cognitive-services/)
* [Snabbstart: Omvandla text till tal, Python](quickstart-python-text-to-speech.md)
* [Snabbstart: Omvandla text till tal, .NET Core](quickstart-dotnet-text-to-speech.md)
* [REST API-referens](rest-apis.md)
