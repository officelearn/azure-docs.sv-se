---
title: Granska åtkomst till Azure-resursroller i PIM - Azure Active Directory | Microsoft Docs
description: Lär dig hur du granskar åtkomst till Azure-resursroller i Azure AD Privileged Identity Management (PIM).
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
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e49f0077444f86eabeed22cc7cecf5e0ac7f8b5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141189"
---
# <a name="review-access-to-azure-resource-roles-in-pim"></a>Granska åtkomst till Azure-resursroller i PIM
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) förenklar hur företag hantera privilegierad åtkomst till resurser i Azure. 

Om du har tilldelats en administrativ roll kan privilegierad rolladministratör för din organisation be dig att regelbundet kontrollera att du fortfarande behöver rollen för jobbet. Du kan få ett e-postmeddelande som innehåller en länk eller du kan gå direkt till den [Azure-portalen](https://portal.azure.com). Följ stegen i den här artikeln för att utföra själv granska av dina tilldelade roller.

Om du är en administratör av Privilegierade roller som är intresserade av åtkomstgranskningar kan få mer information finns på [så här startar du en åtkomstgranskning](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Lägga till programmet Privileged Identity Management
Du kan använda PIM för Azure Active Directory (Azure AD)-program i den [Azure-portalen](https://portal.azure.com/) att utföra din granskning. Om du inte har programmet i din portal, följer du dessa steg för att komma igång.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Välj användaren namn i det övre högra hörnet i Azure Portal och välj den katalog där du kommer du fungerar.
3. Välj **alla tjänster**, och använda den **Filter** att söka efter *Azure AD Privileged Identity Management*.
4. Kontrollera **fäst på instrumentpanelen**, och välj sedan **skapa**. PIM-program öppnas.

## <a name="approve-or-deny-access"></a>Godkänn eller neka åtkomst
När du godkänner eller nekar åtkomst kan be du bara granskaren om du fortfarande använda den här rollen eller inte. Välj **Godkänn** om du vill stanna kvar i rollen eller **neka** om du inte längre behöver åtkomst. Din status ändras endast när granskaren gäller resultaten.

Följ dessa steg för att hitta och slutföra åtkomstgranskningen:
1. Bläddra till Azure AD PIM-programmet.
2. Välj den **granska åtkomst** bladet.

   ![Skärmbild av PIM-program med valt att bladet granska åtkomst](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Välj den granskningen som du vill slutföra. 
4. Välj antingen **godkänna** eller **neka**. I den **ge en orsak ruta**, du kan behöva ta en orsak till ditt beslut.

   ![Skärmbild av granska informationssida](media/azure-pim-resource-rbac/rbac-access-review-choice.png)

## <a name="next-steps"></a>Nästa steg

- [Utför en åtkomstgranskning av Mina Azure AD-roller i PIM](pim-how-to-perform-security-review.md)
