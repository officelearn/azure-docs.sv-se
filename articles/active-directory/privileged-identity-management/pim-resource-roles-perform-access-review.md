---
title: Hur du utför en åtkomst-granskning i Privileged Identity Management för Azure-resurser | Microsoft Docs
description: Det här dokumentet beskriver hur du utför och komma åt granska i PIM för Azure-resurser.
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
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4afb923058143dd1771043db8433aa3a65541bf7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---perform-access-review"></a>Privileged Identity Management - resurs role - utföra åtkomst till granskning
Privileged Identity Management resurser för Azure förenklar hur företag hantera privilegierad åtkomst till resurser i Azure. 

Om du har tilldelats en administrativ roll din organisations administratör av Privilegierade roller kan bli ombedd att regelbundet kontrollera att du fortfarande behöver rollen för jobbet. Du kan få ett e-postmeddelande som innehåller en länk eller du kan gå direkt till den [Azure-portalen](https://portal.azure.com). Följ stegen i den här artikeln för att utföra automatisk granska din tilldelade roller.

Om du är en administratör av Privilegierade roller som är intresserade av åtkomst granskningar kan få mer information på [hur du startar en åtkomst-granskning](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Lägga till programmet Privileged Identity Management
Du kan använda Azure AD Privileged Identity Management (PIM)-program i den [Azure-portalen](https://portal.azure.com/) att utföra din granskning.  Om du inte har programmet Azure AD Privileged Identity Management på portalen, följer du dessa steg för att komma igång.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj ditt användarnamn i det övre högra hörnet i Azure-portalen och välj den katalog där du kommer du att driva.
3. Välj **Alla tjänster** och använd textrutan Filter för att söka efter **Azure AD Privileged Identity Management**.
4. Markera **Fäst på instrumentpanelen** och klicka sedan på **Skapa**. Programmet Privileged Identity Management öppnas.

## <a name="approve-or-deny-access"></a>Godkänna eller neka åtkomst
När du godkänna eller neka åtkomst, du precis uppmanar granskaren om du fortfarande använda den här rollen eller inte. Välj **Godkänn** om du vill hålla rollen eller **neka** om du inte längre behöver åtkomst. Statusen ändras inte direkt, tills granskaren gäller resultaten.
Följ dessa steg för att hitta och Slutför granskningen åtkomst:
1. Gå till Azure AD Privileged Identity Management-programmet.
2. Välj bladet granska åtkomst

![](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Välj granska som du vill slutföra. 
4. Välj antingen **godkänna** eller **neka**. Du kan behöva ta en orsak till ditt beslut i den **motivera** textruta.

![](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
