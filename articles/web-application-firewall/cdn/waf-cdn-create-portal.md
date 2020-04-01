---
title: 'Självstudiekurs: Skapa WAF-princip för Azure CDN - Azure-portal'
description: I den här självstudien får du lära dig hur du skapar en WAF-princip (Web Application Firewall) på Azure CDN med Azure-portalen.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 738be1361bfbd944575abceb08781b241336f6e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79485596"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Självstudiekurs: Skapa en WAF-princip på Azure CDN med Azure-portalen

Den här självstudien visar hur du skapar en grundläggande WAF-princip (Azure Web Application Firewall) och tillämpar den på en slutpunkt på CDN (Azure Content Delivery Network).

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en WAF-princip
> * Associera den med en CDN-slutpunkt
> * Konfigurera WAF-regler

## <a name="prerequisites"></a>Krav

Skapa en Azure CDN-profil och slutpunkt genom att följa instruktionerna i [Snabbstart: Skapa en Azure CDN-profil och slutpunkt](../../cdn/cdn-create-new-endpoint.md). 

## <a name="create-a-web-application-firewall-policy"></a>Skapa en brandvägg för webbprogram

Skapa först en grundläggande WAF-princip med en hanterad standardregeluppsättning (DRS) med hjälp av portalen.

1. Välj **Skapa en resurs**>söka efter **WAF->** väljer brandvägg för **webbprogram** > längst upp till vänster på **skärmen.**
2. På fliken **Grunderna** på sidan **Skapa en WAF-princip** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Granska + skapa:**

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Policy för            |Välj Azure CDN (förhandsversion).|
    | Prenumeration            |Välj ditt prenumerationsnamn för Ytterdörren.|
    | Resursgrupp          |Välj namn på resursgrupp för frontdörren.|
    | Principnamn             |Ange ett unikt namn för WAF-principen.|

   ![Skapa en WAF-princip](../media/waf-cdn-create-portal/basic.png)

3. På fliken **Association** på sidan **Skapa en WAF-princip** väljer du **Lägg till CDN-slutpunkt,** anger följande inställningar och väljer sedan **Lägg till:**

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | CDN-profil              | Välj cdn-profilnamn.|
    | Slutpunkt           | Välj namnet på slutpunkten och välj sedan **Lägg till**.|
    
    > [!NOTE]
    > Om slutpunkten är associerad med en WAF-princip visas den nedtonad. Du måste först ta bort slutpunkten från den associerade principen och sedan associera slutpunkten igen till en ny WAF-princip.
1. Välj **Granska + skapa**och välj sedan **Skapa**.

## <a name="configure-web-application-firewall-policy-optional"></a>Konfigurera brandväggsprincipen för webbprogram (valfritt)

### <a name="change-mode"></a>Ändra läge

Som standard är WAF-principen i *identifieringsläge* när du skapar en WAF-princip. I *identifieringsläge* blockerar WAF inga begäranden. I stället loggas begäranden som matchar WAF-reglerna på WAF-loggar.

Om du vill se WAF i aktion kan du ändra lägesinställningarna från *Identifiering* till *Förebyggande*. I *förebyggande* läge blockeras och loggas begäranden som matchar regler som definieras i DRS (Default Rule Set) och loggas i WAF-loggar.

 ![Ändra WAF-principläge](../media/waf-cdn-create-portal/policy.png)

### <a name="custom-rules"></a>Anpassade regler

Om du vill skapa en anpassad regel väljer du **Lägg till anpassad regel** under avsnittet Anpassade **regler.** Då öppnas den anpassade regelkonfigurationssidan. Det finns två typer av anpassade regler: **matchningsregel** och **hastighetsbegränsningsregel.**

Följande skärmbild visar en anpassad matchningsregel för att blockera en begäran om frågesträngen innehåller **värdeblocket**.

![Ändra WAF-principläge](../media/waf-cdn-create-portal/custommatch.png)

Hastighetsbegränsningsregler kräver ytterligare två fält: **Hastighetsgräns varaktighet** och **tröskelvärde för hastighetsgräns (begäranden)** som visas i följande exempel:

![Ändra WAF-principläge](../media/waf-cdn-create-portal/customrate.png)

### <a name="default-rule-set-drs"></a>Standardregeluppsättning (DRS)

Den hanterade standardregeluppsättningen för Azure är aktiverad som standard. Om du vill inaktivera en enskild regel i en regelgrupp expanderar du reglerna i den regelgruppen, markerar kryssrutan framför regelnumret och väljer **Inaktivera** på fliken ovan. Om du vill ändra åtgärdstyper för enskilda regler i regeluppsättningen markerar du kryssrutan framför regelnumret och väljer sedan fliken **Ändra åtgärd** ovan.

 ![Ändra WAF-regeluppsättning](../media/waf-cdn-create-portal/managed2.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Brandvägg för Azure Web Application](../overview.md)
