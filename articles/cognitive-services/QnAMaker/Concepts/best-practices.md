---
title: Metodtips – QnA Maker
description: Använda dessa bästa metoder för att förbättra din kunskapsbas och ge bättre resultat för din robot program/chatt slutanvändare.
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: fb935aeed7b492a3a0c213d6d7166bd5d80144c1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389299"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Bästa praxis för en kunskapsbas med QnA Maker

[Livs cykeln för kunskaps bas utveckling](../Concepts/development-lifecycle-knowledge-base.md) hjälper dig att hantera din KB från början till slut. Använd dessa metod tips för att förbättra din kunskaps bas och ge bättre resultat till klient programmet eller chattens robots slutanvändare.

## <a name="extraction"></a>Extrahering

QnA Maker-tjänsten är ständigt förbättrad algoritmer som extraherar kunskapsbaser från innehåll och expandera stöds fil- och HTML-format. Följ [rikt linjerna](../Concepts/content-types.md) för data extrahering baserat på din dokument typ.

Vanliga frågor och svar sidor ska i allmänhet vara fristående och inte kombinerade med annan information. Produkthandböcker bör ha tydliga rubriker och helst en indexsida.

### <a name="configuring-multi-turn"></a>Konfigurera multi-turn

[Skapa din kunskaps bas med att](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) extrahering av flera varv har Aktiver ATS. Om din kunskaps bas stöder eller ska ha stöd för fråge hierarkier, kan den här hierarkin extraheras från dokumentet eller skapas när dokumentet har extraherats.

## <a name="creating-good-questions-and-answers"></a>Skapa lämpliga frågor och svar

### <a name="good-questions"></a>Lämpliga frågor

De bästa frågorna är enkla. Överväg att använda nyckel ordet eller frasen för varje fråga och skapa sedan en enkel fråga för nyckel ordet eller frasen.

Lägg till så många alternativa frågor som du behöver men behåll ändringarna enkla. Att lägga till fler ord eller ordföljder som inte är en del av frågans huvudsakliga mål hjälper inte QnA Maker hitta en matchning.


### <a name="add-relevant-alternative-questions"></a>Lägg till relevanta alternativa frågor

Användaren kan ange frågor med antingen ett samtals format, `How do I add a toner cartridge to my printer?` eller en nyckelords sökning som `toner cartridge`. Kunskaps basen bör ha båda typer av frågor för att kunna returnera det bästa svaret på rätt sätt. Om du inte är säker på vilka nyckelord en kund anger använder du Application Insights data för att analysera frågor.

### <a name="good-answers"></a>Lämpliga svar

