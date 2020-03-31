---
title: Begränsad synkronisering för Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du konfigurerar begränsad synkronisering från Azure AD till en hanterad Azure Active Directory Domain Services-domän
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: cc126af67a0d8627d61e595cee56f3df8973340d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613039"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Konfigurera begränsad synkronisering från Azure AD till Azure Active Directory Domain Services

För att tillhandahålla autentiseringstjänster synkroniserar Azure Active Directory Domain Services (Azure AD DS) användare och grupper från Azure AD. I en hybridmiljö kan användare och grupper från en lokal AD DS-miljö (Active Directory Domain Services) först synkroniseras till Azure AD med Azure AD Connect och sedan synkroniseras med Azure AD DS.

Som standard synkroniseras alla användare och grupper från en Azure AD-katalog till en Azure AD DS-hanterad domän. Om du har specifika behov kan du i stället välja att bara synkronisera en definierad uppsättning användare.

Den här artikeln visar hur du skapar en Azure AD DS-hanterad domän som använder begränsad synkronisering och sedan ändrar eller inaktiverar uppsättningen scoped-användare.

## <a name="scoped-synchronization-overview"></a>Översikt över begränsad synkronisering

Som standard synkroniseras alla användare och grupper från en Azure AD-katalog till en Azure AD DS-hanterad domän. Om bara ett fåtal användare behöver komma åt den hanterade domänen kan du bara synkronisera dessa användarkonton. Den här begränsade synkroniseringen är gruppbaserad. När du konfigurerar gruppbaserad scoped-synkronisering synkroniseras endast de användarkonton som tillhör de grupper som du anger till den Hanterade Azure AD DS-domänen.

I följande tabell beskrivs hur du använder begränsad synkronisering:

| Aktuellt tillstånd | Önskat tillstånd | Nödvändig konfiguration |
| --- | --- | --- |
| En befintlig hanterad domän är konfigurerad för att synkronisera alla användarkonton och grupper. | Du vill bara synkronisera användarkonton som tillhör specifika grupper. | Du kan inte ändra från att synkronisera alla användare till att använda begränsad synkronisering. [Ta bort den befintliga hanterade domänen](delete-aadds.md)och följ sedan stegen i den här artikeln för att återskapa en Azure AD DS-hanterad domän med begränsad synkronisering konfigurerad. |
| Ingen befintlig hanterad domän. | Du vill skapa en ny hanterad domän och endast synkronisera användarkonton som tillhör specifika grupper. | Följ stegen i den här artikeln för att skapa en Azure AD DS-hanterad domän med begränsad synkronisering konfigurerad. |
| En befintlig hanterad domän är konfigurerad för att endast synkronisera konton som tillhör specifika grupper. | Du vill ändra listan över grupper vars användare ska synkroniseras till den Hanterade Azure AD DS-domänen. | Följ stegen i den här artikeln för att ändra begränsad synkronisering. |

Du använder Azure-portalen eller PowerShell för att konfigurera de begränsade synkroniseringsinställningarna:

