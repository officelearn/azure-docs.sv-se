---
title: Fördefinierade avsikter
titleSuffix: Azure Cognitive Services
description: LUIS innehåller en uppsättning fördefinierade intentioner för att snabbt lägga till vanliga, konversationsanpassade användarscenarier.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: abdbd0672fbb29f3936e4bda6f71e41c0f287329
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221233"
---
# <a name="add-prebuilt-intents-for-common-intents"></a>Lägg till fördefinierade avsikter för vanliga avsikter 

LUIS innehåller en uppsättning fördefinierade avsikter från fördefinierade domäner för att snabbt lägga till vanliga avsikter och yttranden. Det här är ett snabbt och enkelt sätt att lägga till funktioner till konversationsanpassade klientappen utan att behöva utforma modeller för dessa funktioner. 

## <a name="add-a-prebuilt-intent"></a>Lägg till en fördefinierade avsikt

1. På den **Mina appar** väljer du din app. Din app öppnas den **skapa** i appen. 

1. På den **avsikter** väljer **Lägg till fördefinierade avsikt** från verktygsfältet ovanför listan avsikter. 

1. Välj den **Utilities.Cancel** avsikt från popup-dialogruta. 

    ![Lägg till fördefinierade avsikt](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Välj den **klar** knappen.

## <a name="train-and-test"></a>Träna och testa

1. När syftet har lagts till, träna appen genom att välja **träna** i övre högra verktygsfältet. 

1. Testa nya avsikten genom att välja **Test** i det högra verktygsfältet. 

1. Ange uttryck för att avbryta i textrutan:

    |Testa uttryck|Förutsägelseresultat|
    |--|:--|
    |Jag vill du avbryta min flygning.|0.67|
    |Avbryta köpet från.|0.52|
    |Ställa in mötet.|0.56|

    ![Testa fördefinierade avsikt](./media/luis-prebuilt-intents/test.png)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Fördefinierade entiteter](./luis-prebuilt-entities.md)
