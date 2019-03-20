---
title: Hur du använder negeras entiteter med en modell för konversationen Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder negeras entiteter med en konversation Learner modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 78dc759632c4fc3116a59ea1e5bc0b93200bca45
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58168195"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Hur du använder negeras entiteter med en konversation Learner modell

Den här självstudien visas ”Negatable”-egenskapen för entiteter.

## <a name="video"></a>Video

[![Negeras entiteter självstudiekursen förhandsversion](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudien Bot körs

    npm run tutorial-general

## <a name="details"></a>Information
Egenskapen ”Negatable” för en entitet kan du märka både normal (positiv) och negativa instanser av entiteten, lär baserat på positiva och negativa modeller och rensa värdet för en befintlig entitet. Entiteter med sina ”Negatable” egenskapsuppsättning kallas negeras entiteter i konversationen Leaner.

## <a name="steps"></a>Steg

Starta på startsidan i Webbgränssnittet.

### <a name="create-the-model"></a>Skapa modellen

1. Välj **nya modellen**.
2. Ange **NegatableEntity** för **namn**.
3. Välj **Skapa**.

### <a name="entity-creation"></a>Skapa en entitet

1. Välj **entiteter** i den vänstra panelen, sedan **ny entitet**.
2. Välj **anpassad tränas** för **entitetstypen**.
3. Ange **namn** för **entitetsnamn**.
4. Kontrollera **Negatable** så att användarna kan ange ett värde för entiteten eller säga något är *inte* en entitet värde tar därmed bort matchande entitet värdet.
5. Välj **Skapa**.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Skapa den första åtgärden

1. Välj **åtgärder** i den vänstra panelen, sedan **ny åtgärd**.
2. Ange **jag vet inte ditt namn.** för **Robotens svar...** .
3. Ange **namn** för **diskvalificera berättigar**.
4. Välj **Skapa**.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>Skapa den andra åtgärden

1. Välj **åtgärder** i den vänstra panelen, sedan **ny åtgärd**.
2. Ange **jag vet ditt namn. Det är $name.** för **Robotens svar...** .
3. Välj **Skapa**.

> [!NOTE]
> Den **namn** entitet automatiskt har lagts till som en **krävs entiteter** med referens i svar-uttryck.

Nu har du två åtgärder.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>Träna modellen

1. Välj **Train-dialogrutor** i den vänstra panelen, sedan **dialogrutan Ny träna**.
2. Ange **hello** för användarens uttryck i den vänstra chat-panelen.
3. Välj **poäng åtgärder**.
4. Välj **jag vet inte ditt namn.** från listan över åtgärder. Den: e percentilen är 100% som det enda giltiga åtgärder baserat på begränsningarna.
5. Ange **mitt namn är Frank** för användarens uttryck i den vänstra chat-panelen.
6. Markera **Frank** därefter **+ namn**. Negeras entiteter har två instanser: (+) plus lägger till eller skriver över värdet. (-) minus tar bort värdet.
7. Välj **poäng åtgärder**. Den **namn** entitet nu har definierats som **Frank** i minnet för den modellen, så den **jag vet ditt namn. Det är $name** åtgärden är tillgänglig.
8. Välj **jag vet ditt namn. Det är $name.** från listan över åtgärder.
9. Ange **mitt namn är inte Frank.** för användarens uttryck i den vänstra chat-panelen.
10. Markera **Frank** därefter **-namnet** att rensa värdet från den **namn** entitet.
11. Välj **poäng åtgärder**.
12. Välj **jag vet inte ditt namn.** från listan över åtgärder.
13. Ange **mitt namn är Susan.** för användarens tredje uttryck i den vänstra chat-panelen.
14. Markera **Susan** sedan **+ namn** 

![](../media/T06_training.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flera värden entiteter](./07-multi-value-entities.md)
