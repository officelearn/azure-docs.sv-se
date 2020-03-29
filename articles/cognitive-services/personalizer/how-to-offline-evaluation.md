---
title: Så här utför du offlineutvärdering - Personalizer
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du använder offlineutvärdering för att mäta appens effektivitet och analysera din utbildningsloop.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: ce85c2d264b2b4849a4a36ed757150292fdf39f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622789"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analysera din inlärningsloop med en offlineutvärdering

Läs om hur du slutför en offlineutvärdering och förstår resultaten.

Offlineutvärderingar gör att du kan mäta hur effektiv Personalizer jämförs med programmets standardbeteende, lära dig vilka funktioner som bidrar mest till anpassning och upptäcker nya maskininlärningsvärden automatiskt.

Läs mer om [offlineutvärderingar.](concepts-offline-evaluation.md)

## <a name="prerequisites"></a>Krav

* En konfigurerad Personalizer-loop
* Personalizer-loopen måste ha en representativ mängd data - som en ballpark rekommenderar vi minst 50 000 händelser i sina loggar för meningsfulla utvärderingsresultat. Du kan också tidigare ha exporterat _utbildningsprincipfiler_ som du kan jämföra och testa i samma utvärdering.

## <a name="run-an-offline-evaluation"></a>Kör en offlineutvärdering

1. Leta reda på din Personalizer-resurs i [Azure-portalen.](https://azure.microsoft.com/free/)
1. Gå till avsnittet **Utvärderingar** i Azure-portalen och välj **Skapa utvärdering**.
    ![Gå till avsnittet **Utvärderingar** i Azure-portalen och välj **Skapa utvärdering**.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Konfigurera följande värden:

    * Ett utvärderingsnamn.
    * Start- och slutdatum - det här är datum som anger det dataintervall som ska användas i utvärderingen. Dessa data måste finnas i loggarna, enligt vad som anges i [datalagringsvärdet.](how-to-settings.md)
    * Optimeringsidentifiering inställd på **ja**.

    > [!div class="mx-imgBorder"]
    > ![Välj inställningar för offlineutvärdering](./media/offline-evaluation/create-an-evaluation-form.png)

1. Starta utvärderingen genom att välja **Ok**.

## <a name="review-the-evaluation-results"></a>Granska utvärderingsresultaten

Utvärderingar kan ta lång tid att köra, beroende på hur mycket data som ska bearbetas, antal utbildningsprinciper att jämföra och om en optimering begärdes.

När du är klar kan du välja utvärderingen i listan över utvärderingar och sedan välja **Jämför poängen för ditt program med andra potentiella inlärningsinställningar**. Välj den här funktionen när du vill se hur din nuvarande utbildningspolitik fungerar jämfört med en ny princip.

1. Granska resultaten av [utbildningsprinciperna](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy).

    > [!div class="mx-imgBorder"]
    > [![Granska utvärderingsresultat](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. Välj **Använd** om du vill tillämpa den princip som förbättrar modellen bäst för dina data.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [hur offlineutvärderingar fungerar](concepts-offline-evaluation.md).
