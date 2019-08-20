---
title: 'Azure Active Directory Domain Services: Omfångs synkronisering | Microsoft Docs'
description: Konfigurera omfångs synkronisering från Azure AD till dina hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 7d3bd8c6c62c0b8a1be6203e426337fcee7d2126
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617117"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Konfigurera omfångs synkronisering från Azure AD till din hanterade domän
Den här artikeln visar hur du konfigurerar enbart vissa användar konton som ska synkroniseras från din Azure AD-katalog till din Azure AD Domain Services hanterade domän.


## <a name="group-based-scoped-synchronization"></a>Gruppbaserad synkronisering av omfång
Som standard synkroniseras alla användare och grupper i Azure AD-katalogen med din hanterade domän. Om bara några få användare använder den hanterade domänen kan du bara synkronisera dessa användar konton. Med gruppbaserad omsynkronisering kan du göra det. När det är konfigurerat synkroniseras endast användar konton som tillhör de grupper som du har angett till den hanterade domänen.

Följande tabell hjälper dig att avgöra hur du ska använda omfångs synkronisering:

| **Nuvarande tillstånd** | **Önskat tillstånd** | **Obligatorisk konfiguration** |
| --- | --- | --- |
| Din befintliga hanterade domän är konfigurerad för att synkronisera alla användar konton och grupper. | Du vill bara synkronisera användar konton som tillhör vissa grupper till din hanterade domän. | [Ta bort den befintliga hanterade domänen](delete-aadds.md). Följ sedan instruktionerna i den här artikeln för att återskapa den med konfigurerad omfångs synkronisering. |
| Du har inte en befintlig hanterad domän. | Du vill skapa en ny hanterad domän och endast synkronisera användar konton som tillhör vissa grupper. | Följ anvisningarna i den här artikeln för att skapa en ny hanterad domän med begränsad synkronisering konfigurerad. |
| Din befintliga hanterade domän är konfigurerad för att endast synkronisera konton som tillhör vissa grupper. | Du vill ändra listan över grupper vars användare ska synkroniseras till hantera domän. | Följ instruktionerna i den här artikeln för att ändra omfångs synkroniseringen. |

> [!WARNING]
> **Om du ändrar omfånget för synkronisering går din hanterade domän att gå igenom omsynkroniseringen.**
> 
>  * När du ändrar omfånget för synkronisering för en hanterad domän sker en fullständig omsynkronisering.
>  * Objekt som inte längre behövs i den hanterade domänen tas bort. Nya objekt skapas i den hanterade domänen.
>  * Det kan ta lång tid att slutföra omsynkroniseringen, beroende på antalet objekt (användare, grupper och grupp medlemskap) i din hanterade domän och Azure AD-katalogen. För stora kataloger med hundratals tusen objekt kan omsynkroniseringen ta några dagar.


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>Skapa en ny hanterad domän och aktivera gruppbaserad omsynkronisering med hjälp av Azure Portal

1. Följ [komma igång-guiden](tutorial-create-instance.md) för att skapa en hanterad domän.
2. Välj **omfång** under valet av synkronisering i guiden Skapa Azure AD Domain Services.

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>Skapa en ny hanterad domän och aktivera gruppbaserad synkronisering med hjälp av PowerShell
Använd PowerShell för att slutföra den här uppsättningen steg. Läs anvisningarna för att [aktivera Azure Active Directory Domain Services med hjälp av PowerShell](powershell-create-instance.md). Ett par steg i den här artikeln har ändrats något för att konfigurera omfångst synkronisering.

Utför följande steg för att konfigurera gruppbaserad synkronisering med omfång till din hanterade domän:

1. Utför följande uppgifter:
   * [Uppgift 1: Installera de PowerShell-moduler](powershell-create-instance.md#task-1-install-the-required-powershell-modules)som krävs.
   * [Uppgift 2: Skapa det tjänst huvud namn som krävs i Azure AD](powershell-create-instance.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory)-katalogen.
   * [Uppgift 3: Skapa och konfigurera gruppen AAD DC-administratörer] PowerShell-Create-instance. MD # Task-3-Create-och-Configure-the-AAD-DC-administrators-Group).
   * [Uppgift 4: Registrera Azure AD Domain Services Resource Provider](powershell-create-instance.md#task-4-register-the-azure-ad-domain-services-resource-provider).
   * [Uppgift 5: Skapa en resurs grupp](powershell-create-instance.md#task-5-create-a-resource-group).
   * [Uppgift 6: Skapa och konfigurera det virtuella nätverket](powershell-create-instance.md#task-6-create-and-configure-the-virtual-network).

2. Välj de grupper som du vill synkronisera och ange visnings namnet för de grupper som du vill synkronisera till din hanterade domän.

3. Spara [skriptet i följande avsnitt](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) till en fil med namnet ```Select-GroupsToSync.ps1```. Kör skriptet som nedan:

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

   > [!WARNING]
   > **Glöm inte att lägga till gruppen "AAD DC-administratörer".**
   >
   > Du måste lägga till gruppen "AAD DC-administratörer" i listan över grupper som har kon figurer ATS för omfångs synkronisering. Om du inte tar med den här gruppen går det inte att använda den hanterade domänen.
   >

4. Skapa nu den hanterade domänen och aktivera gruppbaserad synkronisering för den hanterade domänen. Ta med egenskapen ```"filteredSync" = "Enabled"``` ```Properties``` i parametern. Se till exempel följande skript fragment, kopierat från [uppgift 7: Etablera den Azure AD Domain Services hanterade](powershell-create-instance.md#task-7-provision-the-azure-ad-domain-services-managed-domain)domänen.

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso.com"
   $ResourceGroupName = "ContosoAaddsRg"
   $VnetName = "DomainServicesVNet_WUS"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -ApiVersion 2017-06-01 -Force -Verbose
   ```

   > [!TIP]
   > Glöm inte att inkludera ```"filteredSync" = "Enabled"``` ```-Properties``` i parametern, så omfångst synkronisering är aktiverat för den hanterade domänen.


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>Skript för att välja grupper som ska synkroniseras till den hanterade domänen (Select-GroupsToSync. ps1)
Spara följande skript i en fil (```Select-GroupsToSync.ps1```). Det här skriptet konfigurerar Azure AD Domain Services att synkronisera valda grupper till den hanterade domänen. Alla användar konton som tillhör de angivna grupperna kommer att synkroniseras med den hanterade domänen.

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
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```


## <a name="modify-group-based-scoped-synchronization"></a>Ändra gruppbaserad synkronisering med omfång
Om du vill ändra listan över grupper vars användare ska synkroniseras till din hanterade domän, kör du [PowerShell-skriptet](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) igen och anger den nya listan över grupper. Kom ihåg att alltid ange gruppen "AAD DC-administratörer" i den här listan.

> [!WARNING]
> **Glöm inte att lägga till gruppen "AAD DC-administratörer".**
>
> Du måste lägga till gruppen "AAD DC-administratörer" i listan över grupper som har kon figurer ATS för omfångs synkronisering. Om du inte tar med den här gruppen går det inte att använda den hanterade domänen.
>


## <a name="disable-group-based-scoped-synchronization"></a>Inaktivera gruppbaserad synkronisering av omfång
Använd följande PowerShell-skript för att inaktivera gruppbaserad omsynkronisering för din hanterade domän:

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>Nästa steg
* [Förstå synkronisering i Azure AD Domain Services](synchronization.md)
* [Aktivera Azure Active Directory Domain Services med PowerShell](powershell-create-instance.md)
