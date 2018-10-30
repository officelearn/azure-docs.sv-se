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
ms.date: 10/25/2018
ms.author: tulasim
ms.openlocfilehash: 9597b878eb3d92727b352ba42a9e5557bb1cc799
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211442"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>Varför är min URL: er / filerna inte extrahera frågor svar par?

Det är möjligt att QnA Maker inte kan automatiskt extrahera frågor och svar (frågor och svar) innehåll från giltiga URL: er med vanliga frågor och svar. I sådana fall kan du klistra in frågor och svar om innehållet i en txt-fil och se om verktyget kan mata in den. Alternativt kan du redigeringsmässigt lägga till innehåll kunskapsbasen via den [QnA Maker portal](https://qnamaker.ai).

## <a name="how-large-a-knowledge-base-can-i-create"></a>Hur stor kunskapsbas kan jag skapa?

Storleken på kunskapsbasen beror på den SKU för Azure search som du väljer när du skapar QnA Maker-tjänsten. Läs [här](./Tutorials/choosing-capacity-qnamaker-deployment.md) för mer information.

## <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Varför kan jag inte se vad som helst i listrutan när jag försöker skapa en ny kunskapsbas?

Du har inte skapat några QnA Maker-tjänster i Azure ännu. Läs [här](./How-To/set-up-qnamaker-service-azure.md) att lära dig hur du gör.

## <a name="how-do-i-share-a-knowledge-base-with-others"></a>Hur gör jag för att dela en kunskapsbas med andra?

Delning fungerar på nivån för QnA Maker-tjänsten, det vill säga kommer alla kunskapsbaser i tjänsten att delas. Läs [här](./How-To/collaborate-knowledge-base.md) så samarbeta med en kunskapsbas.

## <a name="can-you-share-a-kb-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-kb"></a>Kan du dela en KB med deltagare som inte är i samma AAD-klientorganisation, att ändra ett KB? 

Dela baseras på Azure rollbaserad åtkomstkontroll (RBAC). Om du kan dela _alla_ resurs i Azure med en annan användare, kan du också dela QnA Maker.

## <a name="if-you-have-an-app-service-plan-with-5-qnamaker-kbs-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-kb"></a>Om du har en App Service-Plan med 5 QnAMaker KB-artiklar. Kan du tilldela Läs/Skriv-behörighet till 5 olika användare så att var och en av dem kan komma åt endast 1 QnAMaker KB?

Du kan dela hela QnAMaker tjänsten, inte för enskilda KB-artiklar.

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Hur kan jag ändra standardmeddelandet när ingen bra matchning hittas?

Standardmeddelandet är en del av inställningarna i din App service.
- Gå till din App service-resurs i Azure portal

![appservice qnamaker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klicka på den **inställningar** alternativet

![qnamaker appservice-inställningar](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Ändra värdet för den **DefaultAnswer** inställningen
- Starta om App service

![qnamaker appservice-omstart](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Varför är min SharePoint-länken inte komma extrahera?

Verktyget Parsar endast offentliga URL: er och har inte stöd för autentiserad datakällor just nu. Du kan också ladda ned filen och använda alternativet ladda upp filer för att extrahera frågor och svar.


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Uppdateringar som jag gjorde på min kunskapsbas inte visas publicera. Varför inte?

Varje redigeringsåtgärden måste i uppdatera tabell-, test- eller inställningen, sparas innan de kan publiceras. Se till att klicka på den **spara och träna** knappen efter varje.

## <a name="when-should-i-refresh-my-endpoint-keys"></a>När bör jag uppdatera Mina endpoint-nycklar?

Uppdatera dina endpoint-nycklar om du misstänker att de har komprometterats.

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Har kunskapsbasen stöd för formaterade data eller multimedia?

Kunskapsbasen har stöd för Markdown. Den automatiserade extraheringen från URL: er har dock begränsade HTML-Markdown-funktioner för konvertering mellan. Om du vill använda Markdown med fullständiga funktioner kan du modifiera ditt innehåll direkt i tabellen eller ladda upp en kunskapsbas med det formaterade innehållet.

Multimedia, till exempel bilder och videor, stöds inte just nu.

## <a name="does-qna-maker-support-non-english-languages"></a>Har QnA Maker stöd för andra språk än engelska?

Läs mer om [språk som stöds](./Overview/languages-supported.md).

Om du har innehåll från flera språk, måste du skapa en separat tjänst för varje språk.

## <a name="can-i-use-the-same-azure-search-resource-for-kbs-using-multiple-languages"></a>Kan jag använda samma Azure Search-resurs för KB-artiklar med flera språk?

Om du vill använda flera språk och flera KB-artiklar, måste användaren skapa en QnA Maker-resurs för varje språk. Detta skapar en separat Azure search-tjänster per språk. Blanda olika språk KB-artiklar i en enda Azure search-tjänsten resulterar i degraderat relevans med resultat.

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Måste jag använda Bot Framework för att kunna använda QnA Maker?

Nej, behöver du inte använda Bot Framework med QnA Maker. Men erbjuds QnA Maker som en av flera olika mallar i Azure Bot Service. Bot Service erbjuder snabb, intelligent bot-utveckling via Microsoft Bot Framework och det körs i en miljö utan server.

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>Hur kan jag skapa en robot med QnA Maker?

Följ instruktionerna i [detta](./Tutorials/create-qna-bot.md) dokumentationen för att skapa din robot med Azure Bot Service.

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Hur bäddar jag in QnA Maker-tjänsten på min webbplats?

Följ stegen nedan för att bädda in QnA Maker-tjänsten som en webbchatten-kontroll på din webbplats:

1. Skapa din vanliga frågor och svar-robot genom att följa anvisningarna [här](./Tutorials/create-qna-bot.md).
2. Aktivera webbchatt genom att följa stegen [här](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)


