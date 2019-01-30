---
title: Livscykeln för kunskapsbas - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker lär sig bäst i en iterativ livscykel datamodellen ändras, uttryck exempel, publicering och samla in data från slutpunkt-frågor.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c728946582ec2d0423c1e984b5ff0463f59da466
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207351"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Kunskapsbas livscykel i QnA Maker
QnA Maker lär sig bäst i en iterativ livscykel datamodellen ändras, uttryck exempel, publicering och samla in data från slutpunkt-frågor. 

![Redigeringscykel](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Skapa en kunskapsbas med QnA Maker
Slutpunkt för QnA Maker knowledge base (KB) innehåller en bästa matchning svaret på en användarfråga baserat på innehållet i KB. Det är en engångsåtgärd för att skapa en innehållsdatabasen frågor, svar och associerade metadata för att skapa en kunskapsbas. En kunskapsbas kan skapas med vår högkvalitativa befintliga innehåll som sidor med vanliga frågor och svar, produkthandböcker eller strukturerade Q-A-par. Lär dig hur du [skapa en kunskapsbas](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Testa och uppdatera kunskapsbasen
Kunskapsbasen är klart för testning när det fylls i med innehåll, redigeringsmässigt eller via automatisk extrahering. Testning kan göras via den **Test** panelen genom att ange vanliga användarfrågor om och kontrollera att svaren som returneras är som förväntat och med tillräckligt med förtroendepoäng. Du kan lägga till alternativa frågor för att åtgärda resultat för låga förtroende. Du kan också lägga till nya svar när en fråga returnerar ”ingen matchning hittades i KB” Standard svarstid. Den här tät loop av test-uppdatering fortsätter tills du är nöjd med resultaten. Lär dig hur du [testa kunskapsbasen](../How-To/test-knowledge-base.md).

För stora KB-artiklar, kan testning automatiseras via generateAnswer API: er. 

## <a name="publish-the-knowledge-base"></a>Publicera kunskapsbasen
När du är klar testar kunskapsbasen kan du publicera den. Publicera push-meddelanden den senaste versionen av testade kunskapsbasen till en dedikerad Azure Search index som representerar den **publicerade** knowledge base. Den skapar även en slutpunkt som kan anropas i ditt program eller en chattrobot.

På så sätt kan alla ändringar som görs till testversion av kunskapsbasen påverkar inte den publicerade versionen som kanske bor i ett produktionsprogram.

Var och en av dessa kunskapsbaser kan vara mål för att testa separat. Med API: erna kan du rikta testversion av kunskapsbas med `isTest=true` flaggan i generateAnswer-anropet.

Lär dig hur du [publicera din kunskapsbas](../How-To/publish-knowledge-base.md).

## <a name="monitor-usage"></a>Övervaka användning
Om du vill kunna logga chattloggarna av din tjänst skulle du behöva aktivera Application Insights när du [skapa QnA Maker-tjänsten](../How-To/set-up-qnamaker-service-azure.md).

Du kan få olika analys av din tjänstanvändning. Läs mer om hur du använder application insights för att hämta [analys för QnA Maker-tjänsten](../How-To/get-analytics-knowledge-base.md).

Baserat på vad du lär dig av dina analyser, gör rätt [uppdateringar till din kunskapsbas](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förtroendepoäng](./confidence-score.md)

## <a name="see-also"></a>Se också 

[Kunskapsbas](./knowledge-base.md)
[QnA Maker-översikt](../Overview/overview.md)
