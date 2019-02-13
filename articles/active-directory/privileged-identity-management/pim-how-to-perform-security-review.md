---
title: Utför en åtkomstgranskning av min Azure AD-katalogroller i PIM | Microsoft Docs
description: Lär dig hur du utför en åtkomstgranskning av din Azure AD-katalogroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c7b3905d05e62bd3f11ef4de7f966e6bf9340f0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178914"
---
# <a name="perform-an-access-review-of-my-azure-ad-directory-roles-in-pim"></a>Utför en åtkomstgranskning av min Azure AD-katalogroller i PIM
Azure Active Directory (AD) Privileged Identity Management förenklar hur företag hantera privilegierad åtkomst till resurser i Azure AD och andra Microsoft onlinetjänster som Office 365 eller Microsoft Intune.  

Om du har tilldelats en administrativ roll kan privilegierad rolladministratör för din organisation be dig att regelbundet kontrollera att du fortfarande behöver rollen för jobbet. Du kan få ett e-postmeddelande som innehåller en länk eller du kan gå direkt till den [Azure-portalen](https://portal.azure.com). Följ stegen i den här artikeln för att utföra själv granska av dina tilldelade roller.

Om du är en privilegierad rolladministratör eller den globala administratören som är intresserade av åtkomstgranskningar kan få mer information finns på [så här startar du en åtkomstgranskning](pim-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Lägga till programmet Privileged Identity Management
Du kan använda Azure AD Privileged Identity Management (PIM)-program i den [Azure-portalen](https://portal.azure.com/) att utföra din granskning.  Om du inte har Azure AD Privileged Identity Management-programmet på din portal, följer du dessa steg för att komma igång.

1. Logga in på [Azure Portal](https://portal.azure.com/).
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

- [Utför en åtkomstgranskning av mina Azure-resursroller i PIM](pim-resource-roles-perform-access-review.md)
