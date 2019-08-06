---
title: Vad är QnA Maker-tjänsten?
titleSuffix: Azure Cognitive Services
description: QnA Maker är en molnbaserad NLP-tjänst som enkelt skapar ett naturligt samtals lager över dina data. Den kan användas för att hitta det mest lämpliga svaret för alla angivna språk från en särskild språk version, från din anpassade kunskaps bas (KB) information.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 08/01/2019
ms.author: diberry
ms.openlocfilehash: f56798359cdc8739a363bed3bfddadd584617adf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815477"
---
# <a name="what-is-the-qna-maker-service"></a>Vad är tjänsten QnA Maker?

QnA Maker är en molnbaserad NLP-tjänst (Natural Language Processing) som gör det enkelt att skapa ett naturligt samtals lager över dina data. Den kan användas för att hitta det mest lämpliga svaret för alla angivna språk från en särskild språk version, från din anpassade kunskaps bas (KB) information.

Ett klient program för QnA Maker är ett konversations program som kommunicerar med en användare på naturligt språk för att svara på en fråga. Exempel på klientprogram är appar för sociala medier, chattrobotar och talaktiverade skrivbordsprogram.

## <a name="when-to-use-qna-maker"></a>När du ska använda QnA Maker

* **När du har statisk information** – Använd QNA Maker när du har statisk information i kunskaps basen med svar. Den här kunskaps basen är anpassad efter dina behov, som du har skapat med dokument som [PDF-filer och URL: er](../concepts/data-sources-supported.md).
* **När du vill ge samma svar på en begäran, fråga eller kommando** – när olika användare skickar samma fråga returneras samma svar till båda. 
* **När du vill filtrera statisk information baserat på meta-information** – Lägg till [metadata](../how-to/metadata-generateanswer-usage.md) -taggar för att tillhandahålla ytterligare filtrerings alternativ som är relevanta för klient programmets användare och information. Common metadata-information inkluderar [CHI2TEST](../how-to/chit-chat-knowledge-base.md), innehålls typ, format, innehålls syfte och innehållets aktualitet.
* **När du vill hantera en robot konversation som innehåller statisk information** – dina kunskaper tar en användares konversations text eller kommando och svarar på den. Om svaret är en del av ett fördefinierat konversations flöde, som representeras i din kunskaps bas med [multi-turn-kontext](../how-to/multiturn-conversation.md), kan roboten enkelt tillhandahålla det här flödet.  

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Använda QnA Maker Knowledge Base i en chatt-robot

När en QnA Maker kunskaps bas har publicerats skickar ett klient program en fråga till din kunskaps bas slut punkt och tar emot resultatet som ett JSON-svar. Ett vanligt klient program för QnA Maker är en chatt-robot.

