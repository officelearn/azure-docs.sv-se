---
title: Skapa en grupp för att tilldela roller i Azure Active Directory | Microsoft Docs
description: Förhandsgranska anpassade Azure AD-roller för att delegera identitets hantering. Hantera Azure-roller i Azure Portal, PowerShell eller Graph API.
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
ms.openlocfilehash: 1c5efbbd5f9f5dfe701deb50b51605ab324c081a
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401681"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Skapa en roll tilldelnings bara grupp i Azure Active Directory

Du kan bara tilldela en roll till en grupp som har skapats med egenskapen "isAssignableToRole" inställd på True, eller så har du skapat den i Azure AD-portalen med **Azure AD-roller som du kan tilldela gruppen** aktiverade. Det här gruppattributet gör gruppen ett som kan tilldelas till en roll i Azure Active Directory (Azure AD). Den här artikeln beskriver hur du skapar den här särskilda typen av grupp. **Obs:** En grupp med egenskapen isAssignableToRole inställt på True får inte vara av typen dynamiskt medlemskap. Mer information finns i [använda en grupp för att hantera roll tilldelningar för Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-groups-concept).

## <a name="using-azure-ad-admin-center"></a>Använda Azure AD administrations Center

1. Logga in på [administrations centret för Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med privilegierade roll administratörer eller globala administratörs behörigheter i Azure AD-organisationen.
1. Välj **grupper**  >  **alla grupper**  >  **ny grupp**.

    [![Öppna Azure Active Directory och skapa en ny grupp.](media/roles-groups-create-eligible/new-group.png "Öppna Azure Active Directory och skapa en ny grupp.")](media/roles-groups-create-eligible/new-group.png#<lightbox>)

1. Ange grupp typ, namn och beskrivning på fliken **ny grupp** .
1. Aktivera **Azure AD-roller kan tilldelas till gruppen**. Den här växeln är bara synlig för administratörer av privilegierade roller och globala administratörer eftersom dessa endast är två roller som kan ställa in växeln.

    [![Gör den nya gruppen berättigad för roll tilldelning](media/roles-groups-create-eligible/eligible-switch.png "Gör den nya gruppen berättigad för roll tilldelning")](media/roles-groups-create-eligible/eligible-switch.png#<lightbox>)

1. Välj medlemmar och ägare för gruppen. Du kan också välja att tilldela roller till gruppen, men du behöver inte tilldela en roll här.

    [![Lägg till medlemmar i den roll tilldelnings bara gruppen och tilldela roller.](media/roles-groups-create-eligible/specify-members.png "Lägg till medlemmar i den roll tilldelnings bara gruppen och tilldela roller.")](media/roles-groups-create-eligible/specify-members.png#<lightbox>)

1. När du har angett medlemmar och ägare väljer du **skapa**.

    [![Knappen Skapa visas längst ned på sidan.](media/roles-groups-create-eligible/create-button.png "Knappen Skapa visas längst ned på sidan.")](media/roles-groups-create-eligible/create-button.png#<lightbox>)

Gruppen skapas med roller som du kanske har tilldelat till den.

## <a name="using-powershell"></a>Använda PowerShell

### <a name="install-the-azure-ad-preview-module"></a>Installera Azure AD Preview-modulen

```powershell
install-module azureadpreview
import-module azureadpreview
```

För att kontrol lera att modulen är redo att användas, utfärda följande kommando:

```powershell
get-module azureadpreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Skapa en grupp som kan tilldelas rollen

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

För den här typen av grupp `isPublic` kommer alltid att vara falskt och `isSecurityEnabled` kommer alltid att vara sann.

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>Kopiera en grupps användare och tjänstens huvud namn till en grupp som kan tilldelas av roller

```powershell
#Basic set up
install-module azureadpreview
import-module azureadpreview
get-module azureadpreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating O365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>Använda Microsoft Graph API

### <a name="create-a-role-assignable-group-in-azure-ad"></a>Skapa en roll tilldelnings bara grupp i Azure AD

```powershell
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

För den här typen av grupp `isPublic` kommer alltid att vara falskt och `isSecurityEnabled` kommer alltid att vara sann.

## <a name="next-steps"></a>Nästa steg

- [Tilldela en roll till en moln grupp](roles-groups-assign-role.md)
- [Använd moln grupper för att hantera roll tilldelningar](roles-groups-concept.md)
- [Felsökningsroller tilldelade till molngrupper](roles-groups-faq-troubleshooting.md)
