---
title: Metodtips – QnA Maker
titlesuffix: Azure Cognitive Services
description: Använda dessa bästa metoder för att förbättra din kunskapsbas och ge bättre resultat för din robot program/chatt slutanvändare.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: c796114d124c64ac1c373baacabe00c7dcd70aa7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447637"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Bästa praxis för en kunskapsbas med QnA Maker

Den [kunskapsbas för säkerhetsutveckling](../Concepts/development-lifecycle-knowledge-base.md) guidar dig om hur du hanterar din Kunskapsbas från början till slut. Använd dessa bästa metoder för att förbättra din kunskapsbas och ger bättre resultat till ditt klientprogram eller chatta robotens slutanvändare.

## <a name="extraction"></a>Extrahering

QnA Maker-tjänsten är ständigt förbättrad algoritmer som extraherar kunskapsbaser från innehåll och expandera stöds fil- och HTML-format. Följ den [riktlinjer](../Concepts/data-sources-supported.md) för extrahering av data baserat på din typ av standarddokument. 

Vanliga frågor och svar sidor ska i allmänhet vara fristående och inte kombinerade med annan information. Produkthandböcker bör ha tydliga rubriker och helst en indexsida. 

### <a name="configuring-multi-turn"></a>Konfigurera flera aktivera

Skapa din kunskapsbas med flera aktivera extrahering aktiverat. Om kunskapsbasen har eller ska ha stöd för fråga hierarki, kan den här hierarkin extraheras från dokumentet eller skapas när dokumentet ska extraheras. 

<!--is this a global setting that can only be configured at kb creation time? -->

## <a name="creating-good-questions-and-answers"></a>Skapa bra frågor och svar

### <a name="good-questions"></a>Bra frågor

De bästa frågorna är enkla. Överväg att nyckeln ordet eller frasen för varje fråga och sedan skapa en enkel fråga för nyckeln ordet eller frasen. 

Lägg till så många alternativa frågor som du behöver men att ändringar som är enkelt. QnA Maker hitta en matchning hjälper inte att lägga till fler ord eller fraser som inte är en del av det huvudsakliga målet i frågan. 


### <a name="add-relevant-alternative-questions"></a>Lägga till relevanta alternativa frågor

Användaren kan ange frågor med en konversationsanpassade style textens, `How do I add a toner cartridge to my printer?` eller ett nyckelord som söker som `toner cartridge`. Kunskapsbasen bör ha båda typer av frågor för att returnera det bästa svaret korrekt. Om du inte vet vilka nyckelord som börjar med en kund kan du använda Application Insights-data för att analysera frågor.

### <a name="good-answers"></a>Bra svar

