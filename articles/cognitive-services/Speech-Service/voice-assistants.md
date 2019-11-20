---
title: Röst assistenter – tal tjänst
titleSuffix: Azure Cognitive Services
description: En översikt över funktionerna, funktionerna och begränsningarna för röst assistenter med hjälp av Speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 403768bc81b476dd144d5e4496f3501a3001da8a
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195943"
---
# <a name="about-voice-assistants"></a>Om röst assistenter

Röst assistenter som använder Azure Speech Services ger utvecklare möjlighet att skapa naturliga, mänskliga konversations gränssnitt för sina program och upplevelser.

Röst assistents tjänsten ger snabb, tillförlitlig interaktion mellan en enhet och en assistent implementering som använder antingen (1) bot-ramverkets direkta rad tal kanal eller (2) den integrerade anpassade kommando tjänsten (för hands version) för att slutföra uppgiften.

Program ansluter till röst assistents tjänsten med Speech Software Development Kit (SDK).

   ![Koncept diagram för röst assistentens Dirigerings tjänst flöde](media/voice-assistants/overview.png "Röst assistent flödet")

## <a name="choosing-an-assistant-solution"></a>Välja en assistent lösning

Det första steget för att skapa en röst assistent är att bestämma vad det ska göra. Azure Speech Services innehåller flera, kompletterande lösningar för att utforma dina assistenters interaktioner. Oavsett om du vill ha flexibilitet och mångsidighet att den [direkta linjens tal](direct-line-speech.md) kanal i bot ramverket ger eller enklast [anpassade kommandon (för hands version)](custom-commands.md) för enkla scenarier kan du komma igång genom att välja rätt verktyg.

| Om du vill... | Överväg sedan... | Till exempel... |
|-------------------|------------------|----------------|
|Öppen konversation med robust kompetens integrering och fullständig distributions kontroll | Robot Frameworks [direkta linje tal](direct-line-speech.md) kanal | <ul><li>"Jag måste gå till Seattle"</li><li>"Vilken typ av pizza kan jag beställa?"</li></ul>
|Kommando-och kontroll-eller uppgifts riktad konversation med förenklad redigering och värd | [Anpassade kommandon (förhands granskning)](custom-commands.md) | <ul><li>"Sätt på overhead-ljus"</li><li>"Gör 5 grader till varma"</ul>

Vi rekommenderar [direkt linje tal](direct-line-speech.md) som det bästa valet om du inte är säker på vad du vill att din assistent ska hantera. Den erbjuder integrering med en omfattande uppsättning verktyg och redigerings hjälpmedel, till exempel den [virtuella assistenten och företags mal len](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) och den [QNA Maker tjänsten](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) för att bygga på vanliga mönster och använda dina befintliga kunskaps källor.

[Anpassade kommandon (förhands granskning)](custom-commands.md) ger en strömlinjeformad redigerings-och värd upplevelse som är särskilt anpassad för det naturliga språk kommandot och kontroll scenarier.

   ![Jämförelse av Assistant-lösningar](media/voice-assistants/assistant-solution-comparison.png "Jämförelse av Assistant-lösningar")

## <a name="core-features"></a>Kärn funktioner

Oavsett om du väljer [direkt linje tal](direct-line-speech.md) eller [anpassade kommandon (för hands version)](custom-commands.md) för att skapa dina Assistant-interaktioner, kan du använda en omfattande uppsättning anpassnings funktioner för att anpassa din assistent till ditt varumärke, din produkt och din personlighet.

| Kategori | Funktioner |
|----------|----------|
|[Anpassat nyckelord](speech-devices-sdk-create-kws.md) | Användare kan starta konversationer med assistenter med ett anpassat nyckelord som "Hej contoso". En app gör detta med en anpassad nyckelords motor i tal-SDK: n, som kan konfigureras med ett anpassat nyckelord [som du kan generera här](speech-devices-sdk-create-kws.md). Röst assistenter kan använda kontroll av nyckelord på tjänst sidan för att förbättra noggrannheten för nyckelords aktiveringen (oberoende av enheten).
|[Tal till text](speech-to-text.md) | Röst assistenter konverterar ljud i real tid till tolkad text med [tal-till-text](speech-to-text.md) från Azure Speech Services. Den här texten är tillgänglig, eftersom den har tilldelats till både din assistent implementering och ditt klient program.
|[Text till tal](text-to-speech.md) | Text svar från din assistent är syntetiskt med [text-till-tal](text-to-speech.md) från Azure Speech Services. Denna syntes görs sedan tillgänglig för ditt klient program som en ljud ström. Microsoft erbjuder möjlighet att bygga din egen anpassade, högkvalitativa neurala TTS-röst som ger en röst till ditt varumärke. [Kontakta oss](mailto:mstts@microsoft.com)om du vill veta mer.

## <a name="getting-started-with-voice-assistants"></a>Komma igång med röst assistenter

Vi erbjuder snabb starter som har utformats för att du ska kunna köra kod på mindre än 10 minuter. Den här tabellen innehåller en lista med snabb starter för röst assistenten, sorterade efter språk.

| Snabbstart | Plattform | API-referens |
|------------|----------|---------------|
| C#, UWP | Windows | [Mellan](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Mellan](https://aka.ms/csspeech/javaref) |
| Java | Android | [Mellan](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Exempelkod

Exempel kod för att skapa en röst assistent finns på GitHub. Dessa exempel avser klient programmet för att ansluta till din assistent i flera populära programmeringsspråk.

* [Röst assistents exempel (SDK)](https://aka.ms/csspeech/samples)
* [Självstudie: röst aktivera din assistent med talet SDK,C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Självstudier

En själv studie kurs om hur du [aktiverar röst samtal till din assistent med hjälp av tal-SDK: n och den direkta rad igenkännings kanalen](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Anpassning

Röst assistenter som skapats med hjälp av Azure Speech Services kan använda alla anpassnings alternativ som är tillgängliga för [tal-till-text](speech-to-text.md), [text till tal](text-to-speech.md)och [anpassad val av nyckelord](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Anpassnings alternativen varierar efter språk/språk (se [språk som stöds](supported-languages.md)).

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Nästa steg

* [Hämta en prenumerations nyckel för tal tjänster kostnads fritt](get-started.md)
* [Hämta tal-SDK](speech-sdk.md)
* [Läs mer om anpassade kommandon (förhands granskning)](custom-commands.md)
* [Läs mer om direkt linje tal](direct-line-speech.md)
