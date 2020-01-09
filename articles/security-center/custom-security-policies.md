---
title: Skapa anpassade säkerhets principer i Azure Security Center | Microsoft Docs
description: Azures anpassade princip definitioner övervakas av Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 34dbace304ccf70891ef53dd768de60d87e26967
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666643"
---
# <a name="using-custom-security-policies-preview"></a>Använda anpassade säkerhets principer (förhands granskning)

Azure Security Center skapar säkerhets rekommendationer för att skydda dina system och miljöer. De här rekommendationerna baseras på bransch bästa praxis, som ingår i den allmänna, Standard säkerhets policy som tillhandahålls alla kunder. De kan också komma från Security Centers kunskaper om bransch-och reglerings standarder.

Med den här förhands gransknings funktionen kan du lägga till dina egna *anpassade* initiativ. Du får sedan rekommendationer om din miljö inte följer de principer som du skapar. Alla anpassade initiativ som du skapar visas tillsammans med de inbyggda initiativen på instrument panelen för kontroll av efterlevnad som beskrivs i självstudien för att [förbättra din efterlevnad](security-center-compliance-dashboard.md).

Som beskrivs [här](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location) i Azure policy-dokumentationen, när du anger en plats för ditt eget initiativ, måste det vara en hanterings grupp eller en prenumeration. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Lägga till ett anpassat initiativ i din prenumeration 

1. Öppna sidan **säkerhets princip** från Security Center marginal List.

1. Välj en prenumeration eller hanterings grupp till vilken du vill lägga till ett anpassat initiativ.

    [![att välja en prenumeration som du vill skapa en anpassad princip för](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Du måste lägga till anpassade standarder på prenumerations nivån (eller högre) för att de ska kunna utvärderas och visas i Security Center. 
    >
    > När du lägger till en anpassad standard tilldelar den ett *initiativ* till det omfånget. Vi rekommenderar därför att du väljer det bredaste definitions område som krävs för tilldelningen.

1. På sidan säkerhets princip under anpassade initiativ (för hands version) klickar du på **Lägg till ett anpassat initiativ**.

    [![på * * Lägg till ett anpassat initiativ * *](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Följande sida visas:

    ![Skapa eller Lägg till en princip](media/custom-security-policies/create-or-add-custom-policy.png)

1. På sidan Lägg till anpassade initiativ granskar du listan över anpassade principer som redan har skapats i din organisation. Om du ser en som du vill tilldela till din prenumeration klickar du på **Lägg till**. Om det inte finns något initiativ i listan som uppfyller dina behov hoppar du över det här steget.

1. Så här skapar du ett nytt anpassat initiativ:

    1. Klicka på **Skapa nytt**.
    1. Ange definitionens plats och namn.
    1. Välj de principer som ska inkluderas och klicka på **Lägg till**.
    1. Ange önskade parametrar.
    1. Klicka på **Spara**.
    1. På sidan Lägg till anpassade initiativ klickar du på uppdatera och ditt nya initiativ visas som tillgängligt.
    1. Klicka på **Lägg till** och tilldela den till din prenumeration.

    > [!NOTE]
    > Om du skapar nya initiativ krävs autentiseringsuppgifter för Prenumerationens ägare. Mer information om Azure-roller finns [i behörigheter i Azure Security Center](security-center-permissions.md).

    Ditt nya initiativ börjar gälla och du kan se effekten på följande två sätt:

    * Välj regelefterlevnad i Security Center sid panelen under princip **& efterlevnad.** Instrument panelen för efterlevnad öppnas för att visa ditt nya anpassade initiativ tillsammans med de inbyggda initiativen.
    
    * Du börjar få rekommendationer om din miljö inte följer de principer som du har definierat.

1. Om du vill se de rekommendationer som finns i principen klickar du på **rekommendationer** från sid panelen för att öppna sidan rekommendationer. Rekommendationerna visas med etiketten "anpassad" och är tillgänglig inom cirka en timme.

    [![anpassade rekommendationer](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar anpassade säkerhets principer. 

Information om annat relaterat material finns i följande artiklar: 

- [Översikt över säkerhets principer](tutorial-security-policy.md)
- [En lista över inbyggda säkerhets principer](security-center-policy-definitions.md)