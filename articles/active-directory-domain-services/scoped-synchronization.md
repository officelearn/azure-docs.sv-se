---
title: Omfångs synkronisering för Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du konfigurerar omfångst synkronisering från Azure AD till en Azure Active Directory Domain Services hanterad domän
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 09/06/2019
ms.author: iainfou
ms.openlocfilehash: 5fe19d3800883782187ae15c0a6fc0cd9709f0e9
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842683"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Konfigurera omfångst synkronisering från Azure AD till Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) för att tillhandahålla Authentication Services synkroniserar användare och grupper från Azure AD. I en hybrid miljö kan användare och grupper från en lokal Active Directory Domain Services-miljö (AD DS) först synkroniseras till Azure AD med hjälp av Azure AD Connect och sedan synkroniseras till Azure AD DS. Som standard synkroniseras alla användare och grupper från en Azure AD-katalog till en hanterad Azure AD DS-domän. Om du har speciella behov kan du istället välja att bara synkronisera en definierad uppsättning användare.

Den här artikeln visar hur du skapar en Azure AD DS-hanterad domän som använder omfångs synkronisering och sedan ändrar eller inaktiverar uppsättningen med begränsade användare.

## <a name="scoped-synchronization-overview"></a>Översikt över omfånget synkronisering

Som standard synkroniseras alla användare och grupper från en Azure AD-katalog till en hanterad Azure AD DS-domän. Om bara några få användare behöver åtkomst till den hanterade domänen kan du bara synkronisera dessa användar konton. Den här omfånget är gruppbaserad. När du konfigurerar gruppbaserad omsynkronisering synkroniseras bara de användar konton som tillhör de grupper som du anger till den hanterade domänen i Azure AD DS.

I följande tabell beskrivs hur du använder omfångst synkronisering:

| Aktuellt tillstånd | Önskat tillstånd | Obligatorisk konfiguration |
| --- | --- | --- |
| En befintlig hanterad domän är konfigurerad för att synkronisera alla användar konton och grupper. | Du vill bara synkronisera användar konton som tillhör vissa grupper. | Du kan inte ändra från att synkronisera alla användare till att använda omfångs synkronisering. [Ta bort den befintliga hanterade domänen](delete-aadds.md)och följ sedan stegen i den här artikeln för att återskapa en Azure AD DS-hanterad domän med begränsad synkronisering konfigurerad. |
| Ingen befintlig hanterad domän. | Du vill skapa en ny hanterad domän och endast synkronisera användar konton som tillhör vissa grupper. | Följ stegen i den här artikeln för att skapa en Azure AD DS-hanterad domän med begränsad synkronisering konfigurerad. |
| En befintlig hanterad domän är konfigurerad för att endast synkronisera konton som tillhör vissa grupper. | Du vill ändra listan över grupper vars användare ska synkroniseras till den hanterade Azure AD DS-domänen. | Följ stegen i den här artikeln för att ändra omfångs synkroniseringen. |

Du kan använda Azure Portal eller PowerShell för att konfigurera inställningarna för omfånget synkronisering:

