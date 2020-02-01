---
title: Fel sökning – QnA Maker
titleSuffix: Azure Cognitive Services
description: Den granskade listan med de vanligaste frågorna om QnA Maker tjänsten hjälper dig att komma igång med tjänsten snabbare och med bättre resultat.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 18d7e02689cc9c5fe9282a6a2456b8b1574ec85e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901650"
---
# <a name="troubleshooting-for-qna-maker"></a>Fel sökning för QnA Maker

Den granskade listan med de vanligaste frågorna om QnA Maker tjänsten hjälper dig att komma igång med tjänsten snabbare och med bättre resultat.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="how-to-get-the-qnamaker-service-endpoint"></a>Så här hämtar du QnAMaker-tjänstens slut punkt

QnAMaker-tjänstens slut punkt är användbar för fel sökning när du kontaktar QnAMaker-support eller UserVoice. Slut punkten är en URL i det här formuläret: https://your-resource-name.azurewebsites.net.

1. Gå till QnAMaker-tjänsten (resursgrupp) den [Azure-portalen](https://portal.azure.com)

    ![QnAMaker Azure-resursgrupp i Azure-portalen](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Välj den App Service som är kopplad till QnA Maker resursen. Normalt är namnen desamma.

     ![Välj QnAMaker App Service](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Slut punkts-URL: en är tillgänglig i översikts avsnittet

    ![QnAMaker-slutpunkt](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

## <a name="manage-the-knowledge-base"></a>Hantera kunskapsbasen

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Jag råkade ta bort en del av mitt QnA Maker, vad ska jag göra?

Ta inte bort någon av de Azure-tjänster som skapats tillsammans med QnA Maker-resursen, till exempel search eller Web App. Detta är nödvändigt för att QnA Maker ska fungera om du tar bort ett QnA Maker slutar fungera korrekt.

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

Se [datakällans platser](./Concepts/knowledge-base.md#data-source-locations) för mer information.

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Uppdateringar som jag gjorde på min kunskapsbas inte visas publicera. Varför inte?

Varje redigeringsåtgärden måste i uppdatera tabell-, test- eller inställningen, sparas innan de kan publiceras. Se till att klicka på knappen **Spara och träna** efter varje redigerings åtgärd.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Har kunskapsbasen stöd för formaterade data eller multimedia?

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Automatisk extrahering av multimedia för filer och URL: er

* URL-begränsad HTML-till-markdown konverterings funktion.
* Filer – stöds inte

#### <a name="answer-text-in-markdown"></a>Svars text i markdown
När QnA-uppsättningar finns i kunskaps basen kan du redigera svarets markdown-text för att inkludera länkar till mediet som är tillgängliga från offentliga URL: er.

### <a name="does-qna-maker-support-non-english-languages"></a>Kan QnA Maker användas med andra språk än engelska?

Läs mer om [språk som stöds](./Overview/languages-supported.md).

Om du har innehåll från olika språk måste du skapa en separat tjänst för varje språk.

## <a name="manage-service"></a>Hantera tjänst

### <a name="when-should-i-restart-my-app-service"></a>När bör jag startar om min app service?

Uppdatera din app service när på varningsikonen är bredvid versionsvärdet för kunskapsbasen i den **Endpoint nycklar** tabellen på den **användarinställningar** [sidan](https://www.qnamaker.ai/UserSettings).

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>Jag har tagit bort min befintliga search-tjänst. Hur kan jag åtgärda detta?

Om du tar bort ett Azure Kognitiv sökning-index är åtgärden slutgiltig och indexet kan inte återställas.

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>Jag har tagit bort mitt `testkb`-index i min search-tjänst. Hur kan jag åtgärda detta?

Dina gamla data kan inte återställas. Skapa en ny QnA Maker resurs och skapa din kunskaps bas igen.

### <a name="when-should-i-refresh-my-endpoint-keys"></a>När bör jag uppdatera Mina endpoint-nycklar?

Uppdatera dina endpoint-nycklar om du misstänker att de har komprometterats.

### <a name="can-i-use-the-same-azure-cognitive-search-resource-for-knowledge-bases-using-multiple-languages"></a>Kan jag använda samma Azure Kognitiv sökning-resurs för kunskaps banker med flera språk?

Om du vill använda flera språk och flera kunskapsbaser, har användaren att skapa en QnA Maker-resurs för varje språk. Då skapas en separat Azure Search-tjänst per språk. Blanda kunskapsbaser för olika språk i en enda Azure search-tjänst resulterar i degraderat relevans med resultat.

### <a name="how-can-i-change-the-name-of-the-azure-cognitive-search-resource-used-by-qna-maker"></a>Hur kan jag ändra namnet på den Azure Kognitiv sökning-resurs som används av QnA Maker?

Namnet på Azure Kognitiv sökning-resursen är QnA Maker resurs namnet med vissa slumpmässiga bokstäver sist i slutet. På så sätt blir det svårt att skilja mellan flera Sök efter resurser för QnA Maker. Skapa en separat Sök tjänst (namnge den på det sätt som du vill) och Anslut den till din QnA-tjänst. Stegen liknar de steg du behöver utföra för att [uppgradera en Azure-sökning](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>När QnA Maker returnerar `Runtime core is not initialized,` Hur åtgärdar jag det?

Disk utrymmet för App Service kan vara fullt. Steg för att åtgärda disk utrymmet:

1. I [Azure Portal](https://portal.azure.com)väljer du QNA Maker app service och stoppar sedan tjänsten.
1. När du fortfarande är i App Service väljer du **utvecklingsverktyg**, sedan **Avancerade verktyg**och sedan **gå**. Då öppnas ett nytt fönster i webbläsaren.
1. Välj **fel söknings konsolen**och sedan **cmd** för att öppna ett kommando rads verktyg.
1. Navigera till _platsen/wwwroot/data/QnAMaker/_ Directory.
1. Ta bort alla mappar vars namn börjar med `rd`.

    **Ta inte bort** följande:

    * KbIdToRankerMappings. txt-fil
    * EndpointSettings. JSON-fil
    * EndpointKeys-mapp

1. Starta App Service.
1. Kom åt din kunskaps bas för att kontrol lera att den fungerar nu.


## <a name="integrate-with-other-services-including-bots"></a>Integrera med andra tjänster som exempel robotar

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Måste jag använda Bot Framework för att kunna använda QnA Maker?

Nej, du behöver inte använda [bot Framework](https://github.com/Microsoft/botbuilder-dotnet) med QNA Maker. QnA Maker erbjuds dock som en av flera mallar i [Azure bot service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). Bot Service erbjuder snabb, intelligent bot-utveckling via Microsoft Bot Framework och det körs i en miljö utan server.

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>Hur gör jag för att skapa en ny robot med QnA Maker?

Följ instruktionerna i [detta](./Tutorials/create-qna-bot.md) dokumentationen för att skapa din robot med Azure Bot Service.

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>Hur gör jag för att använda en annan kunskaps bas med en befintlig Azure bot-tjänst?

Du måste ha följande information om din kunskaps bas:

* Kunskaps bas-ID.
* Det anpassade under domän namnet för den publicerade slut punkten i kunskaps basen, som kallas `host`, hittades på sidan **Inställningar** när du har publicerat.
* Kunskaps basens publicerade slut punkts nyckel finns på **inställnings** sidan när du har publicerat.

Med den här informationen går du till appens robots App Service i Azure Portal. Under **Inställningar-> konfiguration – inställningar för > program**, ändra dessa värden.

Kunskaps basens slut punkts nyckel är märkt `QnAAuthkey` i ABS-tjänsten.

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>Kan två eller flera klient program dela en kunskaps bas?

Ja, kunskaps basen kan frågas från valfritt antal klienter. Om svaret från kunskaps basen verkar vara långsamt eller tids gräns, bör du överväga att uppgradera tjänst nivån för App Service som är associerad med kunskaps basen.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Hur bäddar jag in QnA Maker-tjänsten på min webbplats?

Följ stegen nedan för att bädda in QnA Maker-tjänsten som en webbchatten-kontroll på din webbplats:

1. Skapa din vanliga frågor och svar-robot genom att följa anvisningarna [här](./Tutorials/create-qna-bot.md).
2. Aktivera webbchatt genom att följa stegen [här](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

## <a name="data-storage"></a>Datalagring

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Vilka data lagras och där lagras det?

När du skapar din QnA Maker-tjänsten, du har valt en Azure-region. Dina kunskapsbaser och loggfilerna lagras i den här regionen.
