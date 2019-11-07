---
title: Använd HTTPS med Azure CDN Standard regel motor | Microsoft Docs
description: Med standard regel motorn kan du anpassa hur HTTP-begäranden hanteras av Azure CDN från Microsoft, till exempel blockera leverans av vissa typer av innehåll, definiera en princip för cachelagring och ändra HTTP-huvuden.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: b24c4a04e0c02258a918ee075066d90c22ea0c75
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615928"
---
# <a name="azure-cdn-standard-rules-engine"></a>Azure CDN Standard regel motor

> [!NOTE]
> Standard regel motor är endast tillgänglig för Azure CDN från Microsoft. 

Med den Azure CDN Standard regel motorn kan du anpassa hur HTTP-begäranden hanteras. Du kan till exempel framtvinga innehålls leverans över vissa protokoll, definiera en princip för cachelagring eller ändra ett HTTP-huvud. I den här självstudien visas hur du skapar en regel som automatiskt omdirigerar dina användare till HTTPS. 


## <a name="tutorial"></a>Självstudier

1. På sidan **CDN-profil** på Azure CDN från Microsoft-profiler väljer du den slut punkt som du vill konfigurera regler för.
  
2. Välj fliken **regel motor** till vänster.
   
    Bladet regel motor visas med den globala regeln. 
   
    [Sidan nya regler för ![CDN](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > Ordningen för hur flera regler visas påverkar hur de hanteras. En efterföljande regel kan åsidosätta de åtgärder som anges av en tidigare regel.
   >

3. Klicka på knappen **Lägg till regel** och ange ett regel namn. Regel namn måste börja med en bokstav och får bara innehålla siffror och bokstäver.

4. Identifiera vilken typ av förfrågningar som regeln gäller för. Använd List rutan för att välja matchnings villkor för **begäran protokoll** och Använd värdet **är lika med** **http**.
   
   [villkor för att matcha ![CDN-regel](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > Det finns flera matchnings villkor i list rutan. En detaljerad lista över matchnings villkor finns i [regel motor matchnings villkor](cdn-standard-rules-engine-match-conditions.md).
   
5. Välj den åtgärd som ska tillämpas på de identifierade begär Anden. Använd List rutan för att välja åtgärden för **URL-omdirigering** och Använd det **påträffade värdet (302)** för typ och **https** för protokoll. Lämna alla andra fält tomma för att använda de inkommande värdena.
   
   [åtgärd för ![CDN-regel](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Flera åtgärder är tillgängliga i list rutan. En detaljerad lista över åtgärder finns i [regel motor åtgärder](cdn-standard-rules-engine-actions.md).

6. Välj **Spara** för att spara den nya regeln.  Den nya regeln kommer nu att distribueras.
   
   > [!IMPORTANT]
   > Regel ändringar kan ta upp till 15 minuter innan de sprids via Azure CDN.
   >
   

## <a name="see-also"></a>Se även

- [Översikt över Azure CDN](cdn-overview.md)
- [Motor referens för standard regler](cdn-standard-rules-engine-reference.md)
- [Standard regel motor matchnings villkor](cdn-standard-rules-engine-match-conditions.md)
- [Motor åtgärder för standard regler](cdn-standard-rules-engine-actions.md)
