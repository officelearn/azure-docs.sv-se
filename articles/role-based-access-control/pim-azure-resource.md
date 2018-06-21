---
title: Hantera åtkomst till Azure-resurser med Privileged Identity Management (PIM)
description: Lär dig mer om hur du hanterar åtkomst till Azure-resurser med hjälp av Privileged Identity Management (PIM) och rollbaserad åtkomstkontroll (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 838c889f2dc099b4a4c5d84521871c64eb989163
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293758"
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management"></a>Hantera åtkomst till Azure-resurser med Privileged Identity Management

Du kan använda Azure Active Directory Privileged Identity Management (PIM) för att skydda Privilegierade konton från angrepp av cyber för att minska exponeringstid privilegier och öka din insyn i deras användning via rapporter och aviseringar. PIM åstadkommer detta genom att begränsa användare till att endast ta med på sina privilegier just-in-time ”(JIT) eller genom att tilldela behörigheter för en kortare varaktighet efter vilken behörighet återkallas automatiskt. 

Du kan nu använda PIM med Azure rollbaserad åtkomstkontroll (RBAC) för att hantera, styr och övervaka åtkomst till Azure-resurser. PIM kan hantera medlemskapet för inbyggda och anpassade roller som hjälper dig att: 

- Aktivera på begäran, just-in-time ”åtkomst till Azure-resurser
- Gälla åtkomst till företagsresurser automatiskt för tilldelade användare och grupper
- Tilldela tillfällig åtkomst till Azure-resurser för snabb aktiviteter eller -samtal scheman
- Få meddelanden när nya användare eller grupper som har tilldelats resursåtkomst och när de aktiverar berättigade tilldelningar

Mer information finns i [översikt över rollbaserad åtkomstkontroll i Azure PIM](../active-directory/privileged-identity-management/azure-pim-resource-rbac.md).