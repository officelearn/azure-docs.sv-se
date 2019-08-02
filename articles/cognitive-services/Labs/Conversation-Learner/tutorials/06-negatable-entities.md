---
title: Använda negation bara entiteter med en Conversation Learner modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder negation bara entiteter med en Conversation Learner modell.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 4edbfc396d4ab32b991b084a7b738f04b5205418
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704108"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Använda negation bara entiteter med en Conversation Learner modell

Den här självstudien visar egenskapen "Negationable" för entiteter.

## <a name="video"></a>Video

[![Förhands granskning av negation bara entiteter](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Krav
Den här självstudien kräver att roboten för allmän självstudie körs

    npm run tutorial-general

## <a name="details"></a>Information
Med egenskapen "Negationable" för en entitet kan du namnge både normala (positiva) och negativa instanser av entiteten, genom att underbygga på positiva och negativa modeller och rensa värdet för en befintlig entitet. Entiteter med deras "Negationable"-egenskaps uppsättning kallas för att negera entiteter i konversations Snålaren.

## <a name="steps"></a>Steg

Starta på Start sidan i webb gränssnittet.

### <a name="create-the-model"></a>Skapa modellen

1. Välj **ny modell**.
2. Ange **NegatableEntity** som **namn**.
3. Välj **Skapa**.

### <a name="entity-creation"></a>Skapa entitet

1. Välj **entiteter** i den vänstra panelen och sedan **ny entitet**.
2. Välj **anpassad utbildad** för **entitetstyp**.
3. Ange **namnet** på **entitetsnamnet**.
4. Kontrol lera negationer för att ge användarna möjlighet att ange ett enhets värde, eller säg att något *inte* är ett enhets värde och därmed tar bort det matchande enhet svärdet.
5. Välj **Skapa**.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Skapa den första åtgärden

1. Välj **åtgärder** i den vänstra panelen och sedan **ny åtgärd**.
2. Skriv **Jag vet inte ditt namn.** för **robotens svar...** .
3. Ange **namnet** på **kvalificerande rättigheter**.
4. Välj **Skapa**.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>Skapa den andra åtgärden

1. Välj **åtgärder** i den vänstra panelen och sedan **ny åtgärd**.
2. Ange **att jag känner till ditt namn. Det är $name.** för **robotens svar...** .
3. Välj **Skapa**.

> [!NOTE]
> Entiteten **Name** lades automatiskt till som **obligatoriska entiteter** efter referens i svars uttryck.

Nu har du två åtgärder.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>Träna modellen

1. Välj **träna dialog rutor** i den vänstra panelen och sedan **ny träna-dialog ruta**.
2. Ange **Hej** för användarens uttryck i den vänstra Chat-panelen.
3. Välj **Poäng åtgärder**.
4. Välj **Jag vet inte ditt namn.** från listan åtgärder. Percentilen är 100% som den enda giltiga åtgärden baserat på begränsningarna.
5. Ange **mitt namn är Frank** för användarens uttryck i den vänstra Chat-panelen.
6. Markera **Frank** och välj sedan **+ Name**. Negation bara entiteter har två instanser: (+) plus lägger till eller skriver över värdet. (-) minus tar bort värdet.
7. Välj **Poäng åtgärder**. Entiteten **namn** definieras nu som **Frank** i modellens minne, så jag känner till **ditt namn. Det är $name** åtgärden är tillgänglig.
8. Välj **jag känner till ditt namn. Det är $name.** från listan åtgärder.
9. Ange **mitt namn är inte Frank.** för användarens uttryck i den vänstra Chat-panelen.
10. Markera **Frank** och välj sedan **namn** för att rensa värdet från entiteten **namn** .
11. Välj **Poäng åtgärder**.
12. Välj **Jag vet inte ditt namn.** från listan åtgärder.
13. Ange **mitt namn är Susan.** för användarens tredje uttryck i den vänstra panelen i chat.
14. Markera **Susan** och **+ namn** 

![](../media/T06_training.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Entiteter med flera värden](./07-multi-value-entities.md)
