---
title: Berättigade uppgifter och synlighet i PIM - Azure | Microsoft Docs
description: Beskriver hur du tilldelar medlemmar som är berättigade till Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 3551c3231a94f8a844d26a713cbf171ca7653815
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189222"
---
# <a name="eligible-assignments-and-resource-visibility-in-pim"></a>Berättigade uppgifter och synlighet i PIM

Privileged Identity Management (PIM) för Azure-resursroller ger förbättrad säkerhet för organisationer som har viktiga Azure-resurser. Resurs-administratörer kan använda PIM för att tilldela medlemmar som är berättigade till resursroller. Läs mer om de olika tilldelningstyperna och tilldelning av tillstånden för Azure-resursroller i följande avsnitt. 

## <a name="assignment-types"></a>Tilldelningstyper

PIM för Azure-resurser tillhandahåller två distinkta tilldelningstyperna:

- Berättigad
- Active

Berättigade uppgifter kräver medlem i rollen att utföra en åtgärd för att använda rollen. Åtgärder kan innehålla lyckas en kontroll för multifaktorautentisering, vilket ger en motivering eller begära godkännande från utnämnda godkännare.

Aktiva uppgifter kräver inte medlemmen som ska utföra alla åtgärder för att använda rollen. Medlemmar som är tilldelad som aktiv har de behörigheter som tilldelats rollen hela tiden.

## <a name="assignment-duration"></a>Tilldelningsvaraktighet

Resurs-administratörer kan välja mellan två alternativ för varje tilldelning när de konfigurerar PIM-inställningar för en roll. De här alternativen blir standard maximal varaktighet när en medlem har tilldelats rollen i PIM. 

En administratör kan välja någon av tilldelningstyperna:

- Tillåt permanent berättigad uppgift
- Tillåt permanent aktiv uppgift

Eller så kan administratören välja någon av följande typer av tilldelning:

- Berättigade uppgifter upphör efter
- Aktiva uppgifter upphör efter

Om en resursadministratör väljer **Tillåt permanent berättigad uppgift** eller **Tillåt permanent aktiv uppgift**, alla administratörer som tilldelas resursen medlemmar kan tilldela permanent medlemskap.

Om en resursadministratör väljer **berättigade uppgifter upphör efter** eller **aktiva uppgifter upphör efter**, resursadministratör styr livscykeln tilldelningen genom att kräva som alla tilldelningar har ett visst datum för start- och slutdatum.

> [!NOTE] 
> Alla tilldelningar som har ett angivet slutdatum kan förnyas av resurs-administratörer. Medlemmar kan också initiera självbetjäning begäranden till [utöka eller förnya uppgifter](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Tilldelning av tillstånd

PIM för Azure-resurser har två distinkta tilldelning tillstånd som visas på den **aktiva roller** fliken i den **Mina roller**, **roller**, och **medlemmar**vyer av PIM. Dessa tillstånd är:

- Tilldelad
- Aktiverat

När du visar ett medlemskap som anges i **aktiva roller**, du kan använda värdet i den **tillstånd** kolumnen för att skilja mellan användare som är **tilldelad** som aktiv och användare som **aktiverad** en berättigad uppgift, och är nu aktiv.

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-resursroller i PIM](pim-resource-roles-assign-roles.md)
