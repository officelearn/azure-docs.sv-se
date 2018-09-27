---
title: Vanliga frågor och svar – QnA Maker
titleSuffix: Azure Cognitive Services
description: Lista över vanliga frågor och svar för QnA Maker-tjänsten
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: efefd595c43d7f46ff1ead91577d070cf8fb90e4
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47164624"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>Varför är min URL: er / filerna inte extrahera frågor svar par?

Det är möjligt att QnA Maker inte kan automatiskt extrahera frågor och svar (frågor och svar) innehåll från giltiga URL: er med vanliga frågor och svar. I sådana fall kan du klistra in frågor och svar om innehållet i en txt-fil och se om verktyget kan mata in den. Alternativt kan du redigeringsmässigt lägga till innehåll kunskapsbasen.

## <a name="how-large-a-knowledge-base-can-i-create"></a>Hur stor kunskapsbas kan jag skapa?

Storleken på kunskapsbasen beror på den SKU för Azure search som du väljer när du skapar QnA Maker-tjänsten. Läs [här](./Tutorials/choosing-capacity-qnamaker-deployment.md) för mer information.

## <a name="why-do-i-not-see-anything-in-the-drop-down-for-when-i-try-to-create-a-new-knowledge-base"></a>Varför visas inte något i listrutan för när jag försöker skapa en ny kunskapsbas?

Du har inte skapat några QnA Maker-tjänster i Azure ännu. Läs [här](./How-To/set-up-qnamaker-service-azure.md) hur du gör.

## <a name="how-do-i-share-a-knowledge-base-with-other"></a>Hur gör jag för att dela en kunskapsbas med andra?

Delning fungerar på nivån för QnA Maker-tjänsten, kommer d.v.s. alla kunskapsbaser i tjänsterna att delas. Läs [här](./How-To/collaborate-knowledge-base.md) så samarbeta med en kunskapsbas.

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Hur kan jag ändra standardmeddelandet när ingen bra matchning hittas?

Standardmeddelandet är en del av inställningarna i din App service.
- Gå till i din App service-resurs i Azure portal

![appservice qnamaker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klicka på den **inställningar** alternativet

![qnamaker appservice-inställningar](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Ändra värdet för den **DefaultAnswer** inställningen
- Starta om App service

![qnamaker appservice-omstart](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Varför är min SharePoint-länken inte komma extrahera?

Verktyget Parsar endast offentliga URL: er och har inte stöd för autentiserad datakällor just nu. Du kan också ladda ned filen och använda alternativet ladda upp filer för att extrahera frågor och svar.


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Uppdateringar som jag gjorde på min kunskapsbas inte visas publicera. Varför inte?

Varje redigeringsåtgärden måste i en uppdatering av tabell-, test- eller inställningar, sparas innan de kan publiceras. Glöm inte att klicka på Spara och träna knappen efter varje.

## <a name="when-should-i-refresh-my-endpoint-keys"></a>När bör jag uppdatera Mina endpoint-nycklar?

Om du misstänker att de har komprometterats bör du uppdatera dina endpoint-nycklar.

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Har kunskapsbasen stöd för formaterade data eller multimedia?

Kunskapsbasen har stöd för Markdown. Den automatiserade extraheringen från URL: er har dock begränsade HTML-Markdown-funktioner för konvertering mellan. Om du vill använda Markdown med fullständiga funktioner kan du modifiera ditt innehåll direkt i tabellen eller ladda upp en kunskapsbas med det formaterade innehållet.

Multimedia, till exempel bilder och videor, stöds inte just nu.

## <a name="does-qna-maker-support-non-english-languages"></a>Har QnA Maker stöd för andra språk än engelska?

Läs mer om [språk som stöds](./Overview/languages-supported.md).

Om du har innehåll från flera språk, måste du skapa en separat tjänst för varje språk.

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Måste jag använda Bot Framework för att kunna använda QnA Maker?

Nej, behöver du inte använda Bot Framework med QnA Maker. Men erbjuds QnA Maker som en av flera olika mallar i Azure Bot Service. Bot Service erbjuder snabb, intelligent bot-utveckling via Microsoft Bot Framework och körs på en server mindre miljö.

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>Hur kan jag skapa en robot med QnA Maker?

Följ instruktionerna i [detta](./Tutorials/create-qna-bot.md) dokumentationen för att skapa din robot med Azure Bot.

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Hur bäddar jag in QnA Maker-tjänsten på min webbplats?

Följ stegen nedan för att bädda in QnA Maker-tjänsten som en webbchatten-kontroll på din webbplats:

1. Skapa din vanliga frågor och svar-robot genom att följa anvisningarna [här](./Tutorials/create-qna-bot.md).
2. Aktivera webbchatt genom att följa stegen [här](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)


