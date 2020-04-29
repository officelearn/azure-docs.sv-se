---
title: Anpassade kommandon (för hands version) – tal tjänst
titleSuffix: Azure Cognitive Services
description: En översikt över funktionerna, funktionerna och begränsningarna för anpassade kommandon (för hands version), en lösning för att skapa röst program.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 2e1b6ee0bd6c392804915fac6ff23278a00b6d33
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "79367847"
---
# <a name="what-are-custom-commands-preview"></a>Vad är anpassade kommandon (för hands version)?

Röst program, till exempel [röst assistenter](voice-assistants.md) , lyssnar på användare och vidtar en åtgärd som svar, och talar ofta tillbaka. De använder [tal-till-text](speech-to-text.md) för att skriva över användarens tal och sedan vidta åtgärder för textens naturliga språk förståelse. Den här åtgärden innehåller ofta talade utdata från assistenten som genererades med [text till tal](text-to-speech.md). Enheter ansluter till assistenter med tal SDK: s `DialogServiceConnector` objekt.

**Anpassade kommandon (förhands granskning)** är en strömlinjeformad lösning för att skapa röst program. Det ger en enhetlig redigerings upplevelse, en automatisk värd modell och relativt lägre komplexitet jämfört med andra alternativ som [direkt linje tal](direct-line-speech.md). Den här förenklingen kommer dock att ha en lägre flexibilitet. Det innebär att anpassade kommandon (förhands granskning) passar bäst för slut för ande av aktiviteter eller kommando-och-kontroll-scenarier. Den är särskilt väl matchad för Sakernas Internet (IoT) och omdirigerings enheter.

För komplex interaktion och integrering med andra lösningar som den [virtuella assistent lösningen och företags mal len](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) rekommenderar vi att du använder direkt linje tal.

Bra kandidater för anpassade kommandon (förhands granskning) har en fast vokabulär med väldefinierade uppsättningar variabler. Till exempel är start automatiserings uppgifter, som att styra en termostat, idealiska.

   ![Exempel på scenarier för slut för ande av uppgift](media/voice-assistants/task-completion-examples.png "exempel på slutförd uppgift")

## <a name="getting-started-with-custom-commands-preview"></a>Komma igång med anpassade kommandon (förhands granskning)

Det första steget för att använda anpassade kommandon (för hands version) för att göra ett röst program är att [Hämta en tal prenumerations nyckel](get-started.md) och komma åt de anpassade kommandona (för hands version) på [tal Studio](https://speech.microsoft.com). Därifrån kan du skapa ett nytt anpassat kommando (för hands version) och publicera det, och sedan använda ett program på enheten för att kommunicera med det med hjälp av tal-SDK.

   ![Redigerings flöde för anpassade kommandon (förhands granskning)](media/voice-assistants/custom-commands-flow.png "Redigerings flödet för anpassade kommandon (förhands granskning)")

Vi erbjuder snabb starter som har utformats för att du ska kunna köra kod på mindre än 10 minuter.

* [Skapa ett anpassat kommando (för hands version)](quickstart-custom-speech-commands-create-new.md)
* [Skapa ett anpassat kommando (för hands version) med parametrar](quickstart-custom-speech-commands-create-parameters.md)
* [Ansluta till ett anpassat kommando (för hands version) med talet SDK, C #](quickstart-custom-speech-commands-speech-sdk.md)

När du är klar med snabb starterna kan du utforska vår instruktion.

- [Lägg till verifieringar i anpassade kommando parametrar](./how-to-custom-speech-commands-validations.md)
- [Utföra kommandon på klienten med talet SDK](./how-to-custom-speech-commands-fulfill-sdk.md)
- [Lägga till en bekräftelse till ett anpassat kommando](./how-to-custom-speech-commands-confirmations.md)
- [Lägga till en korrigering i ett steg till ett anpassat kommando](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>Nästa steg

* [Hämta en prenumerations nyckel för Speech service kostnads fritt](get-started.md)
* [Gå till tal Studio för att testa anpassade kommandon](https://speech.microsoft.com)
* [Hämta tal-SDK](speech-sdk.md)