| Åtgärd | | |
|--|--|--|
| Skapa en Azure AD DS-hanterad domän och konfigurera begränsad synkronisering | [Azure-portal](#enable-scoped-synchronization-using-the-azure-portal) | [Powershell](#enable-scoped-synchronization-using-powershell) |
| Ändra begränsad synkronisering | [Azure-portal](#modify-scoped-synchronization-using-the-azure-portal) | [Powershell](#modify-scoped-synchronization-using-powershell) |
| Inaktivera begränsad synkronisering | [Azure-portal](#disable-scoped-synchronization-using-the-azure-portal) | [Powershell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Om du ändrar synkroniseringens omfattning kan Azure AD DS-hanterad domän synkronisera om alla data.
> 
>  * När du ändrar synkroniseringsomfånget för en Azure AD DS-hanterad domän sker en fullständig omsynkronisering.
>  * Objekt som inte längre behövs i azure AD DS-hanterad domän tas bort. Nya objekt skapas i den hanterade domänen.
>  * Omsynkronisering kan ta lång tid att slutföra. Synkroniseringstiden beror på antalet objekt som användare, grupper och gruppmedlemskap i Azure AD DS-hanterade domänen och Azure AD-katalogen. För stora kataloger med hundratusentals objekt kan omsynkronisering ta några dagar.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Aktivera begränsad synkronisering med Azure-portalen

1. Följ [självstudien för att skapa och konfigurera en Azure AD DS-instans](tutorial-create-instance-advanced.md). Slutför alla förutsättningar och distributionssteg förutom för synkroniseringsomfång.
1. Välj Scoped i **synkroniseringssteget** och välj sedan Azure AD-grupperna som ska synkroniseras med Azure AD DS-instansen.

Den Hanterade Azure AD DS-domänen kan ta upp till en timme att slutföra distributionen. I Azure-portalen visar **översiktssidan** för din Azure AD DS-hanterade domän aktuell status under hela den här distributionsfasen.

När Azure-portalen visar att den Hanterade Azure AD DS-domänen har slutförts måste följande uppgifter slutföras:

* Uppdatera DNS-inställningar för det virtuella nätverket så att virtuella datorer kan hitta den hanterade domänen för domänanslutning eller autentisering.
    * Om du vill konfigurera DNS väljer du din Azure AD DS-hanterade domän i portalen. I **fönstret Översikt** uppmanas du att automatiskt konfigurera dessa DNS-inställningar.
* [Aktivera lösenordssynkronisering till Azure AD Domain Services](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) så att slutanvändare kan logga in på den hanterade domänen med sina företagsautentiseringsuppgifter.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Ändra begränsad synkronisering med Azure-portalen

Så här ändrar du listan över grupper vars användare ska synkroniseras till den hanterade Azure AD DS-domänen:

1. Sök efter och välj **Azure AD Domain Services**i Azure-portalen . Välj din instans, till exempel *aaddscontoso.com*.
1. Välj **Synkronisering** på menyn till vänster.
1. Om du vill lägga till en grupp väljer du **+ Markera grupper** högst upp och väljer sedan de grupper som ska läggas till.
1. Om du vill ta bort en grupp från synkroniseringsomfånget markerar du den i listan över synkroniserade grupper och väljer **Ta bort grupper**.
1. När alla ändringar görs väljer du **Spara synkroniseringsomfattning**.

Om du ändrar synkroniseringens omfattning kan Azure AD DS-hanterad domän synkronisera om alla data. Objekt som inte längre behövs i Azure AD DS-hanterade domänen tas bort och omsynkronisering kan ta lång tid att slutföra.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Inaktivera begränsad synkronisering med Azure-portalen

Så här inaktiverar du gruppbaserad scoped-synkronisering för en Azure AD DS-hanterad domän:

1. Sök efter och välj **Azure AD Domain Services**i Azure-portalen . Välj din instans, till exempel *aaddscontoso.com*.
1. Välj **Synkronisering** på menyn till vänster.
1. Ange synkroniseringsomfånget från **Scoped** till **Alla**och välj sedan **Spara synkroniseringsomfattning**.

Om du ändrar synkroniseringens omfattning kan Azure AD DS-hanterad domän synkronisera om alla data. Objekt som inte längre behövs i Azure AD DS-hanterade domänen tas bort och omsynkronisering kan ta lång tid att slutföra.

## <a name="powershell-script-for-scoped-synchronization"></a>PowerShell-skript för begränsad synkronisering

Om du vill konfigurera scoped-synkronisering med PowerShell sparar `Select-GroupsToSync.ps1`du först följande skript i en fil med namnet . Det här skriptet konfigurerar Azure AD DS för att synkronisera valda grupper från Azure AD. Alla användarkonton som ingår i de angivna grupperna synkroniseras med den Hanterade Azure AD DS-domänen.

Det här skriptet används i ytterligare steg i den här artikeln.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>Aktivera begränsad synkronisering med PowerShell

Använd PowerShell för att slutföra den här uppsättningen steg. Se instruktionerna för att [aktivera Azure Active Directory Domain Services med PowerShell](powershell-create-instance.md). Ett par steg i den här artikeln ändras något för att konfigurera begränsad synkronisering.

1. Slutför följande uppgifter från artikeln för att aktivera Azure AD DS med PowerShell. Stanna vid steget för att faktiskt skapa den hanterade domänen. Du konfigurerar den begränsade synkronisering som du skapar den Azure AD DS-hanterade domänen.

   * [Installera de PowerShell-moduler som krävs](powershell-create-instance.md#prerequisites).
   * [Skapa den obligatoriska tjänstens huvudnamn och Azure AD-grupp för administrativ åtkomst](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Skapa stöd för Azure-resurser som ett virtuellt nätverk och undernät](powershell-create-instance.md#create-supporting-azure-resources).

1. Bestäm vilka grupper och användare de innehåller som du vill synkronisera från Azure AD. Gör en lista över visningsnamnen för de grupper som ska synkroniseras med Azure AD DS.

1. Kör [skriptet från föregående avsnitt](#powershell-script-for-scoped-synchronization) och använd parametern *-groupsToAdd* för att skicka listan över grupper som ska synkroniseras.

   > [!WARNING]
   > Du måste inkludera gruppen *AAD DC-administratörer* i listan över grupper för begränsad synkronisering. Om du inte inkluderar den här gruppen är azure AD DS-hanterad domän oanvändbar.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Nu skapa Azure AD DS hanterad domän och aktivera gruppbaserad scoped synkronisering. Inkludera *"filteredSync" = "Aktiverad"* i parametern *-Properties.*

    Ange ditt Azure-prenumerations-ID och ange sedan ett namn för den hanterade domänen, till exempel *aaddscontoso.com*. Du kan få ditt prenumerations-ID med [get-azsubscription][Get-AzSubscription] cmdlet. Ange resursgruppsnamnet, det virtuella nätverksnamnet och regionen till de värden som användes i föregående steg för att skapa de stödda Azure-resurserna:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "aaddscontoso.com"
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

Det tar några minuter att skapa resursen och återställa kontrollen till PowerShell-prompten. Den Hanterade Azure AD DS-domänen fortsätter att etableras i bakgrunden och kan ta upp till en timme att slutföra distributionen. I Azure-portalen visar **översiktssidan** för din Azure AD DS-hanterade domän aktuell status under hela den här distributionsfasen.

När Azure-portalen visar att den Hanterade Azure AD DS-domänen har slutförts måste följande uppgifter slutföras:

* Uppdatera DNS-inställningar för det virtuella nätverket så att virtuella datorer kan hitta den hanterade domänen för domänanslutning eller autentisering.
    * Om du vill konfigurera DNS väljer du din Azure AD DS-hanterade domän i portalen. I **fönstret Översikt** uppmanas du att automatiskt konfigurera dessa DNS-inställningar.
* Om du har skapat en Azure AD DS-hanterad domän i en region som stöder tillgänglighetszoner skapar du en nätverkssäkerhetsgrupp för att begränsa trafiken i det virtuella nätverket för den Azure AD DS-hanterade domänen. En Azure-standardbelastningsutjämnare skapas som kräver att dessa regler ska placeras. Den här nätverkssäkerhetsgruppen skyddar Azure AD DS och krävs för att den hanterade domänen ska fungera korrekt.
    * Om du vill skapa nätverkssäkerhetsgruppen och nödvändiga regler väljer du din Azure AD DS-hanterade domän i portalen. I **fönstret Översikt** uppmanas du att automatiskt skapa och konfigurera nätverkssäkerhetsgruppen.
* [Aktivera lösenordssynkronisering till Azure AD Domain Services](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) så att slutanvändare kan logga in på den hanterade domänen med sina företagsautentiseringsuppgifter.

## <a name="modify-scoped-synchronization-using-powershell"></a>Ändra scoped synkronisering med PowerShell

Om du vill ändra listan över grupper vars användare ska synkroniseras med den hanterade Azure AD DS-domänen kör du [PowerShell-skriptet](#powershell-script-for-scoped-synchronization) igen och anger den nya listan över grupper. I följande exempel innehåller de grupper som ska synkroniseras inte längre *GroupName2*och innehåller nu *GroupName3*.

> [!WARNING]
> Du måste inkludera gruppen *AAD DC-administratörer* i listan över grupper för begränsad synkronisering. Om du inte inkluderar den här gruppen är azure AD DS-hanterad domän oanvändbar.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Om du ändrar synkroniseringens omfattning kan Azure AD DS-hanterad domän synkronisera om alla data. Objekt som inte längre behövs i Azure AD DS-hanterade domänen tas bort och omsynkronisering kan ta lång tid att slutföra.

## <a name="disable-scoped-synchronization-using-powershell"></a>Inaktivera begränsad synkronisering med PowerShell

Om du vill inaktivera gruppbaserad scoped-synkronisering för en Azure AD DS-hanterad domän anger du *"filteredSync" = "Disabled"* på Azure AD DS-resursen och uppdaterar sedan den hanterade domänen. När det är klart är alla användare och grupper inställda på att synkronisera från Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Om du ändrar synkroniseringens omfattning kan Azure AD DS-hanterad domän synkronisera om alla data. Objekt som inte längre behövs i Azure AD DS-hanterade domänen tas bort och omsynkronisering kan ta lång tid att slutföra.

## <a name="next-steps"></a>Nästa steg

Mer information om synkroniseringsprocessen finns [i Förstå synkronisering i Azure AD Domain Services](synchronization.md).

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
