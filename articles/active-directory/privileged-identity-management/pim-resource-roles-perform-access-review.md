---
title: Granska åtkomst till Azure Resource roles i PIM – Azure Active Directory | Microsoft Docs
description: Lär dig hur du granskar åtkomsten till Azure Resource roles i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c0ce1a5eee4b8d4cc40c47dadd4bcdc74d03ba
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804098"
---
# <a name="review-access-to-azure-resource-roles-in-pim"></a>Granska åtkomst till Azure Resource roles i PIM
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) gör det enklare för företag att hantera privilegie rad åtkomst till resurser i Azure. 

Om du har tilldelats en administrativ roll kan din organisations administratör för privilegierade roller be dig att regelbundet bekräfta att du fortfarande behöver rollen för ditt jobb. Du kan få ett e-postmeddelande som innehåller en länk, eller så kan du gå direkt till [Azure Portal](https://portal.azure.com). Följ stegen i den här artikeln för att utföra en själv granskning av de tilldelade rollerna.

Om du är en privilegie rad roll administratör som är intresse rad av åtkomst granskningar kan du få mer information om [hur du startar en åtkomst granskning](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Lägga till programmet Privileged Identity Management
Du kan använda Azure Active Directory (Azure AD) PIM-programmet i [Azure Portal](https://portal.azure.com/) för att utföra din granskning. Om du inte har programmet i portalen följer du de här stegen för att komma igång.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Välj ditt användar namn i det övre högra hörnet av Azure Portal och välj den katalog där du kommer att arbeta.
3. Välj **alla tjänster**och Använd rutan **filter** för att söka efter *Azure AD Privileged Identity Management*.
4. Markera **Fäst på instrument panelen**och välj sedan **skapa**. PIM-programmet öppnas.

## <a name="approve-or-deny-access"></a>Godkänn eller neka åtkomst
När du godkänner eller nekar åtkomst visas bara granskaren om du fortfarande använder den här rollen eller inte. Välj **Godkänn** om du vill stanna kvar i rollen eller **neka** om du inte behöver åtkomst längre. Din status ändras bara när granskaren tillämpar resultatet.

Följ de här stegen för att hitta och slutföra åtkomst granskningen:
1. Bläddra till Azure AD PIM-programmet.
2. Välj bladet **Granska åtkomst** .

   ![Skärm bild av PIM-program med bladet granska åtkomst valt](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

3. Välj den granskning som du vill slutföra. 
4. Välj antingen **Godkänn** eller **neka**. I **rutan Ange en orsak**kan du behöva ta med en orsak till ditt beslut.

   ![Skärm bild av sidan gransknings information](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Nästa steg

- [Utföra en åtkomst granskning av mina Azure AD-roller i PIM](pim-how-to-perform-security-review.md)
