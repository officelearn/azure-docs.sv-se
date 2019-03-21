---
title: Hur du använder flera värden entiteter med en modell för konversationen Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder flera värden entiteter med en konversation Learner modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 07550ed9c78aa584e55403d225eef4bcff33b057
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58167430"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Hur du använder flera värden entiteter med en modell för Konversationsdeltagare
Den här kursen visar egenskapen flera värden för entiteter.

## <a name="video"></a>Video

[![Flera värden entiteter självstudiekursen förhandsversion](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudien Bot körs

    npm run tutorial-general

## <a name="details"></a>Information
Flera värden entiteter ackumuleras värden i en lista i stället för att lagra ett enstaka värde.  De här entiteterna är användbara när användarna kan ange fler än ett värde. Toppings på en platt till exempel.

Entiteter markerats som flera värden har varje erkända instans av entiteten läggas till i en lista i Robotens minnet. Efterföljande igenkänning läggs till dess värde i stället skriva över.

## <a name="steps"></a>Steg

Starta på startsidan i Webbgränssnittet.

### <a name="create-the-model"></a>Skapa modellen

1. Välj **nya modellen**.
2. Ange **MultiValueEntities** för **namn**.
3. Välj **Skapa**.

### <a name="entity-creation"></a>Skapa en entitet

1. Välj **entiteter** i den vänstra panelen, sedan **ny entitet**.
2. Välj **anpassad tränas** för **entitetstypen**.
3. Ange **toppings** för **entitetsnamn**.
4. Kontrollera **med flera värden** för att aktivera entiteten ackumuleras ett eller flera värden.
5. Kontrollera **negeras**.
6. Välj **Skapa**.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Skapa den första åtgärden

1. Välj **åtgärder** i den vänstra panelen, sedan **ny åtgärd**.
2. Ange **här är din toppings: $toppings** för **Robotens svar...** . Ledande dollartecknet anger en entitetsreferens.
3. Välj **Skapa**.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>Skapa den andra åtgärden

1. Välj **åtgärder** i den vänstra panelen, sedan **ny åtgärd**.
2. Ange **vilka toppings vill du?** för **Robotens svar...** .
3. Ange **toppings** för **diskvalificera berättigar**.
4. Välj **Skapa**.

Nu har du två åtgärder.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>Träna modellen

1. Välj **Train-dialogrutor** i den vänstra panelen, sedan **dialogrutan Ny träna**.
2. Ange **Hej** för användarens uttryck i den vänstra chat-panelen.
3. Välj **poäng åtgärder**.
4. Välj **vilka toppings vill du?** från listan över åtgärder. Den: e percentilen är 100% som det enda giltiga åtgärder baserat på begränsningarna.
5. Ange **ost och svamp** för användarens uttryck i den vänstra chat-panelen.
6. Markera **ost** därefter **+ toppings**.
7. Markera **svamp** därefter **+ toppings**.
8. Välj **poäng åtgärder**.
9. Välj **här är din toppings: $toppings** från listan över åtgärder.
10. Ange **lägga till peppar** för användarens nästa uttryck i den vänstra chat-panelen.
11. Markera **peppar** därefter **+ toppings**.
12. Välj **poäng åtgärder**.
13. Välj **här är din toppings: $toppings** från listan över åtgärder.
14. Ange **ta bort OST** för användarens tredje uttryck i den vänstra chat-panelen.
15. Markera **ost** därefter **-toppings**.
16. Välj **poäng åtgärder**.
17. Välj **här är din toppings: $toppings** från listan över åtgärder.

![](../media/T07_training.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förtränade entiteter](./08-pre-trained-entities.md)
