---
title: Privileged Identity Management resurser för Azure - berättigade tilldelningar och synlighet | Microsoft Docs
description: Beskriver hur du tilldelar medlemmar som är berättigade till resursroller.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 273b06c91d68a764fe814374c0eca6ed1698cc2e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="eligible-assignments-and-resource-visibility"></a>Berättigad tilldelningar och synlighet

PIM för Azure-resursroller ger förbättrad säkerhet för organisationer med kritiska Azure resoruces. PIM ger resurs administratörer möjligheten att tilldela medlemmar som kvalificerade resursroller. Läs mer om tilldelning av olika typer och tillstånd för Azure-resursroller nedan. 

## <a name="assignment-types"></a>Typer för tilldelning

PIM för Azure-resurser innehåller två distinkta tilldelning typer:

- Berättigad
- Active

Berättigad tilldelningar kräver medlem i rollen att utföra en åtgärd för att använda rollen. Dessa åtgärder kan omfatta efterföljande en Multi-Factor Authentication-kontroll, vilket ger en motivering och begära godkännande från avsedda godkännare.

Aktiva tilldelningar kräver inte medlemmen att utföra alla åtgärder för att använda rollen. Medlemmar som är tilldelad som aktiv har de rättigheter som tillhandahålls av rollen när som helst.

## <a name="assignment-duration"></a>Varaktighet för tilldelning

Resurs-administratörer kan välja ett av två alternativ för varje typ av tilldelning när du konfigurerar PIM-inställningarna för en roll. Dessa alternativ blir standard maximal varaktighet när medlem tilldelas rollen i PIM.

- Tillåt permanent berättigade tilldelning
- Tillåt active permanent tilldelning

eller

- Berättigad tilldelningar efter att gälla
- Aktiva tilldelningar efter att gälla

Om en resursadministratör väljer att ”Tillåt permanent berättigade tilldelning” och/eller ”Tillåt permanent active tilldelning” alla administratörer som tilldelas resursen medlemmar har möjligheten att tilldela permanent medlemskap.

Om du väljer ”berättigade tilldelningar för upphör att gälla efter” och/eller ”active tilldelningar för upphör att gälla efter” kan kontroll över livscykeln för tilldelningen genom att kräva att alla tilldelningar har ett angivet datum för start- och slutdatum.

>[!NOTE] 
>Alla tilldelningar med det angivna slutdatumet kan förnyas av resurs-administratörer och medlemmar kan initiera självservicebegäranden till [utöka eller förnya tilldelningar](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Tillstånd för tilldelning

PIM för Azure-resurser har två distinkta tilldelning tillstånd som visas på fliken ”Active roller” i min roller, roller och medlemmar vyer av PIM. Dessa tillstånd är:

- Tilldelad
- Aktiverad

När du visar ett medlemskap som anges i ”Active roller” kan kolumnen ”tillstånd” du skilja på användare som är ”tilldelad” som aktiv jämfört med användare som ”aktiverad” en berättigad tilldelning och är nu aktiv.

## <a name="next-steps"></a>Nästa steg

[Tilldela roller i PIM](pim-resource-roles-assign-roles.md)

