---
title: Granska åtkomsten till Azure AD-roller i PIM - Azure AD | Microsoft-dokument
description: Lär dig hur du granskar åtkomsten för Azure Active Directory-roller i Azure AD Privileged Identity Management (PIM).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847097"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Granska åtkomsten till Azure AD-roller i privilegierad identitetshantering

Pim (Privileged Identity Management) förenklar hur företag hanterar privilegierad åtkomst till resurser i Azure Active Directory (AD) och andra Microsoft-onlinetjänster som Office 365 eller Microsoft Intune. Följ stegen i den här artikeln för att själv granska dina tilldelade roller.

Om du har tilldelats en administrativ roll kan organisationens privilegierade rolladministratör be dig att regelbundet bekräfta att du fortfarande behöver den rollen för jobbet. Du kan få ett e-postmeddelande som innehåller en länk, eller så kan du gå direkt till [Azure-portalen](https://portal.azure.com) och börja.

Om du är administratör för privilegierad roll eller global administratör som är intresserad av åtkomstgranskningar kan du få mer information om [hur du startar en åtkomstgranskning](pim-how-to-start-security-review.md).

## <a name="add-a-pim-dashboard-tile"></a>Lägga till en panel för en PIM-instrumentpanel

Om du inte har azure AD-privilegierad identitetshanteringstjänst fäst på instrumentpanelen i din Azure-portal följer du dessa steg för att komma igång.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj ditt användarnamn i det övre högra hörnet av Azure-portalen och välj den katalog där du ska arbeta.
3. Välj **Alla tjänster** och använd textrutan Filter för att söka efter **Azure AD Privileged Identity Management**.
4. Markera **Fäst på instrumentpanelen** och klicka sedan på **Skapa**. Programmet Privileged Identity Management öppnas.

## <a name="approve-or-deny-access"></a>Godkänna eller neka åtkomst

När du godkänner eller nekar åtkomst talar du bara om för granskaren om du fortfarande använder den här rollen eller inte. Välj **Godkänn** om du vill stanna kvar i rollen eller **Neka** om du inte behöver åtkomsten längre. Din status ändras inte direkt förrän granskaren tillämpar resultaten.
Så här hittar du och slutför åtkomstgranskningen:

1. I tjänsten Privilegierad identitetshantering väljer du **Granska privilegierad åtkomst**. Om du har några väntande åtkomstgranskningar visas de på sidan Granska Azure AD **Access.**
2. Välj den recension som du vill slutföra.
3. Om du inte har skapat granskningen visas du som den enda användaren i granskningen. Markera markeringen bredvid ditt namn.
4. Välj Antingen **Godkänn** eller **Neka**. Du kan behöva inkludera en orsak till ditt beslut i textrutan **Ange en orsak.**  
5. Stäng bladet **Granska Azure AD-roller.**

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg

- [Utför en åtkomstgranskning av mina Azure-resursroller i PIM](pim-resource-roles-perform-access-review.md)
