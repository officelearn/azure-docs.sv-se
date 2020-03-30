---
title: PowerShell V2-exempel för hantering av grupper – Azure AD | Microsoft-dokument
description: Den här sidan innehåller PowerShell-exempel som hjälper dig att hantera dina grupper i Azure Active Directory
keywords: Azure AD, Azure Active Directory, PowerShell, Grupper, Grupphantering
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a218e956c72f8005e533db7b8800e98ee72ce223
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233119"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Azure Active Directory version 2 cmdlets för grupphantering

> [!div class="op_single_selector"]
> - [Azure-portal](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [Powershell](groups-settings-v2-cmdlets.md)
>
>

Den här artikeln innehåller exempel på hur du använder PowerShell för att hantera dina grupper i Azure Active Directory (Azure AD).  Den berättar också hur du konfigurerar med Azure AD PowerShell-modulen. Först måste du [hämta Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Installera Azure AD PowerShell-modulen

Så här installerar du Azure AD PowerShell-modulen med följande kommandon:

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

Om du vill kontrollera att modulen är klar att användas använder du följande kommando:

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Nu kan du börja använda cmdlets i modulen. En fullständig beskrivning av cmdlets i Azure AD-modulen finns i referensdokumentationen online för [Azure Active Directory PowerShell Version 2](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="connect-to-the-directory"></a>Ansluta till katalogen

Innan du kan börja hantera grupper med Azure AD PowerShell-cmdletar måste du ansluta PowerShell-sessionen till den katalog som du vill hantera. Ange följande kommando:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

Cmdleten frågar dig efter de autentiseringsuppgifter som du vill använda för att komma åt katalogen. I det här exemplet karen@drumkit.onmicrosoft.com använder vi för att komma åt demonstrationskatalogen. Cmdlet returnerar en bekräftelse för att visa sessionen var ansluten framgångsrikt till din katalog:

```powershell
    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

Nu kan du börja använda AzureAD-cmdlets för att hantera grupper i din katalog.

## <a name="retrieve-groups"></a>Hämta grupper

Om du vill hämta befintliga grupper från din katalog använder du cmdleten Get-AzureADGroups. 

Om du vill hämta alla grupper i katalogen använder du cmdleten utan parametrar:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

Cmdlet returnerar alla grupper i den anslutna katalogen.

Du kan använda parametern -objectID för att hämta en viss grupp som du anger gruppens objectID för:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

Cmdlet returnerar nu den grupp vars objectID matchar värdet för den angivna parametern:

```powershell
    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

Du kan söka efter en viss grupp med parametern -filter. Den här parametern tar en ODATA-filtersats och returnerar alla grupper som matchar filtret, som i följande exempel:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

> [!NOTE]
> Azure AD PowerShell-cmdlets implementerar OData-frågestandarden. Mer information finns i **$filter** i [OData-systemfrågealternativen med OData-slutpunkten](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="create-groups"></a>Skapa grupper

Om du vill skapa en ny grupp i katalogen använder du cmdleten New-AzureADGroup. Den här cmdleten skapar en ny säkerhetsgrupp med namnet "Marknadsföring":

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Uppdatera grupper

Om du vill uppdatera en befintlig grupp använder du cmdleten Set-AzureADGroup. I det här exemplet ändrar vi egenskapen DisplayName för gruppen "Intune-administratörer". Först söker vi gruppen med cmdleten Get-AzureADGroup och filtrerar med attributet DisplayName:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

Därefter ändrar vi egenskapen Description till det nya värdet "Intune Device Administrators":

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Nu, om vi hittar gruppen igen, ser vi egenskapen Description uppdateras för att återspegla det nya värdet:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"

    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

## <a name="delete-groups"></a>Ta bort grupper

Om du vill ta bort grupper från katalogen använder du cmdleten Ta bort AzureADGroup enligt följande:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>Hantera gruppmedlemskap

### <a name="add-members"></a>Lägg till medlemmar

Om du vill lägga till nya medlemmar i en grupp använder du cmdleten Add-AzureADGroupMember. Det här kommandot lägger till en medlem i gruppen Intune-administratörer som vi använde i föregående exempel:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Parametern -ObjectId är ObjectID för den grupp som vi vill lägga till en medlem i, och -RefObjectId är ObjectID för den användare som vi vill lägga till som medlem i gruppen.

### <a name="get-members"></a>Hämta medlemmar

Om du vill hämta befintliga medlemmar i en grupp använder du cmdleten Get-AzureGROUPMember, som i det här exemplet:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Ta bort medlemmar

Om du vill ta bort medlemmen som vi tidigare har lagt till i gruppen använder du cmdleten Remove-AzureADGroupMember, som visas här:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Verifiera medlemmar

Om du vill verifiera gruppmedlemskap för en användare använder du cmdleten Select-AzureADGroupIdsUserMemberOf. Den här cmdleten tar som dess parametrar objectid för användaren som ska kontrollera gruppmedlemskapen för, och en lista över grupper som ska kontrolleras för medlemskapen för. Listan över grupper måste anges i form av en komplex variabel av typen "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", så vi måste först skapa en variabel med den typen:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

Därefter tillhandahåller vi värden för groupIds att checka in attributet "GroupIds" för den här komplexa variabeln:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Nu, om vi vill kontrollera gruppmedlemskap för en användare med ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea mot grupperna i $g, bör vi använda:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

Värdet som returneras är en lista över grupper som användaren är medlem i. Du kan också använda den här metoden för att kontrollera medlemskap i kontakter, grupper eller tjänsthuvudnamn för en viss lista över grupper, med Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf eller Välj-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Inaktivera gruppskapande av användarna

Du kan förhindra att användare som inte är administratörer skapar säkerhetsgrupper. Standardbeteendet i Microsoft Online Directory Services (MSODS) är att tillåta användare som inte är administratörer att skapa grupper, oavsett om självbetjäningsgrupphantering (SSGM) också är aktiverad eller inte. Inställningen SSGM styr bara beteendet på åtkomstpanelen Mina appar.

Så här inaktiverar du gruppskapande för användare som inte är administratörer:

1. Kontrollera att användare som inte är administratörer får skapa grupper:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Om den `UsersPermissionToCreateGroupsEnabled : True`returneras kan användare som inte är administratörer skapa grupper. Så här inaktiverar du den här funktionen:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Hantera ägare av grupper

Om du vill lägga till ägare i en grupp använder du cmdleten Add-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Parametern -ObjectId är ObjectID för den grupp som vi vill lägga till en ägare till, och -RefObjectId är ObjectID för användaren eller tjänsthuvudhuvudhuvudet som vi vill lägga till som ägare av gruppen.

Om du vill hämta ägarna till en grupp använder du cmdleten Get-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

Cmdlet returnerar listan över ägare (användare och huvudnamn för tjänsten) för den angivna gruppen:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Om du vill ta bort en ägare från en grupp använder du cmdleten Ta bort AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Reserverade alias

När en grupp skapas tillåter vissa slutpunkter slutanvändaren att ange ett mailNickname eller alias som ska användas som en del av gruppens e-postadress.Grupper med följande mycket privilegierade e-postalias kan bara skapas av en global Azure AD-administratör. 
  
* Missbruk
* admin
* administratör
* värdledare
* majordomo (svenska)
* Postmaster
* Root
* Säker
* security
* ssl-admin
* Webmaster

## <a name="group-writeback-to-on-premises-preview"></a>Gruppåterskrivning till lokalt (förhandsgranskning)

Idag hanteras fortfarande många grupper i lokala Active Directory. Office 365-gruppers tillbakaskrivningsfunktion för Azure AD är nu tillgänglig för förhandsversion för Office 365-grupper för förhandsversionen av Office 365-grupper.

Office 365-grupper skapas och hanteras i molnet. Med tillbakaskrivningsfunktionen kan du skriva tillbaka Office 365-grupper som distributionsgrupper till en Active Directory-skog med Exchange installerat. Användare med lokala Exchange-postlådor kan sedan skicka och ta emot e-postmeddelanden från dessa grupper. Gruppåterskrivningsfunktionen stöder inte Azure AD-säkerhetsgrupper eller distributionsgrupper.

Mer information finns i dokumentationen för [synkroniseringstjänsten Azure AD Connect](../hybrid/how-to-connect-syncservice-features.md).

Office 365-gruppåterställning är en offentlig förhandsversion av Azure Active Directory (Azure AD) och är tillgänglig med alla betalda Azure AD-licensplan. Mer information om förhandsgranskningar finns i [Tilläggsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="next-steps"></a>Nästa steg

Du hittar mer Azure Active Directory PowerShell-dokumentation på [Azure Active Directory Cmdlets](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
