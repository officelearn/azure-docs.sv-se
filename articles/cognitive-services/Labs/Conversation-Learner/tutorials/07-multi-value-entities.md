---
title: Använda entiteter med flera värden med en Conversation Learner modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder entiteter med flera värden med en Conversation Learner modell.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7911dd561da0f1e7b62b1b457ae5b059d5d54767
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704092"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Använda entiteter med flera värden med en Conversation Learner modell
I den här självstudien visas egenskapen multi-Value för entiteter.

## <a name="video"></a>Video

[![Förhands granskning av entiteter med flera värden](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Krav
Den här självstudien kräver att roboten för allmän självstudie körs

    npm run tutorial-general

## <a name="details"></a>Information
Entiteter med flera värden ackumulerar värden i en lista i stället för att lagra ett enda värde.  Dessa entiteter är användbara när användare kan ange mer än ett värde. Toppings på en pizza till exempel.

Entiteter som har marker ATS som flera värden har varje känd instans av entiteten som läggs till i en lista i robotens minne. Efterföljande igenkännings tillägg i enhetens värde i stället för att skrivas över.

## <a name="steps"></a>Steg

Starta på Start sidan i webb gränssnittet.

### <a name="create-the-model"></a>Skapa modellen

1. Välj **ny modell**.
2. Ange **MultiValueEntities** som **namn**.
3. Välj **Skapa**.

### <a name="entity-creation"></a>Skapa entitet

1. Välj **entiteter** i den vänstra panelen och sedan **ny entitet**.
2. Välj **anpassad utbildad** för **entitetstyp**.
3. Ange **toppings** som **enhets namn**.
4. Kontrol lera **multi-Value** för att enheten ska kunna ackumulera ett eller flera värden.
5. Kontrollera negationer.
6. Välj **Skapa**.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Skapa den första åtgärden

1. Välj **åtgärder** i den vänstra panelen och sedan **ny åtgärd**.
2. Ange **följande toppings: $toppings** för robotens **svar...** . Tecknet för inledande dollar indikerar en entitetsreferens.
3. Välj **Skapa**.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>Skapa den andra åtgärden

1. Välj **åtgärder** i den vänstra panelen och sedan **ny åtgärd**.
2. Ange **vilken toppings vill du använda?** för **robotens svar...** .
3. Ange **toppings** för **diskvalificerande rättigheter**.
4. Välj **Skapa**.

Nu har du två åtgärder.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>Träna modellen

1. Välj **träna dialog rutor** i den vänstra panelen och sedan **ny träna-dialog ruta**.
2. Ange **Hej** för användarens uttryck i den vänstra Chat-panelen.
3. Välj **Poäng åtgärder**.
4. Välj **vilken toppings vill du använda?** från listan åtgärder. Percentilen är 100% som den enda giltiga åtgärden baserat på begränsningarna.
5. Ange **ost och svamp** för användarens uttryck i den vänstra panelen i chat.
6. Markera **ost** och välj sedan **+ toppings**.
7. Markera **svamp** och välj sedan **+ toppings**.
8. Välj **Poäng åtgärder**.
9. Välj **här är dina toppings: $toppings** från listan åtgärder.
10. Ange **Lägg till peppar** för användarens nästa uttryck i den vänstra panelen i chat.
11. Markera **peppar** och välj sedan **+ toppings**.
12. Välj **Poäng åtgärder**.
13. Välj **här är dina toppings: $toppings** från listan åtgärder.
14. Ange **ta bort ost** för användarens tredje uttryck i den vänstra panelen i chat.
15. Markera **ost** och Select **-toppings**.
16. Välj **Poäng åtgärder**.
17. Välj **här är dina toppings: $toppings** från listan åtgärder.

![](../media/T07_training.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förtränade entiteter](./08-pre-trained-entities.md)
