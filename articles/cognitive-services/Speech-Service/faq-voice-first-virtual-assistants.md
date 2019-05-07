---
title: Vanliga frågor och svar om Direct Line-tal
titleSuffix: Azure Cognitive Services
description: Få svar på de mest populära frågorna om röst första virtuella assistenter med tal för Direct Line-kanalen.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.openlocfilehash: 8427417c9b579c7dfa21f834ce1ca77099159eb2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072608"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>Röst första virtuella assistenter förhandsversion: Vanliga frågor och svar

Om du inte hittar svar på dina frågor i det här dokumentet kan du kolla [andra supportalternativ](support.md).

## <a name="general"></a>Allmänt

**F: Vad är Direct Line-taligenkänning?**

**S:** Den `SpeechBotConnector` från tal SDK ger dubbelriktad, asynkron kommunikation till robotar som är anslutna till tal för Direct Line-kanal på Bot Framework. Den här kanalen tillhandahåller samordnad åtkomst till tal till text och text till tal från Azure Speech Services som tillåter robotar att bli helt röst i, röst ut konversationsanpassade upplevelser. Med stöd för wake ord och Wake Word verifiering gör den här lösningen det möjligt att skapa mycket anpassningsbara röst första virtuella assistenter för ditt varumärke eller din produkt.

**F: Hur kommer jag igång?**

**S:** Det bästa sättet att börja med skapar en röst-första virtuella assistenter är att börja med [skapar en grundläggande Bot Framework-robot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0). Anslut sedan din robot till den [Direct Line tal kanal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="debugging"></a>Felsökning

**F: Jag får ett 401-fel när de ansluter och inget fungerar. Jag vet mitt tal prenumerationsnyckeln är giltig. Vad är det som händer?**

**S:** I förhandsversion har mycket specifik begränsningar på den prenumeration som används i Direct Line-tal. Kontrollera att du använder den **tal** resurs (Microsoft.CognitiveServicesSpeechServices, ”tal”) och *inte* den **Cognitive Services** resurs ( Microsoft.CognitiveServicesAllInOne, ”alla kognitiva tjänster”). . Observera att endast den **westus2** region stöds för närvarande.

![Korrigera prenumerationen för direktlinje tal](media/voice-first-virtual-assistants/faq-supported-subscription.png "exempel på en kompatibel tal-prenumeration")

**F: Jag får erkännande text tillbaka från Direct Line-tal, men jag ser felet ”1011” och inget från min bot. Varför?**

**S:** Det här felet indikerar ett kommunikationsproblem mellan bot och Direct Line-tal. Se till att du har [anslutna direkt rad tal kanalen](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [lagt till stöd för strömning protokoll](https://aka.ms/botframework/addstreamingprotocolsupport) till bot (med stöd för relaterade Web Socket) och sedan kontrollera att din robot svarar på inkommande begäranden från kanalen.

**F: Det fortfarande fungerar inte och/eller jag får ett annat fel när du använder en SpeechBotConnector och det är oklart på vad jag ska göra. Vad *bör* jag göra?**

**S:** Filbaserade loggning ger betydligt fler detaljer och kan hjälpa dig att påskynda supportärenden. För att möjliggöra detta, se [hur du använder filen loggning](how-to-use-logging.md).

## <a name="next-steps"></a>Nästa steg

* [Felsökning](troubleshooting.md)
* [Viktig information](releasenotes.md)
