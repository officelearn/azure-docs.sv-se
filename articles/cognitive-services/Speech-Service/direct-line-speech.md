---
title: Tal igenkännings tjänst för direkt linje
titleSuffix: Azure Cognitive Services
description: En översikt över funktionerna, funktionerna och begränsningarna för röst assistenter med hjälp av direkt linje tal med Speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: cec3131d791d591375fd87d1c080294c9034a815
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806226"
---
# <a name="about-direct-line-speech"></a>Om direkt linje tal

[Röst assistenter](voice-assistants.md) lyssnar på användare och utför en åtgärd som svar, och talar ofta tillbaka. De använder [tal-till-text](speech-to-text.md) för att skriva över användarens tal och sedan vidta åtgärder för textens naturliga språk förståelse. Den här åtgärden innehåller ofta talade utdata från assistenten som genererades med [text till tal](text-to-speech.md). Enheter ansluter till assistenter med tal SDK: s `DialogServiceConnector`-objekt.

**Direkt linje tal** är en robust lösning från slut punkt till slut punkt för att skapa en flexibel, utöknings bar röst assistent som drivs av bot Framework och dess direkta linje tal kanal, som är optimerad för röst-in, röst samtals interaktion med robotar.

Direkt linje tal ger högsta möjliga anpassnings-och riktigt ambitiös för röst assistenter. Det lämpar sig väl för konversations scenarier som är öppna-avslutade, naturliga eller innehåller hybrider med uppgift att slutföra eller använda kommando-och kontroll. Den här hög flexibiliteten ger en större komplexitet och scenarier som är begränsade till väldefinierade uppgifter med hjälp av naturligt språk kan vilja överväga [anpassade kommandon (för hands version)](custom-commands.md) för en strömlinjeformad lösning.

## <a name="getting-started-with-direct-line-speech"></a>Komma igång med direkt linje tal

De första stegen för att skapa en röst assistent med direkt linje tal är att [Hämta en tal prenumerations nyckel](get-started.md), skapa en ny robot som är associerad med den prenumerationen och ansluta bot till den direkta linjens tal kanal.

   ![Konceptuellt diagram över det direkta linje flödet för dirigering av tal Orchestration-tjänsten](media/voice-assistants/overview-directlinespeech.png "Tal kanal flödet")

En fullständig steg-för-steg-guide om hur du skapar en enkel röst assistent med direkt linje tal finns i [själv studie kursen om hur du aktiverar roboten med tal-SDK och kanal för direkt linje tal](tutorial-voice-enable-your-bot-speech-sdk.md).

Vi erbjuder även snabb starter som är utformade för att du ska kunna köra kod på mindre än 10 minuter. Den här tabellen innehåller en lista med snabb starter för röst assistenter ordnade efter språk.

| Snabbstart | Plattform | API-referens |
|------------|----------|---------------|
| C#, UWP | Windows | [Mellan](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Mellan](https://aka.ms/csspeech/javaref) |
| Java | Android | [Mellan](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Exempelkod

Exempel kod för att skapa en röst assistent finns på GitHub. Dessa exempel avser klient programmet för att ansluta till din assistent i flera populära programmeringsspråk.

* [Röst assistents exempel (SDK)](https://aka.ms/csspeech/samples)
* [Självstudie: röst aktivera din assistent med talet SDK,C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Anpassning

Röst assistenter som skapats med hjälp av tal tjänsten kan använda det fullständiga urvalet av anpassnings alternativ för [tal-till-text](speech-to-text.md), [text till tal](text-to-speech.md)och [anpassad val av nyckelord](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Anpassnings alternativen varierar efter språk/språk (se [språk som stöds](supported-languages.md)).

Direkt linje tal och dess tillhör ande funktioner för röst assistenter är ett idealiskt tillägg till den [virtuella assistentens lösning och företags mal len](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Även om det direkta linje talet kan fungera med alla kompatibla bot, ger dessa resurser en åter användnings bar bas linje för konversationer med hög kvalitet samt vanliga support kunskaper och modeller för att komma igång snabbt.

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Nästa steg

* [Hämta en prenumerations nyckel för Speech service kostnads fritt](get-started.md)
* [Hämta tal-SDK](speech-sdk.md)
* [Skapa och distribuera en grundläggande robot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Hämta lösningen för virtuella assistenter och företags mallar](https://github.com/Microsoft/AI)
