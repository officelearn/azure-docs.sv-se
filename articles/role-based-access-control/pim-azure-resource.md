---
title: Hantera åtkomst till Azure-resurser med Azure AD Privileged Identity Management (PIM)
description: Lär dig mer om hur du hanterar åtkomst till Azure-resurser med Azure Active Directory Privileged Identity Management (PIM) och rollbaserad åtkomstkontroll (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 757068034868744b408c9402b521a0e4c73950f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344622"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Hantera åtkomst till Azure-resurser med Azure AD Privileged Identity Management

Du kan använda Azure Active Directory Privileged Identity Management (PIM) för att skydda Privilegierade konton från skadliga cyberattacker, för att sänka exponeringstiden för privilegier och öka din synlighet av deras användning via rapporter och aviseringar. PIM gör detta genom att begränsa användare till bara att utföra på sina privilegier just-in-time ”(JIT), eller genom att tilldela behörigheter för en förkortad tid efter vilken privilegier återkallas automatiskt. 

Du kan nu använda PIM med Azure rollbaserad åtkomstkontroll (RBAC) för att hantera, kontrollera och övervaka åtkomst till Azure-resurser. PIM kan hantera medlemskapet för inbyggda och anpassade roller som hjälper dig att: 

- Aktivera på begäran, just-in-time ”åtkomst till Azure-resurser
- Upphör att gälla resursåtkomst automatiskt för tilldelade användare och grupper
- Tilldela tillfällig åtkomst till Azure-resurser för Snabbuppgifter eller på anrop scheman
- Få aviseringar när nya användare eller grupper har tilldelats åtkomst till resurser, och när de aktiverar berättigade uppgifter

Mer information finns i [vad är Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).