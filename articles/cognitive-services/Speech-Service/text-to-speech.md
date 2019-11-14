---
title: Text till tal-tal-tjänsten
titleSuffix: Azure Cognitive Services
description: Funktionen för text till tal i tal-tjänsten gör det möjligt för dina program, verktyg eller enheter att konvertera text till naturligt mänskligt syntetiskt tal. Välj Förvals röster eller skapa en egen anpassad röst.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: 0d233f63879326f05cafb873d2a0243543b00c6b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075715"
---
# <a name="what-is-text-to-speech"></a>Vad är text-till-tal?

Text till tal från Azure Speech Services är en tjänst som gör det möjligt för dina program, verktyg eller enheter att konvertera text till naturligt mänskligt syntetiskt tal. Välj mellan standard-och neurala röster eller skapa en egen anpassad röst som är unik för din produkt eller ditt varumärke. 75 + standard röster är tillgängliga på över 45 språk och nationella inställningar och 5 neurala-röster är tillgängliga på 4 språk och nationella inställningar. En fullständig lista finns i [språk som stöds](language-support.md#text-to-speech).

Med text till tal-teknik kan skapare av innehåll interagera med sina användare på olika sätt. Text till tal kan förbättra tillgängligheten genom att ge användarna ett alternativ för att interagera med innehållet audibly. Om användaren har nedsatt syn, ett inlärnings funktions sätt eller kräver navigerings information under körningen, kan text till tal förbättra en befintlig upplevelse. Text till tal är också ett värdefullt tillägg för röst robotar och röst assistenter.

Genom att använda SSML (Speech syntes Markup Language), ett XML-baserat kodspråk, kan utvecklare som använder tjänsten för text till tal ange hur indata ska konverteras till syntetiskt tal. Med SSML kan du justera bredd, uttal, tal, volym och mycket annat. Mer information finns i [SSML](#speech-synthesis-markup-language-ssml).

### <a name="standard-voices"></a>Standard röster

Standard röster skapas med statistisk parameter syntes och/eller kombinations sammanfattnings tekniker. Dessa röster är mycket anpassningsbara och har en naturlig naturlig effekt. Du kan enkelt göra det möjligt för dina program att prata på fler än 45 språk, med ett brett utbud av röst alternativ. Dessa röster ger hög uttal av precision, inklusive stöd för förkortningar, akronym-expansion, datum/tid-tolkningar, polytelefoner med mera. Använd standard röst för att förbättra tillgängligheten för dina program och tjänster genom att tillåta att användare interagerar med din innehålls audibly.

### <a name="neural-voices"></a>Neurala-röster

Neurala-röster använder djup neurala-nätverk för att undvika de traditionella text till tal-systemen som matchar mönstren för stress och intonation i talade språk och för att syntetisera tal i en dator röst. Standard text till tal avbryter prosody i separata språk analys och akustiska förutsägelse steg som styrs av oberoende modeller, vilket kan leda till muffled röst syntes. Vår neurala-funktion gör prosody förutsägelse och röst syntes samtidigt, vilket resulterar i en mer flytande och naturlig röst.

Neurala röster kan användas för att göra interaktioner med chattrobotar och röst assistenter mer naturligt och engagerande, konvertera digitala texter som e-böcker till Audiobooks och förbättra navigerings systemen i bilen. Med den humana naturliga prosody och tydligare utsättande av ord, minskar neurala-röster avsevärt lyssnings utmattning när du interagerar med AI-system.

Neurala-röster stöder olika format, t. ex. neutrala och Cheerful. Till exempel kan Jessa-Voice (en-US) tala cheerfully, som är optimerad för varm, glad konversation. Du kan justera röst resultatet, t. ex. ton, färgdjup och hastighet med hjälp av [tal syntes märknings språk](speech-synthesis-markup.md). En fullständig lista över tillgängliga röster finns i [språk som stöds](language-support.md#text-to-speech).

Mer information om fördelarna med neurala-röster finns i [Microsofts nya neurala text-till-tal-tjänst hjälper datorer att tala som människor](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Anpassade röster

Röst anpassning gör att du kan skapa en igenkännbar, en-av-en-röst för ditt varumärke. Om du vill skapa ett anpassat röst teckensnitt gör du en Studio-inspelning och laddar upp de associerade skripten som tränings data. Tjänsten skapar sedan en unik röst modell som är justerad till din inspelning. Du kan använda det här anpassade röst teckensnittet för att syntetisera tal. Mer information finns i [anpassade röster](how-to-customize-voice-font.md).

## <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

SSML (Speech syntes Markup Language) är ett XML-baserat kodspråk som låter utvecklare ange hur indata ska konverteras till syntetiskt tal med hjälp av text till tal-tjänsten. Jämfört med oformaterad text gör SSML att utvecklare kan finjustera färgdjup, uttal, tal frekvens, volym och annat text till tal-utdata. Normal interpunktion, till exempel pausa efter en punkt eller med rätt intonation när en mening slutar med ett frågetecken, hanteras automatiskt.

Alla text inmatningar som skickas till text till tal-tjänsten måste struktureras som SSML. Mer information finns i [tal syntes märknings språk](speech-synthesis-markup.md).

### <a name="pricing-note"></a>Pris notering

När du använder text till tal-tjänsten debiteras du för varje tecken som konverteras till tal, inklusive interpunktion. SSML-dokumentet är inte fakturerbart, valfria element som används för att justera hur texten konverteras till tal, t. ex. fonem och färgdjup, räknas som fakturerbara tecken. Här är en lista över vad som är fakturerbara:

- Text som skickas till text till tal-tjänsten i SSML-delen av begäran
- Alla markeringar i textfältet för begär ande texten i SSML-format, förutom för `<speak>` och `<voice>` Taggar
- Bokstäver, interpunktion, blank steg, tabbar, markeringar och alla blank stegs tecken
- Varje kod punkt som definierats i Unicode

Detaljerad information finns i [prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Varje kinesiskt, japanskt och Koreanskt språk tecken räknas som två tecken för fakturering.

## <a name="core-features"></a>Kärn funktioner

I den här tabellen listas huvud funktionerna för text till tal:

| Användningsfall                                  | SDK | REST  |
| ----------------------------------------- | --- | ----- |
| Omvandla text till tal.                   | Ja | Ja   |
| Ladda upp data uppsättningar för röst anpassning.     | Nej  | Ja\* |
| Skapa och hantera röst teckensnitts modeller.      | Nej  | Ja\* |
| Skapa och hantera röst teckensnitts distributioner. | Nej  | Ja\* |
| Skapa och hantera röst teckensnitts test.       | Nej  | Ja\* |
| Hantera prenumerationer.                     | Nej  | Ja\* |

\*_dessa tjänster är tillgängliga med hjälp av Cris.AI-slutpunkten. Se [referens för Swagger](https://westus.cris.ai/swagger/ui/index). De här API: erna för anpassad röst utbildning och hantering implementerar begränsning som begränsar begär anden till 25 per 5 sekunder, medan API för tal syntes implementerar sig själv begränsning som tillåter 200-begäranden per sekund som högsta. När begränsningen inträffar får du ett meddelande via meddelande rubriker._

## <a name="get-started-with-text-to-speech"></a>Kom igång med text till tal

Vi erbjuder snabb starter som har utformats för att du ska kunna köra kod på mindre än 10 minuter. Den här tabellen innehåller en lista över text till tal-snabb starter ordnade efter språk.

### <a name="sdk-quickstarts"></a>Snabb starter för SDK

| Snabb start (SDK) | Plattform | API-referens |
| ---------------- | -------- | ------------- |
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)  | Windows | [Mellan](https://aka.ms/csspeech/csharpref) |
| [C#.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Mellan](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Mellan](https://aka.ms/csspeech/csharpref) |
| [C#, Unity](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=unity) | Windows, Android | [Mellan](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Mellan](https://aka.ms/csspeech/cppref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-cpp&tabs=linux) | Linux | [Mellan](https://aka.ms/csspeech/cppref) |
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Mellan](https://aka.ms/csspeech/javaref) |
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=android) | Android | [Mellan](https://aka.ms/csspeech/javaref) |
| [Objective-C](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/objectivec-macos.md) | macOS | [Mellan](https://aka.ms/csspeech/objectivecref) |
| [Objective-C](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/objectivec-ios.md) | iOS | [Mellan](https://aka.ms/csspeech/objectivecref) |
| Införliva (~/articles/Cognitive-Services/Speech-service/quickstarts/text-to-Speech-Langs/Swift-MacOS.MD | macOS | [Mellan](https://aka.ms/csspeech/objectivecref) |
| [Swift](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/swift-ios.md) | iOS | [Mellan](https://aka.ms/csspeech/objectivecref) |
| [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python) | Window, Linux, macOS | [Mellan](https://aka.ms/csspeech/pythonref) |

### <a name="rest-quickstarts"></a>REST-snabb starter

| Snabb start (REST) | Plattform | API-referens |
| ----------------- | -------- | ------------- |
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp) | Windows, macOS, Linux | [Mellan](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Window, macOS, Linux | [Mellan](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Window, macOS, Linux | [Mellan](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Exempelkod

Exempel kod för text till tal finns på GitHub. De här exemplen tar upp text till tal-konvertering i de flesta populära programmeringsspråk.

- [Text till tal-exempel (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Text till tal-exempel (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Referensdokument

- [Speech SDK](speech-sdk-reference.md)
- [Tal enheter SDK](speech-devices-sdk.md)
- [REST API: tal till text](rest-speech-to-text.md)
- [REST API: text till tal](rest-text-to-speech.md)
- [REST API: batch-avskrift och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

- [Skaffa en prenumeration på kostnads fria tal tjänster](get-started.md)
- [Skapa anpassade rösttyper](how-to-customize-voice-font.md)
