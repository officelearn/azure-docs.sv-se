---
title: Röstassistenter ställde vanliga frågor
titleSuffix: Azure Cognitive Services
description: Få svar på de mest populära frågorna om röstassistenter med anpassade kommandon (förhandsgranskning) eller direktlinjetalskanalen.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 8480299c2c889a243150028ac9651f4b62656aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110355"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Röstassistenter ställde vanliga frågor

Om du inte hittar svar på dina frågor i det här dokumentet kan du läsa [andra supportalternativ](support.md).

## <a name="general"></a>Allmänt

**F: Vad är en röstassistent?**

**A.** Precis som Cortana är en röstassistent en lösning som lyssnar på en användares talade yttranden, analyserar innehållet i dessa yttranden för mening, utför en eller flera åtgärder som svar på uttryckets avsikt och ger sedan ett svar till användaren som ofta innehåller en talad komponent. Det är en "voice-in, voice-out" upplevelse för att interagera med ett system. röstassistentförfattare skapar ett program på `DialogServiceConnector` enheten med hjälp av i Tal-SDK för att kommunicera med en assistent som skapats med hjälp av [anpassade kommandon (förhandsversion)](custom-commands.md) eller [direktlinjetalkanalen](direct-line-speech.md) i Bot Framework. Dessa assistenter kan använda anpassade sökord, anpassat tal och anpassad röst för att ge en upplevelse som är skräddarsydd för ditt varumärke eller din produkt.

**F: Ska jag använda anpassade kommandon (förhandsgranskning) eller direktlinjetal? Vad är skillnaden?**

**S:** [Anpassade kommandon (förhandsversion)](custom-commands.md) är en uppsättning verktyg med lägre komplexitet för att enkelt skapa och vara värd för en assistent som är väl lämpad för scenarier för slutförande av uppgifter. [Direct Line Speech](direct-line-speech.md) ger rikare och mer sofistikerade funktioner som kan aktivera robusta konversationsscenarier. Se [jämförelsen av assistentlösningar](voice-assistants.md#choosing-an-assistant-solution) för mer information.

**F: Hur kommer jag igång?**

**A.** Det bästa sättet att börja med att skapa ett anpassat kommandoprogram (Preview) eller grundläggande Bot Framework-bot.

- [Skapa ett anpassat kommandoprogram (förhandsgranska)](quickstart-custom-speech-commands-create-new.md)
- [Skapa en grundläggande Bot Framework bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [Ansluta en robot till direktlinjetalskanalen](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Felsökning

**F: Var är min kanal hemlighet?**

**A.** Om du har använt förhandsgranskningsversionen av Direktlinjetal eller om du läser relaterad dokumentation kan du förvänta dig att hitta en hemlig nyckel på registreringssidan för direktradens talkanal. Fabriken v1.7 `DialogServiceConfig` `FromBotSecret` i Tal-SDK förväntar sig också detta värde.

Den senaste versionen av Direct Line Speech förenklar processen att kontakta din robot från en enhet. På sidan för kanalregistrering associerar listrutan högst upp din direktlinjeanförandekanalregistrering med en talresurs. När den har associerats innehåller v1.8 Speech SDK en `BotFrameworkConfig::FromSubscription` fabriksmetod som konfigurerar en `DialogServiceConnector` för att kontakta den robot som du har associerat med din prenumeration.

Om du fortfarande migrerar klientprogrammet från v1.7 till v1.8 `DialogServiceConfig::FromBotSecret` kan det fortsätta att fungera med ett icke-tomt, icke-null-värde för dess kanalhemliga parameter, t.ex. Det kommer helt enkelt att ignoreras när du använder en talprenumeration som är associerad med en nyare kanalregistrering. Observera att värdet _måste_ vara icke-null och icke-tomt, eftersom dessa kontrolleras på enheten innan servicesidan föreningen är relevant.

En mer detaljerad guide finns i [handledningsavsnittet](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) som går igenom kanalregistreringen.

**F: Jag får ett 401 fel vid anslutning och ingenting fungerar. Jag vet att min talprenumerationsnyckel är giltig. Vad är det som händer?**

**A.** När du hanterar din prenumeration på Azure-portalen ska du se till att du använder **talresursen** (Microsoft.CognitiveServicesSpeechServices, "Speech") och _inte_ **Cognitive Services-resursen** (Microsoft.CognitiveServicesAllInOne, "Alla kognitiva tjänster"). Kontrollera också [stöd för taltjänstregionen för röstassistenter](regions.md#voice-assistants).

![korrekt prenumeration för direktlinjetal](media/voice-assistants/faq-supported-subscription.png "exempel på en kompatibel Speech-prenumeration")

**F: Jag får erkännande `DialogServiceConnector`text tillbaka från min, men jag ser en "1011" fel och ingenting från min bot. Varför?**

**A.** Det här felet indikerar ett kommunikationsproblem mellan assistenten och röstassistenttjänsten.

- För anpassade kommandon (förhandsversion) kontrollerar du att ditt anpassade kommandon (förhandsversion) program publiceras
- För direkt radtal kontrollerar du att du har [anslutit din robot till direktlinjetalskanalen,](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)lagt till stöd för [direktuppspelningsprotokoll](https://aka.ms/botframework/addstreamingprotocolsupport) till din robot (med det relaterade webbuttagsstödet) och kontrollera sedan att roboten svarar på inkommande begäranden från kanalen.

**F: Den här koden fungerar fortfarande inte och/eller så får `DialogServiceConnector`jag ett annat fel när jag använder en . Vad ska jag göra?**

**A.** Filbaserad loggning ger betydligt mer information och kan hjälpa till att påskynda supportärenden. Om du vill aktivera den här funktionen läser du [hur du använder filloggning](how-to-use-logging.md).

## <a name="next-steps"></a>Nästa steg

- [Troubleshooting](troubleshooting.md) (Felsökning)
- [Viktig information](releasenotes.md)
