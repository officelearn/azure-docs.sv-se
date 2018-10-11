---
title: Vanliga frågor och svar – vanliga frågor och svar - Språkförståelse (LUIS)
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller svar på vanliga frågor och svar om Språkförståelse (LUIS).
author: diberry
manager: cgronlun
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/10/2018
ms.author: diberry
ms.openlocfilehash: b5433ea0a92635b5bf9c2a4887451f1e64ec62e6
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067779"
---
# <a name="language-understanding-faq"></a>Language Understanding vanliga frågor och svar

Den här artikeln innehåller svar på vanliga frågor och svar om Språkförståelse (LUIS).

## <a name="luis-authoring"></a>LUIS redigering

### <a name="what-are-the-luis-best-practices"></a>Vilka är de bästa metoderna för LUIS?
Börja med den [redigering cykel](luis-concept-app-iteration.md), läs sedan de [bästa praxis](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Vad är det bästa sättet att börja skapa min app på LUIS?

Det bästa sättet att bygga din app är via en [inkrementella processen](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Vad är en bra idé att modellera avsikter i min app? Bör jag skapa mer specifika eller mer allmänt avsikter?

Välj avsikter som inte är allmän så att de är överlappande, men inte så specifik som den gör det svårt för LUIS för att skilja mellan liknande avsikter. Skapa discriminative specifika avsikter är en av de bästa metoderna för LUIS modellering.

### <a name="is-it-important-to-train-the-none-intent"></a>Är det viktigt att träna avsikt ingen?

Ja, det är bra att träna din **ingen** avsikt med mer yttranden när du lägger till fler etiketter till andra avsikter. Ett bra förhållande är 1 eller 2 etiketter som lagts till i **ingen** för varje 10 etiketter som lagts till i en avsikt. Det här förhållandet ökar discriminative kraften hos LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Hur kan jag för att rätta stavfel i yttranden?

Se den [Bing stavningskontroll kontrollera API V7](luis-tutorial-bing-spellcheck.md) självstudien. LUIS tillämpar begränsningar har införts av Bing stavningskontroll kontrollera API V7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Hur gör jag för att redigera min LUIS-app via programmering?
Redigera LUIS-app via programmering genom att använda den [redigering API](https://aka.ms/luis-authoring-apis). Se [anropa LUIS redigering API](./luis-quickstart-node-add-utterance.md) och [skapa en LUIS-app via programmering med hjälp av Node.js](./luis-tutorial-node-import-utterances-csv.md) exempel på hur du anropar API: et redigering. Redigera-API kräver att du använder en [redigering nyckeln](luis-concept-keys.md#authoring-key) i stället för en slutpunktsnyckel. Programmässig redigering kan upp till 1 000 000 anrop per månad och fem transaktioner per sekund. Mer information på de nycklar som du använder med LUIS finns i [hantera nycklar](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Där matchar mönstret-funktion som tillhandahålls reguljärt uttryck?
Den tidigare **mönsterfunktion** för närvarande är inaktuell, ersatts av  **[mönster](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Hur kan jag använda en entitet för att hämta rätt data?
Se [entiteter](luis-concept-entity-types.md) och [dataextrahering](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Varianter av en exempel-uttryck ska inkludera skiljetecken?
Lägg till olika värden som exempel yttranden med intentionen eller Lägg till mönstret för exempel-uttryck med den [syntaxen för att ignorera](luis-concept-patterns.md#pattern-syntax) skiljetecken.

### <a name="does-luis-currently-support-cortana"></a>Stöder LUIS för närvarande Cortana?

Cortana färdiga appar tagits bort 2017. De stöds inte längre.

## <a name="luis-endpoint"></a>LUIS-slutpunkt

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Varför LUIS lägga till utrymmen i frågan runt eller i mitten ord?
LUIS [tokenizes](luis-glossary.md#token) i uttryck baserat på den [kultur](luis-language-support.md#tokenization). Både det ursprungliga värdet och principfilerna värdet är tillgängliga för [dataextrahering](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Hur jag för att skapa och tilldela en LUIS slutpunktsnyckeln?
[Skapa slutpunktsnyckeln](luis-how-to-azure-subscription.md#create-luis-endpoint-key) i Azure för dina [service](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) nivå. [Tilldela nyckeln](luis-how-to-manage-keys.md#assign-endpoint-key) på den **[nycklar och slutpunkter](luis-how-to-manage-keys.md)** sidan. Det finns inga motsvarande API: et för den här åtgärden. Du måste ändra HTTP-begäran till slutpunkten till [använder den nya slutpunktsnyckeln](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-interpret-luis-scores"></a>Hur tolkar LUIS poäng?
Systemet bör använda högsta bedömnings avsikten oavsett dess värde. Till exempel en poäng under 0,5 (mindre än 50%) inte nödvändigtvis att LUIS har låg förtroende. Tillhandahåller mer utbildningsdata för kan du öka poängen för den mest sannolika avsikten.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Varför ser jag min endpoint träffar i instrumentpanelen för min app?
Totala endpoint träffar i din app instrumentpanelen uppdateras regelbundet, men de mått som är associerade med din LUIS slutpunktsnyckeln i Azure-portalen uppdateras oftare.

Om du inte ser uppdaterade endpoint träffar på instrumentpanelen, logga in på Azure-portalen och hitta den resurs som är associerade med din LUIS slutpunktsnyckeln och öppna **mått** att välja den **totala anrop** mått. Om slutpunktsnyckeln används för mer än en LUIS-app, visar det sammanlagda antalet anrop från alla LUIS-appar som använder den mått i Azure-portalen.

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Min LUIS-app fungerar igår men idag jag får 403-fel. Appen ändras inte. Hur jag för att åtgärda det?
Efter den [instruktioner](#how-do-i-create-and-assign-a-luis-endpoint-key) i nästa vanliga frågor om att skapa en LUIS-slutpunktsnyckeln och tilldela den till appen. Du måste ändra HTTP-begäran till slutpunkten till [använder den nya slutpunktsnyckeln](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-secure-my-luis-endpoint"></a>Hur skyddar jag min LUIS-slutpunkt
Se [skydda slutpunkten](luis-concept-security.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Arbeta inom gränserna för LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Vad är det maximala antalet intentioner och entiteter som har stöd för en LUIS-app?
Se den [gränser](luis-boundaries.md) referens.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Jag vill skapa en LUIS-app med mer än det maximala antalet avsikter. Vad ska jag göra?

Se [bästa praxis för avsikter](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Jag vill skapa en app i LUIS med mer än det maximala antalet enheter. Vad ska jag göra?

Se [bästa praxis för entiteter](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Vilka gränser på antalet och storleken på frasen har visas?
För den maximala längden på en [frasen lista](./luis-concept-feature.md), finns i den [gränser](luis-boundaries.md) referens.

### <a name="what-are-the-limits-on-example-utterances"></a>Vad är begränsningar i exempel yttranden?
Se den [gränser](luis-boundaries.md) referens.

## <a name="testing-and-training"></a>Testning och utbildning

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Jag ser några fel i batchen testning fönstret för några av modeller i min app. Hur kan jag för att lösa det här problemet?

Felen indikerar att det finns en diskrepans mellan etiketterna och förutsägelser från dina modeller. Gör något av följande åtgärder för att åtgärda problemet:
* För att förbättra diskriminering mellan avsikter THOMAS, lägger du till fler etiketter.
* För att LUIS Lär dig snabbare, lägger du till frasen-funktioner som introducerar domänspecifika ordförråd.

Se den [Batch testning](luis-tutorial-batch-testing.md) självstudien.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>När en app har exporterat sedan återimporteras till en ny app (med en ny app-ID), är LUIS förutsägelse poängen olika. Varför inträffar det?

Se [förutsägelse skillnaderna mellan kopior av samma app](luis-concept-prediction-score.md#differences-with-predictions).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Vissa yttranden går du till fel avsikten när jag har gjort ändringar i min app. Det verkar som om problemet försvinna slumpmässigt. Hur jag för att åtgärda det? 

Se [träna med alla data](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Publicering av appar

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Vad är klient-ID i fönstret ”Lägg till en nyckel till din app”?
I Azure representerar en klient klient eller organisation som är associerad med en tjänst. Hitta ditt klient-ID i Azure-portalen i den **katalog-ID** box genom att välja **Azure Active Directory** > **hantera**  >  **Egenskaper**.

![Klient-ID i Azure portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Varför finns det fler endpoint-nycklar som tilldelats min app än jag har tilldelat?
Varje LUIS-app har redigering/starter-nyckel i listan över slutpunkten bekvämlighets skull. Den här nyckeln kan bara ett par endpoint träffar så att du kan testa LUIS.  

Om din app fanns innan LUIS var allmänt tillgänglig (GA), tilldelas LUIS endpoint nycklar i din prenumeration automatiskt. Detta gjordes för att underlätta GA-migrering. Alla nya LUIS endpoint nycklar i Azure-portalen är _inte_ automatiskt tilldela det till LUIS.

## <a name="app-management"></a>Apphantering

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Hur jag för att överföra ägarskapet för en LUIS-app?
Exportera en LUIS-app för att överföra en LUIS-app till Azure-prenumeration, och importera det med ett nytt konto. Uppdatera LUIS-app-ID i klientprogram som anropar den. Den nya appen kan returnera något annorlunda LUIS poäng från den ursprungliga appen.

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Hur jag för att hämta en logg över användare yttranden?
Som standard loggar LUIS-appen yttranden från användare. Om du vill hämta en logg över yttranden som användarna skickar till LUIS-appen, går du till **Mina appar**, och välj appen. I verktygsfältet sammanhangsberoende väljer **exportera Endpoint loggar**. Loggen formateras som en fil med kommaavgränsade värden (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Hur kan jag inaktivera loggning av yttranden?
Du kan stänga av loggning av användaren yttranden genom att ange `log=false` i slutpunkts-URL som ditt klientprogram använder för att fråga LUIS. Men om du inaktiverar loggning inaktiverar LUIS-appens möjlighet att föreslå yttranden eller förbättra prestanda som baseras på [aktiv inlärning](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Om du ställer in `log=false` på grund av data sekretessen, som du kan inte hämta en post med dessa användare yttranden från LUIS eller använder dessa uttryck för att förbättra din app.

Loggning är den enda lagringen av yttranden.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Varför vill inte alla mina endpoint yttranden loggas?
Om du använder din logg för förutsägande analys kan du inte skapa test yttranden i loggen.

## <a name="data-management"></a>Datahantering

### <a name="can-i-delete-data-from-luis"></a>Kan jag ta bort data från LUIS?

* Du kan alltid ta bort exempel yttranden som används för att träna LUIS. Om du tar bort en exempel-uttryck från LUIS-appen tas bort från LUIS-webbtjänsten och är inte tillgänglig för export.
* Du kan ta bort yttranden från listan över användare yttranden som LUIS föreslår i den **granska endpoint yttranden** sidan. Tar bort yttranden i den här listan förhindrar du att de ska visas som förslag, men ta bort inte dem från loggar.
* Om du tar bort ett konto raderas alla appar, och deras exempel yttranden och loggar. Dessa data är kvar på servrarna i 60 dagar innan de tas bort permanent.

### <a name="does-microsoft-access-my-luis-app-data-for-its-own-purposes-for-example-to-enhance-luis-or-microsoft-in-general"></a>Har Microsoft tillgång till min LUIS AppData för sina egna ändamål, till exempel för att förbättra LUIS eller Microsoft i allmänhet?

Nej. LUIS-app-datamodellen är inte används av LUIS för att förbättra LUIS som en plattform eller används av Microsoft på något sätt. Varje AppData är separat och ägs endast av användar- och medarbetare.

Läs mer om [Användarsekretess](luis-user-privacy.md), [ytterligare säkerhet följs](luis-concept-security.md#security-compliance), och [datalagring](luis-concept-data-storage.md).

## <a name="language-and-translation-support"></a>Stöd för språk och översättning

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Jag har en app på ett språk och vill skapa en parallell app i ett annat språk. Vad är det enklaste sättet att göra detta?
1. Exportera din app.
2. Omvandla de märkta yttranden i JSON-filen för den exporterade appen till målspråket.
3. Du kan behöva ändra namnen på avsikter och entiteter eller lämna dem som de är.
4. Slutligen importera appen så att den har en LUIS-app på språket som mål.

## <a name="app-notification"></a>Appmeddelande

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Varför visas ett e-postmeddelande om jag är nästan slut på kvot för?
Din nyckel för redigering/starter får bara 1000 endpoint frågar en månad. Skapa en LUIS-slutpunktsnyckeln (kostnadsfritt eller betalt) och nyckeln när du skapar frågor för slutpunkten. Om du gör endpoint frågor från en bot eller en annan klientprogram, måste du ändra det slutpunktsnyckeln LUIS.

## <a name="integrating-luis"></a>Integrera LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Där är min LUIS-app skapas under Azure web app bot-prenumeration?
Om du väljer en LUIS-mall och välj den **Välj** knappen i panelen fönstret vänster ändras för att inkludera malltypen och frågar i vilken region för att skapa LUIS-mall. Web app bot processen skapa inte dock en LUIS-prenumeration.

![Bot webbappregion för LUIS-mall](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Vilka LUIS-regioner har stöd för Bot Framework tal promotor?
[Tal promotor](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) stöds bara för LUIS-appar i den centrala (USA)-instansen.

## <a name="luis-service"></a>LUIS-tjänsten

### <a name="is-luis-available-on-premises-or-in-private-cloud"></a>Är LUIS tillgängligt lokalt eller i privata moln?
Nej.


### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>Jag har hört talas om en funktion för Language Understanding eller demo men jag inte kommer ihåg vad den anropades på Build-konferensen för 2018?

Följande funktioner har släppts på Build 2018-konferensen:

|Namn|Innehåll|
|--|--|
|Förbättringar|[Reguljärt uttryck](luis-concept-data-extraction.md##regular-expression-entity-data) entitet och [nyckel frasen](luis-concept-data-extraction.md#key-phrase-extraction-entity-data) entitet
|Mönster|Mönster [konceptet](luis-concept-patterns.md), [självstudien](luis-tutorial-pattern.md), [how-to](luis-how-to-model-intent-pattern.md)<br>[Patterns.Any](luis-concept-entity-types.md) entitet konceptet inklusive [Explicit lista](luis-concept-patterns.md#explicit-lists) för undantag<br>[Roller](luis-concept-roles.md) begrepp|
|Integreringar|[Textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) integrering av [attitydanalys](luis-how-to-publish-app.md#enable-sentiment-analysis)<br>[Tal](https://docs.microsoft.com/azure/cognitive-services/speech) integrering av tal promotor tillsammans med [tal SDK](https://aka.ms/SpeechSDK)|
|Dispatch-verktyget|En del av [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools), Dispatch kommandoraden [verktyget](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) att kombinera flera LUIS och QnA Maker appar till enkel LUIS-app för bättre taligenkänning i en robot

Ytterligare redigering [API-vägar](https://github.com/Microsoft/LUIS-Samples/blob/master/authoring-routes.md) ingår.

Videor:
* [Azure Friday på Build 2018: Cognitive Services – Language (LUIS)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Build 2018 AI Show - Nyheter i tjänst för Språkförståelse](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [Session på Build 2018 – Robotintelligens, talfunktioner och regelverk för NLU](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [Build 2018 - LUIS-uppdateringar](https://channel9.msdn.com/events/Build/2018/THR3118/player)

Projekt:
* [Contoso Cafe bot](https://github.com/botbuilderbuild2018/build2018demo) demo - källkod på Github

## <a name="next-steps"></a>Nästa steg

Mer information om LUIS finns i följande resurser:
* [Stack Overflow frågor som taggats med LUIS](https://stackoverflow.com/questions/tagged/luis)
* [MSDN Språkförståelse Intelligent Services (LUIS)-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
