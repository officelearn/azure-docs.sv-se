---
title: Metodtips – QnA Maker
titlesuffix: Azure Cognitive Services
description: Använda dessa bästa metoder för att förbättra din kunskapsbas och ge bättre resultat för din robot program/chatt slutanvändare.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/24/2018
ms.author: tulasim
ms.openlocfilehash: cb171a666a4a54660a3bf54b8f26aed23f60d249
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036311"
---
# <a name="best-practices"></a>Bästa praxis
Den [kunskapsbas för säkerhetsutveckling](../Concepts/development-lifecycle-knowledge-base.md) guidar dig om hur du hanterar din Kunskapsbas från början till slut. Använda dessa bästa metoder för att förbättra din kunskapsbas och ge bättre resultat för din robot program/chatt slutanvändare.

## <a name="extraction"></a>Extrahering
QnA Maker-tjänsten är ständigt förbättrad algoritmer som extraherar kunskapsbaser från innehåll och expandera stöds fil- och HTML-format. Följ den [riktlinjer](../Concepts/data-sources-supported.md) för extrahering av data baserat på din typ av standarddokument. 

Vanliga frågor och svar sidor ska i allmänhet vara fristående och inte kombinerade med annan information. Produkthandböcker bör ha tydliga rubriker och helst en indexsida. 

## <a name="chit-chat"></a>Chit-chatt
Lägg till chit-chatt i din robot att göra din robot konversationsanpassade och mer engagerande, med låg insats. Du kan enkelt lägga till chit-chatt datauppsättningar för 3 fördefinierade personligheter när du skapar din Kunskapsbas och ändra dem när som helst. Lär dig hur du [lägga till chit-chatt i din Kunskapsbas](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Välja en personlighet
Chit-chatt stöds för 3 fördefinierade personligheter: 

|Personligheter|
|--|
|Professional|
|Vän|
|Serien|

Svar mellan formell och informell och ditt eget. Du bör välja den person som är närmast justerad efter tonen som du vill använda för din robot. Du kan visa datauppsättningar, och välj ett namn som används som bas för din robot och sedan anpassa svaren. 

### <a name="edit-bot-specific-questions"></a>Redigera bot-specifika frågor
Det finns några bot-specifika frågor som är en del av datauppsättningen chit-chatt och har fyllts i med allmänna svar. Ändra dessa frågor för att återspegla din bot information bäst. 

Vi rekommenderar att du gör följande chit-chatt kunskapsbaser mer specifika:

* Vem är du?
* Vad kan du göra?
* Hur gammal är du?
* Vem som skapade du?
* Hej
   

## <a name="rankingscoring"></a>Rangordning/bedömning
Kontrollera att du gör på bästa sätt rangordning-funktioner som har stöd för QnA Maker. Detta kommer att förbättra sannolikheten för en viss användarfrågan besvaras med ett lämpligt svar.

### <a name="choosing-a-threshold"></a>Välja ett tröskelvärde
Standard-förtroendepoäng som används som ett tröskelvärde är 50, men du kan ändra den för din Kunskapsbas utifrån dina behov. Eftersom varje KB skiljer sig, bör du testa och välj tröskelvärde som passar bäst för din Kunskapsbas. Läs mer om den [förtroendepoäng](../Concepts/confidence-score.md). 


### <a name="add-alternate-questions"></a>Lägga till alternativa frågor
[Alternativa frågor](../How-To/edit-knowledge-base.md) förbättra sannolikheten för en matchning med en användarfråga. Alternativa frågor är användbara när det finns flera sätt samma fråga kan bli ombedd. Detta kan inkludera ändringar i meningen struktur och word-format.

|Ursprungliga frågan|Alternativa frågor|Ändra| 
|--|--|--|
|Är parkeringssidans tillgängliga?|Har du bil park?|mening struktur|
 |Hej!|Yo<br>Hej det!|Word-format eller slang|

### <a name="use-metadata-filters"></a>Använda filter för filmetadata
[Metadata](../How-To/edit-knowledge-base.md) lägger till möjligheten att begränsa resultatet av en användarfråga baserat på filter. Kunskapsbas-svar kan variera beroende på metadatataggen, även om frågan är samma. Till exempel *”var är parkering finns”* kan ha olika svar om grenen restaurang finns olika – det vill säga metadata är *plats: Seattle* jämfört med *plats: Redmond*.

### <a name="use-synonyms"></a>Använda synonymer
Även om det finns vissa stöd för synonymer i på engelska, [word förändras](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) att lägga till synonymer i nyckelord som tar olika format. Synonymer bör läggas på QnA Maker-tjänstnivå och delas av alla kunskapsbaser i tjänsten.

|Ursprungliga word|Synonymer|
|--|--|
|Köp|köp<br>netbanking<br>NET bank|

### <a name="use-distinct-words-to-differentiate-questions"></a>Använd distinkta ord för att skilja frågor
QnA Maker matchning och rangordning algoritmer som matchar en användarfråga med en fråga i knowledge base, fungerar bäst om varje fråga adresser olika behov. Upprepning av samma ord ange mellan frågor minskar risken för att rätt svar väljs för en viss användarfråga med orden. 

Du kan till exempel ha två separata kunskapsbaser med följande frågor:

|Kunskapsbaser|
|--|
|där är parkering *plats*|
|där är atm *plats*|

Eftersom dessa två kunskapsbaser fraserats med mycket lik ord, ungefär poäng för många av användarfrågor som fraserats som kan leda till den här likheter *”där är den `<x>` plats”*. Försök att tydligt skilja med frågor som *”var är p-plats”* och *”var är atm”*, genom att undvika ord som ”plats” som kan befinna sig i många frågor i din KB. 


## <a name="collaborate"></a>Samarbeta
QnA Maker kan användarna [samarbeta](../How-to/collaborate-knowledge-base.md) på en kunskapsbas. Användare behöver åtkomst till Azure QnA Maker resursgruppen för att komma åt kunskapsbaser. Vissa organisationer vilja indrivningen kunskapsbas redigering och underhåll och fortfarande kunna skydda åtkomsten till deras Azure-resurser. Den här redigeraren godkännaren modellen gör du genom att konfigurera två identiska [QnA Maker services](../How-to/set-up-qnamaker-service-azure.md) i olika prenumerationer och välja en för testning av redigera cykeln. När testningen är klar innehållet i kunskapsbasen överförs med en [import / export-](../Tutorials/migrate-knowledge-base.md) bearbeta till QnA Maker-tjänsten till godkännaren slutligen publicera kunskapsbasen och uppdatera slutpunkten.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Redigera en kunskapsbas](../How-to/edit-knowledge-base.md)
