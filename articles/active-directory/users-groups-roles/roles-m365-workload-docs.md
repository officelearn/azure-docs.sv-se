---
title: Administratören Rollinnehåll för Microsoft 365 arbetsbelastningar – Azure AD | Microsoft Docs
description: Hitta innehåll och API-referenser för administratörsroller för Microsoft 365 arbetsbelastningar i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 8408d620ba78fed07c44feae74c607a555d5563b
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910593"
---
# <a name="administrator-roles-for-microsoft-365-workloads"></a>Administratörsroller för Microsoft 365 arbetsbelastningar

Alla produkter i Microsoft 365 kan hanteras med administrativa roller i Azure AD. Vissa produkter kan du även ange ytterligare roller som är specifika för den här produkten. Information om de roller som stöds av varje produkt, finns i tabellen nedan. Allmän diskussion om problem med delegering finns i [rolldelegering planering i Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Var du hittar innehåll

Microsoft 365 arbetsbelastning | Obsah role | API-innehåll
---------------------- | ------------------ | -----------------
Administratörsroller i Office 365 och Microsoft 365 affärsplaner | [Office 365-administratörsroller](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Inte tillgängligt
Azure Active Directory (Azure AD) och Azure AD Identity Protection| [Azure AD-administratörsroller](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
exchange online| [Exchange-rollbaserad åtkomstkontroll](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell för Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
sharepoint online | [Azure AD-administratörsroller](directory-assign-admin-roles.md)<br>Även [om SharePoint-administratörsrollen i Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Team/Skype för företag | [Azure AD-administratörsroller](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Säkerhets- och Efterlevnadscenter (Office 365 Advanced Threat Protection, Exchange Online-skydd, Information Protection) | [Office 365-administratörsroller](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Säkerhetspoäng | [Azure AD-administratörsroller](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Compliance Manager | [Compliance Manager roller](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Inte tillgängligt
Azure Information Protection | [Azure AD-administratörsroller](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/cloud-app-security/manage-admins) | [API-referens](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP-rollgrupper](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Inte tillgängligt
Windows Defender Advanced Threat Protection | [Windows Defender ATP-rollbaserad åtkomstkontroll](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Inte tillgängligt
Privileged Identity Management | [Azure AD-administratörsroller](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Intune-rollbaserad åtkomstkontroll](https://docs.microsoft.com/intune/role-based-access-control) | [Graph API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Hanterade Desktop | [Azure AD-administratörsroller](directory-assign-admin-roles.md) | [Graph API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Hämta rolltilldelningar](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Nästa steg

* [Tilldela eller ta bort Azure AD-administratörsroller](directory-manage-roles-portal.md)
* [Referera till Azure AD-administratörsroller](directory-assign-admin-roles.md)
