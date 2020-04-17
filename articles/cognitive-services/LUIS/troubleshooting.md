---
title: Vanliga frågor och svar (VANLIGA FRÅGOR) - LUIS
description: Den här artikeln innehåller svar på vanliga frågor om språk understanding (LUIS).
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: 59d9b1f21e1936b7d03293ec3d338677380a7c99
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530274"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Vanliga frågor och svar om språk understanding

Den här artikeln innehåller svar på vanliga frågor om språk understanding (LUIS).

## <a name="whats-new"></a>Nyheter

[Läs mer](whats-new.md) om vad som är nytt i Språk understanding (LUIS).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Redigering

### <a name="what-are-the-luis-best-practices"></a>Vilka är LUIS bästa metoder?
Börja med [redigeringscykeln](luis-concept-app-iteration.md)och läs sedan de [bästa metoderna](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Vilket är det bästa sättet att börja bygga min app i LUIS?

Det bästa sättet att bygga din app är genom en [inkrementell process](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Vad är en bra idé för att modellera avsikterna med min app? Ska jag skapa mer specifika eller mer generiska avsikter?

Välj avsikter som inte är så allmänna att de är överlappande, men inte så specifika att det gör det svårt för LUIS att skilja mellan liknande avsikter. Att skapa diskriminerande specifika avsikter är en av de bästa metoderna för LUIS-modellering.

### <a name="is-it-important-to-train-the-none-intent"></a>Är det viktigt att träna ingen avsikt?

Ja, det är bra att träna din **Ingen** avsikt med fler yttranden som du lägger till fler etiketter till andra avsikter. Ett bra förhållande är 1 eller 2 etiketter som läggs till **i Ingen** för varje 10 etiketter som läggs till i en avsikt. Detta förhållande ökar den diskriminerande kraften i LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Hur korrigerar jag stavfel i yttranden?

Se [bing stavningskontroll API V7](luis-tutorial-bing-spellcheck.md) handledning. LUIS tillämpar gränser som införts av Bing Spell Check API V7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Hur redigerar jag min LUIS-app programmässigt?
Om du vill redigera LUIS-appen programmässigt använder du [redigerings-API:et](https://go.microsoft.com/fwlink/?linkid=2092087). Se [Anropa LUIS-redigerings-API](./get-started-get-model-rest-apis.md) och [Skapa en LUIS-app programmässigt med Node.js](./luis-tutorial-node-import-utterances-csv.md) för exempel på hur du anropar redigerings-API:et. Redigerings-API:et kräver att du använder en [redigeringsnyckel](luis-concept-keys.md#azure-resources-for-luis) i stället för en slutpunktsnyckel. Programmatisk redigering tillåter upp till 1.000.000 samtal per månad och fem transaktioner per sekund. Mer information om de nycklar du använder med LUIS finns i [Hantera nycklar](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Var är funktionen Mönster som gav reguljära uttryck matchning?
Den tidigare **mönsterfunktionen** är för närvarande föråldrad, ersatt av **[Mönster](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Hur använder jag en entitet för att dra ut rätt data?
Se [entiteter](luis-concept-entity-types.md) och [dataextrahering](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Ska varianter av ett exempel yttrande innehålla interpunktion?
Använd någon av följande lösningar:
* Ignorera [interpunktion](luis-reference-application-settings.md#punctuation-normalization)
* Lägg till de olika variationerna som exempelyttranden i avsikten
* Lägg till mönstret för exempelutseendet med [syntaxen om du vill ignorera](luis-concept-patterns.md#pattern-syntax) interpunktionen.

### <a name="does-luis-currently-support-cortana"></a>Har LUIS för närvarande stöd cortana?

Cortana fördefinierade appar var inaktuella 2017. De stöds inte längre.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Hur överför jag äganderätten till en LUIS-app?
Om du vill överföra en LUIS-app till en annan Azure-prenumeration exporterar du LUIS-appen och importerar den med ett nytt konto. Uppdatera LUIS-app-ID:t i klientprogrammet som anropar det. Den nya appen kan returnera något annorlunda LUIS-poäng från den ursprungliga appen.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>En fördefinierad entitet taggas i ett exempelyttrande i stället för min anpassade entitet. Hur åtgärdar jag detta?

I LUIS-portalen kan du märka text för den exakta entiteten som du är intresserad av att extrahera. Om LUIS-portalen inte visar rätt entitetsförutsägels kan du behöva lägga till fler yttranden och märka entiteten i texten eller lägga till en deskriptor (till exempel en funktion).

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Jag försökte importera en app eller version fil men jag fick ett felmeddelande, vad hände?

Läs mer om [versionsimportfel](luis-how-to-manage-versions.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Samarbeta och bidra

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Hur ger jag medarbetare åtkomst till LUIS med Azure Active Directory (Azure AD) eller rollbaserad åtkomstkontroll (RBAC)?

Se [Azure Active Directory-resurser](luis-how-to-collaborate.md#azure-active-directory-resources) och [Azure Active Directory-klientanvändare](luis-how-to-collaborate.md#azure-active-directory-tenant-user) för att lära dig hur du ger medarbetare åtkomst.

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Slutpunkt

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Jag fick en STATUSkod för HTTP 403-fel. Vad kan jag göra?

Du får 403 och 429 felstatuskoder när du överskrider transaktionerna per sekund eller transaktioner per månad för din prisnivå. Öka prisnivån eller använd Språk [understanding-behållare](luis-container-howto.md).

När du använder alla dessa kostnadsfria 1000 slutpunktsfrågor eller överskrider prisnivåns månatliga transaktionskvot visas en STATUSKOD FÖR HTTP 403-fel.

Om du vill åtgärda det här felet måste du antingen [ändra prisnivån](luis-how-to-azure-subscription.md#change-pricing-tier) till en högre nivå eller [skapa en ny resurs](get-started-portal-deploy-app.md#create-the-endpoint-resource) och tilldela den till din [app](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Lösningar för det här felet är:

* I [Azure-portalen](https://portal.azure.com)ändrar du din prisnivå till en högre TPS-nivå på din språkförståelseresurs på **resurshanteringsnivån -> prisnivå.** Du behöver inte göra något i språköverenskommelsen om din resurs redan har tilldelats din språk understanding-app.
*  Om din användning överskrider den högsta prisnivån lägger du till fler språkförståelseresurser med en belastningsutjämnare framför dem. [Behållaren För språk understanding](luis-container-howto.md) med Kubernetes eller Docker Compose kan du hjälpa till med detta.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Jag fick en STATUSkod för HTTP 429-fel. Vad kan jag göra?

Du får 403 och 429 felstatuskoder när du överskrider transaktionerna per sekund eller transaktioner per månad för din prisnivå. Öka prisnivån eller använd Språk [understanding-behållare](luis-container-howto.md).

Den här statuskoden returneras när dina transaktioner per sekund överskrider din prisnivå.

Bland lösningarna finns:

* Du kan [öka din prisnivå](luis-how-to-azure-subscription.md#change-pricing-tier)om du inte är på den högsta nivån.
* Om din användning överskrider den högsta prisnivån lägger du till fler språkförståelseresurser med en belastningsutjämnare framför dem. [Behållaren För språk understanding](luis-container-howto.md) med Kubernetes eller Docker Compose kan du hjälpa till med detta.
* Du kan utfärda begäranden om klientprogram med en [princip för återförsök](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) som du implementerar själv när du får den här statuskoden.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Min slutpunktsfråga gav oväntade resultat. Vad ska jag göra?

Oväntade frågeprognosresultat baseras på tillståndet för den publicerade modellen. Om du vill korrigera modellen kan du behöva ändra modell, träna och publicera igen.

Korrigera modellen börjar med [aktivt lärande](luis-how-to-review-endpoint-utterances.md).

Du kan ta bort icke-deterministisk utbildning genom att uppdatera [API:et för programversionsinställningar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) för att kunna använda alla träningsdata.

Läs de [bästa metoderna](luis-concept-best-practices.md) för andra tips.

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Varför lägger LUIS till blanksteg i frågan runt eller mitt i orden?
LUIS [tokenizes](luis-glossary.md#token) yttrandet baserat på [kulturen](luis-language-support.md#tokenization). Både det ursprungliga värdet och det tokeniserade värdet är tillgängliga för [dataextrahering](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Hur skapar och tilldelar jag en LUIS-slutpunktsnyckel?
[Skapa slutpunktsnyckeln](luis-how-to-azure-subscription.md) i Azure för din [tjänstnivå.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) [Tilldela nyckeln](luis-how-to-azure-subscription.md) på sidan **[Azure Resources.](luis-how-to-azure-subscription.md)** Det finns inget motsvarande API för den här åtgärden. Sedan måste du ändra HTTP-begäran till slutpunkten för att [kunna använda den nya slutpunktsnyckeln](luis-concept-keys.md).

### <a name="how-do-i-interpret-luis-scores"></a>Hur tolkar jag LUIS-poäng?
Systemet bör använda den högsta poängavsikten oavsett dess värde. Till exempel en poäng under 0,5 (mindre än 50%) betyder inte nödvändigtvis att LUIS har lågt förtroende. Att tillhandahålla mer träningsdata kan bidra till att öka [poängen](luis-concept-prediction-score.md) för den mest sannolika avsikten.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Varför ser jag inte mina slutpunktsträffar i appens instrumentpanel?
Den totala slutpunktsträffarna i appens instrumentpanel uppdateras med jämna mellanrum, men måtten som är associerade med LUIS-slutpunktsnyckeln i Azure-portalen uppdateras oftare.

Om du inte ser uppdaterade slutpunktsträffar i instrumentpanelen loggar du in på Azure-portalen och hittar resursen som är associerad med LUIS-slutpunktsnyckeln och öppnar **mått** för att välja måttet **Totalt antal samtal.** Om slutpunktsnyckeln används för mer än en LUIS-app visar måttet i Azure-portalen det sammanlagda antalet samtal från alla LUIS-appar som använder den.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Finns det ett PowerShell-kommando som kommer till slutpunktskvoten?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan använda ett PowerShell-kommando för att se slutpunktskvoten:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Min LUIS app fungerade igår men idag får jag 403 fel. Jag har inte ändrat appen. Vad kan jag göra?
Följ dessa [instruktioner](#how-do-i-create-and-assign-a-luis-endpoint-key) för att skapa en LUIS-slutpunktsnyckel och tilldela den till appen. Sedan måste du ändra klientprogrammets HTTP-begäran till slutpunkten för att [kunna använda den nya slutpunktsnyckeln](luis-concept-keys.md). Om du har skapat en ny resurs i en annan region ändrar du även HTTP-klientbegärans region.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Hur säkrar jag min LUIS-slutpunkt?
Se [Skydda slutpunkten](luis-concept-keys.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Arbeta inom LUIS gränser

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Vad är det maximala antalet avsikter och entiteter som en LUIS-app kan stödja?
Se [gränsreferensen.](luis-boundaries.md)

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Jag vill bygga en LUIS-app med mer än det maximala antalet avsikter. Vad ska jag göra?

Se [Metodtips för avsikter](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Jag vill skapa en app i LUIS med mer än det maximala antalet entiteter. Vad ska jag göra?

Se [Metodtips för entiteter](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Vilka är gränserna för antalet och storleken på fraslistor?
För den maximala längden på en [fraslista](./luis-concept-feature.md)finns i [gränsreferensen.](luis-boundaries.md)

### <a name="what-are-the-limits-on-example-utterances"></a>Vilka är gränserna för exempelyttranden?
Se [gränsreferensen.](luis-boundaries.md)

## <a name="testing-and-training"></a>Testning och utbildning

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Jag ser några fel i batchtestfönstret för några av modellerna i min app. Hur kan jag lösa detta problem?

Felen tyder på att det finns en viss diskrepans mellan etiketterna och förutsägelserna från dina modeller. Gör en eller båda av följande uppgifter för att lösa problemet:
* Lägg till fler etiketter för att hjälpa LUIS att förbättra diskrimineringen mellan avsikter.
* Om du vill hjälpa LUIS att lära sig snabbare lägger du till fraslistefunktioner som introducerar domänspecifikt ordförråd.

Se [självstudien för batchtestning.](luis-tutorial-batch-testing.md)

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>När en app exporteras importeras sedan till en ny app (med ett nytt app-ID) är LUIS-förutsägelsepoängen olika. Varför sker det här?

Se [Förutsägelseskillnader mellan kopior av samma app](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Vissa yttranden går till fel avsikt när jag har gjort ändringar i min app. Problemet verkar försvinna slumpmässigt. Vad kan jag göra?

Se [Träna med alla data](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Apppublicering

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Vad är klient-ID i fönstret "Lägg till en nyckel i din app"?
I Azure representerar en klient klient eller organisation som är associerad med en tjänst. Hitta ditt klient-ID i Azure-portalen i rutan **Katalog-ID** genom att välja **Azure Active Directory** > **Manage** > **Properties**.

![Klient-ID i Azure-portalen](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Varför tilldelas fler slutpunktsnycklar till min app än vad jag har tilldelat?
Varje LUIS-app har redigerings-/startnyckeln i slutpunktslistan som en bekvämlighet. Den här nyckeln tillåter bara ett fåtal slutpunktsträffar så att du kan prova LUIS.

Om din app fanns innan LUIS var allmänt tillgänglig (GA) tilldelas LUIS-slutpunktsnycklar i din prenumeration automatiskt. Detta gjordes för att underlätta GA-migrering. Alla nya LUIS-slutpunktsnycklar i Azure-portalen tilldelas _inte_ automatiskt till LUIS.

## <a name="key-management"></a>Nyckelhantering

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Hur vet jag vilken nyckel jag behöver, var jag får det och vad jag gör med den?

Se [Slutpunktsnycklar för redigering och frågeprediktion i LUIS](luis-concept-keys.md) om du vill veta mer om skillnaderna mellan redigeringsnyckeln och körningsnyckeln för förutsägelse.

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Jag fick ett felmeddelande om att vara utan kvot. Vad kan jag göra?

Mer information finns i Korrigering av HTTP-statuskod [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) och [429.](#i-received-an-http-429-error-status-code-how-do-i-fix-it)

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Jag måste hantera fler slutpunktsfrågor. Hur gör jag det?

Mer information finns i Korrigering av HTTP-statuskod [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) och [429.](#i-received-an-http-429-error-status-code-how-do-i-fix-it)

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Jag har skapat en redigeringsnyckel men den visas inte i LUIS-portalen. Vad hände?

Redigeringsnycklar är tillgängliga i LUIS-portalen efter [att ha migrerat till redigeringsnyckelupplevelsen](luis-migration-authoring.md).

## <a name="app-management"></a>Apphantering

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Hur laddar jag ned en logg över användaryttranden?
Som standard loggar LUIS-appen yttranden från användare. Om du vill hämta en logg med yttranden som användarna skickar till luis-appen går du till **Mina appar**och väljer appen. Välj **Exportera slutpunktsloggar**i det kontextuella verktygsfältet . Loggen är formaterad som en CSV-fil (kommaavgränsad värde).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Hur inaktiverar jag loggning av yttranden?
Du kan inaktivera loggning av användaryttranden genom att ange `log=false` i slutpunkts-URL:en som klientprogrammet använder för att fråga LUIS. Om du inaktiverar loggning inaktiveras dock LUIS-appens möjlighet att föreslå yttranden eller förbättra prestanda som baseras på [aktiv inlärning](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Om du `log=false` anger på grund av datasekretessproblem kan du inte hämta en post för dessa användaryttranden från LUIS eller använda dessa yttranden för att förbättra din app.

Loggning är den enda lagringen av yttranden.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Varför vill jag inte att alla mina slutpunktsyttranden loggas?
Om du använder loggen för förutsägelseanalys ska du inte fånga upp testyttranden i loggen.

## <a name="data-management"></a>Datahantering

### <a name="can-i-delete-data-from-luis"></a>Kan jag ta bort data från LUIS?

* Du kan alltid ta bort exempelyttranden som används för att träna LUIS. Om du tar bort ett exempelutseende från LUIS-appen tas det bort från LUIS-webbtjänsten och är inte tillgänglig för export.
* Du kan ta bort yttranden från listan över användaryttranden som LUIS föreslår på sidan **Granska slutpunktsyttranden.** Om du tar bort yttranden från den här listan kan de föreslås, men de tas inte bort från loggar.
* Om du tar bort ett konto tas alla appar bort tillsammans med deras exempelyttranden och loggar. Data lagras på servrarna i 60 dagar innan de tas bort permanent.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Hur hanterar Microsoft data som jag skickar till LUIS?

[Säkerhetscenter](https://www.microsoft.com/trustcenter) förklarar våra åtaganden och dina alternativ för datahantering och åtkomst i Azure Services.

## <a name="language-and-translation-support"></a>Språk- och översättningsstöd

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Jag har en app på ett språk och vill skapa en parallell app på ett annat språk. Vad är det enklaste sättet att göra det?
1. Exportera din app.
2. Översätt de märkta yttrandena i JSON-filen för den exporterade appen till målspråket.
3. Du kan behöva ändra namnen på avsikter och entiteter eller lämna dem som de är.
4. Importera slutligen appen för att ha en LUIS-app på målspråket.

## <a name="app-notification"></a>App meddelande

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Varför fick jag ett e-postmeddelande som säger att jag nästan inte har någon kvot?
Din redigerings-/startnyckel tillåts endast 1000 slutpunktsfrågor per månad. Skapa en LUIS-slutpunktsnyckel (ledig eller betald) och använd den nyckeln när du skapar slutpunktsfrågor. Om du gör slutpunktsfrågor från en bot eller ett annat klientprogram måste du ändra LUIS-slutpunktsnyckeln där.

## <a name="bots"></a>Bots

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Min LUIS bot fungerar inte. Vad gör jag nu?

Det första problemet är att isolera om problemet är relaterat till LUIS eller händer utanför LUIS middleware.

#### <a name="resolve-issue-in-luis"></a>Lösa problem i LUIS
Skicka samma uttryck till LUIS från [LUIS-slutpunkten](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Om du får ett felmeddelande löser du problemet i LUIS tills felet inte längre returneras. Vanliga fel är:

* `Out of call volume quota. Quota will be replenished in <time>.`- Det här problemet anger att du antingen måste ändra från en redigeringsnyckel till en [slutpunktsnyckel](luis-how-to-azure-subscription.md) eller om du behöver ändra [tjänstnivåer](luis-how-to-azure-subscription.md#change-pricing-tier).

#### <a name="resolve-issue-in-azure-bot-service"></a>Lösa problem i Azure Bot Service

Om du använder Azure Bot Service och problemet är att `Sorry, my bot code is having an issue`test i **webbchatt** returnerar kontrollerar du dina loggar:

1. Välj **Skapa**för din robot i avsnittet **Bot-hantering** i Azure-portalen för din robot.
1. Öppna kodredigeraren online.
1. Markera botnamnet (det andra objektet till höger) högst upp, i det blå navigeringsfältet).
1. I den senaste listrutan väljer du **Öppna Kudu Console**.
1. Välj **Loggfiler**och välj sedan **Program**. Granska alla loggfiler. Om felet inte visas i programmappen granskar du alla loggfiler under **Loggfiler**.
1. Kom ihåg att återskapa projektet om du använder ett kompilerat språk som C#.

> [!Tip]
> Konsolen kan också installera paket.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Lös problemet medan du felsöker på den lokala datorn med Bot Framework.

Mer information om lokal felsökning av en bot finns i [Felsöka en bot](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0).

## <a name="integrating-luis"></a>Integrera LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Var skapas min LUIS-app under azure web app bot-prenumerationsprocessen?
Om du väljer en LUIS-mall och väljer knappen **Välj** i mallfönstret ändras det vänstra fönstret så att malltypen inkluderas och i vilket område som ska skapa LUIS-mallen. Webbapp bot processen skapar inte en LUIS-prenumeration ändå.

![LUIS-mallwebbapproregion](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Vilka LUIS-regioner stöder Bot Framework-talpriming?
[Talpriming](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) stöds endast för LUIS-appar i den centrala (USA) instansen.

## <a name="api-programming-strategies"></a>API-programmeringsstrategier

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Hur får jag programmässigt LUIS-regionen för en resurs?

Använd LUIS-exemplet för att [söka efter region](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) programmässigt med C# eller Node.Js.

## <a name="luis-service"></a>LUIS-tjänst

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Är SPRÅKFÖRSTÅELSE (LUIS) tillgängligt lokalt eller privat moln?

Ja, du kan använda [LUIS-behållaren](luis-container-howto.md) för dessa scenarier om du har den nödvändiga anslutningen till mätaranvändningen.

## <a name="migrating-to-the-next-version"></a>Migrera till nästa version

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Hur migrerar jag till förhandsversionen av V3 API?

Se [API v2 till v3 Migreringsguide för LUIS-appar](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Bygg 2019 Konferensmeddelanden

Följande funktioner släpptes på Build 2019 Conference:

* [Förhandsgranskning av V3 API-migreringsguide](luis-migration-api-v3.md)
* [Förbättrad instrumentpanel för analys](luis-how-to-use-dashboard.md)
* [Förbättrade fördefinierade domäner](luis-reference-prebuilt-domains.md)
* [Dynamiska listentiteter](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externa enheter](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Videor:

* [Så här använder du Azure Conversational AI för att skala upp ditt företag för nästa generation](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Nästa steg

Mer information om LUIS finns i följande resurser:
* [Stack-spillfrågor taggade med LUIS](https://stackoverflow.com/questions/tagged/luis)
* [MSDN Språk understanding Intelligent Services (LUIS) Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
