---
title: Anpassade röst första virtuella assistenter (förhandsversion) – Speech Services
titleSuffix: Azure Cognitive Services
description: En översikt över de funktioner, funktioner och begränsningar för anpassade voice-först-virtuella assistenter med tal för Direct Line-kanalen på Bot Framework och den Cognitive Services tal Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: c9ed6f898f4bcf8a5d4b2548cc1f3cf51f71bb2a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138168"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>Om anpassade röst första virtuella assistenter förhandsversionen

Anpassade virtuella assistenter med hjälp av Azure Speech Services gör att utvecklare kan skapa naturlig, människoliknande konversationsanpassade gränssnitt för sina program och upplevelser. I Bot Framework Direct Line tal kanal förbättrar funktionerna genom att tillhandahålla en samordnad, dirigerad startpunkten till en kompatibel robot som gör det möjligt för röst i röst ut interaktion med låg fördröjning och hög tillförlitlighet. Dessa robotar kan använda Microsofts Språkförståelse (LUIS) för interaktion med naturligt språk. Direct Line tal används av enheter med hjälp av tal Software Development Kit (SDK).

   ![Begreppsmässiga diagram över direktlinje tal orchestration service flödet](media/voice-first-virtual-assistants/overview.png "The tal kanal flöde")

Direct Line tal och dess associerade funktioner för anpassad röst första virtuella assistenter är ett perfekt komplement till den [virtuella Assistant lösning](https://docs.microsoft.com/azure/bot-service/bot-builder-virtual-assistant-introduction) och [företagets mall](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Även om Direct Line-tal kan arbeta med valfri kompatibel bot, ange resurserna en återanvändbara baslinje för högkvalitativa konversationsanpassade upplevelser som vanliga stödjande kunskaper och modeller för att komma igång snabbt.

## <a name="core-features"></a>Kärnfunktioner

| Category | Funktioner |
|----------|----------|
|[Anpassad aktivering word](speech-devices-sdk-create-kws.md) | Du kan aktivera användare kan börja konversationer med robotar med hjälp av ett anpassat nyckelord som ”Hey Contoso”. Den här uppgiften utförs med en anpassad aktivering word-motorn i tal SDK, som kan konfigureras med en anpassad aktivering word [som du kan skapa här](speech-devices-sdk-create-kws.md). Direct Line tal kanalen innehåller tjänstsidan wake word verifiering som förbättrar precisionen i wake word aktiveringen jämfört med den fristående enheten.
|[Tal till text](speech-to-text.md) | Direct Line tal kanalen innehåller i realtid avskrift med ljud i tolkade texten med [tal till text](speech-to-text.md) från Azure Speech Services. Den här texten är tillgänglig för både din robot och klientprogrammet som den är transkriberas.
|[Text till tal](text-to-speech.md) | Textbaserade svaren från din robot ska syntetiseras med [text till tal](text-to-speech.md) från Azure Speech Services. Den här syntes görs sedan tillgänglig för klientprogrammet som en ljudström. Microsoft ger dig möjlighet att skapa din egen anpassade, hög kvalitet Neural text till tal-röst som ger en röst till ditt varumärke, mer [Kontakta oss](mailto:mstts@microsoft.com).
|[Direct Line-tal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Direct Line-tal kan en sömlöst anslutning mellan ditt klientprogram och en kompatibel robot funktionerna i Azure Speech Services som en kanal i Bot Framework. Läs mer om hur du konfigurerar din robot för att använda Direct Line tal kanalen [appens sida i Bot Framework-dokumentationen](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="sample-code"></a>Exempelkod

Exempelkod för att skapa en röst-första virtuella assistenter är tillgänglig på GitHub. De här exemplen omfattar klientprogram för att ansluta till din robot i flera vanliga programmeringsspråk.

* [Röst första virtuella assistenter exempel (SDK)](https://aka.ms/csspeech/samples)
* [Snabbstart: röst-första virtuella assistenter (C#)](quickstart-virtual-assistant-csharp-uwp.md)
* [Snabbstart: röst första virtuella assistenter (Java)](quickstart-virtual-assistant-java-jre.md)

## <a name="customization"></a>Anpassning

Röst första virtuella assistenter som skapats med Azure Speech Services kan använda ett komplett utbud av tillgängliga för anpassningsalternativ [tal till text](speech-to-text.md), [text till tal](text-to-speech.md), och [anpassade nyckelord val av](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Anpassningsalternativ varierar beroende på språk och nationella (se [språk som stöds](supported-languages.md)).

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Nästa steg

* [Skaffa en prenumerationsnyckel för Speech Services utan kostnad](get-started.md)
* [Hämta tal SDK](speech-sdk.md)
* [Skapa och distribuera en grundläggande bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Hämta virtuell Assistant lösningen och företagets mall](https://github.com/Microsoft/AI)
