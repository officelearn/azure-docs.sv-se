---
title: Metodtips - QnA Maker
description: Använd de här metodtipsen för att förbättra kunskapsbasen och ge bättre resultat för slutanvändarna för program-/chattroboten.
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 9a6f7f7d6edc4544942476050a1ed3c2011af7fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053124"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Bästa praxis för en QnA Maker-kunskapsbas

[Kunskapsbasens utvecklingslivscykel](../Concepts/development-lifecycle-knowledge-base.md) vägleder dig om hur du hanterar din KB från början till. Använd de här metodtipsen för att förbättra kunskapsbasen och ge bättre resultat till klientprogrammet eller chattrobotens slutanvändare.

## <a name="extraction"></a>Extrahering

QnA Maker-tjänsten förbättrar kontinuerligt algoritmerna som extraherar QnAs från innehåll och utökar listan över fil- och HTML-format som stöds. Följ [riktlinjerna](../Concepts/content-types.md) för dataextrahering baserat på dokumenttypen.

I allmänhet bör FAQ-sidor vara fristående och inte kombineras med annan information. Produktmanualer bör ha tydliga rubriker och helst en indexsida.

### <a name="configuring-multi-turn"></a>Konfigurera flera svängar

[Skapa din kunskapsbas](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) med extraktion med flera varv aktiverat. Om kunskapsbasen har eller bör stödja frågehierarkin kan den här hierarkin extraheras från dokumentet eller skapas när dokumentet har extraherats.

## <a name="creating-good-questions-and-answers"></a>Skapa bra frågor och svar

### <a name="good-questions"></a>Bra frågor

De bästa frågorna är enkla. Tänk på nyckelordet eller frasen för varje fråga och skapa sedan en enkel fråga för det nyckelordet eller frasen.

Lägg till så många alternativa frågor som du behöver men håll ändringarna enkla. Att lägga till fler ord eller fraser som inte ingår i huvudmålet för frågan hjälper inte QnA Maker att hitta en matchning.


### <a name="add-relevant-alternative-questions"></a>Lägg till relevanta alternativa frågor

Användaren kan ange frågor med antingen ett `How do I add a toner cartridge to my printer?` konversationsformat med `toner cartridge`text eller en nyckelordssökning, till exempel . Kunskapsbasen bör ha båda stilarna av frågor för att korrekt returnera det bästa svaret. Om du inte är säker på vilka sökord en kund anger använder du Application Insights-data för att analysera frågor.

### <a name="good-answers"></a>Bra svar

