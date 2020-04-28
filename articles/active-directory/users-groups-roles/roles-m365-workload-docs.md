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
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 430b65217da33185cd3b5c235fb148f3350e1bfe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74024460"
---
# <a name="administrator-roles-for-microsoft-365-services"></a>Administratörs roller för Microsoft 365 Services

Alla produkter i Microsoft 365 kan hanteras med administrativa roller i Azure AD. Vissa produkter ger också ytterligare roller som är speciella för produkten. Information om vilka roller som stöds av varje produkt finns i tabellen nedan. Allmänna diskussioner om Delegerings problem finns i [planering av roll delegering i Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Var du hittar innehåll

Microsoft 365 tjänst | Roll innehåll | API-innehåll
---------------------- | ------------------ | -----------------
Administratörs roller i Office 365 och Microsoft 365 affärs planer | [Office 365-administratörsroller](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Inte tillgängligt
Azure Active Directory (Azure AD) och Azure AD Identity Protection| [Administratörs roller för Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta roll tilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
exchange online| [Rollbaserad åtkomst kontroll i Exchange](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell för Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Hämta roll tilldelningar](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
sharepoint online | [Administratörs roller för Azure AD](directory-assign-admin-roles.md)<br>Även [om rollen SharePoint-administratör i Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta roll tilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Team/Skype för företag | [Administratörs roller för Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta roll tilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Säkerhets & Compliance Center (Office 365 Avancerat skydd, Exchange Online Protection, Information Protection) | [Office 365-administratörsroller](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Hämta roll tilldelningar](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Säkra Poäng | [Administratörs roller för Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta roll tilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Compliance Manager (Efterlevnadshanteraren) | [Compliance Manager-roller](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Inte tillgängligt
Azure Information Protection | [Administratörs roller för Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta roll tilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/cloud-app-security/manage-admins) | [API-referens](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP-rollgrupper](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Inte tillgängligt
Windows Defender Advanced Threat Protection | [Windows Defender ATP – rollbaserad åtkomst kontroll](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Inte tillgängligt
Privileged Identity Management | [Administratörs roller för Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta roll tilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Intune-rollbaserad åtkomst kontroll](https://docs.microsoft.com/intune/role-based-access-control) | [Graph API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Hämta roll tilldelningar](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Hanterat skriv bord | [Administratörs roller för Azure AD](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta roll tilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Nästa steg

* [Tilldela eller ta bort administratörs roller för Azure AD](directory-manage-roles-portal.md)
* [Roll referens för Azure AD-administratörer](directory-assign-admin-roles.md)
