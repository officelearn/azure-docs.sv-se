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
ms.openlocfilehash: c97f6414876441290cade68b8f9a054970586402
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507741"
---
# <a name="about-voice-assistants"></a>Om röst assistenter

Röst assistenter som använder Azure Speech Services ger utvecklare möjlighet att skapa naturliga, mänskliga konversations gränssnitt för sina program och upplevelser.

Röst assistents tjänsten ger snabb, tillförlitlig interaktion mellan en enhet och en assistent implementering som använder antingen (1) bot-ramverkets direkta rad tal kanal eller (2) den integrerade anpassade kommando tjänsten (för hands version) för att slutföra uppgiften.

Program ansluter till röst assistents tjänsten med Speech Software Development Kit (SDK).

   ![Koncept diagram för röst assistentens Dirigerings tjänst flöde](media/voice-assistants/overview.png "Röst assistent flödet")

## <a name="core-features"></a>Kärn funktioner

| Kategori | Funktioner |
|----------|----------|
|[Anpassat nyckelord](speech-devices-sdk-create-kws.md) | Användare kan starta konversationer med assistenter med ett anpassat nyckelord som "Hej contoso". En app gör detta med en anpassad nyckelords motor i tal-SDK: n, som kan konfigureras med ett anpassat nyckelord [som du kan generera här](speech-devices-sdk-create-kws.md). Röst assistenter kan använda kontroll av nyckelord på tjänst sidan för att förbättra noggrannheten för nyckelords aktiveringen (oberoende av enheten).
|[Tal till text](speech-to-text.md) | Röst assistenter konverterar ljud i real tid till tolkad text med [tal-till-text](speech-to-text.md) från Azure Speech Services. Den här texten är tillgänglig, eftersom den har tilldelats till både din assistent implementering och ditt klient program.
|[Text till tal](text-to-speech.md) | Text svar från din assistent är syntetiskt med [text-till-tal](text-to-speech.md) från Azure Speech Services. Denna syntes görs sedan tillgänglig för ditt klient program som en ljud ström. Microsoft erbjuder möjlighet att bygga din egen anpassade, högkvalitativa neurala TTS-röst som ger en röst till ditt varumärke. [Kontakta oss](mailto:mstts@microsoft.com)om du vill veta mer.

## <a name="comparing-assistant-solutions"></a>Jämföra assistent lösningar

Röst assistenten ansluter ditt program på enhet till din unika Assistant-implementering. Utvecklare skapar röst assistenter med antingen (1) bot-ramverkets [direkta linje tal](direct-line-speech.md) kanal eller (2) den [anpassade kommando lösningen (för hands version)](custom-commands.md) .

   ![Jämförelse av Assistant-lösningar](media/voice-assistants/assistant-solution-comparison.png "Jämförelse av Assistant-lösningar")

| Lösning | Funktioner |
|----------|----------|
|[Anpassade kommandon (förhands granskning)](custom-commands.md) | Anpassade kommandon (förhands granskning) tillhandahåller en strömlinjeformad redigerings-och värd lösning för röst assistenter. Den är anpassad till behoven för slut för ande av aktiviteter och kommando-och-kontroll-scenarier.
|[Direkt linje tal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Direkt linje tal möjliggör en smidig och sömlös anslutning mellan (1) klient programmet, (2) en kompatibel robot och (3) funktionerna i Azure Speech Services. Mer information om hur du konfigurerar din robot för att använda den direkta rad igenkännings kanalen finns på [sidan i dokumentationen för bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

När du har skapat en röst assistent med någon av dessa lösningar ansluter du ditt program på enheten till den med hjälp av `DialogServiceConnector` i tal-SDK: n. Mer information finns i snabb starter och exempel för varje lösning.

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