---
title: Skapa anpassade säkerhetsprinciper i Azure Security Center | Microsoft-dokument
description: Azure-anpassade principdefinitioner som övervakas av Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: memildin
ms.openlocfilehash: c709890ae6c57a001c6a0e9df4e973bd3bd24602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258268"
---
# <a name="using-custom-security-policies"></a>Använda anpassade säkerhetsprinciper

För att skydda dina system och din miljö genererar Azure Security Center säkerhetsrekommendationer. Dessa rekommendationer baseras på branschens bästa praxis, som ingår i den allmänna, standardsäkerhetsprincip som levereras till alla kunder. De kan också komma från Security Centers kunskap om bransch- och regleringsstandarder.

Med den här funktionen kan du lägga till egna *anpassade* initiativ. Du får sedan rekommendationer om din miljö inte följer de principer du skapar. Alla anpassade initiativ som du skapar visas tillsammans med de inbyggda initiativen i instrumentpanelen för regelefterlevnad som beskrivs i självstudien [Förbättra regelefterlevnaden.](security-center-compliance-dashboard.md)

Som beskrivs [här](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location) i Azure Policy-dokumentationen måste den vara en hanteringsgrupp eller en prenumeration när du anger en plats för ditt anpassade initiativ. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Så här lägger du till ett anpassat initiativ i din prenumeration 

1. Öppna sidan **Säkerhetsprincip** i Säkerhetscenters sidofält.

1. Välj en prenumeration eller hanteringsgrupp som du vill lägga till ett anpassat initiativ till.

    [![Välja en prenumeration som du ska skapa din anpassade princip för](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Du måste lägga till anpassade standarder på prenumerationsnivå (eller högre) för att de ska kunna utvärderas och visas i Security Center. 
    >
    > När du lägger till en anpassad standard tilldelas ett *initiativ* till det scopet. Vi rekommenderar därför att du väljer det bredaste scope som krävs för den tilldelningen.

1. Klicka på Lägg till ett anpassat **initiativ**under Dina anpassade initiativ på sidan Säkerhetsprincip.

    [![Klicka på **Lägg till ett anpassat initiativ**](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Följande sida visas:

    ![Skapa eller lägga till en princip](media/custom-security-policies/create-or-add-custom-policy.png)

1. På sidan Lägg till anpassade initiativ går du igenom listan över anpassade principer som redan har skapats i organisationen. Om du ser en som du vill tilldela din prenumeration klickar du på **Lägg till**. Om det inte finns ett initiativ i listan som uppfyller dina behov hoppar du över det här steget.

1. Så här skapar du ett nytt anpassat initiativ:

    1. Klicka på **Skapa ny**.
    1. Ange definitionens plats och namn.
    1. Markera de principer som ska inkluderas och klicka på **Lägg till**.
    1. Ange önskade parametrar.
    1. Klicka på **Spara**.
    1. På sidan Lägg till anpassade initiativ klickar du på Uppdatera och det nya initiativet visas som tillgängligt.
    1. Klicka på **Lägg till** och tilldela den till din prenumeration.

    > [!NOTE]
    > För att skapa nya initiativ krävs autentiseringsuppgifter för prenumerationsägare. Mer information om Azure-roller finns [i Behörigheter i Azure Security Center](security-center-permissions.md).

    Ditt nya initiativ träder i kraft och du kan se effekten på följande två sätt:

    * Välj **Regelefterlevnad**& i sidofältet i Säkerhetscenter. Instrumentpanelen för efterlevnad öppnas för att visa ditt nya anpassade initiativ tillsammans med de inbyggda initiativen.
    
    * Du börjar få rekommendationer om din miljö inte följer de principer som du har definierat.

1. Om du vill se de rekommendationer som följer av principen klickar du på **Rekommendationer** från sidofältet för att öppna rekommendationer sidan. Rekommendationerna visas med en "Anpassad" etikett och vara tillgängliga inom ungefär en timme.

    [![Anpassade rekommendationer](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar anpassade säkerhetsprinciper. 

För annat relaterat material, se följande artiklar: 

- [Översikt över säkerhetsprinciper](tutorial-security-policy.md)
- [En lista över de inbyggda säkerhetsprinciperna](security-center-policy-definitions.md)