---
title: Roller som du inte kan hantera i Privilegierad identitetshantering – Azure Active Directory | Microsoft-dokument
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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72895208"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Roller som du inte kan hantera i privilegierad identitetshantering

Med Azure Active Directory (Azure AD) Privilegierad identitetshantering (PIM) kan du hantera alla [Azure AD-roller](../users-groups-roles/directory-assign-admin-roles.md) och alla [Azure-resursroller](../../role-based-access-control/built-in-roles.md). Dessa roller inkluderar även dina anpassade roller som är kopplade till hanteringsgrupper, prenumerationer, resursgrupper och resurser. Det finns dock få roller som du inte kan hantera. I den här artikeln beskrivs de roller som du inte kan hantera i Privilegierad identitetshantering.

## <a name="classic-subscription-administrator-roles"></a>Administratörsroller för klassiska prenumerationer

Du kan inte hantera följande klassiska prenumerationsadministratörsroller i Privilegierad identitetshantering:

- Kontoadministratör
- Tjänstadministratör
- Medadministratör

Mer information om de klassiska rollerna för prenumerationsadministratörer finns i [Klassiska prenumerationsadministratörsroller, Azure RBAC-roller och Azure AD-administratörsroller](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Hur är det med office 365-administratörsroller?

Roller inom Exchange Online eller SharePoint Online, med undantag för Exchange-administratör och SharePoint-administratör, representeras inte i Azure AD och kan därför inte hanteras i Privilegierad identitetshantering. Mer information om dessa Office 365-tjänster finns i [Office 365-administratörsroller](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> SharePoint-administratören har administrativ åtkomst till SharePoint Online via administrationscentret för SharePoint Online och kan utföra nästan alla uppgifter i SharePoint Online. Berättigade användare kan uppleva fördröjningar med den här rollen i SharePoint efter aktivering i Privilegierad identitetshantering.

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i privilegierad identitetshantering](pim-how-to-add-role-to-user.md)
- [Tilldela Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-assign-roles.md)
