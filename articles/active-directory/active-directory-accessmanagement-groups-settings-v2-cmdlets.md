---
title: "PowerShell-exempel för att hantera grupper i Azure Active Directory | Microsoft Docs"
description: "Den här sidan innehåller PowerShell-exempel som hjälper dig att hantera grupper i Azure Active Directory"
keywords: Azure AD, Azure Active Directory PowerShell grupper, grupphantering
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 7a5023dc-2727-4c25-8254-b531fc3244ac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.reviewer: rodejo
ms.openlocfilehash: 3f57e1a0ded679325c8c739e73cc79f69c037191
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Azure Active Directory version 2-cmdlets för grupphantering
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-groups-create-azure-portal.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

Den här artikeln innehåller exempel på hur du använder PowerShell för att hantera grupper i Azure Active Directory (AD Azure).  Den också förklarar hur du att börja arbeta med Azure AD PowerShell-modulen. Först måste du [ladda ned Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Installera Azure AD PowerShell-modulen
Om du vill installera Azure AD PowerShell-modulen använder du följande kommandon:

    PS C:\Windows\system32> install-module azuread

Om du vill verifiera att modulen har installerats, använder du följande kommando:

    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}

Nu kan du börja använda cmdlet: arna i modulen. En fullständig beskrivning av cmdletar i Azure AD-modulen finns i dokumentationen för online-referens för [Azure Active Directory PowerShell Version 2](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="connect-to-the-directory"></a>Ansluta till katalogen
Innan du kan börja hantera grupper med hjälp av Azure AD PowerShell-cmdlets, måste du ansluta din PowerShell-session till den katalog som du vill hantera. Ange följande kommando:

    PS C:\Windows\system32> Connect-AzureAD

Cmdlet efterfrågar autentiseringsuppgifter som du vill använda för åtkomst till din katalog. I det här exemplet använder karen@drumkit.onmicrosoft.com åtkomst till katalogen demonstrationen. Om du vill visa sessionen var ansluten till din katalog returnerar cmdleten:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Nu kan du börja använda AzureAD-cmdlets för att hantera grupper i katalogen.

## <a name="retrieve-groups"></a>Hämta grupper
Använd cmdleten Get-AzureADGroups för att hämta befintliga grupper från din katalog. 

Om du vill hämta alla grupper i katalogen, använder du cmdlet utan parametrar:

    PS C:\Windows\system32> get-azureadgroup

Cmdleten returnerar alla grupper i katalogen anslutna.

Du kan använda parametern - objekt-ID för att hämta en specifik grupp som du anger gruppens objectID:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

Cmdleten returnerar nu gruppen vars objectID överensstämmer med värdet för parametern du angav:

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

Du kan söka efter en specifik grupp med hjälp av Filterparametern -. Den här parametern tar ett filter ODATA-satsen och returnerar alla grupper som matchar filtret, som i följande exempel:

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
> Azure AD PowerShell-cmdlets följer OData-frågan. Mer information finns i **$filter** i [frågealternativ för OData-system med OData-slutpunkt](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="create-groups"></a>Skapa grupper
Använd cmdleten New-AzureADGroup om du vill skapa en ny grupp i katalogen. Denna cmdlet skapar en ny säkerhetsgrupp som heter ”Marknadsföring”:

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="update-groups"></a>Uppdatera grupper
Använd cmdlet Set-AzureADGroup om du vill uppdatera en befintlig grupp. I det här exemplet ändrar vi DisplayName-egenskap i gruppen ”Intune-administratörer”. Först måste vi är att hitta gruppen med hjälp av cmdleten Get-AzureADGroup och filter som använder attributet visningsnamn:

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

Nu ska ändrar vi beskrivningsegenskapen till det nya värdet ”Intune Enhetsadministratörer”:

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Nu om vi upptäcker gruppen kan se beskrivningsegenskapen har uppdaterats för att återspegla det nya värdet:

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
Om du vill ta bort grupper från katalogen, använder du cmdleten Remove-AzureADGroup enligt följande:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="manage-group-membership"></a>Hantera gruppmedlemskap 
### <a name="add-members"></a>Lägg till medlemmar
Använd cmdleten Add-AzureADGroupMember om du vill lägga till nya medlemmar i en grupp. Detta kommando lägger till en medlem i gruppen Intune-administratörer vi använde i det förra exemplet:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Parametern - ObjectId är ObjectID i gruppen som vi vill lägga till en medlem och RefObjectId - är objekt-ID för den användare som vi vill lägga till som en medlem i gruppen.

### <a name="get-members"></a>Hämta medlemmar
Använd cmdleten Get-AzureADGroupMember som i följande exempel för att få de befintliga medlemmarna i en grupp:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User

### <a name="remove-members"></a>Ta bort medlemmar
Ta bort medlemmen som vi tidigare har lagts till i gruppen med cmdleten Remove-AzureADGroupMember som visas här:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

### <a name="verify-members"></a>Kontrollera medlemmar
Använd cmdleten väljer AzureADGroupIdsUserIsMemberOf för att verifiera en användares gruppmedlemskap. Denna cmdlet tar som dess parametrar ObjectId för den användare som du vill kontrollera gruppmedlemskap och en lista över grupper som du vill kontrollera medlemskap. I listan över grupper måste vara angivna i form av en komplex variabel av typen ”Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck”, så vi först skapa en variabel med den typen:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Nu ska ange vi värden för groupIds att checka in attributet ”GroupIds” för den här variabeln för komplex:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Nu, om vi vill kontrollera gruppmedlemskap för en användare med ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea mot grupper i $g ska användas:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


Värdet som returneras är en lista över grupper som användaren är medlem. Du kan också använda den här metoden om du vill kontrollera kontakter, grupper eller tjänstens huvudnamn medlemskap för en angiven lista med grupper, Välj AzureADGroupIdsContactIsMemberOf, Välj AzureADGroupIdsGroupIsMemberOf eller välj AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Inaktivera skapande av en grupp av användare
Du kan förhindra att icke-administratörer att skapa säkerhetsgrupper. Standardbeteendet i Microsoft Online Directory Services (MSODS) är att tillåta icke-administratörer att skapa grupper, oavsett om grupphantering via självbetjäning (SSGM) har aktiverats. SSGM inställningen styr beteendet i åtkomstpanelen Mina appar. 

Inaktivera för icke-administratörer att skapa gruppen:

1. Kontrollera att icke-administratörer ska kunna skapa grupper:
   
  ````
  PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
  ````
  
2. Om den returnerar `UsersPermissionToCreateGroupsEnabled : True`, icke-administratörer kan skapa grupper. Inaktivera den här funktionen:
  
  ```` 
  Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
  ````
  
## <a name="manage-owners-of-groups"></a>Hantera ägare av grupper
Om du vill lägga till ägare till en grupp, använder du Lägg till AzureADGroupOwner-cmdlet:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Parametern - ObjectId är ObjectID i gruppen som vi vill lägga till en ägare och RefObjectId - är objekt-ID för den användare som vi vill lägga till som en ägare av gruppen.

Använd cmdleten Get-AzureADGroupOwner för att hämta ägare för en grupp:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

Cmdleten returnerar listan över ägare för den angivna gruppen:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Använd cmdleten Remove-AzureADGroupOwner om du vill ta bort en ägare från en grupp:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="reserved-aliases"></a>Reserverade alias 
När en grupp har skapats, vissa slutpunkter gör det möjligt för användaren att ange en mailNickname eller alias som ska användas som en del av e-postadress i gruppen. Grupper med följande mycket Privilegierade e-post-alias kan bara skapas av en global administratör för Azure AD. 
  
* missbruk 
* Admin 
* Administratören 
* hostmaster 
* majordomo 
* postmaster 
* rot 
* Skydda 
* security 
* SSL-admin 
* webbadministratör 

## <a name="next-steps"></a>Nästa steg
Du kan hitta mer Azure Active Directory PowerShell-dokumentationen på [Azure Active Directory-cmdletarna](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
