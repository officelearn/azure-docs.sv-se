---
title: Utvecklingslivscykeln för av en knowledge base - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Utvecklingslivscykeln för av en knowledge base
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: 9ecdd2c7823eed145621b214690eff7681065507
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "35356033"
---
# <a name="knowledge-base-lifecycle"></a>Kunskapsbasen livscykel
Frågor och svar om Maker lär sig bäst i en iterativ livscykel Modelländringar, utterance exempel, publicering och samlar in data från slutpunkten frågor. 

![Redigera cykel](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Skapa en frågor och svar om Maker kunskapsbas
Frågor och svar om Maker knowledge base (KB) slutpunkt ger ett bäst matchar svar till en användarens fråga baserat på innehållet i KB. Skapa en kunskapsbas är en engångsåtgärd för att skapa en innehållsdatabas frågor, svar och tillhörande metadata. Kan skapa en kunskapsbas crawlning av befintlig innehåll, till exempel vanliga frågor och svar sidor, produkthandböcker eller strukturerade Q-A-par. Lär dig hur du [skapa en kunskapsbas](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testa och uppdatera kunskapsbasen
Kunskapsbasen är redo för testning när det fylls i med innehåll, redigeringsmässigt eller via automatisk extrahering. Testa kan göras via den **Test** panelen genom att ange vanliga användarfrågor och kontrollera att de svar som returnerades är som förväntat och med tillräckligt förtroende poäng. Du kan lägga till alternativa frågor för att åtgärda låg förtroende resultat. Du kan också lägga till nya svar när en fråga returnerar ”ingen matchning hittades i KB” Standard svarstid. Den här tät loop test-uppdatering fortsätter tills du är nöjd med resultaten. Lär dig hur du [testa kunskapsbasen](../How-To/test-knowledge-base.md).

För stora KBs automatiseras testning via generateAnswer API: er. 

## <a name="publish-the-knowledge-base"></a>Publicera i kunskapsbasen
När du är klar testning i knowledge base, kan du publicera den. Publicera push-meddelanden den senaste versionen av testade kunskapsbas till en dedikerad Azure Search index som representerar den **publicerade** knowledge base. Det skapar även en slutpunkt som kan anropas i programmet eller chatt bot.

På så sätt kan alla ändringar som görs till testversionen av kunskapsbasen påverkar inte den publicerade versionen som kanske bor i ett produktionsprogram.

Var och en av dessa knowledge baser kan vara mål för att testa separat. Använder API: er kan du rikta testversion av kunskapsbasen med `isTest=true` -flaggan i generateAnswer-anropet.

Lär dig hur du [publicera kunskapsbasen](../How-To/publish-knowledge-base.md).

## <a name="monitor-usage"></a>Övervaka användning
Om du vill kunna logga chatt-loggarna för din tjänst måste du aktivera Application Insights när du [skapa frågor och svar om Maker tjänsten](../How-To/set-up-qnamaker-service-azure.md).

Du kan hämta olika analytics för din användning av tjänsten. Mer information om hur du använder programinsikter för att hämta [analytics för frågor och svar om Maker tjänsten](../How-To/get-analytics-knowledge-base.md).

Baserat på vad du lära dig från dina analytics, se lämpliga [uppdateringar i knowledge base](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förtroende poäng](./confidence-score.md)

## <a name="see-also"></a>Se också 

[Kunskapsbasen](./knowledge-base.md)
[frågor och svar om Maker översikt](../Overview/overview.md)
