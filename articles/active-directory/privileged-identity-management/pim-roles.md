---
title: Roller som du inte kan hantera i PIM - Azure | Microsoft Docs
description: Beskriver de roller som du inte kan hantera i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/18/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: 80fbad64cda9267e468f9385d48dd5d40468eaca
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203427"
---
# <a name="roles-you-cannot-manage-in-pim"></a>Roller som du inte kan hantera i PIM

Azure AD Privileged Identity Management (PIM) gör det möjligt för dig att hantera [Azure AD-katalogroller](../users-groups-roles/directory-assign-admin-roles.md) och alla [Azure-resursroller](../../role-based-access-control/built-in-roles.md). Dessa roller inkluderar även din anpassade roller som är kopplade till dina hanteringsgrupper, prenumerationer, resursgrupper och resurser. Det finns dock några roller som du inte kan hantera. Den här artikeln beskrivs de roller som du inte kan hantera i PIM.

## <a name="classic-subscription-administrator-roles"></a>Administratörsroller för klassiska prenumerationer

Du kan inte hantera följande klassiska prenumeration-administratörsroller i PIM:

- Kontoadministratör
- Tjänstadministratör
- Medadministratör

Läs mer om administratörsroller för klassiska prenumerationer [administratörsroller för klassiska prenumeration, Azure RBAC-roller och Azure AD-administratörsroller](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Vad gäller för Office 365-administratörsroller?

Roller i Exchange Online eller SharePoint Online, förutom Exchange-administratör och SharePoint-administratör visas inte i Azure AD och kan inte hanteras i PIM. Mer information om dessa Office 365-tjänster finns i [Office 365-administratörsroller](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> SharePoint-administratör har administrativ åtkomst till SharePoint Online via administrationscentret för SharePoint Online och kan utföra nästan alla aktiviteter i SharePoint Online. Berättigade användare avbrott med hjälp av den här rollen i SharePoint när du har aktiverat i PIM.

## <a name="next-steps"></a>Nästa steg

- [Börja använda PIM](pim-getting-started.md)
- [Tilldela Azure AD-katalogroller i PIM](pim-how-to-add-role-to-user.md)
- [Tilldela Azure-resursroller i PIM](pim-resource-roles-assign-roles.md)
