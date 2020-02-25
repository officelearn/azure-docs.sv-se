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
ms.date: 02/21/2020
ms.author: diberry
ms.openlocfilehash: 91a63e87e937eab4c02d2b86026aa09472cb15ef
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561109"
---
# <a name="what-is-the-qna-maker-service"></a>Vad är tjänsten QnA Maker?

QnA Maker är en molnbaserad NLP-tjänst (Natural Language Processing) som gör det enkelt att skapa ett naturligt samtals lager över dina data. Den kan användas för att hitta det mest lämpliga svaret för alla angivna språk från en särskild språk version, från din anpassade kunskaps bas (KB) information.

Ett klient program för QnA Maker är ett konversations program som kommunicerar med en användare på naturligt språk för att svara på en fråga. Exempel på klientprogram är appar för sociala medier, chattrobotar och talaktiverade skrivbordsprogram.

## <a name="when-to-use-qna-maker"></a>När du ska använda QnA Maker

* **När du har statisk information** – Använd QNA Maker när du har statisk information i kunskaps basen med svar. Den här kunskaps basen är anpassad efter dina behov, som du har skapat med dokument som [PDF-filer och URL: er](../concepts/content-types.md).
* **När du vill ge samma svar på en begäran, fråga eller kommando** – när olika användare skickar samma fråga returneras samma svar.
* **När du vill filtrera statisk information baserat på meta-information** – Lägg till [metadata](../how-to/metadata-generateanswer-usage.md) -taggar för att tillhandahålla ytterligare filtrerings alternativ som är relevanta för klient programmets användare och information. Common metadata-information inkluderar [CHI2TEST](../how-to/chit-chat-knowledge-base.md), innehålls typ, format, innehålls syfte och innehållets aktualitet.
* **När du vill hantera en robot konversation som innehåller statisk information** – din kunskaps bas tar en användares konversations text eller kommando och svarar på den. Om svaret är en del av ett fördefinierat konversations flöde, som representeras i din kunskaps bas med [multi-turn-kontext](../how-to/multiturn-conversation.md), kan roboten enkelt tillhandahålla det här flödet.

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Använda QnA Maker Knowledge Base i en chatt-robot

När en QnA Maker kunskaps bas har publicerats skickar ett klient program en fråga till din kunskaps bas slut punkt och tar emot resultatet som ett JSON-svar. Ett vanligt klient program för QnA Maker är en chatt-robot.

![Ställ en robot till en fråga och få svar från kunskaps bas innehållet](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Steg|Åtgärd|
|:--|:--|
|1|Klient programmet skickar användarens _fråga_ (text i sina egna ord), "Hur gör jag för att program mässigt uppdatera min kunskaps bas?" till din kunskaps bas slut punkt.|
|2|QnA Maker använder den utbildade kunskaps basen för att tillhandahålla rätt svar och eventuella uppföljnings instruktioner som kan användas för att förfina sökningen efter det bästa svaret. QnA Maker returnerar ett JSON-formaterat svar.|
|3|Klient programmet använder JSON-svaret för att fatta beslut om hur du ska fortsätta konversationen. Dessa beslut kan omfatta det bästa svaret och Visa fler alternativ för att förfina sökningen efter det bästa svaret. |
|||

## <a name="what-is-a-knowledge-base"></a>Vad är en kunskaps bas?

QnA Maker [importerar ditt innehåll](../concepts/knowledge-base.md) till en kunskaps bas med frågor och svars uppsättningar. Import processen extraherar information om relationen mellan delarna i det strukturerade och det delvis strukturerade innehållet för att innebära relationer mellan frågan och svars uppsättningarna. Du kan redigera dessa frågor och svars uppsättningar eller lägga till nya uppsättningar.

Innehållet i frågan och svars uppsättningen innehåller:
* Alla alternativa formulär för frågan
* Metadata-taggar som används för att filtrera svars alternativ under sökningen
* Följ anvisningarna för att fortsätta Sök förfiningen

![Exempel fråga och svara med metadata](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

När du har publicerat din kunskaps bas skickar ett klient program en användares fråga till din slut punkt. Din QnA Maker tjänst bearbetar frågan och svarar med det bästa svaret.

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Skapa, hantera och publicera till en bot utan kod

På QnA Makers portalen får du en fullständig redigerings upplevelse för kunskaps basen. Du kan importera dokument i det aktuella formuläret till din kunskaps bas. Dessa dokument (till exempel vanliga frågor och svar, produkt handböcker, kalkyl blad eller webb sidor) konverteras till frågor och svars uppsättningar. Varje uppsättning analyseras för uppföljnings instruktioner och är anslutna till andra uppsättningar. Det slutliga _markdown_ -formatet stöder omfattande presentationer, inklusive bilder och länkar.

När din kunskaps bas har redigerats publicerar du kunskaps basen till en fungerande [Azure Web App-robot](https://azure.microsoft.com/services/bot-service/) utan att skriva någon kod. Testa din robot i [Azure Portal](https://portal.azure.com) eller ladda ned och fortsätt utveckla.

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>Sök kvalitet och rangordning ger bästa möjliga svar

QnA Makers systemet är ett skiktat rangordnings sätt. Data lagras i Azure Search, som också fungerar som det första rangordnings skiktet. De främsta resultaten från Azure Search skickas sedan genom QnA Maker NLP omrangordnings modell för att producera de slutliga resultaten och förtroende poängen.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker förbättrar konversations processen

QnA Maker tillhandahåller frågor med flera frågor och aktiv utbildning för att hjälpa dig att förbättra dina grundläggande frågor och svars uppsättningar.

**Med frågor med flera turn** får du möjlighet att ansluta frågor och svar-par. Den här anslutningen gör att klient programmet kan tillhandahålla ett bästa svar och ger fler frågor för att förfina sökningen efter ett slut svar.

När kunskaps basen tar emot frågor från användare på den publicerade slut punkten, QnA Maker använda **sig** av de verkliga frågorna för att föreslå ändringar i kunskaps basen för att förbättra kvaliteten.

## <a name="development-lifecycle"></a>Utvecklingscykel

QnA Maker ger redigering, utbildning och publicering tillsammans med samarbets behörigheter för att integrera i livs cykeln för fullständig utveckling.

> [!div class="mx-imgBorder"]
> ![konceptuell bild av utvecklings cykel](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="how-do-i-start"></a>Hur gör jag för att start?

**Steg 1**: skapa en QNA Maker resurs i [Azure Portal](https://portal.azure.com).

**Steg 2**: skapa en kunskaps bas i [QNA Maker](https://www.qnamaker.ai) portalen. Lägg till [filer och URL: er](../concepts/content-types.md) för att skapa kunskaps basen.

**Steg 3**: publicera din kunskaps bas och testa från din anpassade slut punkt med hjälp av [sväng eller Postman](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

**Steg 4**: från klient programmet anropar du kunskaps bas slut punkten program mässigt. Klient programmet bearbetar JSON-svaret för att visa det bästa svaret på användaren.

## <a name="next-steps"></a>Nästa steg
QnA Maker innehåller allt du behöver för att bygga, hantera och distribuera din anpassade kunskaps bas.

> [!div class="nextstepaction"]
> [Granska de senaste ändringarna](../whats-new.md)
