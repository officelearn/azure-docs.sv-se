---
title: Så här utför du en åtkomstgranskning | Microsoft Docs
description: Lär dig hur du utför en granskning med Azure Privileged Identity Management-programmet.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: f3d8cbdcef25509304785090190f172ae20913b6
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440816"
---
# <a name="how-to-perform-an-access-review-in-azure-ad-privileged-identity-management"></a>Hur du utför en åtkomstgranskning i Azure AD Privileged Identity Management
Azure Active Directory (AD) Privileged Identity Management förenklar hur företag hantera privilegierad åtkomst till resurser i Azure AD och andra Microsoft onlinetjänster som Office 365 eller Microsoft Intune.  

Om du har tilldelats en administrativ roll kan privilegierad rolladministratör för din organisation be dig att regelbundet kontrollera att du fortfarande behöver rollen för jobbet. Du kan få ett e-postmeddelande som innehåller en länk eller du kan gå direkt till den [Azure-portalen](https://portal.azure.com). Följ stegen i den här artikeln för att utföra själv granska av dina tilldelade roller.

Om du är en privilegierad rolladministratör eller den globala administratören som är intresserade av åtkomstgranskningar kan få mer information finns på [så här startar du en åtkomstgranskning](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Lägga till programmet Privileged Identity Management
Du kan använda Azure AD Privileged Identity Management (PIM)-program i den [Azure-portalen](https://portal.azure.com/) att utföra din granskning.  Om du inte har Azure AD Privileged Identity Management-programmet på din portal, följer du dessa steg för att komma igång.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj ditt användarnamn i det övre högra hörnet av Azure-portalen och välj den katalog där du kommer du fungerar.
3. Välj **Alla tjänster** och använd textrutan Filter för att söka efter **Azure AD Privileged Identity Management**.
4. Markera **Fäst på instrumentpanelen** och klicka sedan på **Skapa**. Programmet Privileged Identity Management öppnas.

## <a name="approve-or-deny-access"></a>Godkänn eller neka åtkomst
När du godkänner eller nekar åtkomst kan be du bara granskaren om du fortfarande använda den här rollen eller inte. Välj **Godkänn** om du vill stanna kvar i rollen eller **neka** om du inte längre behöver åtkomst. Din status ändras inte direkt, tills granskaren gäller resultaten.
Följ dessa steg för att hitta och slutföra åtkomstgranskningen:

1. I PIM-programmet väljer **Granska privilegierad åtkomst**. Om du har alla väntande åtkomstgranskningar som de visas på bladet Azure AD Access granskningar.
2. Välj den granskningen som du vill slutföra.
3. Om du har skapat granskningen visas som den enda användaren i granskningen. Markera kryssrutan bredvid ditt namn.
4. Välj antingen **godkänna** eller **neka**. Du kan behöva inkludera en orsak till ditt beslut i den **ange en orsak till** textrutan.  
5. Stäng den **granska Azure AD-roller** bladet.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
