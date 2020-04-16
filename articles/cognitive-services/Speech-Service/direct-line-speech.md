---
title: Direktlinjetal - taltjänst
titleSuffix: Azure Cognitive Services
description: En översikt över funktioner, funktioner och begränsningar för röstassistenter som använder Direktlinjetal med Speech Software Development Kit (SDK).
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 6dc125b68af8f9a07731aaae9d858c19c51f934f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402396"
---
# <a name="what-is-direct-line-speech"></a>Vad är Direct Line Speech?

**Direct Line Speech** är en robust, heltäckande lösning för att skapa en flexibel, utökningsbar röstassistent. Den drivs av Bot Framework och dess Direct Line Speech kanal, som är optimerad för voice-in, voice-out interaktion med bots.

[Röstassistenter](voice-assistants.md) lyssnar på användare och vidtar en åtgärd som svar, ofta talar tillbaka. De använder [tal-till-text för](speech-to-text.md) att transkribera användarens tal och vidtar sedan åtgärder för att förstå textens naturliga språk. Den här åtgärden innehåller ofta upplästa utdata från assistenten som genereras med [text-till-tal](text-to-speech.md).

Direct Line Speech erbjuder de högsta nivåerna av anpassning och förfining för röstassistenter. Den är utformad för konversationsscenarier som är öppna, naturliga eller hybrider av de två med slutförande av uppgifter eller kommando- och kontrollanvändning. Den här höga flexibiliteten ger en större komplexitet, och scenarier som är begränsade till väldefinierade uppgifter med hjälp av indata för naturligt språk kanske vill överväga [anpassade kommandon (förhandsversion)](custom-commands.md) för en strömlinjeformad lösningsupplevelse.

## <a name="getting-started-with-direct-line-speech"></a>Komma igång med direktlinjetal

De första stegen för att skapa en röstassistent med direkt radtal är att [hämta en talprenumerationsnyckel,](get-started.md)skapa en ny robot som är associerad med den prenumerationen och ansluta roboten till direct line-talkanalen.

   ![Konceptuellt diagram över serviceflödet direktlinjetalsorkestrering](media/voice-assistants/overview-directlinespeech.png "Flödet i talkanalen")

En komplett steg-för-steg-guide om hur du skapar en enkel röstassistent med direktlinjetal finns [i självstudien för talaktivering av din robot med tal-SDK och direktlinjetalskanalen](tutorial-voice-enable-your-bot-speech-sdk.md).

Vi erbjuder även snabbstarter som utformats för att du ska kunna köra kod och lära dig API:erna snabbt. Den här tabellen innehåller en lista över snabbstarter för röstassistenter ordnade efter språk och plattform.

| Snabbstart | Plattform | API-referens |
|------------|----------|---------------|
| C#, UWP | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Bläddra](https://aka.ms/csspeech/javaref) |
| Java | Android | [Bläddra](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Exempelkod

Exempelkod för att skapa en röstassistent finns på GitHub. Dessa exempel täcker klientprogrammet för anslutning till din assistent i flera populära programmeringsspråk.

* [Exempel på röstassistenter (SDK)](https://aka.ms/csspeech/samples)
* [Självstudiekurs: Röst aktivera din assistent med Tal SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Anpassning

Röstassistenter som skapats med taltjänsten kan använda alla anpassningsalternativ som är tillgängliga för [tal till text,](speech-to-text.md) [text till tal](text-to-speech.md)och anpassad [nyckelordsval](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Anpassningsalternativen varierar beroende på språk/språk (se [Språk som stöds).](supported-languages.md)

Direktlinjetal och dess tillhörande funktioner för röstassistenter är ett idealiskt komplement till [Virtual Assistant Solution och Enterprise Template](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Även om Direct Line Speech kan fungera med alla kompatibla robotar, ger dessa resurser en återanvändbar baslinje för högkvalitativa konversationsupplevelser samt vanliga stödjande färdigheter och modeller för att komma igång snabbt.

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Nästa steg

* [Skaffa en prenumerationsnyckel för taltjänsten gratis](get-started.md)
* [Hämta tal-SDK](speech-sdk.md)
* [Skapa och distribuera en grundläggande robot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Hämta virtual assistant-lösningen och företagsmallen](https://github.com/Microsoft/AI)
