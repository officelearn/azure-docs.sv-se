---
title: Vad är QnA Maker-tjänsten?
titleSuffix: Azure Cognitive Services
description: QnA Maker är en molnbaserad NLP-tjänst som enkelt skapar ett naturligt konversationslager över dina data. Den kan användas för att hitta det lämpligaste svaret för en viss inmatning av naturligt språk, från din anpassade kunskapsbas (KB) av information.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 02/21/2020
ms.author: diberry
ms.openlocfilehash: 2863a086343b0a5d3bc69ae319dbe93d557bcb4a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052931"
---
# <a name="what-is-the-qna-maker-service"></a>Vad är QnA Maker-tjänsten?

[!INCLUDE [TLS 1.2 enforcement](../../../../includes/cognitive-services-tls-announcement.md)]

QnA Maker är en molnbaserad NLP-tjänst (Natural Language Processing) som enkelt skapar ett naturligt konversationslager över dina data. Den kan användas för att hitta det lämpligaste svaret för en viss inmatning av naturligt språk, från din anpassade kunskapsbas (KB) av information.

Ett klientprogram för QnA Maker är alla konversationsprogram som kommunicerar med en användare på naturligt språk för att svara på en fråga. Exempel på klientprogram är appar för sociala medier, chattrobotar och talaktiverade skrivbordsprogram.

## <a name="when-to-use-qna-maker"></a>När ska QnA Maker användas

* **När du har statisk information** - Använd QnA Maker när du har statisk information i din kunskapsbas med svar. Den här kunskapsbasen är anpassad till dina behov, som du har skapat med dokument som [PDF-filer och webbadresser](../concepts/content-types.md).
* **När du vill ge samma svar på en begäran, fråga eller kommando** - när olika användare skickar samma fråga returneras samma svar.
* **När du vill filtrera statisk information baserat på metainformation** bör du lägga till [metadatataggar](../how-to/metadata-generateanswer-usage.md) för att tillhandahålla ytterligare filtreringsalternativ som är relevanta för klientprogrammets användare och informationen. Gemensam metadatainformation omfattar [chit-chat,](../how-to/chit-chat-knowledge-base.md)innehållstyp eller format, innehållssyfte och innehållsfrilighet.
* **När du vill hantera en bot-konversation som innehåller statisk information** – tar din kunskapsbas en användares konversationstext eller kommando och svarar på den. Om svaret är en del av ett förutbestämt konversationsflöde, representerat i din kunskapsbas med [multi-turn-kontext,](../how-to/multiturn-conversation.md)kan roboten enkelt ge det här flödet.

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Använd QnA Maker-kunskapsbasen i en chattrobot

När en QnA Maker-kunskapsbas har publicerats skickar ett klientprogram en fråga till din kunskapsbasslutpunkt och får resultaten som ett JSON-svar. Ett vanligt klientprogram för QnA Maker är en chattrobot.

