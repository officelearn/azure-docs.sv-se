---
title: Vanliga frågor och svar – QnA Maker
titleSuffix: Azure Cognitive Services
description: Granskad lista över vanliga frågor för QnA Maker-tjänsten hjälper dig att använda tjänsten snabbare och bättre resultat.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 8104497a1808aa4e92d62f45e37525dba2f47742
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607180"
---
# <a name="frequently-asked-questions-for-qna-maker"></a>Vanliga frågor om QnA Maker

Granskad lista över vanliga frågor för QnA Maker-tjänsten hjälper dig att använda tjänsten snabbare och bättre resultat.

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>Använd hjälp-roboten i QnA Maker-portalen

QnA Maker ger en **hjälpa** bot inom QnA Maker-portalen för att hjälpa dig. Hjälp-roboten är tillgänglig på varje webbsida. Roboten använder QnA Maker för att ge svar och ger den [ C# Bot Framework Kodprojekt](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot) till roboten så att du snabbt igång med din egen svar-robot. 

![! [QnA Maker ger en ** hjälp ** bot QnA Maker-portalen som hjälper dig att.] (. / media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>Hantera kunskapsbasen

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Jag råkade ta bort en del av mitt QnA Maker, vad ska jag göra? 

Alla borttagningar är permanent, inklusive frågor och svar-par, filer, URL: er, anpassade frågor och svar, kunskapsbaser eller Azure-resurser. Kontrollera att du exporterar kunskapsbasen från den **inställningar** sidan innan du tar bort någon del av kunskapsbasen. 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>Varför är min URL: er / filerna inte extrahering frågor svar par?

Det är möjligt att QnA Maker inte kan automatiskt extrahera frågor och svar (frågor och svar) innehåll från giltiga URL: er med vanliga frågor och svar. I sådana fall kan du klistra in frågor och svar om innehållet i en txt-fil och se om verktyget kan mata in den. Alternativt kan du redigeringsmässigt lägga till innehåll kunskapsbasen via den [QnA Maker portal](https://qnamaker.ai).

### <a name="how-large-a-knowledge-base-can-i-create"></a>Hur stor kunskapsbas kan jag skapa?

Storleken på kunskapsbasen beror på den SKU för Azure search som du väljer när du skapar QnA Maker-tjänsten. Läs [här](./Tutorials/choosing-capacity-qnamaker-deployment.md) för mer information.

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Varför kan jag inte se vad som helst i listrutan när jag försöker skapa en ny kunskapsbas?

Du har inte skapat några QnA Maker-tjänster i Azure ännu. Läs [här](./How-To/set-up-qnamaker-service-azure.md) att lära dig hur du gör.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Hur gör jag för att dela en kunskapsbas med andra?

Delning fungerar på nivån för QnA Maker-tjänsten, det vill säga kommer alla kunskapsbaser i tjänsten att delas. Läs [här](./How-To/collaborate-knowledge-base.md) så samarbeta med en kunskapsbas.

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>Kan du dela en kunskapsbas med deltagare som inte är i samma AAD-klientorganisation, att ändra en kunskapsbas? 

Dela baseras på Azure rollbaserad åtkomstkontroll (RBAC). Om du kan dela _alla_ resurs i Azure med en annan användare, kan du också dela QnA Maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>Om du har en App Service-Plan med 5 QnAMaker kunskapsbaser. Kan du tilldela Läs/Skriv-behörighet till 5 olika användare så att var och en av dem kan komma åt endast 1 QnAMaker kunskapsbas?

Du kan dela hela QnAMaker tjänsten, inte för enskilda kunskapsbaser.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Hur kan jag ändra standardmeddelandet när ingen bra matchning hittas?

Standardmeddelandet är en del av inställningarna i din App service.
- Gå till din App service-resurs i Azure portal

![appservice qnamaker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klicka på den **inställningar** alternativet

![qnamaker appservice-inställningar](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Ändra värdet för den **DefaultAnswer** inställningen
- Starta om App service

![qnamaker appservice-omstart](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Varför är min SharePoint-länken inte komma extrahera?

Se [datakällans platser](./Concepts/data-sources-supported.md#data-source-locations) för mer information.

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Uppdateringar som jag gjorde på min kunskapsbas inte visas publicera. Varför inte?

Varje redigeringsåtgärden måste i uppdatera tabell-, test- eller inställningen, sparas innan de kan publiceras. Se till att klicka på den **spara och träna** knappen efter varje.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Har kunskapsbasen stöd för formaterade data eller multimedia?

Kunskapsbasen har stöd för Markdown. Den automatiserade extraheringen från URL: er har dock begränsade HTML-Markdown-funktioner för konvertering mellan. Om du vill använda Markdown med fullständiga funktioner kan du modifiera ditt innehåll direkt i tabellen eller ladda upp en kunskapsbas med det formaterade innehållet.

Multimedia, till exempel bilder och videor, stöds inte just nu.

### <a name="does-qna-maker-support-non-english-languages"></a>Har QnA Maker stöd för andra språk än engelska?

Läs mer om [språk som stöds](./Overview/languages-supported.md).

Om du har innehåll från flera språk, måste du skapa en separat tjänst för varje språk.

## <a name="manage-service"></a>Hantera tjänst

### <a name="when-should-i-restart-my-app-service"></a>När bör jag startar om min app service? 

Uppdatera din app service när på varningsikonen är bredvid versionsvärdet för kunskapsbasen i den **Endpoint nycklar** tabellen på den **användarinställningar** [sidan](https://www.qnamaker.ai/UserSettings).

### <a name="when-should-i-refresh-my-endpoint-keys"></a>När bör jag uppdatera Mina endpoint-nycklar?

Uppdatera dina endpoint-nycklar om du misstänker att de har komprometterats.

### <a name="can-i-use-the-same-azure-search-resource-for-knowledge-bases-using-multiple-languages"></a>Kan jag använda samma Azure Search-resurs för kunskapsbaser med flera språk?

Om du vill använda flera språk och flera kunskapsbaser, har användaren att skapa en QnA Maker-resurs för varje språk. Detta skapar en separat Azure search-tjänst per språk. Blanda kunskapsbaser för olika språk i en enda Azure search-tjänst resulterar i degraderat relevans med resultat.

### <a name="how-can-i-change-the-name-of-the-azure-search-resource-used-by-qna-maker"></a>Hur kan jag ändra namnet på Azure Search-resurs som används av QnA Maker?

Namnet på resursen för Azure Search är resursnamnet QnA Maker med vissa slumpmässiga bokstäver som tillägg i slutet. På så sätt blir det svårt att skilja mellan flera Sök efter resurser för QnA Maker. Skapa en separat Azure Search-tjänst (namngivning av det sätt som du vill) och ansluter den till din QnA Service. Stegen är liknande de steg som du behöver göra att [uppgradera ett Azure Search](How-To/upgrade-qnamaker-service.md#upgrade-azure-search-service).

## <a name="integrate-with-other-services-including-bots"></a>Integrera med andra tjänster som exempel robotar

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Måste jag använda Bot Framework för att kunna använda QnA Maker?

Nej, behöver du inte använda Bot Framework med QnA Maker. Men erbjuds QnA Maker som en av flera olika mallar i Azure Bot Service. Bot Service erbjuder snabb, intelligent bot-utveckling via Microsoft Bot Framework och det körs i en miljö utan server.

### <a name="how-can-i-create-a-bot-with-qna-maker"></a>Hur kan jag skapa en robot med QnA Maker?

Följ instruktionerna i [detta](./Tutorials/create-qna-bot.md) dokumentationen för att skapa din robot med Azure Bot Service.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Hur bäddar jag in QnA Maker-tjänsten på min webbplats?

Följ stegen nedan för att bädda in QnA Maker-tjänsten som en webbchatten-kontroll på din webbplats:

1. Skapa din vanliga frågor och svar-robot genom att följa anvisningarna [här](./Tutorials/create-qna-bot.md).
2. Aktivera webbchatt genom att följa stegen [här](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

## <a name="data-storage"></a>Datalagring

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Vilka data lagras och där lagras det? 

När du skapar din QnA Maker-tjänsten, du har valt en Azure-region. Dina kunskapsbaser och loggfilerna lagras i den här regionen. 
