---
title: Visa roller som har tilldelats till en grupp i Azure Active Directory | Microsoft Docs
description: Lär dig hur rollerna som har tilldelats en grupp kan visas med hjälp av Azure AD administrations Center. Att Visa grupper och tilldelade roller är standard användar behörigheter.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e578c90a05085df33c2d547402256cfc38229aa3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377537"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Visa roller som har tilldelats till en grupp i Azure Active Directory

I det här avsnittet beskrivs hur de roller som har tilldelats en grupp kan visas med hjälp av Azure AD administrations Center. Att Visa grupper och tilldelade roller är standard användar behörigheter.

1. Logga in på [administrations centret för Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med eventuella autentiseringsuppgifter som inte är administratör eller administratör.

1. Välj den grupp som du är intresse rad av.

1. Välj **tilldelade roller**. Nu kan du se alla Azure AD-roller som har tilldelats den här gruppen.

   ![Visa alla roller som har tilldelats en vald grupp](./media/groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>Använda PowerShell

### <a name="get-object-id-of-the-group"></a>Hämta objekt-ID för gruppen

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>Visa roll tilldelning till en grupp

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Använda Microsoft Graph API

### <a name="get-object-id-of-the-group"></a>Hämta objekt-ID för gruppen

```powershell
GET https://graph.microsoft.com/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>Hämta roll tilldelningar till en grupp

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>Nästa steg

- [Använd moln grupper för att hantera roll tilldelningar](groups-concept.md)
- [Felsökningsroller tilldelade till molngrupper](groups-faq-troubleshooting.md)
