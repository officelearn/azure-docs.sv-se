---
title: Administratörsrolldokument för Microsoft 365-tjänster – Azure AD | Microsoft-dokument
description: Hitta innehålls- och API-referenser för administratörsroller för Microsoft 365-tjänster i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 430b65217da33185cd3b5c235fb148f3350e1bfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024460"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Administratörsroller för Microsoft 365-tjänster

Alla produkter i Microsoft 365 kan hanteras med administrativa roller i Azure AD. Vissa produkter ger också ytterligare roller som är specifika för den produkten. Information om de roller som stöds av varje produkt finns i tabellen nedan. Allmänna diskussioner om delegeringsproblem finns i [rolldelegeringsplanering i Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Var du hittar innehåll

Microsoft 365-tjänst | Rollinnehåll | API-innehåll
---------------------- | ------------------ | -----------------
Administratörsroller i Office 365- och Microsoft 365-affärsplaner | [Office 365-administratörsroller](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Inte tillgängligt
Azure Active Directory (Azure AD) och Azure AD-identitetsskydd| [Azure AD-administratörsroller](directory-assign-admin-roles.md) | [Api för diagram](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
exchange online| [Rollbaserad åtkomstkontroll för Exchange](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell för utbyte](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
sharepoint online | [Azure AD-administratörsroller](directory-assign-admin-roles.md)<br>Om även [SharePoint-administratörsrollen i Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Api för diagram](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Team/Skype för företag | [Azure AD-administratörsroller](directory-assign-admin-roles.md) | [Api för diagram](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Security & Compliance Center (Office 365 Avancerat skydd mot hot, utbyte onlineskydd, informationsskydd) | [Office 365-administratörsroller](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Växla PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Säkra poäng | [Azure AD-administratörsroller](directory-assign-admin-roles.md) | [Api för diagram](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Compliance Manager (Efterlevnadshanteraren) | [Rollerna för Efterlevnadshanteraren](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Inte tillgängligt
Azure Information Protection | [Azure AD-administratörsroller](directory-assign-admin-roles.md) | [Api för diagram](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/cloud-app-security/manage-admins) | [API-referens](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP-rollgrupper](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Inte tillgängligt
Windows Defender Advanced Threat Protection | [Windows Defender ATP-rollbaserad åtkomstkontroll](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Inte tillgängligt
Privileged Identity Management | [Azure AD-administratörsroller](directory-assign-admin-roles.md) | [Api för diagram](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Intune rollbaserad åtkomstkontroll](https://docs.microsoft.com/intune/role-based-access-control) | [Api för diagram](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Hanterat skrivbord | [Azure AD-administratörsroller](directory-assign-admin-roles.md) | [Api för diagram](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Nästa steg

* [Tilldela eller ta bort Azure AD-administratörsroller](directory-manage-roles-portal.md)
* [Referens för Azure AD-administratörsroller](directory-assign-admin-roles.md)
