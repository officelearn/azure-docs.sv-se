---
title: Roller som du inte kan hantera i PIM – Azure Active Directory | Microsoft Docs
description: Beskriver de roller som du inte kan hantera i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/18/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: d66d433d9de537358777e54e3c7d5489c25c849b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804083"
---
# <a name="roles-you-cannot-manage-in-pim"></a>Roller som du inte kan hantera i PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) gör att du kan hantera alla [Azure AD-roller](../users-groups-roles/directory-assign-admin-roles.md) och alla [Azure-resurs roller](../../role-based-access-control/built-in-roles.md). Dessa roller innehåller också dina anpassade roller som är kopplade till dina hanterings grupper, prenumerationer, resurs grupper och resurser. Det finns dock några roller som du inte kan hantera. Den här artikeln beskriver de roller som du inte kan hantera i PIM.

## <a name="classic-subscription-administrator-roles"></a>Klassiska prenumerationsadministratörsroller

Du kan inte hantera följande klassiska prenumerations administratörs roller i PIM:

- Kontoadministratör
- Tjänstadministratör
- Medadministratör

Mer information om administratörs rollerna för den klassiska prenumerationen finns i [klassiska prenumerationer på prenumerationer, Azure RBAC-roller och administratörs roller för Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Vad gäller Office 365-administratörs roller?

Roller i Exchange Online eller SharePoint Online, förutom Exchange-administratör och SharePoint-administratör, visas inte i Azure AD och kan därför inte hanteras i PIM. Mer information om de här Office 365-tjänsterna finns i [Administratörs roller för office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> SharePoint-administratören har administrativ åtkomst till SharePoint Online via administrations centret för SharePoint Online och kan utföra nästan alla uppgifter i SharePoint Online. Berättigade användare kan uppleva fördröjningar med den här rollen i SharePoint efter aktivering i PIM.

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i PIM](pim-how-to-add-role-to-user.md)
- [Tilldela Azure-resurs roller i PIM](pim-resource-roles-assign-roles.md)
