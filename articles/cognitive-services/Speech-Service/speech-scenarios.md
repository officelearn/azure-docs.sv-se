---
title: Azure Cognitive Services tal scenarier | Azure Microsoft Docs
description: Scenarier
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 07/02/2018
ms.author: panosper
ms.openlocfilehash: 9e6be3608f5aa5ec5d68e6bbefff6da6c23c62fd
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247962"
---
# <a name="speech-scenarios"></a>Tal-scenarier

Det finns många scenarier som kan få behörighet med hjälp av tal-teknik. Vi analyserar några av de vanligaste och peka på relevanta funktioner i dokumentationen. För de flesta av innehållet, den [SDK](speech-sdk.md) central gör det möjligt för dessa scenarier.

Sidan beskriver hur du:
> [!div class="checklist"]
> * Skapa rösten appar
> * Transkribera anrop center ljud anrop
> * Röst robotar

## <a name="voice-triggered-apps"></a>Röst utlöses appar

Många användare vill aktivera röstinmatning på sina program. Röstinmatning är ett bra sätt att göra din app flexibel, oavsett om det med hjälp av det händer kostnadsfritt (till exempel i en bil) eller snabbare olika uppgifter som att fråga riktningar nyheter eller väder-information. 

### <a name="voice-triggered-apps-with-baseline-models"></a>Röst utlöses appar med basmodeller

Om din app kommer att användas av allmänheten i miljöer där bakgrundsljud inte är orimlig, det enklaste och snabbaste sättet att göra detta på att bara hämta våra [tal SDK](speech-sdk.md) och följer relevant [ Exempel](quickstart-csharp-dotnet-windows.md). SDK: N som drivs av din [Azure prenumerationsnyckel](https://azure.microsoft.com/try/cognitive-services/) gör att utvecklare kan ladda upp ljud till baslinje talmodeller för taligenkänning som driver Cortana och Skype. Mdoels är avancerade och som används av de tidigare nämnda produkterna. Du kan vara igång på några minuter.

### <a name="voice-triggered-apps-with-custom-models"></a>Röst utlöses appar med anpassade modeller

Om din app adresser en specifik domän, (say kemi, biologi eller särskilda intaget via behov) så kan du överväga för att anpassa en [språkmodellen](how-to-customize-language-model.md). Anpassning av en språkmodell Lär avkodaren om de vanligaste fraser och ord som används av din app. Kodaren kommer att kunna mer exakt transkribera röstindata för med en anpassad språkmodell för en viss domän i stället för baslinje. På samma sätt om bakgrundsljud var din app ska användas är framträdande kan du anpassa en akustisk modell. Utforska dokumentationen för andra fall där [språk anpassning](how-to-customize-language-model.md) och [akustisk anpassning](how-to-customize-acoustic-models.md) ange värde och gå till vår [anpassning portal](https://customspeech.ai) för kick-Starting upplevelse för modellen när du skapar. Liknar basmodeller, anpassade modeller anropas vår [tal SDK](speech-sdk.md) och följer relevant [exempel](quickstart-csharp-dotnet-windows.md).

## <a name="transcribe-call-center-audio-calls"></a>Transkribera anrop center ljud anrop

Call Center ackumuleras stora mängder ljud. Dolda inom dessa ljudfiler ligger-värde som kan hämtas genom avskrift. Varaktigheten för anropet kan sentiment, enligt kunden och allmän värdet anropet till anroparen identifieras genom att hämta anrop avskrifter.

Bäst startpunkten är den [Batch avskrift API](batch-transcription.md) tillsammans med relaterade [exempel](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

Behöver du först skaffa ett [Azure prenumerationsnyckel](https://azure.microsoft.com/try/cognitive-services/) och du behöver läsa den [dokumentation]([Batch transcription API](batch-transcription.md)).

### <a name="transcribe-call-center-audio-calls-with-baseline-models"></a>Transkribera anrop center ljud anrop med basmodeller

Beslut som behöver göras är om du vill använda de interna basmodeller kunna utföra avskrift anpassa ett språk eller en akustisk modell, eller bådadera. Om du vill använda basmodeller kräver API: et endast API-nyckeln. API: et kommer internt anropa modellen med bäst för dina data och anpassningar.

### <a name="transcribe-call-center-audio-calls-with-custom-models"></a>Transkribera anrop center ljud anrop med anpassade modeller

Om du planerar att använda en anpassad modell, behöver du ID: T för den modellen tillsammans med API-nyckel. Modell-ID hämtas från den [anpassning portal](https://customspeech.ai). Detta är inte den slutpunkts-ID som du hittar i vyn information om slutpunkten men modell-ID som du kan hämta när du klickar på ”Details” för den modellen.

## <a name="voice-bots"></a>Röst robotar

Utvecklare kan göra det möjligt för sina program med röst-utdata. Med Taltjänsten kan synthetize tal för ett antal [språk](supported-languages.md) och ger den [slutpunkter](rest-apis.md) för åtkomst och att lägga till den här funktionen i din app.

Dessutom för användare som vill lägga till fler personlighet och unikhet till sina bots Speech Service ger utvecklare möjlighet att anpassa en unik rösttyp. Ett liknande sätt att anpassa taligenkänningens modeller rösttyper kräver användardata. Utvecklare är Överför dessa data i vår [röst anpassning portal](https://customspeech.ai) och börja skapa ditt unika varumärke på röst för din robot. Beskrivs [här](how-to-text-to-speech.md) samt de [vanliga frågor och svar](faq-text-to-speech.md) sidor 

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Börja med tal SDK](speech-sdk.md)