De bästa svaren är enkla svar men inte alltför enkelt. Använd inte svar `yes` som `no`och . Om ditt svar ska länka till andra källor eller ge en omfattande upplevelse med [submit the query](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) media och länkar, använd `strictFilters` [metadatataggning](../how-to/edit-knowledge-base.md#add-metadata) för att skilja mellan svar och skicka sedan frågan med metadatataggar i egenskapen för att få rätt svarsversion.

|Svar|Follup-up-uppmaningar|
|--|--|
|Stäng av surface-datorn med strömbrytaren på tangentbordet.|* Tangentkombinationer till viloläge, stänga av och starta om.<br>* Hur hård-starta en Surface laptop<br>* Hur man ändrar BIOS för en Surface laptop<br>* Skillnader mellan sömn, stänga av och starta om|
|Kundtjänst är tillgänglig via telefon, Skype och sms 24 timmar om dygnet.|* Kontaktuppgifter till försäljning.<br> * Kontor och lagra platser och timmar för ett besök personligen.<br> * Tillbehör för en Surface laptop.|

## <a name="chit-chat"></a>Chit-Chat (Chit-Chat)
Lägg till småprat till din bot, för att göra din bot mer konversera och engagerande, med låg ansträngning. Du kan enkelt lägga till småchattdatauppsättningar från fördefinierade personligheter när du skapar din KB och ändra dem när som helst. Läs om hur du [lägger till småprat i din KB](../How-To/chit-chat-knowledge-base.md).

Chit-chat stöds på [många språk](../how-to/chit-chat-knowledge-base.md#language-support).

### <a name="choosing-a-personality"></a>Välja en personlighet
Chit-chat stöds för flera fördefinierade personligheter:

|Personlighet |QnA Maker-datauppsättningsfil |
|---------|-----|
|Professionell |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Vänlig |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Kvick |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Omtänksam |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Entusiastiska |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Svaren sträcker sig från formella till informella och vanvördiga. Du bör välja den personlighet som är närmast i linje med den ton du vill ha för din bot. Du kan visa [datauppsättningarna](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)och välja en som fungerar som bas för din robot och sedan anpassa svaren.

### <a name="edit-bot-specific-questions"></a>Redigera botspecifika frågor
Det finns några bot-specifika frågor som är en del av chit-chat datauppsättningen, och har fyllts i med generiska svar. Ändra dessa svar för att bäst återspegla dina bot detaljer.

Vi rekommenderar att du gör följande chit-chat QnAs mer specifikt:

* Vem är du?
* Vad kan du göra?
* How old are you?
* Vem skapade dig?
* Hello

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Lägga till anpassad chit-chat med en metadatatagg

Om du lägger till dina egna chit-chat QnA-par, se till att lägga till metadata så att dessa svar returneras. Metadatanamn/värdeparet `editorial:chitchat`är .

## <a name="searching-for-answers"></a>Söka efter svar

GenerateAnswer API använder både frågor och svaret för att söka efter bästa svar på en användares fråga.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Söka frågor endast när svaret inte är relevant

Använd [`RankerType=QuestionOnly`](#choosing-ranker-type) om du inte vill söka svar.

Ett exempel på detta är när kunskapsbasen är en katalog med akronymer som frågor med sin fullständiga form som svar. Värdet på svaret hjälper inte att söka efter rätt svar.

## <a name="rankingscoring"></a>Ranking/poängsättning
Se till att du gör det bästa av rankingfunktioner QnA Maker stöder. Om du gör det kan du öka sannolikheten för att en viss användarfråga besvaras med ett lämpligt svar.

### <a name="choosing-a-threshold"></a>Välja ett tröskelvärde

[Standardkonfidenspoängen](confidence-score.md) som används som tröskelvärde är 0, men du kan [ändra tröskelvärdet](confidence-score.md#set-threshold) för din KB baserat på dina behov. Eftersom varje KB är olika bör du testa och välja den tröskel som passar bäst för din KB.

### <a name="choosing-ranker-type"></a>Välja rankertyp
Som standard söker QnA Maker igenom frågor och svar. Om du bara vill söka igenom frågor, för `RankerType=QuestionOnly` att generera ett svar, använd i post-brödtexten för GenerateAnswer-begäran.

### <a name="add-alternate-questions"></a>Lägg till alternativa frågor
[Alternativa frågor](../How-To/edit-knowledge-base.md) ökar sannolikheten för en matchning med en användarfråga. Alternativa frågor är användbara när det finns flera sätt på vilka samma fråga kan ställas. Detta kan inkludera ändringar i meningsstrukturen och ordformatet.

|Ursprunglig fråga|Alternativa frågor|Ändra|
|--|--|--|
|Finns det parkering?|Har du parkering?|meningsstruktur|
 |Hej|Yo<br>Hallå där!|ordstil eller slang|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Använda metadatataggar för att filtrera frågor och svar

[Metadata](../How-To/edit-knowledge-base.md) lägger till möjligheten för ett klientprogram att veta att det inte bör ta alla svar utan i stället begränsa resultatet av en användarfråga baserat på metadatataggar. Kunskapsbassvaret kan skilja sig åt beroende på metadatataggen, även om frågan är densamma. Till exempel *"där är parkering finns"* kan ha ett annat svar om platsen för restaurangen grenen är annorlunda - det vill vara metadata är *Plats: Seattle* kontra *Plats: Redmond*.

### <a name="use-synonyms"></a>Använd synonymer
Även om det finns visst stöd för synonymer i det engelska språket, använd skiftlägesokänsliga ordändringar via [API:et för ändringar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) för att lägga till synonymer i nyckelord som har olika form. Synonymer läggs till på QnA Maker-servicenivå och delas av alla kunskapsbaser i tjänsten.

|Originalord|Synonymer|
|--|--|
|Köpa|köp<br>nettobank<br>nettobank|

### <a name="use-distinct-words-to-differentiate-questions"></a>Använd olika ord för att skilja frågor
QnA Maker's rankningsalgoritm, som matchar en användarfråga med en fråga i kunskapsbasen, fungerar bäst om varje fråga tar upp ett annat behov. Upprepning av samma orduppsättning mellan frågor minskar sannolikheten för att rätt svar väljs för en viss användarfråga med dessa ord.

Du kan till exempel ha två separata QnA:er med följande frågor:

|QnAs (andra)|
|--|
|var är *parkeringsplatsen*|
|var är bankomaten *plats*|

Eftersom dessa två QnAs är formulerade med mycket liknande ord, kan denna likhet orsaka mycket liknande poäng för många användarfrågor som är formulerade som *"var är `<x>` platsen".* Försök istället att tydligt skilja med frågor som *"var är parkeringsplatsen"* och *"var är bankomaten",* genom att undvika ord som "plats" som kan vara i många frågor i din KB.

## <a name="collaborate"></a>Samarbeta
QnA Maker tillåter användare att [samarbeta](../How-to/collaborate-knowledge-base.md) på en kunskapsbas. Användare behöver åtkomst till Azure QnA Maker-resursgruppen för att komma åt kunskapsbaserna. Vissa organisationer kanske vill lägga ut kunskapsbasen för redigering och underhåll och ändå kunna skydda åtkomsten till sina Azure-resurser. Den här editor-approveer-modellen görs genom att ställa in två identiska [QnA Maker-tjänster](../How-to/set-up-qnamaker-service-azure.md) i olika prenumerationer och välja en för redigeringstestningscykeln. När testningen är klar överförs kunskapsbasinnehållet med en [import-exportprocess](../Tutorials/migrate-knowledge-base.md) till QnA Maker-tjänsten för godkännaren som slutligen publicerar kunskapsbasen och uppdaterar slutpunkten.



## <a name="active-learning"></a>Aktiv inlärning

[Aktivt lärande](../How-to/use-active-learning.md) gör det bästa jobbet med att föreslå alternativa frågor när den har ett brett utbud av kvalitet och kvantitet användarbaserade frågor. Det är viktigt att tillåta klient-program användarfrågor att delta i active learning feedback loop utan censur. När frågor har föreslagits i QnA Maker-portalen kan du **[filtrera efter förslag](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** och sedan granska och acceptera eller avvisa dessa förslag.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Redigera en kunskapsbas](../How-to/edit-knowledge-base.md)
