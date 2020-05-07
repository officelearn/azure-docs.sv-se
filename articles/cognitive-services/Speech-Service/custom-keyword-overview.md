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
ms.openlocfilehash: dd5748cf8afe19a49e5ea406aea9b558432eeaf3
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82802185"
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

## <a name="sample-code"></a>Exempelkod

Exempel kod för att integrera en nyckelords modell finns på GitHub. Dessa exempel avser klient programmet för att integrera ditt nyckelord i flera populära programmeringsspråk.

* [Självstudie: röst – aktivera produkten med talet SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Röst assistents exempel (SDK)](https://aka.ms/csspeech/samples)

## <a name="tutorial"></a>Självstudier

* Så här [skapar du ett anpassat nyckelord med tal Studio](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
* Så här [aktiverar du din produkt med hjälp av tal-SDK: n](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="reference-docs"></a>Referens dokument

* [Rikt linjer för anpassad namngivning av nyckelord](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-kws-guidelines)
* [Speech SDK](speech-sdk-reference.md)

## <a name="next-steps"></a>Nästa steg

* [Hämta en prenumerations nyckel för Speech service kostnads fritt](get-started.md)
* [Skapa ett anpassat nyckelord](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)
* [Hämta tal-SDK](speech-sdk.md)
* [Lär dig mer om röst assistenter](voice-assistants.md)
