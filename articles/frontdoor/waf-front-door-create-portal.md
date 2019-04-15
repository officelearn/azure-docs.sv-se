---
title: Skapa en brandväggsprincip för web-program för Azure åtkomsten med hjälp av Azure portal
titlesuffix: Azure web application firewall
description: Lär dig hur du skapar en princip för web application firewall (WAF) med hjälp av Azure portal.
services: frontdoor
documentationcenter: na
author: KumudD
manager: twooley
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/8/2019
ms.author: kumud;tyao
ms.openlocfilehash: 26db3a67c3efbd0ba2a5c58facd0c07175f7ed12
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564153"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>Skapa en WAF-princip för Azure åtkomsten med hjälp av Azure portal

Den här artikeln beskriver hur du skapar en grundläggande Azure web application firewall (WAF) princip och tillämpa den på en frontend-värd på Azure ytterdörren.

## <a name="prerequisites"></a>Förutsättningar

Skapa en profil för åtkomsten genom att följa anvisningarna som beskrivs i [snabbstarten: Skapa en profil för ytterdörren](quickstart-create-front-door.md). 

## <a name="create-a-waf-policy"></a>Skapa en WAF-princip

Skapa först en grundläggande WAF-princip med hanterade standard regeln ange (DRS) med hjälp av portalen. 

1. Längst upp till vänster på skärmen Välj **skapa en resurs**> Sök efter **WAF**> Välj **Brandvägg för webbaserade program (förhandsversion)** > Välj  **Skapa**.
2. I den **grunderna** fliken den **skapa en princip för WAF** sidan, ange eller Välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **granska + skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration            |Välj ditt ytterdörren prenumerationsnamn.|
    | Resursgrupp          |Välj din ytterdörren resursgruppens namn.|
    | Principnamn             |Ange ett unikt namn för din WAF-princip.|

   ![Skapa en WAF-princip](./media/waf-front-door-create-portal/basic.png)

3. I den **Association** fliken den **skapa en princip för WAF** väljer **Lägg till frontend-värd**, anger du följande inställningar och välj sedan **Lägg till**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Ytterdörren              | Välj ditt profilnamn för åtkomsten.|
    | Klientdelsvärd           | Välj namnet på din ytterdörren värden och sedan **Lägg till**.|
    
    > [!NOTE]
    > Om frontend-värden är kopplad till en WAF-princip, är det visas som nedtonade. Du måste först ta bort frontend-värden från den associerade principen och associerar frontend-värden till en ny WAF-princip.
1. Välj **granska + skapa**och välj sedan **skapa**.

## <a name="configure-waf-rules-optional"></a>Konfigurera WAF-regler (valfritt)

### <a name="change-mode"></a>Ändra läge

När du skapar en princip för WAF principen som standard WAF är i **identifiering** läge. I **identifiering** WAF-läge blockerar inte några förfrågningar, i stället begäranden WAF-regler för matchning loggas på WAF-loggar.
Om du vill se hur WAF i praktiken kan du ändra lägesinställningarna för från **identifiering** till **Dataförlustskydd**. I **Dataförlustskydd** läge, begär att matcha regler som har definierats i standard regeln ange (DRS) är blockerade och loggades WAF-loggar.

 ![Ändra WAF principläge](./media/waf-front-door-create-portal/policy.png)

### <a name="default-rule-set-drs"></a>Regeluppsättning för standard (DRS)

Azure-hanterade regeln standarduppsättningen är aktiverat som standard. Om du vill inaktivera en regel inom en regelgrupp Expandera regler i för regelgruppen, Välj den **kryssrutan** framför regeln antal och välj **inaktivera** på fliken ovan. Ändra typer av åtgärder för enskilda regler i regeln ange, markerar du kryssrutan framför regel-numret och välj sedan den **ändra åtgärd** flik ovan.

 ![Ändra WAF regeluppsättning](./media/waf-front-door-create-portal/managed.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure waf](waf-overview.md).
- Läs mer om [Azure ytterdörren](front-door-overview.md).




