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
ms.date: 03/31/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6eaa50f57dd8037ef0ad96b69284f565bd3558f
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607529"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Roller som du inte kan hantera i privilegierad identitetshantering

Med Azure Active Directory (Azure AD) Privilegierad identitetshantering (PIM) kan du hantera alla [Azure AD-roller](../users-groups-roles/directory-assign-admin-roles.md) och alla [Azure-roller](../../role-based-access-control/built-in-roles.md). Azure-roller kan också innehålla dina anpassade roller som är kopplade till dina hanteringsgrupper, prenumerationer, resursgrupper och resurser. Det finns dock få roller som du inte kan hantera. I den här artikeln beskrivs de roller som du inte kan hantera i Privilegierad identitetshantering.

## <a name="classic-subscription-administrator-roles"></a>Administratörsroller för klassiska prenumerationer

Du kan inte hantera följande klassiska prenumerationsadministratörsroller i Privilegierad identitetshantering:

- Kontoadministratör
- Tjänstadministratör
- Medadministratör

Mer information om de klassiska rollerna för prenumerationsadministratörer finns i [Klassiska prenumerationsadministratörsroller, Azure RBAC-roller och Azure AD-administratörsroller](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Hur är det med office 365-administratörsroller?

Vi stöder alla Office365-roller i portalen Azure AD-roller och administratörer, till exempel Exchange-administratör och SharePoint-administratör, men vi stöder inte specifika roller i Exchange RBAC eller SharePoint RBAC. Mer information om dessa Office 365-tjänster finns i [Office 365-administratörsroller](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Kvalificerade användare för SharePoint-administratörsrollen samt alla roller som försöker komma åt Microsoft Security and Compliance Center kan uppleva förseningar på upp till några timmar efter att deras roll har aktiverats. Vi arbetar med dessa team för att åtgärda problemen.

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i privilegierad identitetshantering](pim-how-to-add-role-to-user.md)
- [Tilldela Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-assign-roles.md)