![Ställ en robot till en fråga och få svar från kunskaps bas innehållet](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Steg|Action|
|:--|:--|
|1|Klient programmet skickar användarens _fråga_ (text i sina egna ord), "Hur gör jag för att program mässigt uppdatera min kunskaps bas?" till din kunskaps bas slut punkt.|
|2|QnA Maker använder den utbildade kunskaps basen för att tillhandahålla rätt svar och eventuella uppföljnings instruktioner som kan användas för att förfina sökningen efter det bästa svaret. QnA Maker returnerar ett JSON-formaterat svar.|
|3|Klient programmet använder JSON-svaret för att fatta beslut om hur du ska fortsätta konversationen. Dessa beslut kan omfatta att visa det bästa svaret eller Visa fler alternativ för att förfina sökningen efter det bästa svaret. |
|||

## <a name="what-is-a-knowledge-base"></a>Vad är en kunskaps bas? 

QnA Maker [importerar ditt innehåll](../concepts/data-sources-supported.md) till en kunskaps bas med frågor och svars uppsättningar. Import processen extraherar information om relationen mellan delarna i det strukturerade och det delvis strukturerade innehållet för att innebära relationer mellan frågan och svars uppsättningarna. Du kan redigera dessa frågor och svars uppsättningar eller lägga till nya.  

Innehållet i frågan och svars uppsättningen innehåller alla alternativa frågor för ett särskilt svar, metadata-taggar som används för att filtrera svars alternativ under sökningen och Uppföljnings instruktioner för att fortsätta Sök förfiningen.

![Exempel fråga och svara med metadata](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

När du har publicerat din kunskaps bas skickar ett klient program en användares fråga till din slut punkt. Din QnA Maker tjänst bearbetar frågan och svarar med det bästa svaret. 

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Skapa, hantera och publicera till en bot utan kod

På QnA Makers portalen får du en fullständig redigerings upplevelse för kunskaps basen. Du kan importera dokument i det aktuella formuläret till din kunskaps bas. Dessa dokument (till exempel vanliga frågor och svar, produkt handböcker, kalkyl blad eller webb sidor) konverteras till frågor och svars uppsättningar. Varje uppsättning analyseras för uppföljnings instruktioner och är anslutna till andra uppsättningar. Det slutliga markdown-formatet stöder omfattande presentationer, inklusive bilder och länkar. 

När din kunskaps bas har redigerats publicerar du kunskaps basen till en fungerande [Azure Web App-robot](https://azure.microsoft.com/services/bot-service/) utan att skriva någon kod. Testa din robot i [Azure Portal](https://portal.azure.com) eller ladda ned och fortsätt utveckla. 

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>Sök kvalitet och rangordning ger bästa möjliga svar

QnA Makers systemet är ett skiktat rangordnings sätt. Data lagras i Azure Search, som också fungerar som det första rangordnings skiktet. De främsta resultaten från Azure Search skickas sedan genom QnA Maker NLP omrangordnings modell för att producera de slutliga resultaten och förtroende poängen.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker förbättrar konversations processen

QnA Maker tillhandahåller frågor med flera frågor och aktiv utbildning för att hjälpa dig att förbättra dina grundläggande frågor och svars uppsättningar. 

**Med frågor med flera turn** får du möjlighet att ansluta frågor och svar-par. Den här anslutningen gör att klient programmet kan tillhandahålla ett bästa svar och ger fler frågor för att förfina sökningen efter ett slut svar. 

När kunskaps basen tar emot frågor från användare på den publicerade slut punkten, QnA Maker använda **sig** av de verkliga frågorna för att föreslå ändringar i kunskaps basen för att förbättra kvaliteten. 

## <a name="development-lifecycle"></a>Utvecklingscykel

QnA Maker ger redigering, utbildning och publicering tillsammans med samarbets behörigheter för att integrera i livs cykeln för fullständig utveckling. 

## <a name="how-do-i-start"></a>Hur börjar jag?

**Steg 1**: Skapa en QnA Maker resurs i [Azure Portal](https://portal.azure.com). 

**Steg 2**: Skapa en kunskaps bas i [QNA Maker](https://www.qnamaker.ai) portalen. Lägg till [filer och URL: er](../concepts/data-sources-supported.md) för att skapa kunskaps basen.  

**Steg 3**: Publicera din kunskaps bas och testa från din anpassade slut punkt med hjälp av [sväng](../quickstarts/get-answer-from-kb-using-curl.md) eller [Postman](../quickstarts/get-answer-from-kb-using-postman.md). 

**Steg 4**: Från klient programmet anropar du kunskaps basns slut punkt via programmering och läser JSON-svaret Visa det bästa svaret till användaren.  

## <a name="news-and-updates"></a>Nyheter och uppdateringar

Lär dig vad som är nytt med QnA Maker.

* 2019 juni
    * Förbättrad rangordnings modell för franska, italienska, tyska, spanska, portugisiska
* April 2019
    * Stöd för extrahering av webbplats innehåll
    * Stöd för SharePoint-dokument
* Mars 2019
    * Aktiv inlärning 
    * Förbättrad ranknings modell för NLP för engelska, 

## <a name="next-steps"></a>Nästa steg
QnA Maker innehåller allt du behöver för att bygga, hantera och distribuera din anpassade kunskaps bas. 

> [!div class="nextstepaction"]
> [Skapa en QnA Maker-tjänst](../how-to/set-up-qnamaker-service-azure.md)
