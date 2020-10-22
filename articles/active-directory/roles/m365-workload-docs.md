---
title: Administratörs roll dokument i Microsoft 365 Services – Azure AD | Microsoft Docs
description: Hitta innehålls-och API-referenser för administratörs roller för Microsoft 365 Services i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 241858caef2129a0651c2118c09e5e7f1e1e3e01
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377542"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Administratörs roller för Microsoft 365 Services

Alla produkter i Microsoft 365 kan hanteras med administrativa roller i Azure AD. Vissa produkter ger också ytterligare roller som är speciella för produkten. Information om vilka roller som stöds av varje produkt finns i tabellen nedan. Allmänna diskussioner om Delegerings problem finns i [planering av roll delegering i Azure Active Directory](concept-delegation.md).

## <a name="where-to-find-content"></a>Var du hittar innehåll

Microsoft 365 tjänst | Roll innehåll | API-innehåll
---------------------- | ------------------ | -----------------
Administratörs roller i Office 365 och Microsoft 365 affärs planer | [Microsoft 365 administratörs roller](/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Inte tillgängligt
Azure Active Directory (Azure AD) och Azure AD Identity Protection| [Administratörs roller för Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Hämta roll tilldelningar](/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Rollbaserad åtkomst kontroll i Exchange](/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell för Exchange](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Hämta roll tilldelningar](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
sharepoint online | [Administratörs roller för Azure AD](permissions-reference.md)<br>Även [om administrations rollen för SharePoint i Microsoft 365](/sharepoint/sharepoint-admin-role) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Hämta roll tilldelningar](/graph/api/directoryrole-list?view=graph-rest-1.0)
Team/Skype för företag | [Administratörs roller för Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Hämta roll tilldelningar](/graph/api/directoryrole-list?view=graph-rest-1.0)
Säkerhets & Compliance Center (Office 365 Avancerat skydd, Exchange Online Protection, Information Protection) | [Office 365-administratörsroller](/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Hämta roll tilldelningar](/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Säkra Poäng | [Administratörs roller för Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Hämta roll tilldelningar](/graph/api/directoryrole-list?view=graph-rest-1.0)
Efterlevnadshanteraren | [Compliance Manager-roller](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Inte tillgängligt
Azure Information Protection | [Administratörs roller för Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Hämta roll tilldelningar](/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Rollbaserad åtkomstkontroll](/cloud-app-security/manage-admins) | [API-referens](/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP-rollgrupper](/azure-advanced-threat-protection/atp-role-groups) | Inte tillgängligt
Windows Defender Advanced Threat Protection | [Windows Defender ATP – rollbaserad åtkomst kontroll](/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Inte tillgängligt
Privileged Identity Management | [Administratörs roller för Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Hämta roll tilldelningar](/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Intune-rollbaserad åtkomst kontroll](/intune/role-based-access-control) | [Graph API](/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Hämta roll tilldelningar](/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Hanterat skriv bord | [Administratörs roller för Azure AD](permissions-reference.md) | [Graph API](/graph/api/overview?view=graph-rest-1.0)<br>[Hämta roll tilldelningar](/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Nästa steg

* [Tilldela eller ta bort administratörs roller för Azure AD](manage-roles-portal.md)
* [Roll referens för Azure AD-administratörer](permissions-reference.md)