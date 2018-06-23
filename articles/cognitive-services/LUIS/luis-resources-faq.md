---
title: Vanliga frågor och svar om språk förstå (THOMAS) i Azure | Microsoft Docs
description: Få svar på vanliga frågor och svar om språk förstå (THOMAS)
author: v-geberr
manager: kaiqb
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: fd63ffd312e3ac17a6376eb3c9bef8f1978e3935
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333623"
---
# <a name="language-understanding-faq"></a>Språk förstå vanliga frågor och svar

Den här artikeln innehåller svar på vanliga frågor och svar om språk förstå (THOMAS).

## <a name="luis-authoring"></a>THOMAS redigering

### <a name="what-are-the-luis-best-practices"></a>Vad är bästa praxis THOMAS? 
Börja med den [redigering cykel](luis-concept-app-iteration.md), Läs den [metodtips](luis-concept-best-practices.md). 

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Vad är det bästa sättet att börja bygga appen min i THOMAS?

Det bästa sättet att bygga din app är via en [stegvis processen](luis-concept-app-iteration.md). 

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Vad är en bra idé att modellera innehållet i min app? Bör jag skapa mer specifika eller generisk avsikter?

Välj avsikter som inte är så Allmänt om överlappande, men inte så specifik som den gör det svårt för THOMAS att skilja mellan liknande avsikter. Skapa discriminative specifika avsikter är bästa praxis för THOMAS modellering.

### <a name="is-it-important-to-train-the-none-intent"></a>Är det viktigt att träna avsiktshantering ingen?

Ja, är det bra att träna din **ingen** avsiktshantering med mer utterances när du lägger till fler etiketter andra avsikter. Ett bra förhållande är 1 eller 2 etiketter som lagts till i **ingen** för varje 10 etiketter som lagts till syftet. Förhållandet förstärker discriminative kraften hos THOMAS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Hur kan jag åtgärda stavfel i utterances?

