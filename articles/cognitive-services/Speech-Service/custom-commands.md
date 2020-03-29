---
title: Anpassade kommandon (förhandsgranskning) – taltjänst
titleSuffix: Azure Cognitive Services
description: En översikt över funktioner, funktioner och begränsningar för anpassade kommandon (förhandsversion), en lösning för att skapa röstprogram.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 2e1b6ee0bd6c392804915fac6ff23278a00b6d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367847"
---
# <a name="what-are-custom-commands-preview"></a>Vad är anpassade kommandon (förhandsversion)?

Röstprogram som [Röstassistenter](voice-assistants.md) lyssnar på användare och vidtar en åtgärd som svar, ofta talar tillbaka. De använder [tal-till-text för](speech-to-text.md) att transkribera användarens tal och vidtar sedan åtgärder för att förstå textens naturliga språk. Den här åtgärden innehåller ofta upplästa utdata från assistenten som genereras med [text-till-tal](text-to-speech.md). Enheter ansluter till assistenter med Tal-SDK:s `DialogServiceConnector` objekt.

**Anpassade kommandon (förhandsversion)** är en strömlinjeformad lösning för att skapa röstprogram. Det ger en enhetlig redigeringsupplevelse, en automatisk värdmodell och relativt lägre komplexitet jämfört med andra alternativ som [Direct Line Speech](direct-line-speech.md). Denna förenkling kommer dock med en minskning av flexibiliteten. Anpassade kommandon (förhandsversion) passar därför bäst för slutförande av uppgifter eller kommando- och kontrollscenarier. Det är särskilt väl matchade för Sakernas Internet (IoT) och huvudlösa enheter.

För komplex konversationsinteraktion och integrering med andra lösningar som [Virtual Assistant Solution och Enterprise Template](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) uppmanas du att använda Direktlinjetal.

Bra kandidater för anpassade kommandon (förhandsversion) har ett fast ordförråd med väldefinierade variabler. Till exempel, hemautomation uppgifter, som att styra en termostat, är idealiska.

   ![Exempel på scenarier för slutförande av uppgifter](media/voice-assistants/task-completion-examples.png "exempel på slutförande av uppgift")

## <a name="getting-started-with-custom-commands-preview"></a>Komma igång med anpassade kommandon (förhandsversion)

Det första steget för att använda anpassade kommandon (förhandsversion) för att skapa ett röstprogram är att [få en talprenumerationsnyckel](get-started.md) och komma åt Custom Commands (Preview) Builder i [Talstudion](https://speech.microsoft.com). Därifrån kan du skapa ett nytt anpassat kommandoprogram (Förhandsgranska) och publicera det, varefter ett program på enheten kan kommunicera med det med tal-SDK.

   ![Skapa flöde för anpassade kommandon (förhandsgranskning)](media/voice-assistants/custom-commands-flow.png "Redigeringsflödet för anpassade kommandon (förhandsgranskning)")

Vi erbjuder snabbstarter utformade för att du ska kunna köra kod på mindre än 10 minuter.

* [Skapa ett anpassat kommandoprogram (förhandsgranska)](quickstart-custom-speech-commands-create-new.md)
* [Skapa ett anpassat kommandoprogram (förhandsgranska) med parametrar](quickstart-custom-speech-commands-create-parameters.md)
* [Ansluta till ett anpassat kommandoprogram (förhandsversion) med Tal SDK, C #](quickstart-custom-speech-commands-speech-sdk.md)

När du är klar med snabbstarterna kan du utforska våra instruktioner.

- [Lägga till valideringar i parametrar för anpassat kommando](./how-to-custom-speech-commands-validations.md)
- [Uppfylla kommandon på klienten med tal-SDK](./how-to-custom-speech-commands-fulfill-sdk.md)
- [Lägga till en bekräftelse till ett anpassat kommando](./how-to-custom-speech-commands-confirmations.md)
- [Lägga till en korrigering i ett steg till ett anpassat kommando](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>Nästa steg

* [Skaffa en prenumerationsnyckel för taltjänsten gratis](get-started.md)
* [Gå till Talstudion för att prova anpassade kommandon](https://speech.microsoft.com)
* [Hämta tal-SDK](speech-sdk.md)
