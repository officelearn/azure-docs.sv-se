---
title: 'Självstudiekurs: Skapa WAF-princip för Azure Front Door - Azure-portal'
description: I den här självstudien får du lära dig hur du skapar en WAF-princip (Web Application Firewall) med hjälp av Azure-portalen.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: be66a93ea4a518b26d973d222caf58e73b6986a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79475849"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Självstudiekurs: Skapa en brandvägg för webbprogram på Azure Front Door med Azure-portalen

Den här självstudien visar hur du skapar en grundläggande WAF-princip (Azure Web Application Firewall) och tillämpar den på en frontend-värd på Azure Front Door.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en WAF-princip
> * Associera den med en frontend värd
> * Konfigurera WAF-regler

## <a name="prerequisites"></a>Krav

Skapa en profil för ytterdörren genom att följa instruktionerna som beskrivs i [Snabbstart: Skapa en profil för ytterdörren](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Skapa en brandvägg för webbprogram

Skapa först en grundläggande WAF-princip med hanterad standardregeluppsättning (DRS) med hjälp av portalen. 

1. På den övre vänstra sidan av skärmen väljer du **Skapa en resurs**>söka efter **WAF->** väljer brandvägg för **webbprogram (förhandsgranskning)** > väljer **Skapa**.
2. På fliken **Grunderna** på sidan **Skapa en WAF-princip** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Granska + skapa:**

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration            |Välj ditt prenumerationsnamn för Ytterdörren.|
    | Resursgrupp          |Välj namn på resursgrupp för frontdörren.|
    | Principnamn             |Ange ett unikt namn för WAF-principen.|

   ![Skapa en WAF-princip](../media/waf-front-door-create-portal/basic.png)

3. På fliken **Association** på sidan **Skapa en WAF-princip** väljer du **Lägg till klientdelsvärd,** anger följande inställningar och väljer sedan **Lägg till:**

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Ytterdörren              | Välj ditt profilnamn för ytterdörren.|
    | Frontend värd           | Välj namnet på din dörrvärd och välj sedan **Lägg till**.|
    
    > [!NOTE]
    > Om frontend-värden är associerad med en WAF-princip visas den som nedtonad. Du måste först ta bort frontend-värden från den associerade principen och sedan associera klientdelens värd för en ny WAF-princip.
1. Välj **Granska + skapa**och välj sedan **Skapa**.

## <a name="configure-web-application-firewall-rules-optional"></a>Konfigurera brandväggsregler för webbprogram (valfritt)

### <a name="change-mode"></a>Ändra läge

När du skapar en WAF-princip är den standardprincipen för WAF i **identifieringsläge.** I **identifieringsläge** blockerar WAF inte några begäranden, i stället loggas begäranden som matchar WAF-reglerna på WAF-loggar.
Om du vill se WAF i aktion kan du ändra lägesinställningarna från **Identifiering** till **Förebyggande**. I **förebyggande** läge blockeras och loggas begäranden som matchar regler som definieras i DRS (Default Rule Set) och loggas i WAF-loggar.

 ![Ändra WAF-principläge](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Anpassade regler

Du kan skapa en anpassad regel genom att välja **Lägg till anpassad regel** under avsnittet Anpassade **regler.** Detta startar den anpassade regelkonfigurationssidan. Nedan visas ett exempel på att konfigurera en anpassad regel för att blockera en begäran om frågesträngen innehåller **blockme**.

![Ändra WAF-principläge](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Standardregeluppsättning (DRS)

Azure-hanterad standardregeluppsättning är aktiverad som standard. Om du vill inaktivera en enskild regel i en regelgrupp expanderar du reglerna i den regelgruppen, markerar **kryssrutan** framför regelnumret och väljer **Inaktivera** på fliken ovan. Om du vill ändra åtgärdstyper för enskilda regler i regeluppsättningen markerar du kryssrutan framför regelnumret och väljer sedan fliken **Ändra åtgärd** ovan.

 ![Ändra WAF-regeluppsättning](../media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure Web Application Firewall](../overview.md)
> [Läs mer om Azure Front Door](../../frontdoor/front-door-overview.md)