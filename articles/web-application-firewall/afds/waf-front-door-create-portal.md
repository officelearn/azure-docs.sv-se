---
title: 'Självstudie: skapa WAF-princip för Azures frontend-Azure Portal'
description: I den här självstudien får du lära dig hur du skapar en WAF-princip (Web Application Firewall) med hjälp av Azure Portal.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: victorh
ms.openlocfilehash: 7c7ea5297276ed9a1d1f2ca8f4190997dcab57c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90602225"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>Självstudie: skapa en brand Väggs princip för webb program på Azure-frontend med hjälp av Azure Portal

I den här självstudien får du lära dig hur du skapar en grundläggande WAF-princip (Azure Web Application Firewall) och använder den på en klient dels värd i Azures front dörr.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en WAF-princip
> * Koppla den till en klient dels värd
> * Konfigurera WAF-regler

## <a name="prerequisites"></a>Krav

Skapa en profil för en frontend-dörr genom att följa anvisningarna i [snabb start: skapa en profil för front dörren](../../frontdoor/quickstart-create-front-door.md). 

## <a name="create-a-web-application-firewall-policy"></a>Skapa en brand Väggs princip för webb program

Börja med att skapa en grundläggande WAF-princip med hanterad standard regel uppsättning (DRS) med hjälp av portalen. 

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**>söker efter **WAF**>väljer **brand vägg för webbaserade program (för hands version)** > väljer **skapa**.
2. På fliken **grundläggande** på sidan **skapa en WAF-princip** anger eller väljer du följande information, accepterar standardinställningarna för återstående inställningar och väljer sedan **Granska + skapa**:

    | Inställningen                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration            |Välj prenumerations namnet på din front dörr.|
    | Resursgrupp          |Välj resurs grupps namnet för front dörren.|
    | Principnamn             |Ange ett unikt namn för din WAF-princip.|

   ![Skapa en WAF-princip](../media/waf-front-door-create-portal/basic.png)

3. På fliken **Association** på sidan **skapa en WAF-princip** väljer du **Lägg till klient dels värd**, anger följande inställningar och väljer sedan **Lägg till**:

    | Inställningen                 | Värde                                              |
    | ---                     | ---                                                |
    | Front dörr              | Välj profil namnet för din klient del.|
    | Klient dels värd           | Välj namnet på värddatorn för din frontend-dörr och välj sedan **Lägg till**.|
    
    > [!NOTE]
    > Om klient dels värden är kopplad till en WAF-princip visas den som nedtonad. Du måste först ta bort klient dels värden från den associerade principen och sedan associera klient dels värden på nytt till en ny WAF-princip.
1. Välj **Granska + skapa** och välj sedan **Skapa**.

## <a name="configure-web-application-firewall-rules-optional"></a>Konfigurera brand Väggs regler för webb program (valfritt)

### <a name="change-mode"></a>Ändra läge

När du skapar en WAF-princip är standard principen för WAF i **identifierings** läge. I **identifierings** läge blockerar WAF inte några begär Anden, i stället loggas begär Anden som matchar WAF-reglerna på WAF-loggar.
Om du vill se WAF i praktiken kan du ändra läges inställningarna från **identifiering** till **förebyggande**. I **skydds** läge blockeras och loggas begär Anden som matchar regler som definieras i standard regel UPPSÄTTNINGEN (DRS) och loggas på WAF-loggar.

 ![Ändra WAF princip läge](../media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>Anpassade regler

Du kan skapa en anpassad regel genom att välja **Lägg till anpassad regel** under avsnittet **anpassade regler** . Då startas sidan anpassad regel konfiguration. Nedan visas ett exempel på hur du konfigurerar en anpassad regel för att blockera en begäran om frågesträngen innehåller **blockme**.

![Anpassade regler](../media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>Standard regel uppsättning (DRS)

Azure-hanterad standard regel uppsättning är aktiverat som standard. Om du vill inaktivera en enskild regel i en regel grupp expanderar du reglerna inom den regel gruppen, markerar **kryss rutan** framför regel numret och väljer **inaktivera** på fliken ovan. Om du vill ändra åtgärds typer för enskilda regler i regel uppsättningen markerar du kryss rutan framför regel numret och väljer sedan fliken **ändra åtgärd** ovan.

 ![Ändra WAF regel uppsättning](../media/waf-front-door-create-portal/managed2.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen och alla relaterade resurser när de inte längre behövs.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azures front dörr](../../frontdoor/front-door-overview.md)