De bästa svaren är enkla svar, men inte för enkla. Använd inte svar som `yes` och `no`. Om ditt svar ska länkas till andra källor eller ger en omfattande upplevelse med medier och länkar, använder du [metadata-märkning](../how-to/edit-knowledge-base.md#add-metadata) för att skilja mellan svar och [skickar sedan frågan](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) med metadata-Taggar i `strictFilters`-egenskapen för att få rätt svars version.

|Svar|Follup-prompter|
|--|--|
|Stäng av arbets ytans bärbara dator med strömbrytaren på tangent bordet.|* Viktiga kombinationer av ström spar läge, avstängning och omstart.<br>* Så här kan du starta en bärbar dator på en Surface<br>* Ändra BIOS för en Surface-dator<br>* Skillnader mellan ström spar läge, Stäng av och starta om|
|Kund tjänst är tillgänglig via telefon, Skype och textmeddelande dygnet runt, 24 timmar per dag.|* Kontakt information för försäljning.<br> * Kontor och lagrings platser och timmar för ett personligt besök.<br> * Tillbehör för en Surface-dator.|

## <a name="chit-chat"></a>Chit-chatt
Lägg till chit-chatt i din robot att göra din robot konversationsanpassade och mer engagerande, med låg insats. Du kan enkelt lägga till data uppsättningar för CHI2TEST från fördefinierade personliga objekt när du skapar dina KB och ändra dem när som helst. Lär dig hur du [lägger till en CHI2TEST i din KB](../How-To/chit-chat-knowledge-base.md).

CHI2TEST-chatt stöds på [många språk](../how-to/chit-chat-knowledge-base.md#language-support).

### <a name="choosing-a-personality"></a>Välja en personlighet
CHI2TEST-chatt stöds för flera fördefinierade personliga uppgifter:

|Personlighet |QnA Maker data uppsättnings fil |
|---------|-----|
|Professionell |[qna_chitchat_professional. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Eget |[qna_chitchat_friendly. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witty |[qna_chitchat_witty. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Caring |[qna_chitchat_caring. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Entusiastisk |[qna_chitchat_enthusiastic. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Svar mellan formell och informell och ditt eget. Du bör välja den person som är närmast justerad efter tonen som du vill använda för din robot. Du kan visa [data uppsättningarna](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)och välja en som fungerar som en bas för din robot och sedan anpassa svaren.

### <a name="edit-bot-specific-questions"></a>Redigera bot-specifika frågor
Det finns några bot-specifika frågor som är en del av datauppsättningen chit-chatt och har fyllts i med allmänna svar. Ändra dessa frågor för att återspegla din bot information bäst.

Vi rekommenderar att du gör följande chit-chatt kunskapsbaser mer specifika:

* Vem är du?
* Vad kan du göra?
* Hur gammal är du?
* Vem som skapade du?
* Hello

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Lägger till anpassad CHI2TEST-chatt med en metadata-tagg

Om du lägger till dina egna CHI2TEST QnA-par, se till att lägga till metadata så att svaren returneras. Namnet/värde-paret för metadata är `editorial:chitchat`.

## <a name="searching-for-answers"></a>Söker efter svar

GenerateAnswer-API: et använder både frågor och svar för att söka efter bästa svar på användarens fråga.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Sök bara efter frågor när svar inte är relevant

Använd [`RankerType=QuestionOnly`](#choosing-ranker-type) om du inte vill söka efter svar.

Ett exempel på detta är när kunskaps basen är en katalog med akronymer som frågor med fullständigt formulär som svar. Svarets värde kommer inte att hjälpa dig att söka efter lämpligt svar.

## <a name="rankingscoring"></a>Rangordning/bedömning
Kontrollera att du gör på bästa sätt rangordning-funktioner som har stöd för QnA Maker. Detta kommer att förbättra sannolikheten för en viss användarfrågan besvaras med ett lämpligt svar.

### <a name="choosing-a-threshold"></a>Välja ett tröskelvärde

Standard utgångs [poängen](confidence-score.md) som används som tröskelvärde är 0, men du kan [Ändra tröskelvärdet](confidence-score.md#set-threshold) för dina KB utifrån dina behov. Eftersom varje KB skiljer sig, bör du testa och välj tröskelvärde som passar bäst för din Kunskapsbas.

### <a name="choosing-ranker-type"></a>Väljer rangordnings typ
Som standard söker QnA Maker igenom frågor och svar. Om du bara vill söka igenom frågor, för att generera ett svar, använder du `RankerType=QuestionOnly` i INLÄGGs texten i GenerateAnswer-begäran.

### <a name="add-alternate-questions"></a>Lägga till alternativa frågor
[Alternativa frågor](../How-To/edit-knowledge-base.md) förbättrar sannolikheten för en matchning med en användar fråga. Alternativa frågor är användbara när det finns flera sätt samma fråga kan bli ombedd. Detta kan inkludera ändringar i meningen struktur och word-format.

|Ursprungliga frågan|Alternativa frågor|Ändra|
|--|--|--|
|Är parkeringssidans tillgängliga?|Har du bil park?|mening struktur|
 |Hej!|Yo<br>Hej det!|Word-format eller slang|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Använd metadata-taggar för att filtrera frågor och svar

[Metadata](../How-To/edit-knowledge-base.md) lägger till möjligheten för ett klient program att veta att det inte ska ta med alla svar utan i stället begränsa resultatet av en användar fråga baserat på metadata-taggar. Kunskapsbas-svar kan variera beroende på metadatataggen, även om frågan är samma. Till exempel *"Where är parkerings plats"* kan ha ett annat svar om platsen för restaurang grenen är en annan – det vill säga att metadata är *plats: Seattle* respektive *plats: Redmond*.

### <a name="use-synonyms"></a>Använda synonymer
Även om det finns stöd för synonymer på det engelska språket använder du Skift läges känsliga ord ändringar via [API: erna](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) för att lägga till synonymer till nyckelord som tar olika formulär. Synonymer läggs till på QnA Maker service nivå och delas av alla kunskaps banker i tjänsten.

|Ursprungliga word|Synonymer|
|--|--|
|Köp|köp<br>NET-Banks<br>NET bank|

### <a name="use-distinct-words-to-differentiate-questions"></a>Använd distinkta ord för att skilja frågor
QnA Makerens rangordning, som matchar en användar fråga med en fråga i kunskaps basen, fungerar bäst om varje fråga behandlar olika behov. Upprepning av samma ord ange mellan frågor minskar risken för att rätt svar väljs för en viss användarfråga med orden.

Du kan till exempel ha två separata kunskapsbaser med följande frågor:

|Kunskapsbaser|
|--|
|där är parkerings *platsen*|
|var är ATM- *platsen*|

Eftersom dessa två kring är formulerade med mycket liknande ord, kan den här likheten orsaka mycket liknande Poäng för många användar frågor som är formulerade som *"där är `<x>` plats"* . Försök i stället att tydligt särskilja med frågor som *"Where är parkerings partiet"* och *"var är ATM"* , genom att undvika ord som "plats" som kan finnas i många frågor i din KB.

## <a name="collaborate"></a>Samarbeta
QnA Maker gör det möjligt för användare att [samar beta](../How-to/collaborate-knowledge-base.md) i en kunskaps bas. Användare behöver åtkomst till Azure QnA Maker resursgruppen för att komma åt kunskapsbaser. Vissa organisationer vilja indrivningen kunskapsbas redigering och underhåll och fortfarande kunna skydda åtkomsten till deras Azure-resurser. Den här redigeraren – god kännare modell görs genom att konfigurera två identiska [QNA Maker-tjänster](../How-to/set-up-qnamaker-service-azure.md) i olika prenumerationer och välja en för redigerings test cykeln. När testet är klart överförs kunskaps bas innehållet med en [import-export-](../Tutorials/migrate-knowledge-base.md) process till QNA Maker tjänsten för god kännaren som slutligen publicerar kunskaps basen och uppdaterar slut punkten.



## <a name="active-learning"></a>Aktiv inlärning

[Active Learning](../How-to/improve-knowledge-base.md) gör det bästa sättet att föreslå alternativa frågor när det finns många olika kvalitets-och mängd användarbaserade frågor. Det är viktigt att tillåta att klient programs användar frågor deltar i den aktiva inlärnings feedback-loopen utan censorship. När frågorna föreslås i QnA Maker Portal kan du **[Filtrera efter förslag](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** och sedan granska och godkänna eller avvisa dessa förslag.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Redigera en kunskaps bas](../How-to/edit-knowledge-base.md)