![Ställ en bot en fråga och få svar från kunskapsbasinnehåll](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Steg|Åtgärd|
|:--|:--|
|1|Klientprogrammet skickar användarens _fråga_ (text med egna ord), "Hur uppdaterar jag min kunskapsbas programmatiskt?" till din kunskapsbasslutpunkt.|
|2|QnA Maker använder den utbildade kunskapsbasen för att ge rätt svar och eventuella uppföljningsanspråk som kan användas för att förfina sökningen efter det bästa svaret. QnA Maker returnerar ett JSON-formaterat svar.|
|3|Klientprogrammet använder JSON-svaret för att fatta beslut om hur konversationen ska fortsätta. Dessa beslut kan omfatta att visa det översta svaret och presentera fler val för att förfina sökandet efter det bästa svaret. |
|||

## <a name="what-is-a-knowledge-base"></a>Vad är en kunskapsbas?

QnA Maker [importerar ditt innehåll](../concepts/knowledge-base.md) till en kunskapsbas med fråge- och svarsuppsättningar. I importprocessen extraheras information om relationen mellan de delar av ditt strukturerade och halvstrukturerade innehåll för att antyda relationer mellan fråge- och svarsuppsättningarna. Du kan redigera dessa fråge- och svarsuppsättningar eller lägga till nya uppsättningar.

Innehållet i fråge- och svarsuppsättningen innehåller:
* Alla alternativa former av frågan
* Metadatataggar som används för att filtrera svarsalternativ under sökningen
* Uppföljningsan följande för att fortsätta sökförfiningen

![Exempel på frågor och svar med metadata](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

När du har publicerat kunskapsbasen skickar ett klientprogram en användares fråga till slutpunkten. Din QnA Maker-tjänst bearbetar frågan och svarar med det bästa svaret.

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Skapa, hantera och publicera till en bot utan kod

QnA Maker-portalen ger den kompletta kunskapsbasförfattarupplevelsen. Du kan importera dokument i aktuell form till kunskapsbasen. Dessa dokument (till exempel vanliga frågor och svar, produkthandbok, kalkylblad eller webbsida) konverteras till fråge- och svarsuppsättningar. Varje uppsättning analyseras för uppföljningsansaner och ansluts till andra uppsättningar. Det slutliga _markdown-formatet_ stöder omfattande presentation, inklusive bilder och länkar.

När kunskapsbasen har redigerats publicerar du kunskapsbasen till en fungerande [Azure Web App-robot](https://azure.microsoft.com/services/bot-service/) utan att skriva någon kod. Testa din robot i [Azure-portalen](https://portal.azure.com) eller hämta och fortsätt utvecklingen.

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>Sökkvalitet och rankning ger bästa möjliga svar

QnA Maker system är en skiktad ranking strategi. Data lagras i Azure-sökning, som också fungerar som det första rankningslagret. De bästa resultaten från Azure-sökning skickas sedan via QnA Maker's NLP-omrankningsmodell för att producera slutresultatet och konfidenspoängen.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker förbättrar konversationsprocessen

QnA Maker ger flervarvsuppmaningar och aktiv inlärning som hjälper dig att förbättra dina grundläggande fråge- och svarsuppsättningar.

**Flervarna uppmaningar** ger dig möjlighet att koppla ihop frågor och svar. Den här anslutningen gör det möjligt för klientprogrammet att ge ett toppsvar och ger fler frågor för att förfina sökningen efter ett slutligt svar.

När kunskapsbasen får frågor från användare vid den publicerade slutpunkten tillämpar QnA Maker **aktivt lärande** på dessa verkliga frågor för att föreslå ändringar i din kunskapsbas för att förbättra kvaliteten.

## <a name="development-lifecycle"></a>Utvecklingscykel

QnA Maker tillhandahåller författar-, utbildnings- och publiceringstillstånd tillsammans med samarbetsbehörigheter för att integreras i hela utvecklingslivscykeln.

> [!div class="mx-imgBorder"]
> ![Konceptuell bild av utvecklingscykeln](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="how-do-i-start"></a>Hur börjar jag?

**Steg 1:** Skapa en QnA Maker-resurs i [Azure-portalen](https://portal.azure.com).

**Steg 2**: Skapa en kunskapsbas i [QnA](https://www.qnamaker.ai) Maker-portalen. Lägg till [filer och webbadresser](../concepts/content-types.md) för att skapa kunskapsbasen.

**Steg 3:** Publicera din kunskapsbas och testa från din anpassade slutpunkt med [cURL eller Postman](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

**Steg 4**: Från ditt klientprogram, programmässigt ringa din kunskapsbas slutpunkt. Klientprogrammet bearbetar JSON-svaret för att visa det bästa svaret för användaren.

## <a name="next-steps"></a>Nästa steg
QnA Maker tillhandahåller allt du behöver för att bygga, hantera och distribuera din anpassade kunskapsbas.

> [!div class="nextstepaction"]
> [Granska de senaste ändringarna](../whats-new.md)
