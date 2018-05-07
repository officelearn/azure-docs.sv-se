---
title: Utför en åtkomst-granskning i Privileged Identity Management för Azure-resurser | Microsoft Docs
description: Det här dokumentet beskriver hur du utför en åtkomst-granskning i PIM för Azure-resurser, enligt resursrollen.
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
ms.openlocfilehash: 47d981ce8034d725fe2b119392334a156852698d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="perform-an-access-review-in-pim-according-to-resource-role"></a>Utför en åtkomst-granskning i PIM, enligt resurs-roll
Privileged Identity Management (PIM) för Azure-resurser förenklar hur företag hantera privilegierad åtkomst till resurser i Azure. 

Om du har tilldelats en administrativ roll kanske din organisations administratör av Privilegierade roller du ombeds att regelbundet kontrollera att du fortfarande behöver rollen för jobbet. Du kan få ett e-postmeddelande som innehåller en länk eller du kan gå direkt till den [Azure-portalen](https://portal.azure.com). Följ stegen i den här artikeln för att utföra automatisk granska din tilldelade roller.

Om du är en administratör av Privilegierade roller som är intresserade av åtkomst granskningar kan få mer information på [hur du startar en åtkomst-granskning](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Lägga till programmet Privileged Identity Management
Du kan använda Azure Active Directory (AD Azure) PIM-program i den [Azure-portalen](https://portal.azure.com/) att utföra din granskning. Om du inte har programmet i portalen, följer du dessa steg för att komma igång.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj ditt användar-namnet i det övre högra hörnet i Azure-portalen och välj den katalog där du ska du fungerar.
3. Välj **alla tjänster**, och använda den **Filter** att söka efter *Azure AD Privileged Identity Management*.
4. Kontrollera **fäst på instrumentpanelen**, och välj sedan **skapa**. PIM-programmet öppnas.

## <a name="approve-or-deny-access"></a>Godkänna eller neka åtkomst
När du godkänna eller neka åtkomst, du precis uppmanar granskaren om du fortfarande använda den här rollen eller inte. Välj **Godkänn** om du vill hålla rollen eller **neka** om du inte längre behöver åtkomst. Statusen ändras endast när granskaren gäller resultaten.

Följ dessa steg för att hitta och Slutför granskningen åtkomst:
1. Bläddra till Azure AD PIM-program.
2. Välj den **granska åtkomst** bladet.

   ![Skärmbild av PIM-program med granska åtkomst bladet markerade](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. Välj granska som du vill slutföra. 
4. Välj antingen **godkänna** eller **neka**. I den **ange en orsak ruta**, kanske du måste ange en orsak till ditt beslut.

   ![Skärmbild av granska informationssidan](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
