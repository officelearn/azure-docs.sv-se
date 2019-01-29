---
title: Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure AD Graph API
description: En självstudie som steg för steg beskriver hur du använder en systemtilldelad hanterad identitet för en virtuell Windows-dator för att få åtkomst till Azure AD Graph API.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: daveba
ms.openlocfilehash: 4396ab093b20e4a7fb89ddbfe821be117bb8ebfb
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438685"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Självstudier: Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure AD Graph API

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

Den här självstudien visar hur du använder en systemtilldelad hanterad identitet för en virtuell Windows-dator för att få åtkomst till Microsoft Graph API för att hämta dess gruppmedlemskap. Hanterade identiteter för Azure-resurser hanteras automatiskt av Azure och gör att du kan autentisera mot tjänster som stöder Azure AD-autentisering, utan att du behöver skriva in autentiseringsuppgifter i koden.  I den här självstudien kör du frågor om medlemskapet för din virtuella dators identitet i Azure AD-grupper. Gruppinformation används ofta i, till exempel, auktoriseringsbeslut. Under ytan representeras den virtuella datorns identitet av **tjänstens huvudnamn** i Azure AD. Innan du kör gruppfrågan lägger du till tjänsthuvudnamnet som representerar den virtuella datorns identitet till en grupp i Azure AD. Du kan använda detta med Azure PowerShell, Azure AD PowerShell eller Azure CLI.

> [!div class="checklist"]
> * Anslut till Azure AD
> * Lägga till den virtuella datorns identitet i Azure AD 
> * Bevilja den virtuella datorns identitet åtkomst till Azure AD Graph 
> * Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använda den för att anropa Azure AD Graph

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Om du vill bevilja en virtuell dators identitet åtkomst till Azure AD Graph måste ditt konto tilldelas rollen **Global administratör** i Azure AD.

## <a name="connect-to-azure-ad"></a>Anslut till Azure AD

Du måste ansluta till Azure AD för att tilldela den virtuella datorn till en grupp samt ge den virtuella datorn behörighet att hämta dess gruppmedlemskap.

```powershell
Connect-AzureAD
```

## <a name="add-your-vm-identity-to-a-group-in-azure-ad"></a>Lägga till den virtuella datorns identitet i Azure AD

När du aktiverade systemtilldelad identitet på den virtuella Windows-datorn skapades ett tjänsthuvudnamn i Azure AD.  Nu måste du lägga till den virtuella datorn i en grupp.  Följande exempel skapar en ny grupp i Azure AD och lägger till tjänstens huvudnamn för din virtuella dator i den gruppen:

```powershell
New-AzureADGroup -DisplayName "myGroup" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$AzureADGroup = Get-AzureADGroup -Filter "displayName eq 'myGroup'"
$ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
Add-AzureADGroupMember -ObjectId $AzureADGroup.ObjectID -RefObjectId $ManagedIdentitiesServicePrincipal.ObjectId
```
## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Bevilja den virtuella datorn åtkomst till Azure AD Graph API

Med hjälp av hanterade identiteter för Azure-resurser kan din kod hämta åtkomsttoken för att autentisera mot resurser som har stöd för Azure AD-autentisering. Microsoft Azure AD Graph API stöder Azure AD-autentisering. I det här steget beviljar du den virtuell datorns tjänsthuvudnamn åtkomst till Azure AD Graph så att den kan köra frågor om gruppmedlemskap. Tjänsthuvudnamn beviljas åtkomst till Microsoft eller Azure AD Graph via **Programbehörigheter**. Den typ av programbehörighet du behöver tilldela beror på vilken entitet du vill ha åtkomst till i MS eller Azure AD Graph.

I den här självstudien beviljar du den virtuella datorns identitet möjligheten att köra frågor om gruppmedlemskap med programbehörigheten ```Directory.Read.All```. Om du vill tilldela den här behörigheten måste du ha ett användarkonto som har tilldelats rollen Global administratör i Azure AD. Normalt skulle du tilldela en programbehörighet genom att gå till programmets registrering i Azure-portalen och lägga till behörigheten där. Men hanterade identiteter för Azure-resurser registrerar inte programobjekt i Azure AD, utan registrerar bara tjänsthuvudnamn. Du registrerar programbehörigheten med hjälp av kommandoradsverktyget Azure AD PowerShell. 

