---
title: PowerShell-exempel för att hantera grupper – Azure Active Directory | Microsoft Docs
description: Den här sidan innehåller PowerShell-exempel för att hjälpa dig att hantera dina grupper i Azure Active Directory
keywords: Azure AD, Azure Active Directory, PowerShell, Groups, Group management
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb48d37e1cf552f9ad375906d8cd05301ac2dd0c
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65407876"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Azure Active Directory version 2-cmdletar för grupphantering

> [!div class="op_single_selector"]
> * [Azure Portal](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> * [PowerShell](groups-settings-v2-cmdlets.md)
>
>

Den här artikeln innehåller exempel på hur du använder PowerShell för att hantera dina grupper i Azure Active Directory (AD Azure).  Du får också information du så här att komma igång med Azure AD PowerShell-modulen. Först måste du [ladda ned Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Installera Azure AD PowerShell-modulen
Om du vill installera Azure AD PowerShell-modulen, använder du följande kommandon:

    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread

Verifiera att modulen är redo att användas, använder du följande kommando:

    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}

Nu kan du börja använda cmdletarna i modulen. En fullständig beskrivning av cmdlets i Azure AD-modulen finns i online referensdokumentationen för [Azure Active Directory PowerShell Version 2](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="connect-to-the-directory"></a>Ansluta till katalogen
Innan du kan börja hantera grupper med hjälp av Azure AD PowerShell-cmdlets, måste du ansluta din PowerShell-session till den katalog som du vill hantera. Ange följande kommando:

    PS C:\Windows\system32> Connect-AzureAD

Cmdleten uppmanar dig de autentiseringsuppgifter som du vill använda för att få åtkomst till din katalog. I det här exemplet använder vi karen@drumkit.onmicrosoft.com att komma åt demonstration katalogen. Cmdleten returnerar en bekräftelse för att visa sessionen har anslutits till din katalog:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Nu kan du börja använda AzureAD-cmdletar för att hantera grupper i din katalog.

## <a name="retrieve-groups"></a>Hämta grupper
Använd cmdleten Get-AzureADGroups för att hämta befintliga grupper från din katalog. 

Använd cmdleten utan parametrar för att hämta alla grupper i katalogen:

    PS C:\Windows\system32> get-azureadgroup

Cmdleten returnerar alla grupper i den anslutna katalogen.

Du kan använda parametern - objectID för att hämta en specifik grupp som du anger gruppens objekt-ID:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

Cmdleten returnerar nu gruppen vars objectID matchar värdet på parametern som du har angett:

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

Du kan söka efter en specifik grupp med hjälp av Filterparametern -. Den här parametern tar en ODATA-sats för filter och returnerar alla grupper som matchar filtret, som i följande exempel:

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

> [!NOTE] 
> Azure AD PowerShell-cmdletar följer OData-fråga. Mer information finns i **$filter** i [OData system-frågealternativ med OData-slutpunkten](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="create-groups"></a>Skapa grupper
Använd cmdleten New-AzureADGroup för att skapa en ny grupp i din katalog. Denna cmdlet skapar en ny säkerhetsgrupp med namnet ”marknadsföring”:

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="update-groups"></a>Uppdatera grupper
Använd cmdleten Set-AzureADGroup om du vill uppdatera en befintlig grupp. I det här exemplet ändrar vi egenskapen DisplayName för gruppen ”Intune-administratörer”. Först måste vi är att hitta gruppen med hjälp av cmdleten Get-AzureADGroup och filtrera med hjälp av attributet visningsnamn:

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

Nu ska ändrar vi egenskapen Description till det nya värdet ”Intune Enhetsadministratörer”:

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Nu om vi hitta gruppen igen ser vi beskrivningsegenskapen uppdateras för att återspegla det nya värdet:

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

## <a name="delete-groups"></a>Ta bort grupper
Ta bort grupper från din katalog genom att använda cmdleten Remove-AzureADGroup på följande sätt:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="manage-group-membership"></a>Hantera gruppmedlemskap 
### <a name="add-members"></a>Lägg till medlemmar
Använd cmdleten Add-AzureADGroupMember om du vill lägga till nya medlemmar i en grupp. Det här kommandot lägger till en medlem i gruppen Intune-administratörer som vi använde i föregående exempel:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Parametern - ObjectId är ObjectID för gruppen som vi vill lägga till en medlem och RefObjectId - är ObjectID för den användare som vi vill lägga till som en medlem i gruppen.

### <a name="get-members"></a>Hämta medlemmar
Hämta de befintliga medlemmarna i en grupp med cmdleten Get-AzureADGroupMember, som i följande exempel:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User

### <a name="remove-members"></a>Ta bort medlemmar
Ta bort medlemmen som vi tidigare har lagts till i gruppen med cmdleten Remove-AzureADGroupMember, som visas här:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

### <a name="verify-members"></a>Kontrollera medlemmar
Du kan kontrollera gruppmedlemskap för en användare genom att använda cmdleten väljer AzureADGroupIdsUserIsMemberOf. Den här cmdleten tar som dess parametrar ObjectId för den användare som du vill kontrollera gruppmedlemskap och en lista över grupper som du vill kontrollera medlemskap. I listan över grupper måste vara angivna i form av en komplex variabel av typen ”Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck”, så vi först skapa en variabel med den typen:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Nu ska ange vi värden för groupIds att checka in attributet ”GroupIds” för den här variabeln för komplex:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Nu, om vi vill kontrollera gruppmedlemskap för en användare med ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea mot grupper i $g ska användas:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


Värdet som returneras är en lista över grupper som den här användaren är medlem. Du kan också använda den här metoden om du vill kontrollera kontakter, grupper eller tjänstens huvudnamn medlemskap för en specifik lista med grupper, med hjälp av Select-AzureADGroupIdsContactIsMemberOf, Välj AzureADGroupIdsGroupIsMemberOf eller Välj AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Inaktivera skapande av en grupp av användare
Du kan förhindra att icke-administratörer kan skapa säkerhetsgrupper. Standardbeteendet i Microsoft Online Directory Services (MSODS) är att tillåta icke-administratörer att skapa grupper, oavsett om grupphantering (SSGM) aktiveras också. SSGM-inställningen styr beteendet endast i åtkomstpanelen Mina appar. 

Inaktivera skapande av en grupp för icke-administratörer:

1. Kontrollera att icke-administratörer har behörighet att skapa grupper:
   
   ```
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Om den returnerar `UsersPermissionToCreateGroupsEnabled : True`, icke-administratörer kan skapa grupper. Inaktivera den här funktionen:
  
   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Hantera ägare av grupper
Om du vill lägga till ägare till en grupp, använder du Lägg till AzureADGroupOwner-cmdlet:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Parametern - ObjectId är ObjectID för gruppen som vi vill lägga till en ägare och RefObjectId - är ObjectID för användaren eller tjänstens huvudnamn vi vill lägga till som ägare av gruppen.

Använd cmdleten Get-AzureADGroupOwner för att hämta ägare för en grupp:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

Cmdleten returnerar listan över ägare (användare och tjänstens huvudnamn) för den angivna gruppen:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Om du vill ta bort ägare från en grupp kan du använda cmdleten Remove-AzureADGroupOwner:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="reserved-aliases"></a>Reserverade alias 
När en grupp skapas vissa slutpunkter gör det möjligt för användaren att ange en mailNickname eller alias som ska användas som en del av e-postadressen till gruppen. Grupper med följande med hög behörighetsnivå e-post-alias kan endast skapas av en global administratör för Azure AD. 
  
* missbruk 
* admin 
* administratör 
* hostmaster 
* majordomo 
* postmaster 
* rot 
* skydda 
* säkerhet 
* ssl-admin 
* webbadministratör 

## <a name="next-steps"></a>Nästa steg
Du kan hitta fler Azure Active Directory PowerShell-dokumentation på [Azure Active Directory-cmdletar](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
