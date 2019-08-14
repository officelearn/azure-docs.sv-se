---
title: Anpassad röst för första virtuella assistenter (för hands version) – tal tjänst
titleSuffix: Azure Cognitive Services
description: En översikt över funktionerna, funktionerna och begränsningarna för anpassade röst-och första virtuella assistenter med hjälp av den direkta rad igenkännings kanalen i bot Framework och Cognitive Services Speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: f77a95af1edcd37043ea1e067560577140a81a04
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967593"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>Om anpassad röst för hands version för första och andra virtuella assistenter

Anpassade virtuella assistenter som använder Azure Speech Services ger utvecklare möjlighet att skapa naturliga, mänskliga konversations gränssnitt för sina program och upplevelser. Robot Framework: s direkta rad tal kanal förbättrar dessa funktioner genom att tillhandahålla en samordnad, dirigerad start punkt till en kompatibel robot som möjliggör röst i, röst ut-interaktion med låg latens och hög tillförlitlighet. Dessa robotar kan använda Microsofts Language Understanding (LUIS) för att interagera med naturligt språk. Direkt linje tal kan nås av enheter med hjälp av Speech Software Development Kit (SDK).

   ![Konceptuellt diagram över det direkta linje flödet för dirigering av tal Orchestration-tjänsten](media/voice-first-virtual-assistants/overview.png "Tal kanal flödet")


Direkt linje tal och dess tillhör ande funktioner för anpassade röst-och första virtuella assistenter är ett idealiskt tillägg till den [virtuella assistentens lösning och företags mal len](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Även om det direkta linje talet kan fungera med alla kompatibla bot, ger dessa resurser en åter användnings bar bas linje för konversationer med hög kvalitet samt vanliga support kunskaper och modeller för att komma igång snabbt.


## <a name="core-features"></a>Kärn funktioner

| Category | Funktioner |
|----------|----------|
|[Anpassat aktiverings ord](speech-devices-sdk-create-kws.md) | Du kan göra det möjligt för användare att starta konversationer med robotar med ett anpassat nyckelord som "Hej contoso". Den här uppgiften utförs med en anpassad aktiverings ord motor i tal-SDK, som kan konfigureras med ett anpassat aktiverings ord [som du kan generera här](speech-devices-sdk-create-kws.md). Den direkta linjens tal kanal innehåller kontroll av inaktive rad ord på tjänst sidan som förbättrar precisionen vid aktivering av aktiverings ord listan jämfört med enbart enheten.
|[Tal till text](speech-to-text.md) | Den direkta rad igenkännings kanalen innehåller ljud avskrifter i tolkad text med [tal-till-text](speech-to-text.md) från Azure Speech Services. Den här texten är tillgänglig för både roboten och klient programmet som det har tilldelats.
|[Text till tal](text-to-speech.md) | Text svar från roboten kommer att syntetiseras med [text till tal](text-to-speech.md) från Azure Speech Services. Denna syntes kommer sedan att göras tillgänglig för klient programmet som en ljud ström. Microsoft erbjuder möjlighet att bygga din egen anpassade, högkvalitativa neurala TTS-röst som ger en röst till ditt varumärke, för att lära dig mer om [kontakten oss](mailto:mstts@microsoft.com).
|[Direkt linje tal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Som en kanal i bot-ramverket aktiverar direkt linje tal en smidig och sömlös anslutning mellan klient programmet, en kompatibel bot och funktionerna i Azure Speech Services. Mer information om hur du konfigurerar din robot för att använda den direkta rad igenkännings kanalen finns på [sidan i dokumentationen för bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="get-started-with-virtual-assistants"></a>Kom igång med virtuella assistenter

Vi erbjuder snabb starter som har utformats för att du ska kunna köra kod på mindre än 10 minuter. Den här tabellen innehåller en lista över de första snabb starterna för virtuella assistenter som organiseras efter språk.

| Snabbstart | Plattform | API-referens |
|------------|----------|---------------|
| C#, UWP | Windows | [Mellan](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Mellan](https://aka.ms/csspeech/javaref) |
| Java | Android | [Mellan](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Exempelkod

Exempel kod för att skapa en röst-första virtuell assistent finns på GitHub. Dessa exempel avser klient programmet för att ansluta till din robot i flera populära programmeringsspråk.

* [Röst-första exempel på virtuella assistenter (SDK)](https://aka.ms/csspeech/samples)
* [Självstudier: Röst möjliggör din robot med talet SDK,C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Självstudie
En själv studie kurs om hur du [aktiverar rösten – aktivera din robot med hjälp av tal-SDK och kanal för direkt linje tal](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Anpassning

Röst-första virtuella assistenter som skapats med Azure Speech Services kan använda det fullständiga intervallet av anpassnings alternativ som är tillgängliga för [tal-till-text](speech-to-text.md), [text till tal](text-to-speech.md)och [anpassad val av nyckelord](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Anpassnings alternativen varierar efter språk/språk (se [språk som stöds](supported-languages.md)).

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Nästa steg

* [Hämta en prenumerations nyckel för tal tjänster kostnads fritt](get-started.md)
* [Hämta tal-SDK](speech-sdk.md)
* [Skapa och distribuera en grundläggande robot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Hämta lösningen för virtuella assistenter och företags mallar](https://github.com/Microsoft/AI)
