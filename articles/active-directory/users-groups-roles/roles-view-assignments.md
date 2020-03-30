---
title: Visa anpassade rolltilldelningar i Azure AD-portalen | Microsoft-dokument
description: Du kan nu se och hantera medlemmar i en Azure AD-administratörsroll i Azure AD-administrationscentret.
services: active-directory
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
ms.openlocfilehash: f04bd7788a9cc9657e14aedfb153182d6e53eb95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259713"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Visa anpassade rolltilldelningar i Azure Active Directory

I den här artikeln beskrivs hur du visar anpassade roller som du har tilldelat i Azure Active Directory (Azure AD). I Azure Active Directory (Azure AD) kan roller tilldelas i ett organisationsomfattande scope eller med ett programomfång.

- Rolltilldelningar i organisationsomfattande omfång läggs till och kan ses i listan över enskilda programrolltilldelningar.
- Rolltilldelningar i det enskilda programomfånget läggs inte till och kan inte ses i listan över organisationsomfattande begränsade tilldelningar.

## <a name="view-role-assignments-in-the-azure-portal"></a>Visa rolltilldelningar i Azure-portalen

Den här proceduren beskriver visning av tilldelningar av en roll med organisationsomfattande omfång.

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com) med privilegierad rolladministratör eller globala administratörsbehörigheter i Azure AD-organisationen.
1. Välj **Azure Active Directory**, välj Roller och **administratörer**och välj sedan en roll för att öppna den och visa dess egenskaper.
1. Välj **Tilldelningar** om du vill visa tilldelningarna för rollen.

    ![Visa rolltilldelningar och behörigheter när du öppnar en roll från listan](./media/roles-view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Visa rolltilldelningar med Azure AD PowerShell

I det här avsnittet beskrivs hur du visar tilldelningar av en roll med organisationsomfattande omfång. Den här artikeln använder [Azure Active Directory PowerShell Version 2-modulen.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) Om du vill visa scopetilldelningar med ett program med PowerShell kan du använda cmdlets i [Tilldela anpassade roller med PowerShell](roles-assign-powershell.md).

### <a name="prepare-powershell"></a>Förbered PowerShell

Först måste du [hämta Azure AD preview PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/).

Så här installerar du Azure AD PowerShell-modulen med följande kommandon:

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Om du vill kontrollera att modulen är klar att användas använder du följande kommando:

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>Visa tilldelningar för en roll

Exempel på hur du visar tilldelningar av en roll.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Visa rolltilldelningar med Microsoft Graph API

I det här avsnittet beskrivs hur du visar tilldelningar av en roll med organisationsomfattande omfång.  Om du vill visa scopetilldelningar med ett program med Graph API kan du använda åtgärderna i [Tilldela anpassade roller med Graph API](roles-assign-graph.md).

HTTP-begäran om att få en rolltilldelning för en viss rolldefinition.

HÄMTA

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Svar

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-assignments-of-single-application-scope"></a>Visa tilldelningar av scope för ett enda program

I det här avsnittet beskrivs hur du visar tilldelningar av en roll med ett programomfång. Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com) med privilegierad rolladministratör eller globala administratörsbehörigheter i Azure AD-organisationen.
1. Välj **Appregistreringar**och välj sedan appregistreringen för att visa dess egenskaper. Du kanske måste välja **Alla program** för att se den fullständiga listan över appregistreringar i din Azure AD-organisation.

    ![Skapa eller redigera appregistreringar från sidan Appregistreringar](./media/roles-create-custom/appreg-all-apps.png)

1. I appregistreringen väljer du **Roller och administratörer**och väljer sedan en roll för att visa dess egenskaper.

    ![Visa rolltilldelningar för appregistreringar från sidan Appregistreringar](./media/roles-view-assignments/appreg-assignments.png)

1. Välj **Tilldelningar** om du vill visa tilldelningarna för rollen. När du öppnar tilldelningsvyn inifrån appregistreringen visas de tilldelningar som är begränsade till den här Azure AD-resursen.

    ![Visa rolltilldelningar för appregistrering från egenskaperna för en appregistrering](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>Nästa steg

* Känn dig välkommen att dela med oss på [Azure AD administrativa roller forum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Mer information om roller och administratörsrolltilldelning finns i [Tilldela administratörsroller](directory-assign-admin-roles.md).
* Standardanvändarbehörigheter finns i en [jämförelse av standardbehörighet för gäst- och medlemsanvändare](../fundamentals/users-default-permissions.md).