Finns det [Bing stavningskontroll kontrollera API V7](luis-tutorial-bing-spellcheck.md) kursen. THOMAS framtvingar begränsningar har införts av Bing stavningskontroll kontrollera API V7. 

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Hur redigerar jag min THOMAS appen via programmering?
Redigera THOMAS appen via programmering genom att använda den [redigering API](https://aka.ms/luis-authoring-apis). Se [anropa THOMAS redigering API](./luis-quickstart-node-add-utterance.md) och [bygga en THOMAS app programmässigt med Node.js](./luis-tutorial-node-import-utterances-csv.md) exempel på hur du anropar API: et redigering. Redigering API kräver att du använder en [redigering nyckeln](luis-concept-keys.md#authoring-key) i stället för en slutpunktsnyckel. Programmässiga redigering kan upp till 1 000 000 anrop per månad och fem transaktioner per sekund. Mer information om nycklar som du använder med THOMAS finns [hantera nycklar](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Där är funktionen mönster som tillhandahöll reguljärt uttryck matchar?
Den tidigare **mönsterfunktion** för närvarande är föråldrad, ersatts av  **[mönster](luis-concept-patterns.md)**. 

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Hur använder jag en entitet för att dra ut rätt data? 
Se [entiteter](luis-concept-entity-types.md) och [data extrahering](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Ska innehålla variationer av en exempel-utterance skiljetecken? 
Lägg till olika variationer som exempel utterances att avsikten eller Lägg till mönstret för exempel utterance med den [syntax för att ignorera](luis-concept-patterns.md#pattern-syntax) skiljetecken. 

## <a name="luis-endpoint"></a>THOMAS slutpunkt

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Varför THOMAS lägga till utrymmen i frågan runt eller mitt i ord?
THOMAS [tokenizes](luis-glossary.md#token) utterance baserat på de [kultur](luis-supported-languages.md#tokenization). Både det ursprungliga värdet och principfilerna värdet är tillgängliga för [data extrahering](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Hur skapar jag och tilldela en THOMAS slutpunktsnyckel?
[Skapa slutpunkten nyckeln](luis-how-to-azure-subscription.md#create-luis-endpoint-key) i Azure för din [service](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) nivå. [Tilldela nyckeln](Manage-keys.md#assign-endpoint-key) på den **[publicera](publishapp.md)** sidan. Det finns inget motsvarande API för den här åtgärden. Du måste ändra HTTP-begäran till slutpunkten för [använder den nya nyckeln endpoint](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-interpret-luis-scores"></a>Hur tolkar THOMAS poäng? 
Systemet bör använda högsta bedömningsprofil avsikten oavsett dess värde. Till exempel en poäng nedan 0,5 (mindre än 50%) inte nödvändigtvis att THOMAS har låg förtroende. Tillhandahåller mer utbildningsdata kan du öka poängen för den mest sannolika avsikten.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Varför ser jag min endpoint träffar i min app instrumentpanelen?
Totalt endpoint träffar i din app instrumentpanelen uppdateras regelbundet, men de mätvärden som är associerade med din prenumeration THOMAS nyckel i Azure-portalen uppdateras oftare. 

Om du inte ser uppdaterade endpoint träffar i instrumentpanelen, logga in på Azure-portalen och hitta resursen som är associerade med din prenumeration THOMAS-nyckel och öppna **mått** att välja den **Totalt antal anrop** mått. Om prenumerationen nyckel används för flera THOMAS app, visar mått i Azure-portalen det sammanlagda antalet anrop från alla THOMAS appar som använder den.

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Min app THOMAS fungerade igår men idag jag får 403-fel. Appen ändras inte. Hur kan jag göra? 
Efter den [instruktioner](#how-do-i-create-and-assign-a-luis-endpoint-key) i nästa vanliga frågor om att skapa en nyckel för THOMAS slutpunkt och tilldela den till appen. Du måste ändra HTTP-begäran till slutpunkten för [använder den nya nyckeln endpoint](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-secure-my-luis-endpoint"></a>Hur skyddar min THOMAS slutpunkten? 
Se [skydda slutpunkten](luis-concept-security.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Arbetar inom THOMAS gränser

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Vad är det maximala antalet avsikter och enheter som har stöd för en THOMAS app?
Finns det [gränser](luis-boundaries.md) referens.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Jag vill skapa en THOMAS app med mer än det maximala antalet avsikter. Vad ska jag göra?

Se [bästa praxis för avsikter](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Jag vill skapa en app i THOMAS med mer än det maximala antalet enheter. Vad ska jag göra?

Se [bästa praxis för entiteter](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Vad är gränsen för antalet och storleken på frasen visar?
För den maximala längden på en [frasen listan](./luis-concept-feature.md), finns det [gränser](luis-boundaries.md) referens.

### <a name="what-are-the-limits-on-example-utterances"></a>Vad är begränsningar i exempel utterances?
Finns det [gränser](luis-boundaries.md) referens.

## <a name="testing-and-training"></a>Testa och utbildning

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Det finns några fel i batchen testa fönstret för vissa av modellerna i min app. Hur kan jag åtgärda det här problemet?

Fel indikerar att det finns vissa avvikelse mellan etiketterna och förutsägelser från modeller. Gör något av följande åtgärder för att lösa problemet:
* Lägg till fler etiketter för att förbättra diskriminering mellan avsikter THOMAS.
* Lägg till fras-funktioner som införa domänspecifika ordförråd för att hjälpa THOMAS Lär dig snabbare.

Finns det [Batch testning](luis-tutorial-batch-testing.md) kursen.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>När en app exporteras sedan återimporteras till en ny app (med en ny app-ID), är THOMAS förutsägelse resultat olika. Varför händer det här? 

Se [förutsägelse skillnaderna mellan kopior av samma app](luis-concept-prediction-score.md#differences-with-predictions).

## <a name="app-publishing"></a>Publicering av appar

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Vad är klient-ID i fönstret ”Lägg till en nyckel till din app”?
I Azure representerar en klient klient eller organisation som är associerad med en tjänst. Hitta din klient-ID i Azure-portalen i den **katalog-ID** genom att välja **Azure Active Directory** > **hantera**  >  **Egenskaper**.

![Klient-ID i Azure-portalen](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

### <a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>Varför är det mer prenumeration nycklar på min app publicera sidan än tilldelats appen? 
Varje THOMAS app har redigering/starter-nyckel. THOMAS prenumeration nycklar som skapades under tidsperioden GA visas på sidan Publicera, oavsett om du har lagt till dem i appen. Detta gjordes för att underlätta GA-migrering. Alla nya THOMAS prenumeration nycklar visas inte på sidan Publicera. 

## <a name="app-management"></a>Apphantering

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Hur jag för att överföra ägarskap för en THOMAS app?
Exportera THOMAS appen för att överföra en THOMAS app till Azure-prenumeration, och importera det med ett nytt konto. Uppdatera THOMAS app-ID i klientprogrammet som anropar den. Den nya appen kan returnera något annorlunda THOMAS resultat från den ursprungliga app. 

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Hur jag för att hämta en logg över användare utterances?
Som standard loggas appen THOMAS utterances från användare. Om du vill hämta en logg över utterances som användarna skickar till appen THOMAS, gå till **Mina appar**, och klicka på de tre punkterna (***...*** ) i listan för din app. Klicka på **exportera Endpoint loggar**. Loggen formateras som en fil med kommaavgränsade värden (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Hur kan jag inaktivera loggning av utterances?
Du kan inaktivera loggning av användaren utterances genom att ange `log=false` i slutpunkts-URL som ditt klientprogram som använder för att fråga THOMAS. Men om du inaktiverar loggning inaktiverar appen THOMAS möjlighet att föreslå utterances eller förbättra prestanda som baseras på [active learning](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Om du ställer in `log=false` på grund av data sekretessen, du kan inte hämta en post för dessa användare utterances från THOMAS eller använder dessa utterances för att förbättra din app.

Loggning är endast lagring av utterances. 

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Varför vill alla mina endpoint utterances loggas?
Om du använder loggen för förutsägelse analys inte in test utterances i loggen.

## <a name="data-management"></a>Datahantering

### <a name="can-i-delete-data-from-luis"></a>Kan jag ta bort data från THOMAS? 

* Du kan alltid ta bort exempel utterances används för att träna THOMAS. Om du tar bort en exempel-utterance från din THOMAS tas bort från webbtjänsten THOMAS och är inte tillgängligt för export.
* Du kan ta bort utterances från listan över användare utterances som THOMAS föreslås i den **granska endpoint utterances** sidan. Ta bort utterances från den här listan förhindrar som föreslås, men ta bort inte dem från loggar.
* Om du tar bort ett konto raderas alla appar, tillsammans med deras exempel utterances och loggar. Data finns kvar på servrarna i 60 dagar innan den tas bort permanent.

## <a name="language-and-translation-support"></a>Stöd för språket och översättning 

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Jag har en app på ett språk och vill skapa en parallell app i ett annat språk. Vad är det enklaste sättet att göra detta?
1. Exportera din app.
2. Översätta märkt utterances i JSON-filen för den exportera appen till mål-språk.
3. Du kan behöva ändra namnen på avsikter och entiteter eller lämna dem som de är.
4. Importera slutligen appen om du vill ha en THOMAS app på språket som mål.

## <a name="app-notification"></a>Avisering via App

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Varför visas ett e-postmeddelande som talar om jag är nästan slut kvoten?
Redigera/starter-nyckel är endast tillåtet 1000 endpoint frågar en månad. Skapa en THOMAS prenumeration nyckel (ledigt eller betald) och Använd nyckeln när slutpunkten frågor. Om du gör endpoint frågor från en bot eller ett annat klientprogram som du behöver ändra THOMAS slutpunktsnyckel det. 

## <a name="integrating-luis"></a>Integrera THOMAS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Där skapas min THOMAS appen under Azure web app bot prenumeration?
Om du väljer en THOMAS mall och välj den **Välj** knapp i panelen den vänstra rutan ändras för att inkludera malltypen och ber i vilken region THOMAS mallen ska skapas. Web app bot processen skapar inte en THOMAS prenumeration.

![THOMAS mallen web app bot region](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Vilka regioner THOMAS stöd Bot Framework tal promotor?
[Tal promotor](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) stöds endast för THOMAS appar i den centrala (USA)-instansen. 

## <a name="luis-service"></a>THOMAS service 

### <a name="is-luis-available-on-premise-or-in-private-cloud"></a>Är THOMAS tillgängliga lokalt eller i privata moln?
Nej. 

## <a name="changes-to-the-docs"></a>Ändringar av dokumenten

### <a name="where-did-the-tutorials-go"></a>Var finns självstudierna? 
Artiklar som tidigare fanns i avsnittet självstudiekursen är nu i avsnittet anvisningar i dokumenten. 

|Självstudier|
|--|
|Integrera THOMAS med en bot med [C#](luis-csharp-tutorial-build-bot-framework-sample.md) och [Node.js](luis-nodejs-tutorial-build-bot-framework-sample.md)|
|Lägg till Application Insights Bot med [C#](luis-tutorial-bot-csharp-appinsights.md) och [Node.js](luis-tutorial-function-appinsights.md)|
|Skapa en THOMAS app genom programmering med [Node.js](luis-tutorial-node-import-utterances-csv.md)|
|Använd [sammansatt entitet](luis-tutorial-composite-entity.md) att extrahera grupperade data|
|Lägg till [listan entiteten](luis-tutorial-list-entity.md) för identifiering av ökade entitet med Node.js|
|Prognosens noggrannhet kan förbättras med en [frasen listan](luis-tutorial-interchangeable-phrase-list.md), [mönster](luis-tutorial-pattern.md), och [batch testning](luis-tutorial-batch-testing.md)|
|[Stavningskontroll](luis-tutorial-batch-testing.md) med Bing stavningskontroll kontrollera API v7

### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>Jag fick information om en språk förstå funktion eller demo Build 2018 konferensrum, men jag kommer inte ihåg vad den anropades? 

Följande funktioner har getts ut på Build 2018 konferens:

|Namn|Innehåll|
|--|--|
|Förbättringar|[Reguljärt uttryck](luis-concept-data-extraction.md##regular-expression-entity-data) entiteten och [nyckel frasen](luis-concept-data-extraction.md#key-phrase-extraction-entity-data) entitet
|Mönster|Mönster [konceptet](luis-concept-patterns.md), [kursen](luis-tutorial-pattern.md), [anvisningar](luis-how-to-model-intent-pattern.md)<br>[Patterns.Any](luis-concept-entity-types.md) entitet konceptet inklusive [Explicit lista](luis-concept-patterns.md#explicit-lists) för undantag<br>[Roller](luis-concept-roles.md) begrepp|
|Integreringar|[Textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) integrering av [sentiment analys](publishapp.md#enable-sentiment-analysis)<br>[Tal](https://docs.microsoft.com/azure/cognitive-services/speech) integrering av [tal promotor](publishapp.md#enable-speech-priming) tillsammans med [tal SDK](https://aka.ms/SpeechSDK)|
|Dispatch-verktyget|En del av [BotBuilder verktyg](https://github.com/Microsoft/botbuilder-tools), Dispatch kommandoraden [verktyget](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) att kombinera flera THOMAS och frågor och svar om Maker appar i samma THOMAS app för att bättre avsiktshantering redovisas i en Bot

Ytterligare redigering [API vägar](https://github.com/Microsoft/LUIS-Samples/blob/master/authoring-routes.md) ingår. 

Video: 
* [Azure fredag Build 2018: Kognitiva Services - språk (THOMAS)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Build 2018 AI Show - vad är nytt i språk förstå Service](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [2018 build-Session - Bot intelligence talfunktioner och NLU bästa praxis](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [2018 build - THOMAS uppdateringar](https://channel9.msdn.com/events/Build/2018/THR3118/player)

Projekt: 
* [Contoso Cafe bot](https://github.com/botbuilderbuild2018/build2018demo) demo - källkoden på Github

## <a name="next-steps"></a>Nästa steg

Mer information om THOMAS finns i följande resurser:
* [Stacken spill frågor som är märkta med THOMAS](https://stackoverflow.com/questions/tagged/luis)
* [MSDN-språk förstå Intelligent Services (THOMAS)-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