De bästa svaren är enkel svar men inte för enkelt. Använd inte svar som `yes` och `no`. Om ditt svar bör länka till andra källor eller ger en rik upplevelse med media och länkar, [metadata taggning](./knowledge-base.md#key-knowledge-base-concepts) att skilja mellan svar sedan [skicka frågan](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) med metadatataggar i `strictFilters` egenskapen för att hämta rätt svar-version.

## <a name="chit-chat"></a>Chit-chatt
Lägg till chit-chatt i din robot att göra din robot konversationsanpassade och mer engagerande, med låg insats. Du kan enkelt lägga till chit-chatt datauppsättningar från fördefinierade personligheter när du skapar din Kunskapsbas och ändra dem när som helst. Lär dig hur du [lägga till chit-chatt i din Kunskapsbas](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Välja en personlighet
Chit-chatt stöds för flera fördefinierade personligheter: 

|Personlighet |QnA Maker datamängdfil |
|---------|-----|
|Professionell |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Eget |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Spirituell |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Sköta |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Entusiastisk |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Svar mellan formell och informell och ditt eget. Du bör välja den person som är närmast justerad efter tonen som du vill använda för din robot. Du kan visa den [datauppsättningar](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets), en som används som bas för din robot, och välj sedan anpassa svaren. 

### <a name="edit-bot-specific-questions"></a>Redigera bot-specifika frågor
Det finns några bot-specifika frågor som är en del av datauppsättningen chit-chatt och har fyllts i med allmänna svar. Ändra dessa frågor för att återspegla din bot information bäst. 

Vi rekommenderar att du gör följande chit-chatt kunskapsbaser mer specifika:

* Vem är du?
* Vad kan du göra?
* Hur gammal är du?
* Vem som skapade du?
* Hej
   
### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Att lägga till anpassade chit-chatt med en metadatatagg

Om du lägger till egna frågor och svar om chit-chatt-par, se till att lägga till metadata så att dessa svar returneras. Namn/värde-par metadata är `editorial:chitchat`.

## <a name="searching-for-answers"></a>Söka efter svar

GenerateAnswer API använder både frågor och svar för att söka efter bästa svar på en användares fråga.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Söka frågor endast när svaret inte är relevant

Använd den [ `RankerType=QuestionOnly` ](#choosing-ranker-type) om du inte vill att söka efter svar. 

Ett exempel på detta är när kunskapsbasen är en katalog med förkortningar som frågor med sina fullständiga formen som svar. Värdet för svaret kan inte söka efter rätt svar.

## <a name="rankingscoring"></a>Rangordning/bedömning
Kontrollera att du gör på bästa sätt rangordning-funktioner som har stöd för QnA Maker. Detta kommer att förbättra sannolikheten för en viss användarfrågan besvaras med ett lämpligt svar.

### <a name="choosing-a-threshold"></a>Välja ett tröskelvärde

Standard [förtroendepoäng](confidence-score.md#) som används som ett tröskelvärde är 50, men du kan [ändra tröskelvärdet](confidence-score.md#set-threshold) för din Kunskapsbas utifrån dina behov. Eftersom varje KB skiljer sig, bör du testa och välj tröskelvärde som passar bäst för din Kunskapsbas. 

### <a name="choosing-ranker-type"></a>Välja rankningen typ
Som standard söker QnA Maker genom frågor och svar. Om du vill söka igenom frågor endast kan använda för att generera ett svar på `RankerType=QuestionOnly` i själva POST GenerateAnswer begäran.

### <a name="add-alternate-questions"></a>Lägga till alternativa frågor
[Alternativa frågor](../How-To/edit-knowledge-base.md) förbättra sannolikheten för en matchning med en användarfråga. Alternativa frågor är användbara när det finns flera sätt samma fråga kan bli ombedd. Detta kan inkludera ändringar i meningen struktur och word-format.

|Ursprungliga frågan|Alternativa frågor|Ändra| 
|--|--|--|
|Är parkeringssidans tillgängliga?|Har du bil park?|mening struktur|
 |Hej!|Yo<br>Hej det!|Word-format eller slang|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Använda metadatataggar om du vill filtrera frågor och svar

[Metadata](../How-To/edit-knowledge-base.md) lägger till möjligheten för ett klientprogram veta det inte bör ta alla svar utan för att begränsa resultatet av en användarfråga baserat på metadatataggar. Kunskapsbas-svar kan variera beroende på metadatataggen, även om frågan är samma. Till exempel *”var är parkering finns”* kan ha olika svar om grenen restaurang finns olika – det vill säga metadata är *plats: Seattle* jämfört med *plats: Redmond*.

### <a name="use-synonyms"></a>Använda synonymer
Det finns vissa stöd för synonymer i på engelska, använder skiftlägeskänsliga word förändras via den [förändras API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) att lägga till synonymer i nyckelord som tar olika format. Synonymer läggs till på QnA Maker-tjänstnivå och delas av alla kunskapsbaser i tjänsten.

|Ursprungliga word|Synonymer|
|--|--|
|Köp|köp<br>NET-bank<br>NET bank|

### <a name="use-distinct-words-to-differentiate-questions"></a>Använd distinkta ord för att skilja frågor
QnA Maker Rangordningsalgoritmen som matchar en användarfråga med en fråga i knowledge base, fungerar bäst om varje fråga adresser olika behov. Upprepning av samma ord ange mellan frågor minskar risken för att rätt svar väljs för en viss användarfråga med orden. 

Du kan till exempel ha två separata kunskapsbaser med följande frågor:

|Kunskapsbaser|
|--|
|där är parkering *plats*|
|där är ATM *plats*|

Eftersom dessa två kunskapsbaser fraserats med mycket lik ord, ungefär poäng för många av användarfrågor som fraserats som kan leda till den här likheter *”där är den `<x>` plats”* . Försök att tydligt skilja med frågor som *”var är p-plats”* och *”var är ATM”* , genom att undvika ord som ”plats” som kan befinna sig i många frågor i din KB. 

## <a name="collaborate"></a>Samarbeta
QnA Maker kan användarna [samarbeta](../How-to/collaborate-knowledge-base.md) på en kunskapsbas. Användare behöver åtkomst till Azure QnA Maker resursgruppen för att komma åt kunskapsbaser. Vissa organisationer vilja indrivningen kunskapsbas redigering och underhåll och fortfarande kunna skydda åtkomsten till deras Azure-resurser. Den här redigeraren godkännaren modellen gör du genom att konfigurera två identiska [QnA Maker services](../How-to/set-up-qnamaker-service-azure.md) i olika prenumerationer och välja en för testning av redigera cykeln. När testningen är klar innehållet i kunskapsbasen överförs med en [import / export-](../Tutorials/migrate-knowledge-base.md) bearbeta till QnA Maker-tjänsten till godkännaren slutligen publicera kunskapsbasen och uppdatera slutpunkten.



## <a name="active-learning"></a>Aktiv inlärning

[Aktiv inlärning](../How-to/improve-knowledge-base.md) fungerar på bästa sätt för att föreslå alternativa frågor när den har en mängd olika kvalitet och kvantitet för användarbaserad frågor. Det är viktigt att tillåta-klientprogram användarfrågor att delta i aktivt learning återkopplingssystem utan censur. När frågor föreslås i QnA Maker-portalen, kan du **[filtrera efter förslag](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** och sedan granska och godkänna eller avvisa dessa förslag. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Redigera en kunskapsbas](../How-to/edit-knowledge-base.md)
