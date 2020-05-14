---
title: Anpassade nyckelord – tal tjänst
titleSuffix: Azure Cognitive Services
description: En översikt över funktioner, funktioner och begränsningar för anpassade nyckelord med hjälp av Speech Software Development Kit (SDK).
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.openlocfilehash: 0a6df626f9567b51a8371c17004b454f9c5b4597
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202036"
---
# <a name="what-is-a-keyword"></a>Vad är ett nyckelord?

Ett nyckelord är ett ord eller en kort fras som gör att produkten röst aktive rad. Till exempel är "Hej Cortana" ett nyckelord för Cortana-assistenten. Röst aktivering gör att användarna kan börja interagera med produkten helt utan kostnad genom att bara prata med nyckelordet. När din produkt kontinuerligt lyssnar efter nyckelordet bearbetas alla ljud lokalt på användarens enhet tills en identifiering görs för att säkerställa att data förblir så privat som möjligt.

## <a name="core-features-of-custom-keyword"></a>Huvud funktioner i anpassat nyckelord

Med anpassade nyckelords anpassnings-, prestanda-och integrerings funktioner kan du skräddarsy röst aktivering så att den passar din produkts vision och användarnas behov.

| Funktion | Beskrivning |
|----------|----------|
| Anpassning av nyckelord | Som ett tillägg till ditt varumärke stärker ett nyckelord det aktie som du har skapat med kunderna. Med den anpassade nyckelords portalen i tal Studio kan du ange ett ord eller en kort fras som bäst motsvarar ditt varumärke. Du kan anpassa ditt nyckelord ytterligare genom att välja rätt uttal, som kommer att användas av nyckelords modellen som genereras.
| Nyckelords verifiering | När det finns hög exakthet i nyckelordet som identifieras lokalt skickas ljud till molnet för att ytterligare verifiera att en användare har sagt detta nyckelord. Nyckelords verifiering ger ett extra säkerhets lager genom att minska effekten av felaktig lokal identifiering och skydda användar sekretessen.
| Röst assistent & Speech SDK integration | Nyckelord som genereras från det anpassade nyckelordet i tal Studio kan enkelt integreras i din enhet eller i ditt program via tal-SDK: n. Du behöver bara peka SDK: t till den nyckelords modell som tillhandahålls av tal Studio och din produkt kommer att vara röst aktive rad med nyckelords verifiering. Du kan slutföra din produkts röst upplevelse genom att skapa en egen [röst assistent](voice-assistants.md).

## <a name="get-started-with-custom-keywords"></a>Kom igång med anpassade nyckelord

* Självstudie: [skapa ett anpassat nyckelord med tal Studio](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)
* Självstudie: så här [aktiverar du din produkt med tal-SDK med hjälp av C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* Snabb start: [identifiera nyckelord med talet SDK, på universell Windows-plattform med C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* Snabb start: [identifiera nyckelord med talet SDK, på Android med Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>Nästa steg

* [Hämta en prenumerations nyckel för Speech service kostnads fritt](get-started.md)
* [Hämta tal-SDK](speech-sdk.md)
* [Lär dig mer om röst assistenter](voice-assistants.md)
