---
title: Granska åtkomst till Azure AD-roller i PIM – Azure AD | Microsoft Docs
description: Lär dig hur du granskar åtkomsten till Azure Active Directory roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76eccb5d62b68865b7a117312be62753f203e2cb
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847097"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Granska åtkomst till Azure AD-roller i Privileged Identity Management

Privileged Identity Management (PIM) fören klar hur företag hanterar privilegie rad åtkomst till resurser i Azure Active Directory (AD) och andra Microsoft-onlinetjänster som Office 365 eller Microsoft Intune. Följ stegen i den här artikeln för att själv granska de tilldelade rollerna.

Om du har tilldelats en administrativ roll kan organisationens privilegierande roll be dig att regelbundet bekräfta att du fortfarande behöver rollen för ditt jobb. Du kan få ett e-postmeddelande som innehåller en länk, eller så kan du gå direkt till [Azure Portal](https://portal.azure.com) och börja.

Om du är en privilegie rad roll administratör eller global administratör som är intresse rad av åtkomst granskningar kan du få mer information om [hur du startar en åtkomst granskning](pim-how-to-start-security-review.md).

## <a name="add-a-pim-dashboard-tile"></a>Lägg till en panel i PIM-instrumentpanelen

Om du inte har fäst Azure AD Privileged Identity Management-tjänsten på instrument panelen i Azure Portal följer du stegen nedan för att komma igång.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj ditt användar namn i det övre högra hörnet av Azure Portal och välj den katalog där du kommer att arbeta.
3. Välj **Alla tjänster** och använd textrutan Filter för att söka efter **Azure AD Privileged Identity Management**.
4. Markera **Fäst på instrumentpanelen** och klicka sedan på **Skapa**. Programmet Privileged Identity Management öppnas.

## <a name="approve-or-deny-access"></a>Godkänn eller neka åtkomst

När du godkänner eller nekar åtkomst visas bara granskaren om du fortfarande använder den här rollen eller inte. Välj **Godkänn** om du vill stanna kvar i rollen eller **neka** om du inte behöver åtkomst längre. Din status ändras inte direkt, tills granskaren tillämpar resultatet.
Följ de här stegen för att hitta och slutföra åtkomst granskningen:

1. I Privileged Identity Management-tjänsten väljer du **Granska privilegie rad åtkomst**. Om du har väntande åtkomst granskningar visas de på sidan för **åtkomst granskningar** för Azure AD.
2. Välj den granskning som du vill slutföra.
3. Om du inte har skapat granskningen visas den enda användaren i granskningen. Markera kryss rutan bredvid ditt namn.
4. Välj antingen **Godkänn** eller **neka**. Du kan behöva ange en orsak till ditt beslut i text rutan **Ange en orsak** .  
5. Stäng bladet **Granska Azure AD-roller** .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg

- [Utför en åtkomstgranskning av mina Azure-resursroller i PIM](pim-resource-roles-perform-access-review.md)
