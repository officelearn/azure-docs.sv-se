---
title: Hantera åtkomst till Azure-resurser med Azure AD och PIM
description: Lär dig mer om att hantera åtkomst till Azure-resurser med hjälp av Azure Active Directory Privileged Identity Management (PIM) och rollbaserad åtkomst kontroll (RBAC).
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
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138041"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Hantera åtkomst till Azure-resurser med Azure AD Privileged Identity Management

För att skydda privilegierade konton från skadliga cyberhot-attacker kan du använda Azure Active Directory Privileged Identity Management (PIM) för att minska exponerings tiden för privilegier och öka din insyn i användningen genom rapporter och aviseringar. PIM gör detta genom att begränsa användare till att endast ta hänsyn till sina privilegier "just-in-Time" (JIT) eller genom att tilldela behörigheter för kortare varaktighet efter vilken privilegierna återkallas automatiskt. 

Nu kan du använda PIM med rollbaserad åtkomst kontroll (RBAC) i Azure för att hantera, kontrol lera och övervaka åtkomst till Azure-resurser. PIM kan hantera medlemskap i inbyggda och anpassade roller som hjälper dig att: 

- Aktivera på begäran, "just-in-Time"-åtkomst till Azure-resurser
- Förfaller resurs åtkomsten automatiskt för tilldelade användare och grupper
- Ge tillfällig åtkomst till Azure-resurser för snabb uppgifter eller jour scheman
- Få aviseringar när nya användare eller grupper tilldelas resurs åtkomst och när de aktiverar kvalificerade tilldelningar

Mer information finns i [Vad är Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).