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
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e98fd089ce8ec1285232840a40bb42ac5b81446
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795444"
---
# <a name="troubleshooting-for-qna-maker"></a>Fel sökning för QnA Maker

Den granskade listan med de vanligaste frågorna om QnA Maker tjänsten hjälper dig att komma igång med tjänsten snabbare och med bättre resultat.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="how-to-get-the-qnamaker-service-endpoint"></a>Så här hämtar du QnAMaker-tjänstens slut punkt

QnAMaker-tjänstens slut punkt är användbar för fel sökning när du kontaktar QnAMaker-support eller UserVoice. Slut punkten är en URL i det här formuläret: https://your-resource-name.azurewebsites.net.
    
1. Gå till din QnAMaker-tjänst (resurs grupp) i [Azure Portal](https://portal.azure.com)

    ![QnAMaker Azure-resurs grupp i Azure Portal](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Välj den App Service som är kopplad till QnA Maker resursen. Normalt är namnen desamma.

     ![Välj QnAMaker App Service](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Slut punkts-URL: en är tillgänglig i översikts avsnittet

    ![QnAMaker-slutpunkt](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>Använd hjälp roboten i QnA Maker Portal

QnA Maker ger en **Hjälp** robot på QNA Maker portal som hjälp. Hjälp roboten är tillgänglig på alla webb sidor. Roboten använder QNA Maker för att ge svar och ger [ C# bot Framework-kodfragmentet](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support) till roboten så att du snabbt kan komma igång med din egen svars robot. 

![! [QnA Maker innehåller en * *-hjälp * * bot i QnA Maker portalen som hjälp.] (./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>Hantera kunskaps basen

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Jag har råkat ta bort en del av mitt QnA Maker, vad ska jag göra? 

Ta inte bort någon av de Azure-tjänster som skapats tillsammans med QnA Maker-resursen, till exempel search eller Web App. Detta är nödvändigt för att QnA Maker ska fungera om du tar bort ett QnA Maker slutar fungera korrekt.

Alla borttagningar är permanenta, inklusive frågor och svars par, filer, URL: er, anpassade frågor och svar, kunskaps banker eller Azure-resurser. Se till att exportera din kunskaps bas från sidan **Inställningar** innan du tar bort någon del av kunskaps basen. 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>Varför extraherar mina URL: er inte svars par för frågor?

Det är möjligt att QnA Maker inte kan automatiskt extrahera viss fråga-och-svara (QnA)-innehåll från giltiga URL: er för vanliga frågor och svar. I sådana fall kan du klistra in QnA-innehållet i en txt-fil och se om verktyget kan mata in det. Alternativt kan du lägga till innehåll i kunskaps basen på [QNA Maker portalen](https://qnamaker.ai).

### <a name="how-large-a-knowledge-base-can-i-create"></a>Hur stor kunskapsbas kan jag skapa?

Kunskaps bas storleken beror på vilken SKU av Azure Search du väljer när du skapar tjänsten QnA Maker. Läs [här](./Tutorials/choosing-capacity-qnamaker-deployment.md) om du vill ha mer information.

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Varför kan jag inte se något i list rutan när jag försöker skapa en ny kunskaps bas?

Du har inte skapat några QnA Maker tjänster i Azure ännu. Läs [här](./How-To/set-up-qnamaker-service-azure.md) för att lära dig hur du gör det.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Hur gör jag för att dela en kunskaps bas med andra?

Delningen fungerar på samma nivå som en QnA Maker tjänst, det vill säga att alla kunskaps banker i tjänsten delas. Läs mer [här](./How-To/collaborate-knowledge-base.md) om hur du samarbetar i en kunskaps bas.

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>Kan du dela en kunskaps bas med en deltagare som inte tillhör samma AAD-klient, för att ändra en kunskaps bas? 

Delning baseras på rollbaserad åtkomst kontroll (RBAC) i Azure. Om du kan dela _en_ resurs i Azure med en annan användare kan du också dela QNA Maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>Om du har en App Service-plan med 5 QnAMaker kunskaps Bases. Kan du tilldela läs-och Skriv behörighet till 5 olika användare så att var och en av dem bara har åtkomst till 1 QnAMaker kunskaps bas?

Du kan dela en hel QnAMaker-tjänst, inte enskilda kunskaps baser.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Hur kan jag ändra standard meddelandet när ingen lämplig matchning hittas?

Standard meddelandet är en del av inställningarna i din app service.
- Gå till App Service-resursen i Azure Portal

![qnamaker AppService](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klicka på **inställnings** alternativet

![qnamaker AppService-inställningar](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Ändra värdet för **DefaultAnswer** -inställningen
- Starta om App Service

![qnamaker AppService-omstart](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Varför hämtas inte min SharePoint-länk?

Mer information finns i [platser för data källor](./Concepts/data-sources-supported.md#data-source-locations) .

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Uppdateringarna som jag gjorde i min kunskaps bas avspeglas inte vid publicering. Varför inte?

Alla redigerings åtgärder, oavsett om de finns i en uppdatering, testning eller inställning i en tabell, måste sparas innan de kan publiceras. Se till att klicka på knappen **Spara och träna** efter varje redigerings åtgärd.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Stöder kunskaps basen omfattande data eller Multimedia?

Kunskapsbasen har stöd för Markdown. Den automatiska extraheringen från URL: er har dock begränsad konvertering av HTML-till-markdown-funktioner. Om du vill använda fullständig fullfjädrade markdown kan du ändra innehållet direkt i tabellen eller ladda upp en kunskaps bas med det omfattande innehållet.

Multimedia, till exempel bilder och videor, stöds inte för tillfället.

### <a name="does-qna-maker-support-non-english-languages"></a>Kan QnA Maker användas med andra språk än engelska?

Se mer information om [vilka språk som stöds](./Overview/languages-supported.md).

Om du har innehåll från olika språk måste du skapa en separat tjänst för varje språk.

## <a name="manage-service"></a>Hantera tjänst

### <a name="when-should-i-restart-my-app-service"></a>När ska jag starta om min app service? 

Uppdatera App Service när varnings ikonen är bredvid versions värdet för kunskaps basen i tabellen **slut punkts nycklar** på [sidan](https://www.qnamaker.ai/UserSettings) **användar inställningar** .

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>Jag har tagit bort min befintliga search-tjänst. Hur kan jag åtgärda detta?

Om du tar bort ett Azure Kognitiv sökning-index är åtgärden slutgiltig och indexet kan inte återställas. 

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>Jag har tagit bort mitt `testkb`-index i min search-tjänst. Hur kan jag åtgärda detta? 

Dina gamla data kan inte återställas. Skapa en ny QnA Maker resurs och skapa din kunskaps bas igen.

### <a name="when-should-i-refresh-my-endpoint-keys"></a>När ska jag uppdatera mina slut punkts nycklar?

Uppdatera dina slut punkts nycklar om du misstänker att de har komprometterats.

### <a name="can-i-use-the-same-azure-cognitive-search-resource-for-knowledge-bases-using-multiple-languages"></a>Kan jag använda samma Azure Kognitiv sökning-resurs för kunskaps banker med flera språk?

Om du vill använda flera språk och flera kunskaps baser måste användaren skapa en QnA Maker resurs för varje språk. Då skapas en separat Azure Search-tjänst per språk. Att blanda olika språk kunskaps baser i en enda Azure Search-tjänst leder till försämrade resultat.

### <a name="how-can-i-change-the-name-of-the-azure-cognitive-search-resource-used-by-qna-maker"></a>Hur kan jag ändra namnet på den Azure Kognitiv sökning-resurs som används av QnA Maker?

Namnet på Azure Kognitiv sökning-resursen är QnA Maker resurs namnet med vissa slumpmässiga bokstäver sist i slutet. Detta gör det svårt att skilja mellan flera Sök resurser för QnA Maker. Skapa en separat Sök tjänst (namnge den på det sätt som du vill) och Anslut den till din QnA-tjänst. Stegen liknar de steg du behöver utföra för att [uppgradera en Azure-sökning](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>När QnA Maker returnerar `Runtime core is not initialized,` hur löser jag det?

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


## <a name="integrate-with-other-services-including-bots"></a>Integrera med andra tjänster, inklusive robotar

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Måste jag använda Bot Framework för att kunna använda QnA Maker?

Nej, du behöver inte använda [bot Framework](https://github.com/Microsoft/botbuilder-dotnet) med QNA Maker. QnA Maker erbjuds dock som en av flera mallar i [Azure bot service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). Bot service möjliggör snabb intelligent bot-utveckling via Microsoft bot Framework och körs i en server som är mindre miljö.

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>Hur gör jag för att skapa en ny robot med QnA Maker?

Följ anvisningarna i [den här](./Tutorials/create-qna-bot.md) dokumentationen för att skapa din robot med Azure bot service.

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

Följ de här stegen för att bädda in QnA Maker tjänsten som en webb chatts kontroll på din webbplats:

1. Skapa din vanliga bot-robot genom att följa anvisningarna [här](./Tutorials/create-qna-bot.md).
2. Aktivera webbchatten genom att följa stegen [här](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

## <a name="data-storage"></a>Datalagring

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Vilka data lagras och var lagras de? 

När du skapar din QnA Maker-tjänst valde du en Azure-region. Dina kunskaps baser och loggfiler lagras i den här regionen. 