Azure AD Graph:
- Tjänsthuvudnamnets appId (används vid tilldelning av programbehörighet): 00000002-0000-0000-c000-000000000000
- Resurs-ID (används vid begäran av åtkomsttoken från hanterade identiteter för Azure-resurser): https://graph.windows.net
- Referens för behörighetsomfång: [Referens för Azure AD Graph-behörigheter](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-azure-ad-powershell"></a>Tilldela programbehörigheter med Azure AD PowerShell

Du behöver Azure AD PowerShell om du vill använda det här alternativet. Om du inte har installerat det [hämtar du den senaste versionen](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) innan du fortsätter.

1. Öppna ett PowerShell-fönster och anslut till Azure AD:

   ```powershell
   Connect-AzureAD
   ```

2. Kör följande PowerShell-kommandon för att tilldela programbehörigheten ``Directory.Read.All`` till tjänstens huvudnamn som representerar den virtuella datorns identitet.

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   New-AzureAdServiceAppRoleAssignment -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId -PrincipalId $ManagedIdentitiesServicePrincipal.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
   ``` 

   Utdata från det slutliga kommandot bör se ut så här och returnerar ID:t för tilldelningen:
        
   `ObjectId`:`gzR5KyLAiUOTiqFhNeWZWBtK7ZKqNJxAiWYXYVHlgMs`

   `ResourceDisplayName`:`Windows Azure Active Directory`

   `PrincipalDisplayName`:`myVM` 

   Om anropen till `New-AzureAdServiceAppRoleAssignment` misslyckas med felet kan programbehörigheten `bad request, one or more properties are invalid` redan har tilldelats till tjänstens huvudnamn för den virtuella datorns identitet. Du kan använda följande PowerShell-kommandon för att kontrollera om programbehörigheten redan finns mellan den virtuella datorns identitet och Azure AD Graph:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   ```

   Du kan använda följande PowerShell-kommandon för att lista alla programbehörigheter som har beviljats för Azure AD Graph:

   ```powershell
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId
   ``` 

   Du kan använda följande PowerShell-kommandon för att ta bort programbehörigheter som har tilldelats till den virtuella datorns identitet för Azure AD Graph:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}   
   $ServiceAppRoleAssignment = Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   Remove-AzureADServiceAppRoleAssignment -AppRoleAssignmentId $ServiceAppRoleAssignment.ObjectId -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId
   ```
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet för att anropa Azure AD Graph 

Om du vill använda den virtuella datorns systemtilldelade hanterade identitet för autentisering i Azure AD Graph måste du göra begäranden från den virtuella datorn.

1. I portalen går du till **Virtuella datorer** och sedan till den virtuella Windows-datorn. Under bladet **Översikt** klickar du på **Anslut**.  
2. Ange det **användarnamn** och **lösenord** som du använde när du skapade den virtuella Windows-datorn.
3. Nu när du har skapat en anslutning till fjärrskrivbord med den virtuella datorn öppnar du PowerShell i fjärrsessionen.  
4. Använd PowerShells Invoke-WebRequest och skicka en begäran till den lokala slutpunkten för hanterade identiteter för Azure-resurser för att hämta en åtkomsttoken för Azure AD Graph.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -Method GET -Headers @{Metadata="true"}
   ```

   Konvertera svaret från ett JSON-objekt till ett PowerShell-objekt.

   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extrahera åtkomsttoken från svaret.

   ```powershell
   $AccessToken = $content.access_token
   ```

5. Med hjälp av objekt-ID:t för tjänstens huvudnamn för den virtuella datorns identitet (du kan hämta värdet från variabeln som deklareras i föregående steg: ``$ManagedIdentitiesServicePrincipal.ObjectId``) kan du fråga Azure AD Graph API för att hämta dess gruppmedlemskap. Ersätt <OBJECT ID> med objekt-ID:t från det föregående steget och <ACCESS-TOKEN> med den tidigare hämtade åtkomsttoken:

   ```powershell
   Invoke-WebRequest 'https://graph.windows.net/<Tenant ID>/servicePrincipals/<VM Object ID>/getMemberGroups?api-version=1.6' -Method POST -Body '{"securityEnabledOnly":"false"}' -Headers @{Authorization="Bearer $AccessToken"} -ContentType "application/json"
   ```
   
   Svaret innehåller `Object ID` för gruppen du har lagt till den virtuella datorns tjänsthuvudnamn i tidigare.

   Svar:

   ```powershell   
   Content : {"odata.metadata":"https://graph.windows.net/<Tenant ID>/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder en systemtilldelad hanterad identitet för en virtuell Windows-dator för att få åtkomst till Azure Key AD Graph.  Mer information om Azure AD Graph finns i:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
