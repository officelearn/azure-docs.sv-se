---
title: Hantera åtkomst till Azure-resurser med Azure AD och PIM
description: Lär dig mer om hur du hanterar åtkomst till Azure-resurser med hjälp av Azure Active Directory Privileged Identity Management (PIM) och rollbaserad åtkomstkontroll (RBAC).
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
ms.openlocfilehash: 53c6375ce04425eae27f4c935e613cc27d058dc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138041"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Hantera åtkomst till Azure-resurser med Azure AD-privilegierad identitetshantering

För att skydda privilegierade konton från skadliga cyberattacker kan du använda AZURE Active Directory Privileged Identity Management (PIM) för att sänka exponeringstiden för privilegier och öka din insyn i deras användning via rapporter och aviseringar. PIM gör detta genom att begränsa användare att bara ta på sig sina privilegier "just in time" (JIT), eller genom att tilldela privilegier för en förkortad varaktighet varefter privilegier återkallas automatiskt. 

Du kan nu använda PIM med Azure-rollbaserad åtkomstkontroll (RBAC) för att hantera, kontrollera och övervaka åtkomst till Azure-resurser. PIM kan hantera medlemskap i inbyggda och anpassade roller som hjälper dig: 

- Aktivera åtkomst på begäran, "precis i tid"-åtkomst till Azure-resurser
- Upphör att gälla resursåtkomst automatiskt för tilldelade användare och grupper
- Tilldela tillfällig åtkomst till Azure-resurser för snabbaktiviteter eller jourscheman
- Få aviseringar när nya användare eller grupper tilldelas resursåtkomst och när de aktiverar kvalificerade tilldelningar

Mer information finns i [Vad är Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).