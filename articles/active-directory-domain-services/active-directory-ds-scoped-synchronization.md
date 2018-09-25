---
title: 'Azure Active Directory Domain Services: Omfattar synkronisering | Microsoft Docs'
description: Konfigurera begränsade synkronisering från Azure AD till din hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2018
ms.author: maheshu
ms.openlocfilehash: ada6e7536ec91e5b1f35f1740177ae264fe68cf7
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063499"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Konfigurera begränsade synkronisering från Azure AD till din hanterade domän
Den här artikeln visar hur du konfigurerar endast specifika användarkonton som ska synkroniseras från din Azure AD-katalog till din hanterade domän i Azure AD Domain Services.


## <a name="group-based-scoped-synchronization"></a>Gruppbaserad begränsade synkronisering
Som standard synkroniseras alla användare och grupper i Azure AD-katalogen till din hanterade domän. Om den hanterade domänen används endast av några av användarna, kanske du vill synkronisera enbart de användarkontona till den hanterade domänen. Gruppbaserad begränsade synkronisering kan du göra detta. När du konfigurerat, synkroniseras användarkonton som hör till de grupper som du har angett till den hanterade domänen.


## <a name="get-started-install-the-required-powershell-modules"></a>Kom igång: Installera PowerShell-moduler som krävs

### <a name="install-and-configure-azure-ad-powershell"></a>Installera och konfigurera Azure AD PowerShell
Följ instruktionerna i artikeln om du vill [installerar Azure AD PowerShell-modulen och ansluter till Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Installera och konfigurera Azure PowerShell
Följ instruktionerna i artikeln om du vill [installera Azure PowerShell-modulen och ansluta till din Azure-prenumeration](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).



## <a name="enable-group-based-scoped-synchronization"></a>Aktivera gruppbaserad begränsade synkronisering
Utför följande steg för att konfigurera gruppbaserad begränsade synkronisering till din hanterade domän:

1. Välj de grupper som du vill synkronisera och ange visningsnamnet för de grupper som du vill att synkroniseras till din hanterade domän.

2. Spara skriptet i följande avsnitt för att en fil med namnet ```Select-GroupsToSync.ps1```. Kör skriptet som den här:

  ```powershell
  .\Select-GroupsToSync.ps1 -groupsToAdd @(“GroupName1”, “GroupName2”)
  ```

3. Nu kan aktivera gruppbaserad begränsade synkronisering för den hanterade domänen.

  ```powershell
  // Login to your Azure AD tenant
  Login-AzureRmAccount

  // Retrieve the Azure AD Domain Services resource.
  $DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

  // Enable group-based scoped synchronization.
  $enableScopedSync = @{"filteredSync" = "Enabled"}

  Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
  ```

## <a name="disable-group-based-scoped-synchronization"></a>Inaktivera gruppbaserad begränsade synkronisering
Använd följande PowerShell-skript för att inaktivera gruppbaserad begränsad synkronisering för din hanterade domän:

```powershell
// Login to your Azure AD tenant
Login-AzureRmAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>Skript för att välja grupper ska synkroniseras till den hanterade domänen (Välj GroupsToSync.ps1)
Spara följande skript i en fil (```Select-GroupsToSync.ps1```). Det här skriptet konfigurerar Azure AD Domain Services för att synkronisera valda grupper till den hanterade domänen. Alla användarkonton som hör till de angivna grupperna kommer att synkroniseras till den hanterade domänen.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occured assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="next-steps"></a>Nästa steg
* [Förstå synkronisering i Azure AD Domain Services](active-directory-ds-synchronization.md)
