---
title: Förbättra kunskapsbasen - QnA Maker
description: Förbättra kvaliteten på din kunskapsbas med aktivt lärande. Granska, acceptera eller avvisa, lägg till utan att ta bort eller ändra befintliga frågor.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 33b3c547b0aea9a1e235bf8a05d01aa16b468a71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071137"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Använda aktiv inlärning för att förbättra kunskapsbasen

[Aktivt lärande](../Concepts/active-learning-suggestions.md) gör att du kan förbättra kvaliteten på din kunskapsbas genom att föreslå alternativa frågor. Användarinlämningar beaktas och visas som förslag i listan över alternativa frågor. Du har flexibiliteten att antingen lägga till dessa förslag som alternativa frågor eller avvisa dem.

Din kunskapsbas ändras inte automatiskt. För att en ändring ska börja gälla måste du acceptera förslagen. Dessa förslag lägger till frågor men ändrar inte eller tar bort befintliga frågor.


## <a name="upgrade-runtime-version-to-use-active-learning"></a>Uppgradera körningsversionen för att använda aktiv inlärning

Active Learning stöds i runtime version 4.4.0 och senare. Om kunskapsbasen skapades i en tidigare version [uppgraderar du körningen](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) så att den här funktionen används.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Aktivera aktiv inlärning för alternativa frågor

Aktiv inlärning är inaktiverad som standard. Aktivera den för att se föreslagna frågor. När du har aktiverat aktiv inlärning måste du skicka information från klientappen till QnA Maker. Mer information finns i [Arkitekturflöde för att använda GenerateAnswer- och Train API:er från en robot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Välj **Publicera** om du vill publicera kunskapsbasen. Aktiva utbildningsfrågor samlas endast in från endpointen GenerateAnswer API prediction. Frågorna till testfönstret i QnA Maker-portalen påverkar inte aktiv inlärning.

1. Om du vill aktivera aktiv inlärning i QnA Maker-portalen går du till det övre högra hörnet och väljer ditt **namn**, går till [**Serviceinställningar**](https://www.qnamaker.ai/UserSettings).

    ![Aktivera aktiva inlärnings föreslagna frågealternativ från sidan Tjänstinställningar. Välj ditt användarnamn i menyn längst upp till höger och välj sedan Serviceinställningar.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Hitta tjänsten QnA Maker och växla **sedan active learning**.

    > [!div class="mx-imgBorder"]
    > [![Växla på active learning-funktionen på sidan Tjänstinställningar. Om du inte kan växla funktionen kan du behöva uppgradera tjänsten.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > Den exakta versionen på föregående bild visas endast som ett exempel. Din version kan vara annorlunda.

    När **Active Learning** är aktiverat föreslår kunskapsbasen nya frågor med jämna mellanrum baserat på användarinskickade frågor. Du kan inaktivera **Aktiv inlärning** genom att växla inställningen igen.

## <a name="review-suggested-alternate-questions"></a>Granska föreslagna alternativa frågor

[Granska alternativa föreslagna frågor](improve-knowledge-base.md) på sidan **Redigera** i varje kunskapsbas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en kunskapsbas](./manage-knowledge-bases.md)