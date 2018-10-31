---
title: 'Azure Active Directory Domain Services: Omfattar synkronisering | Microsoft Docs'
description: Konfigurera begränsade synkronisering från Azure AD till din hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: ergreenl
ms.openlocfilehash: 7726a28e0254f4316759c3d8c74f57db6a9e399e
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242202"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Konfigurera begränsade synkronisering från Azure AD till din hanterade domän
Den här artikeln visar hur du konfigurerar endast specifika användarkonton som ska synkroniseras från din Azure AD-katalog till din hanterade domän i Azure AD Domain Services.


## <a name="group-based-scoped-synchronization"></a>Gruppbaserad begränsade synkronisering
Som standard synkroniseras alla användare och grupper i Azure AD-katalogen till din hanterade domän. Om bara ett fåtal användare använda den hanterade domänen, kan du synkronisera enbart de användarkontona. Gruppbaserad begränsade synkronisering kan du göra detta. När du konfigurerat, synkroniseras användarkonton som hör till de grupper som du har angett till den hanterade domänen.

Tabellen nedan hjälper dig att avgöra hur du använder begränsade synkronisering:

| **Aktuell status** | **Önskat tillstånd** | **Konfiguration som krävs** |
| --- | --- | --- |
| Din befintliga hanterade domän är konfigurerad för att synkroniseras alla användarkonton och grupper. | Du vill synkronisera endast användarkonton som tillhör specifika grupper till din hanterade domän. | [Ta bort den befintliga hanterade domänen](active-directory-ds-disable-aadds.md). Följ instruktionerna i den här artikeln för att återskapa det begränsade synkronisering är konfigurerat. |
| Du har inte en befintlig hanterad domän. | Du vill skapa en ny hanterad domän och synkronisera endast användarkonton som tillhör specifika grupper. | Följ instruktionerna i den här artikeln för att skapa en ny hanterad domän med begränsade synkronisering har konfigurerats. |
| Din befintliga hanterade domän är konfigurerad för att synkronisera bara de konton som tillhör specifika grupper. | Du vill ändra i listan över grupper vars användare ska synkroniseras till Hantera domänen. | Följ anvisningarna i den här artikeln för att ändra begränsade synkronisering. |

> [!WARNING]
> **Ändrar omfånget av synkronisering gör din hanterade domän genomgå för omsynkronisering.**
>
 * När du ändrar synkronisering omfånget för en hanterad domän, sker en fullständig omsynkronisering.
 * Objekt som inte längre behövs i den hanterade domänen tas bort. Nya objekt skapas i den hanterade domänen.
 * Omsynkroniseringen kan ta lång tid att slutföra beroende på hur många objekt (användare, grupper och gruppmedlemskap) i den hanterade domänen och Azure AD-katalogen. Omsynkroniseringen kan ta ett par dagar för stora kataloger med flera hundra tusentals objekt.
>
>


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization"></a>Skapa en ny hanterad domän och aktivera gruppbaserad begränsade synkronisering
Använda PowerShell för att slutföra den här uppsättningen med steg. Se anvisningarna för att [aktiverar Azure Active Directory Domain Services med hjälp av PowerShell](active-directory-ds-enable-using-powershell.md). Några steg i den här artikeln har ändrats något om du vill konfigurera begränsade synkronisering.

Utför följande steg för att konfigurera gruppbaserad begränsade synkronisering till din hanterade domän:

1. Utför följande uppgifter:
  * [Uppgift 1: Installera PowerShell-moduler som krävs](active-directory-ds-enable-using-powershell.md#task-1-install-the-required-powershell-modules).
  * [Uppgift 2: Skapa nödvändiga tjänstens huvudnamn i Azure AD-katalogen](active-directory-ds-enable-using-powershell.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory).
  * [Uppgift 3: Skapa och konfigurera gruppen ”AAD DC-administratörer”](active-directory-ds-enable-using-powershell.md#task-3-create-and-configure-the-aad-dc-administrators-group).
  * [Uppgift 4: Registrera Azure AD Domain Services-resursprovidern](active-directory-ds-enable-using-powershell.md#task-4-register-the-azure-ad-domain-services-resource-provider).
  * [Uppgift 5: Skapa en resursgrupp](active-directory-ds-enable-using-powershell.md#task-5-create-a-resource-group).
  * [Uppgift 6: Skapa och konfigurera det virtuella nätverket](active-directory-ds-enable-using-powershell.md#task-6-create-and-configure-the-virtual-network).

2. Välj de grupper som du vill synkronisera och ange visningsnamnet för de grupper som du vill att synkroniseras till din hanterade domän.

3. Spara den [skript i följande avsnitt](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) till en fil med namnet ```Select-GroupsToSync.ps1```. Kör skriptet som den här:

  ```powershell
  .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
  ```

  > [!WARNING]
  > **Glöm inte att inkludera gruppen ”AAD DC-administratörer”.**
  >
  > I listan över grupper som konfigurerats för begränsade synkronisering måste du inkludera gruppen ”AAD DC-administratörer”. Om du inte använder den här gruppen, blir den hanterade domänen inte kan användas.
  >

4. Nu kan skapa den hanterade domänen och aktivera gruppbaserad begränsade synkronisering för den hanterade domänen. Egenskapen ```"filteredSync" = "Enabled"``` i den ```Properties``` parametern. Till exempel finns i den följande skript fragment som kopieras från [uppgift 7: etablera den hanterade domänen i Azure AD Domain Services](active-directory-ds-enable-using-powershell.md#task-7-provision-the-azure-ad-domain-services-managed-domain).

  ```powershell
  $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
  $ManagedDomainName = "contoso100.com"
  $ResourceGroupName = "ContosoAaddsRg"
  $VnetName = "DomainServicesVNet_WUS"
  $AzureLocation = "westus"

  # Enable Azure AD Domain Services for the directory.
  New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
  ```

  > [!TIP]
  > Glöm inte att inkludera ```"filteredSync" = "Enabled"``` i den ```-Properties``` parameter, så begränsade synkronisering har aktiverats för den hanterade domänen.


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


## <a name="modify-group-based-scoped-synchronization"></a>Ändra gruppbaserad begränsade synkronisering
Om du vill ändra i listan över grupper vars användare ska synkroniseras till din hanterade domän, kör den [PowerShell-skript](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) och ange den nya listan över grupper. Kom ihåg att alltid ange gruppen ”AAD DC-administratörer” i den här listan.

> [!WARNING]
> **Glöm inte att inkludera gruppen ”AAD DC-administratörer”.**
>
> I listan över grupper som konfigurerats för begränsade synkronisering måste du inkludera gruppen ”AAD DC-administratörer”. Om du inte använder den här gruppen, blir den hanterade domänen inte kan användas.
>


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

## <a name="next-steps"></a>Nästa steg
* [Förstå synkronisering i Azure AD Domain Services](active-directory-ds-synchronization.md)
* [Aktivera Azure Active Directory Domain Services med hjälp av PowerShell](active-directory-ds-enable-using-powershell.md)
