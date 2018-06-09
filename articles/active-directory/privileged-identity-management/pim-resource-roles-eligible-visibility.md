---
title: Berättigad tilldelningar och synlighet för Azure i Privileged Identity Management | Microsoft Docs
description: Beskriver hur du tilldelar medlemmar som är berättigade till resursroller när du använder PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: c100cf5421539c985b8132bfd8af59ed0ac2e01c
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233432"
---
# <a name="eligible-assignments-and-resource-visibility-with-privileged-identity-management"></a>Berättigad tilldelningar och synlighet med Privileged Identity Management

Privileged Identity Management (PIM) för Azure-resursroller ger förbättrad säkerhet för organisationer som har viktiga Azure-resurser. Resurs-administratörer kan använda PIM för att tilldela medlemmar som är berättigade till resursroller. Läs mer om tilldelning av olika typer och tilldelning tillstånd för Azure-resursroller i följande avsnitt. 

## <a name="assignment-types"></a>Typer för tilldelning

PIM för Azure-resurser innehåller två distinkta tilldelning typer:

- Berättigad
- Active

Berättigad tilldelningar kräver medlem i rollen att utföra en åtgärd för att använda rollen. Åtgärder kan innehålla efterföljande multifaktorautentisering kontroll, tillhandahåller en motivering eller begära godkännande från avsedda godkännare.

Aktiva tilldelningar behöver medlemmen att utföra alla åtgärder för att använda rollen. Medlemmar som är tilldelad som aktiv har de behörigheter som tilldelats till rollen vid alla tidpunkter.

## <a name="assignment-duration"></a>Varaktighet för tilldelning

Resurs-administratörer kan välja mellan två alternativ för varje Tilldelningstyp av när de konfigurerar PIM-inställningarna för en roll. Dessa alternativ blir standard maximal varaktighet när medlem tilldelas rollen i PIM. 

En administratör kan välja någon av följande typer av tilldelning:

- Tillåt permanent berättigade tilldelning
- Tillåt permanent aktiv uppgift

Eller så kan administratören välja någon av följande typer av tilldelning:

- Berättigad tilldelningar efter att gälla
- Aktiva uppgifter upphör efter

Om en resursadministratör väljer **Tillåt permanent berättigade tilldelning** eller **Tillåt permanent active tilldelning**, alla administratörer som tilldelas resursen medlemmar kan tilldela permanent medlemskap.

Om en resursadministratör väljer **ut berättigade tilldelningar efter** eller **ut active tilldelningar efter**, resursadministratör styr tilldelningen livscykel genom att kräva som alla tilldelningar har ett angivet datum för start- och slutdatum.

> [!NOTE] 
> Alla tilldelningar som har det angivna slutdatumet kan förnyas av resurs-administratörer. Medlemmar kan också initiera självservicebegäranden till [utöka eller förnya tilldelningar](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Tillstånd för tilldelning

PIM för Azure-resurser har två distinkta tilldelning tillstånd som visas på den **Active roller** fliken i den **min roller**, **roller**, och **medlemmar**vyer av PIM. Dessa tillstånd är:

- Tilldelad
- Aktiverat

När du visar ett medlemskap som anges i **Active roller**, du kan använda värdet i den **tillstånd** kolumnen att skilja mellan en användare som är **tilldelad** som aktiv och användare som **aktiverad** en berättigad tilldelning och är nu aktiv.

## <a name="next-steps"></a>Nästa steg

[Tilldela roller i Privileged Identity Manager](pim-resource-roles-assign-roles.md)
