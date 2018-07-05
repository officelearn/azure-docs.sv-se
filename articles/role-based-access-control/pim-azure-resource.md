---
title: Hantera åtkomst till Azure-resurser med Privileged Identity Management (PIM)
description: Lär dig mer om hur du hanterar åtkomst till Azure-resurser med Privileged Identity Management (PIM) och rollbaserad åtkomstkontroll (RBAC).
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
ms.openlocfilehash: 141cba29f5027ce092775d97c1abe9ecf11badf5
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436053"
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management"></a>Hantera åtkomst till Azure-resurser med Privileged Identity Management

Du kan använda Azure Active Directory Privileged Identity Management (PIM) för att skydda Privilegierade konton från skadliga cyberattacker, för att sänka exponeringstiden för privilegier och öka din synlighet av deras användning via rapporter och aviseringar. PIM gör detta genom att begränsa användare till bara att utföra på sina privilegier just-in-time ”(JIT), eller genom att tilldela behörigheter för en förkortad tid efter vilken privilegier återkallas automatiskt. 

Du kan nu använda PIM med Azure rollbaserad åtkomstkontroll (RBAC) för att hantera, kontrollera och övervaka åtkomst till Azure-resurser. PIM kan hantera medlemskapet för inbyggda och anpassade roller som hjälper dig att: 

- Aktivera på begäran, just-in-time ”åtkomst till Azure-resurser
- Upphör att gälla resursåtkomst automatiskt för tilldelade användare och grupper
- Tilldela tillfällig åtkomst till Azure-resurser för Snabbuppgifter eller på anrop scheman
- Få aviseringar när nya användare eller grupper har tilldelats åtkomst till resurser, och när de aktiverar berättigade uppgifter

Mer information finns i [översikt över rollbaserad åtkomstkontroll i Azure PIM](../active-directory/privileged-identity-management/azure-pim-resource-rbac.md).