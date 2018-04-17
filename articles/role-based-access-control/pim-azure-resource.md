---
title: Hantera åtkomst till Azure-resurser med Privileged Identity Management (PIM)
description: Lär dig mer om hur du använder åtkomsthantering av rollbaserad i PIM åtkomst till Azure-resurser.
services: active-directory
documentationcenter: ''
author: skwan
manager: mtillman
editor: bryanla
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 497efb7889029ef5a9478c59ee75ccabd5b7d87b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management"></a>Hantera åtkomst till Azure-resurser med Privileged Identity Management

Du kan använda Azure Active Directory Privileged Identity Management (PIM) för att skydda Privilegierade konton från angrepp av cyber för att minska exponeringstid privilegier och öka din insyn i deras användning via rapporter och aviseringar. PIM åstadkommer detta genom att begränsa användare till att endast ta med på sina privilegier just-in-time ”(JIT) eller genom att tilldela behörigheter för en kortare varaktighet efter vilken behörighet återkallas automatiskt. 

Du kan nu använda PIM med rollbaserad åtkomstkontroll (RBAC) att hantera, styr och övervaka åtkomst till Azure-resurser. PIM kan hantera medlemskapet för inbyggda och anpassade roller som hjälper dig att: 

- Aktivera på begäran, just-in-time ”åtkomst till Azure-resurser
- Gälla åtkomst till företagsresurser automatiskt för tilldelade användare och grupper
- Tilldela tillfällig åtkomst till Azure-resurser för snabb aktiviteter eller -samtal scheman
- Få meddelanden när nya användare eller grupper som har tilldelats resursåtkomst och när de aktiverar berättigade tilldelningar

Mer information finns i [Overview of Role-Based åtkomstkontroll i Azure PIM](../active-directory/privileged-identity-management/azure-pim-resource-rbac.md).