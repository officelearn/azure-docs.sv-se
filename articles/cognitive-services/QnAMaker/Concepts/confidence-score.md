---
title: Förtroendepoäng – QnA Maker
titleSuffix: Azure Cognitive Services
description: Ett förtroenderesultat Anger graden av matchning mellan användarens fråga och svaret som returnerades.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 33da5cf5724b8314ce813f12eb077d9a15ec1b2a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041531"
---
# <a name="confidence-score"></a>Förtroendepoäng

Ett förtroenderesultat Anger graden av matchning mellan användarens fråga och svaret som returnerades.

När en användarfråga matchas mot kunskapsbas innehåll, det kan finnas mer än en svaret som returnerades. Svar returneras i en rankad minskande förtroendepoäng.

Det är ett förtroenderesultat mellan 0 och 100.

|Poäng värde|Konfidensbedömning|
|--|--|
|100|En exakt matchning av användarfråga och en KB-fråga|
|90|Hög exakthet – de flesta av ord som matchar|
|40-60|Verkliga förtroende - viktiga ord matchar|
|10|Låg förtroende - viktiga ord stämmer inte överens|
|0|Ingen matchning av ord|


## <a name="similar-confidence-scores"></a>Liknande förtroende-poäng
När flera svar har en liknande förtroendepoäng, är det troligt att frågan var för allmän och därför matchade med samma sannolikhet med flera svar. Försök att strukturera dina kunskapsbaser bättre så att varje QnA-enhet har en distinkt avsikt.


## <a name="improving-confidence-scores"></a>Förbättra förtroende poäng
För att förbättra förtroendepoäng för ett visst svar till en användarfråga, du kan lägga till användarfrågan kunskapsbasen som en annan fråga på det svaret.
   
## <a name="confidence-score-differences"></a>Förtroende poäng skillnader
Förtroendepoäng av ett svar kan ändras negligibly mellan test och publicerade versionen av kunskapsbasen även om innehållet är samma. Det beror på att innehållet i testet och publicerade kunskapsbasen finns i olika Azure Search-index.

Se här hur [publicera](../How-To/publish-knowledge-base.md) åtgärden fungerar.


## <a name="no-match-found"></a>Ingen matchning hittades
När ingen bra matchning hittas av rankningen, förtroendepoäng 0,0 eller ”None” returneras och Standardsvaret är ”bra att hitta någon matchning i KB”. Du kan åsidosätta den här Standardsvar i koden bot eller program som anropar slutpunkten. Alternativt kan du kan också ange åsidosättning svaret i Azure och detta ändrar standardvärdet för alla kunskapsbaser som distribueras i en viss QnA Maker-tjänsten.

1. Gå till den [Azure-portalen](http://portal.azure.com) och navigera till den resursgrupp som representerar QnA Maker-tjänsten som du skapade.

2. Klicka för att öppna den **Apptjänst**.

    ![Få åtkomst till App service](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klicka på **programinställningar** och redigera den **DefaultAnswer** till önskad Standardsvaret. Klicka på **Spara**.

    ![Ändra Standardsvar](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Starta om App service

    ![QnA Maker appservice omstart](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Datakällor som stöds](./data-sources-supported.md)

## <a name="see-also"></a>Se också 

[Översikt över QnA Maker](../Overview/overview.md)
