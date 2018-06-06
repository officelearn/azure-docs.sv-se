---
title: Hur du utför en åtkomst-granskning | Microsoft Docs
description: Lär dig hur du utför en granskning med Azure Privileged Identity Management-programmet.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 651e179773ca225e8c05d2a17711e3eebbe6f706
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698041"
---
# <a name="how-to-perform-an-access-review-in-azure-ad-privileged-identity-management"></a>Hur du utför en åtkomst-granskning i Azure AD Privileged Identity Management
Azure Active Directory (AD) Privileged Identity Management förenklar hur företag hantera privilegierad åtkomst till resurser i Azure AD och andra Microsoft online services som Office 365 eller Microsoft Intune.  

Om du har tilldelats en administrativ roll din organisations administratör av Privilegierade roller kan bli ombedd att regelbundet kontrollera att du fortfarande behöver rollen för jobbet. Du kan få ett e-postmeddelande som innehåller en länk eller du kan gå direkt till den [Azure-portalen](https://portal.azure.com). Följ stegen i den här artikeln för att utföra automatisk granska din tilldelade roller.

Om du är en administratör av Privilegierade roller som är intresserade av åtkomst granskningar kan få mer information på [hur du startar en åtkomst-granskning](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Lägga till programmet Privileged Identity Management
Du kan använda Azure AD Privileged Identity Management (PIM)-program i den [Azure-portalen](https://portal.azure.com/) att utföra din granskning.  Om du inte har programmet Azure AD Privileged Identity Management på portalen, följer du dessa steg för att komma igång.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj ditt användarnamn i det övre högra hörnet i Azure-portalen och välj den katalog där du kommer du att driva.
3. Välj **Alla tjänster** och använd textrutan Filter för att söka efter **Azure AD Privileged Identity Management**.
4. Markera **Fäst på instrumentpanelen** och klicka sedan på **Skapa**. Programmet Privileged Identity Management öppnas.

## <a name="approve-or-deny-access"></a>Godkänna eller neka åtkomst
När du godkänna eller neka åtkomst, du precis uppmanar granskaren om du fortfarande använda den här rollen eller inte. Välj **Godkänn** om du vill hålla rollen eller **neka** om du inte längre behöver åtkomst. Statusen ändras inte direkt, tills granskaren gäller resultaten.
Följ dessa steg för att hitta och Slutför granskningen åtkomst:

1. Markera i PIM-programmet **Granska privilegierad åtkomst**. Om du har alla väntande åtkomst granskningar som de visas i bladet granskningar Azure AD-åtkomst.
2. Välj granska som du vill slutföra.
3. Om du har skapat granskningen visas som den enda användaren i granskningen. Markera kryssrutan bredvid ditt namn.
4. Välj antingen **godkänna** eller **neka**. Du kan behöva ta en orsak till ditt beslut i den **motivera** textruta.  
5. Stäng den **granska Azure AD-roller** bladet.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
