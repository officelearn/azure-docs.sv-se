---
title: Förtroende Score - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Förklarar förtroende poäng
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: c97bdb7e57275ebd1893bc28248c4ecc6b35c153
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354060"
---
# <a name="confidence-score"></a>Förtroendepoäng

En förtroende poäng Anger graden av matchning mellan användaren fråga och svar som returneras.

När en användarfråga matchas mot kunskapsbas innehåll, det kan finnas mer än ett svar som returneras. Svar returneras i en ranked minskande förtroende poäng.

En förtroende poäng är mellan 0 och 100.

|Poäng värde|Konfidensbedömning|
|--|--|
|100|En exakt matchning av användarfrågan och en KB-fråga|
|90|Hög exakthet - de flesta av ord matchar|
|40-60|Verkligt förtroende - viktiga ord matchar|
|10|Låg förtroende - viktiga ord matchar inte|
|0|Matchar inte word|


## <a name="similar-confidence-scores"></a>Liknande förtroende resultat
När flera svar har en liknande förtroende poäng, är det troligt att frågan var för Allmänt och därför matchade med samma sannolikhet med flera svar. Försök att strukturera din QnAs bättre så att alla frågor och svar om entiteten har en distinkta avsikt.


## <a name="improving-confidence-scores"></a>Förbättra förtroende resultat
För att förbättra förtroende poängen för ett visst svar till en användarfråga du kan lägga till användarfrågan kunskapsbasen som en annan fråga i svaret.
   
## <a name="confidence-score-differences"></a>Förtroende poäng skillnader
Förtroende poängen för ett svar kan ändra negligibly mellan test och publicerade versionen av kunskapsbasen även om innehållet är samma. Det beror på att innehållet i testet och publicerade kunskapsbasen finns i olika Azure Search-index.

Visas här hur [publicera](../How-To/publish-knowledge-base.md) åtgärden fungerar.


## <a name="no-match-found"></a>Ingen matchning hittades
När ingen bra matchning hittas av ranker, förtroende poängsättningen för 0,0 eller ”None” returneras och svaret som standard är ”ingen bra matchning hittades i KB”. Du kan åsidosätta detta Standardsvar i koden bot eller ett program anropar slutpunkten. Alternativt kan du även konfigurera åsidosättande svaret i Azure och detta ändrar standardvärdet för alla knowledge baser distribueras i en viss frågor och svar om Maker-tjänst.

1. Gå till den [Azure-portalen](http://portal.azure.com) och navigera till den resursgrupp som representerar frågor och svar om Maker-tjänst som du skapade.

2. Klicka för att öppna den **Apptjänst**.

    ![Åtkomst till apptjänst](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klicka på **programinställningar** och redigera den **DefaultAnswer** till önskade standard svaret. Klicka på **Spara**.

    ![Ändra standard svarstid](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Starta om din apptjänst

    ![Frågor och svar om Maker apptjänst omstart](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Datakällor som stöds](./data-sources-supported.md)

## <a name="see-also"></a>Se också 

[Frågor och svar om Maker översikt](../Overview/overview.md)
