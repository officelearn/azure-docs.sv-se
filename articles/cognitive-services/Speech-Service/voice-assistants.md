---
title: Röstassistenter - Taltjänst
titleSuffix: Azure Cognitive Services
description: En översikt över funktioner, funktioner och begränsningar för röstassistenter som använder Speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369904"
---
# <a name="what-is-a-voice-assistant"></a>Vad är röstassistent?

Röstassistenter som använder taltjänsten ger utvecklare möjlighet att skapa naturliga, människoliknande konversationsgränssnitt för sina program och upplevelser.

Röstassistenttjänsten ger snabb och tillförlitlig interaktion mellan en enhet och en assistentimplementering som använder antingen (1) Bot Frameworks direct line speech-kanal eller (2) den integrerade tjänsten Custom Commands (Preview) för slutförande av uppgifter.

Program ansluter till röstassistenttjänsten med Speech Software Development Kit (SDK).

   ![Konceptuellt diagram över röstassistentens orkestreringsflöde](media/voice-assistants/overview.png "Röstassistentens flöde")

## <a name="choosing-an-assistant-solution"></a>Välja en assistentlösning

Det första steget för att skapa en röstassistent är att bestämma vad den ska göra. Taltjänsten innehåller flera kompletterande lösningar för att skapa assistentinteraktioner. Oavsett om du vill ha den flexibilitet och mångsidighet som Bot Frameworks [direct line-talkanal](direct-line-speech.md) ger eller enkelheten i [anpassade kommandon (förhandsversion)](custom-commands.md) för enkla scenarier, kommer du att komma igång om du väljer rätt verktyg.

| Om du vill... | Tänk då på... | Till exempel... |
|-------------------|------------------|----------------|
|Öppen konversation med robust integrering av färdigheter och fullständig driftsättningskontroll | Bot Framework's [Direct Line Tal](direct-line-speech.md) kanal | <ul><li>"Jag måste åka till Seattle"</li><li>"Vilken typ av pizza kan jag beställa?"</li></ul>
|Kommando- och kontroll- eller uppgiftsorienterad konversation med förenklad redigering och hosting | [Anpassade kommandon (förhandsgranskning)](custom-commands.md) | <ul><li>"Slå på luftljuset"</li><li>"Gör det 5 grader varmare"</ul>

Vi rekommenderar [direktlinjetal](direct-line-speech.md) som det bästa standardvalet om du ännu inte är säker på vad du vill att assistenten ska hantera. Det erbjuder integration med en omfattande uppsättning verktyg och redigeringshjälpmedel som [Virtual Assistant Solution och Enterprise Template](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) och [QnA Maker-tjänsten](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) för att bygga vidare på vanliga mönster och använda dina befintliga kunskapskällor.

[Anpassade kommandon (förhandsversion)](custom-commands.md) ger en strömlinjeformad redigerings- och värdupplevelse som är särskilt anpassad för kommando- och kontrollscenarier för naturligt språk.

   ![Jämförelse av assistentlösningar](media/voice-assistants/assistant-solution-comparison.png "Jämförelse av assistentlösningar")

## <a name="core-features"></a>Kärnfunktioner

Oavsett om du väljer [Direktlinjetal](direct-line-speech.md) eller [anpassade kommandon (förhandsversion)](custom-commands.md) för att skapa assistentinteraktioner kan du använda en omfattande uppsättning anpassningsfunktioner för att anpassa assistenten till ditt varumärke, din produkt och din personlighet.

| Kategori | Funktioner |
|----------|----------|
|[Anpassat nyckelord](speech-devices-sdk-create-kws.md) | Användare kan starta konversationer med assistenter med ett anpassat nyckelord som "Hej Contoso". En app gör detta med en anpassad sökordsmotor i Tal-SDK, som kan konfigureras med ett anpassat nyckelord [som du kan generera här](speech-devices-sdk-create-kws.md). Röstassistenter kan använda nyckelordsverifiering på servicesidan för att förbättra sökordsaktiveringens noggrannhet (enbart jämfört med enheten).
|[Tal till text](speech-to-text.md) | Röstassistenter konverterar realtidsljud till tolkad text med [tal-till-text](speech-to-text.md) från taltjänsten. Den här texten är tillgänglig, eftersom den transkriberas, till både assistentimplementeringen och klientprogrammet.
|[Text till tal](text-to-speech.md) | Textsvar från assistenten syntetiseras med hjälp av [Text-till-tal](text-to-speech.md) från taltjänsten. Den här syntesen görs sedan tillgänglig för klientprogrammet som en ljudström. Microsoft erbjuder möjligheten att bygga din egen anpassade, högkvalitativa Neural TTS röst som ger en röst till ditt varumärke. Kontakta [oss](mailto:mstts@microsoft.com)om du vill veta mer.

## <a name="getting-started-with-voice-assistants"></a>Komma igång med röstassistenter

Vi erbjuder snabbstarter utformade för att du ska kunna köra kod på mindre än 10 minuter. Den här tabellen innehåller en lista över snabbstarter för röstassistenter, ordnade efter språk.

| Snabbstart | Plattform | API-referens |
|------------|----------|---------------|
| C#, UWP | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Bläddra](https://aka.ms/csspeech/javaref) |
| Java | Android | [Bläddra](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Exempelkod

Exempelkod för att skapa en röstassistent finns på GitHub. Dessa exempel täcker klientprogrammet för anslutning till din assistent i flera populära programmeringsspråk.

* [Exempel på röstassistenter (SDK)](https://aka.ms/csspeech/samples)
* [Självstudiekurs: Röst aktivera din assistent med Tal SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Självstudier

En handledning om hur [du röstaktiverar assistenten med hjälp av engelska kanalen Tal SDK och Direkt linje](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Anpassning

Röstassistenter som skapats med hjälp av taltjänsten kan använda alla anpassningsalternativ som är tillgängliga för [tal till text,](speech-to-text.md) [text till tal](text-to-speech.md)och anpassad [nyckelordsval](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Anpassningsalternativen varierar beroende på språk/språk (se [Språk som stöds).](supported-languages.md)

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Nästa steg

* [Skaffa en prenumerationsnyckel för taltjänsten gratis](get-started.md)
* [Hämta tal-SDK](speech-sdk.md)
* [Läs mer om anpassade kommandon (förhandsgranskning)](custom-commands.md)
* [Läs mer om Direktlinjetal](direct-line-speech.md)
