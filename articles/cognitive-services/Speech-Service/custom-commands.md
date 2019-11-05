---
title: Anpassade kommandon (för hands version) – tal tjänst
titleSuffix: Azure Cognitive Services
description: En översikt över funktionerna, funktionerna och begränsningarna för anpassade kommandon (för hands version), en lösning för att skapa röst assistenter.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: e41a037e657a8d2b484a3fcb62eb3bc03f53682d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507092"
---
# <a name="custom-commands-preview"></a>Anpassade kommandon (förhands granskning)

[Röst assistenter](voice-assistants.md) lyssnar på användare och utför en åtgärd som svar, och talar ofta tillbaka. De använder [tal-till-text](speech-to-text.md) för att skriva över användarens tal och sedan vidta åtgärder för textens naturliga språk förståelse. Den här åtgärden innehåller ofta talade utdata från assistenten som genererades med [text till tal](text-to-speech.md). Enheter ansluter till assistenter med tal SDK: s `DialogServiceConnector`-objekt.

**Anpassade kommandon (förhands granskning)** är en strömlinjeformad lösning för att skapa en röst assistent. Det ger en enhetlig redigerings upplevelse, en automatisk värd modell och relativt lägre komplexitet jämfört med andra alternativ för att skapa assistenter, t. ex. [direkt linje tal](direct-line-speech.md). Den här förenklingen kommer dock att ha en lägre flexibilitet. Det innebär att anpassade kommandon (förhands granskning) passar bäst för slut för ande av aktiviteter eller kommando-och-kontroll-scenarier.

För komplex interaktion och integrering med andra lösningar som den [virtuella assistent lösningen och företags mal len](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) rekommenderar vi att du använder direkt linje tal.

Bra kandidater för anpassade kommandon (förhands granskning) har en fast vokabulär med väldefinierade uppsättningar variabler. Till exempel är start automatiserings uppgifter, som att styra en termostat, idealiska.

   ![Exempel på scenarier för slut för ande av uppgift](media/voice-assistants/task-completion-examples.png "exempel på slutförd uppgift")

## <a name="getting-started-with-custom-commands-preview"></a>Komma igång med anpassade kommandon (förhands granskning)

Det första steget för att använda anpassade kommandon (för hands version) för att skapa en röst assistent är att [Hämta en röst prenumerations nyckel](get-started.md) och komma åt de anpassade kommandona (Preview) Builder i [tal Studio](https://speech.microsoft.com). Därifrån kan du skapa ett nytt anpassat kommando (för hands version) och publicera det, och sedan använda ett program på enheten för att kommunicera med det med hjälp av tal-SDK.

   ![Redigerings flöde för anpassade kommandon (förhands granskning)](media/voice-assistants/custom-commands-flow.png "Redigerings flödet för anpassade kommandon (förhands granskning)")

Vi erbjuder snabb starter som har utformats för att du ska kunna köra kod på mindre än 10 minuter.

* [Skapa ett anpassat kommando (för hands version)](quickstart-custom-speech-commands-create-new.md)
* [Skapa ett anpassat kommando (för hands version) med parametrar](quickstart-custom-speech-commands-create-parameters.md)
* [Ansluta till ett anpassat kommando (för hands version) med talet SDK,C#](quickstart-custom-speech-commands-speech-sdk.md)

## <a name="sample-code"></a>Exempelkod

Exempel kod för att skapa en röst assistent med anpassade kommandon (för hands version) finns på GitHub.

* [Röst assistents exempel (SDK)](https://aka.ms/csspeech/samples)

## <a name="customization"></a>Anpassning

Röst assistenter som skapats med hjälp av Azure Speech Services kan använda alla anpassnings alternativ som är tillgängliga för [tal-till-text](speech-to-text.md), [text till tal](text-to-speech.md)och [anpassad val av nyckelord](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Anpassnings alternativen varierar efter språk/språk (se [språk som stöds](supported-languages.md)).

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)

## <a name="next-steps"></a>Nästa steg

* [Hämta en prenumerations nyckel för tal tjänster kostnads fritt](get-started.md)
* [Hämta tal-SDK](speech-sdk.md)
