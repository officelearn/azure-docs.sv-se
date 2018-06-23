---
title: Vanliga frågor och svar - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Vanliga frågor och svar
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: a6bf32549715d0357771b3f3b0ff72f64788ec20
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354081"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>Varför är min webbadresser / fil(er) inte extraherar frågor svar par?

Det är möjligt att frågor och svar om Maker inte kan automatiskt-extrahera några frågor och svar (frågor och svar) innehåll från giltiga URL: er med vanliga frågor och svar. I sådana fall kan du klistra in frågor och svar om innehållet i en txt-fil och se om verktyget kan mata in den. Alternativt kan du kan redigeringsmässigt lägga till innehåll i knowledge base.

## <a name="how-large-a-knowledge-base-can-i-create"></a>Hur stor kunskapsbas kan jag skapa?

Storleken på kunskapsbasen beror på SKU Azure-sökningen som du väljer när du skapar frågor och svar om Maker-tjänsten. Läs [här](./Tutorials/choosing-capacity-qnamaker-deployment.md) för mer information.

## <a name="why-do-i-not-see-anything-in-the-drop-down-for-when-i-try-to-create-a-new-knowledge-base"></a>Varför inte visas i listrutan för när jag försöker skapa en ny kunskapsbas?

Du har inte skapat några frågor och svar om Maker tjänster i Azure ännu. Läs [här](./How-To/set-up-qnamaker-service-azure.md) hur du gör.

## <a name="how-do-i-share-a-knowledge-base-with-other"></a>Hur jag för att dela en knowledge base med andra?

Delning fungerar på nivån för en tjänst för frågor och svar om Maker, kommer d.v.s. alla knowledge baser Services att delas. Läs [här](./How-To/collaborate-knowledge-base.md) så att samarbeta på en knowledge base.

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Hur kan jag ändra standardmeddelandet när ingen bra matchning hittas?

Standardmeddelandet är en del av inställningarna i din apptjänst.
- Gå till i din App service-resurs i Azure-portalen

![qnamaker apptjänst](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klicka på den **inställningar** alternativet

![qnamaker apptjänst inställningar](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Ändra värdet för den **DefaultAnswer** inställning
- Starta om din apptjänst

![qnamaker apptjänst omstart](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Varför är min SharePoint-länken inte komma extrahera?

Verktyget Parsar endast offentliga URL: er och autentiserad datakällor har inte stöd för tillfället. Du kan också ladda ned filen och använder alternativet ladda upp filen för att extrahera frågor och svar.


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Uppdateringar som jag har gjort i min kunskapsbas inte visas på Publicera. Varför inte?

Varje Redigera åtgärd måste i en tabell update-, test- eller inställningar, sparas innan den kan publiceras. Se till att klicka på Spara och träna knappen efter varje redigeringsåtgärd.

## <a name="when-should-i-refresh-my-endpoint-keys"></a>När bör jag uppdatera Mina endpoint nycklar?

Om du misstänker att de har komprometterats bör du uppdatera dina nycklar för slutpunkten.

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Har omfattande data för kunskapsbas support eller multimedia?

Kunskapsbasen har stöd för Markdown. Auto-extrahering från URL: er har dock begränsat HTML-Markdown-konvertering kapaciteten. Om du vill använda riktiga Markdown du ändra ditt innehåll direkt i tabellen, eller överför en kunskapsbas med det omfattande innehållet.

Multimedia, till exempel bilder och videor, stöds inte just nu.

## <a name="does-qna-maker-support-non-english-languages"></a>Frågor och svar om Maker som har stöd för andra språk än engelska?

Se mer information [språk som stöds](./Overview/languages-supported.md).

Om du har innehåll från flera språk måste du skapa en separat tjänst för varje språk.

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Måste jag använda Bot Framework för att kunna använda QnA Maker?

Nej, behöver du inte använda Bot Framework med frågor och svar om Maker. Dock erbjuds frågor och svar om Maker som en av flera mallar i Azure Bot Service. Bot-tjänster möjliggör snabb intelligent bot utveckling genom Microsoft Bot Framework och körs på en server lägre miljön.

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>Hur kan jag skapa en bot med frågor och svar om Maker?

Följ instruktionerna i [detta](./Tutorials/create-qna-bot.md) dokumentation för att skapa din Bot med Azure Bot.

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Hur bäddar jag in QnA Maker-tjänsten på min webbplats?

Följ dessa steg för att bädda in tjänsten frågor och svar om Maker som en webbchatt kontroll i din webbplats:

1. Skapa din vanliga frågor och svar bot genom att följa anvisningarna [här](./Tutorials/create-qna-bot.md).
2. Aktivera webbchatt genom att följa stegen [här](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-channel-connect-webchat)


