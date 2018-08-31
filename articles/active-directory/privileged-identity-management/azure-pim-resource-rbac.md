---
title: Visa som har Azure-resursroller i PIM | Microsoft Docs
description: Visa vem som har Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: aee172bc6fc77aaac8d2d52037a481fdb976d308
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188976"
---
# <a name="view-who-has-azure-resource-roles-in-pim"></a>Visa som har Azure-resursroller i PIM

Med Azure Active Directory Privileged Identity Management (PIM), som du kan hantera, kontrollera och övervaka åtkomst till Azure-resurser i din organisation. Detta inkluderar prenumerationer, resursgrupper och även virtuella datorer. Alla resurser i Azure-portalen som utnyttjar Azure rollbaserad åtkomstkontroll (RBAC)-funktioner kan dra nytta av säkerhets- och livscykel hanteringsfunktioner i Azure AD PIM. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>PIM för Azure-resurser som hjälper administratörer att resursen

- Se vilka användare och grupper tilldelas roller för Azure-resurser du administrerar
- Aktivera på begäran, åtkomst just-in-time ”för att hantera resurser, till exempel prenumerationer och resursgrupper
- Upphör att gälla åtkomst till tilldelade användare/grupper automatiskt med nya Tilldelningsinställningar för Tidsbundna
- Tilldela tillfälligt resursåtkomst för Snabbuppgifter eller på anrop scheman
- Använda Multifaktorautentisering för åtkomst till resurser på alla inbyggda eller anpassade roller 
- Få rapporter om aktivitet för resursen åtkomst korrelerade resurs under en användares aktiva session
- Få aviseringar när nya användare eller grupper har tilldelats åtkomst till resurser, och när de aktiverar berättigade uppgifter

## <a name="view-activation-and-azure-resource-activity"></a>Visa aktivering och Azure-resurs-aktivitet

Om du vill se vilka åtgärder som en viss användare vidtog för olika resurser kan granska du aktiviteten Azure-resurs som är associerade med en viss aktiveringsperioden (för berättigade användare). Starta genom att välja en användare från vyn medlemmar eller från listan över medlemmar i en viss roll. Resultatet visar en grafisk representation av användarens åtgärder på Azure-resurser efter datum och de senaste rollaktiveringar under den samma tidsperioden.

![](media/azure-pim-resource-rbac/user-details.png)

Att välja en specifik rollaktivering visar rollaktiveringsinformation och motsvarande Azure Resource-aktivitet som inträffade när användaren var aktiv.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="review-who-has-access-in-a-subscription"></a>Granska vem som har åtkomst i en prenumeration

Välj fliken medlemmar i det vänstra navigeringsfönstret för att granska rolltilldelningar i din prenumeration, eller välja roller och välja en ansvarar för att granska medlemmar. 

Välj granska åtgärdsfält att visa befintliga åtkomstgranskningar och välj Lägg till att skapa en ny granskning.

![](media/azure-pim-resource-rbac/owner.png)

[Läs mer om åtkomstgranskningar](pim-how-to-perform-security-review.md)

>[!NOTE]
Granskningar stöds endast för resurstyper för prenumerationen just nu.

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-resursroller i PIM](pim-resource-roles-assign-roles.md)
- [Godkänn eller neka begäranden för Azure-resursroller i PIM](pim-resource-roles-approval-workflow.md)
- [Inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md)