| Action | | |
|--|--|--|
| Skapa en Azure AD DS-hanterad domän och konfigurera omfångs synkronisering | [Azure Portal](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| Ändra omfångs synkronisering | [Azure Portal](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| Inaktivera omfångs synkronisering | [Azure Portal](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Att ändra omfånget för synkronisering innebär att den Azure AD DS-hanterade domänen synkroniserar om alla data.
> 
>  * När du ändrar omfånget för synkronisering för en Azure AD DS-hanterad domän sker en fullständig omsynkronisering.
>  * Objekt som inte längre krävs i den hanterade Azure AD DS-domänen tas bort. Nya objekt skapas i den hanterade domänen.
>  * Det kan ta lång tid att slutföra omsynkroniseringen. Synkroniseringstid beror på antalet objekt, till exempel användare, grupper och grupp medlemskap i den hanterade domänen i Azure AD DS och Azure AD-katalogen. För stora kataloger med hundratals tusen objekt kan omsynkroniseringen ta några dagar.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Aktivera omfångs synkronisering med hjälp av Azure Portal

1. Följ [själv studie kursen för att skapa och konfigurera en Azure AD DS-instans](tutorial-create-instance.md). Slutför alla nödvändiga komponenter och distributions steg förutom för omfånget synkronisering.
1. Välj **omfång** i steget synkronisering och välj sedan de Azure AD-grupper som ska synkroniseras till Azure AD DS-instansen.

Den hanterade domänen i Azure AD DS kan ta upp till en timme att slutföra distributionen. På sidan Azure Portal visar **översikts** sidan för din Azure AD DS-hanterade domän den aktuella statusen i den här distributions fasen.

När Azure Portal visar att den hanterade domänen för Azure AD DS har slutfört etableringen måste följande åtgärder utföras:

* Uppdatera DNS-inställningarna för det virtuella nätverket så att virtuella datorer kan hitta den hanterade domänen för domän anslutning eller autentisering.
    * Om du vill konfigurera DNS väljer du din Azure AD DS-hanterade domän i portalen. I **översikts** fönstret uppmanas du att konfigurera dessa DNS-inställningar automatiskt.
* [Aktivera Lösenordssynkronisering till Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) så att slutanvändarna kan logga in på den hanterade domänen med sina företags uppgifter.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Ändra omfångs synkronisering med Azure Portal

Om du vill ändra listan över grupper vars användare ska synkroniseras till den hanterade Azure AD DS-domänen utför du följande steg:

1. I Azure Portal väljer du din Azure AD DS-instans, till exempel *contoso.com*.
1. Välj **synkronisering** på menyn till vänster.
1. Om du vill lägga till en grupp väljer du **+ Välj grupper** högst upp och väljer sedan de grupper som ska läggas till.
1. Om du vill ta bort en grupp från omfånget synkronisering väljer du den i listan över synkroniserade grupper och väljer **ta bort grupper**.
1. När alla ändringar görs väljer du **Spara omfånget för synkronisering**.

Att ändra omfånget för synkronisering innebär att den Azure AD DS-hanterade domänen synkroniserar om alla data. Objekt som inte längre behövs i den hanterade domänen i Azure AD DS tas bort och omsynkronisering kan ta lång tid att slutföra.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Inaktivera omfångs synkronisering med Azure Portal

Utför följande steg för att inaktivera gruppbaserad omsynkronisering för en Azure AD DS-hanterad domän:

1. I Azure Portal väljer du din Azure AD DS-instans, till exempel *contoso.com*.
1. Välj **synkronisering** på menyn till vänster.
1. Ange omfånget för synkroniseringen från **omfång** till **alla**och välj sedan **Spara omfånget för synkronisering**.

Att ändra omfånget för synkronisering innebär att den Azure AD DS-hanterade domänen synkroniserar om alla data. Objekt som inte längre behövs i den hanterade domänen i Azure AD DS tas bort och omsynkronisering kan ta lång tid att slutföra.

## <a name="powershell-script-for-scoped-synchronization"></a>PowerShell-skript för omfångst synkronisering

Om du vill konfigurera omfångs synkronisering med PowerShell måste du först spara följande skript till `Select-GroupsToSync.ps1`en fil med namnet. Det här skriptet konfigurerar Azure AD DS för att synkronisera valda grupper från Azure AD. Alla användar konton som ingår i de angivna grupperna synkroniseras med den hanterade Azure AD DS-domänen.

Det här skriptet används i de ytterligare stegen i den här artikeln.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>Aktivera omfångs synkronisering med PowerShell

Använd PowerShell för att slutföra den här uppsättningen steg. Läs anvisningarna för att [aktivera Azure Active Directory Domain Services med hjälp av PowerShell](powershell-create-instance.md). Ett par steg i den här artikeln har ändrats något för att konfigurera omfångst synkronisering.

1. Utför följande åtgärder från artikeln för att aktivera Azure AD DS med hjälp av PowerShell. Stoppa i steget för att skapa den hanterade domänen. Du konfigurerar den omfångs synkronisering som du skapar den hanterade Azure AD DS-domänen.

   * [Installera de PowerShell-moduler som krävs](powershell-create-instance.md#prerequisites).
   * [Skapa den nödvändiga tjänstens huvud namn och Azure AD-grupp för administrativ åtkomst](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Skapa stöd för Azure-resurser som ett virtuellt nätverk och undernät](powershell-create-instance.md#create-supporting-azure-resources).

1. Bestäm vilka grupper och användare de innehåller som du vill synkronisera från Azure AD. Skapa en lista över visnings namnen för de grupper som ska synkroniseras till Azure AD DS.

1. Kör [skriptet från föregående avsnitt](#powershell-script-for-scoped-synchronization) och Använd parametern *-groupsToAdd* för att skicka listan över grupper som ska synkroniseras.

   > [!WARNING]
   > Du måste inkludera *Administratörs gruppen för AAD-domänkontrollanten* i listan över grupper för omfångs synkronisering. Om du inte tar med den här gruppen går det inte att använda den hanterade domänen i Azure AD DS.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Skapa nu den hanterade domänen för Azure AD DS och aktivera gruppbaserad synkronisering av omfattningar. Inkludera *"filteredSync" = "Enabled"* i parametern *-Properties* .

    Ange ditt ID för Azure-prenumerationen och ange sedan ett namn för den hanterade domänen, till exempel *contoso.com*. Du kan hämta ditt prenumerations-ID med hjälp av cmdleten [Get-AzSubscription][Get-AzSubscription] . Ange resurs gruppens namn, det virtuella nätverks namnet och regionen till de värden som användes i föregående steg för att skapa de stödda Azure-resurserna:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

Det tar några minuter att skapa resursen och returnera kontrollen till PowerShell-prompten. Azure AD DS-hanterad domän fortsätter att tillhandahållas i bakgrunden och kan ta upp till en timme att slutföra distributionen. På sidan Azure Portal visar **översikts** sidan för din Azure AD DS-hanterade domän den aktuella statusen i den här distributions fasen.

När Azure Portal visar att den hanterade domänen för Azure AD DS har slutfört etableringen måste följande åtgärder utföras:

* Uppdatera DNS-inställningarna för det virtuella nätverket så att virtuella datorer kan hitta den hanterade domänen för domän anslutning eller autentisering.
    * Om du vill konfigurera DNS väljer du din Azure AD DS-hanterade domän i portalen. I **översikts** fönstret uppmanas du att konfigurera dessa DNS-inställningar automatiskt.
* [Aktivera Lösenordssynkronisering till Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) så att slutanvändarna kan logga in på den hanterade domänen med sina företags uppgifter.

## <a name="modify-scoped-synchronization-using-powershell"></a>Ändra omfångs synkronisering med PowerShell

Om du vill ändra listan över grupper vars användare ska synkroniseras till den hanterade Azure AD DS-domänen kör du [PowerShell-skriptet](#powershell-script-for-scoped-synchronization) igen och anger den nya listan över grupper. I följande exempel innehåller grupperna för synkronisering inte längre *GroupName2*och innehåller nu *GroupName3*.

> [!WARNING]
> Du måste inkludera *Administratörs gruppen för AAD-domänkontrollanten* i listan över grupper för omfångs synkronisering. Om du inte tar med den här gruppen går det inte att använda den hanterade domänen i Azure AD DS.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Att ändra omfånget för synkronisering innebär att den Azure AD DS-hanterade domänen synkroniserar om alla data. Objekt som inte längre behövs i den hanterade domänen i Azure AD DS tas bort och omsynkronisering kan ta lång tid att slutföra.

## <a name="disable-scoped-synchronization-using-powershell"></a>Inaktivera omfångs synkronisering med PowerShell

Om du vill inaktivera gruppbaserad omsynkronisering för en Azure AD DS-hanterad domän anger du *"filteredSync" = "inaktive rad"* på Azure AD DS-resursen och uppdaterar sedan den hanterade domänen. När det är klart är alla användare och grupper inställda för synkronisering från Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Att ändra omfånget för synkronisering innebär att den Azure AD DS-hanterade domänen synkroniserar om alla data. Objekt som inte längre behövs i den hanterade domänen i Azure AD DS tas bort och omsynkronisering kan ta lång tid att slutföra.

## <a name="next-steps"></a>Nästa steg

Läs mer om synkroniseringsprocessen i [förstå synkronisering i Azure AD Domain Services](synchronization.md).

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
