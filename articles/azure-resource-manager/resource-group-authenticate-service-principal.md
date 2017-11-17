---
title: "Skapa en identitet för Azure-app med PowerShell | Microsoft Docs"
description: "Beskriver hur du använder Azure PowerShell för att skapa ett Azure Active Directory-program och tjänstens huvudnamn och bevilja åtkomst till resurser via rollbaserad åtkomstkontroll. Den visar hur du autentiserar program med ett lösenord eller ett certifikat."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/28/2017
ms.author: tomfitz
ms.openlocfilehash: 57eec4277e584c3c2828e0fe029b9db10428934e
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten för att få åtkomst till resurser

När du har en app eller skript som behöver åtkomst till resurser, kan du ställa in en identitet för appen och autentisera appen med sina egna autentiseringsuppgifter. Den här identiteten kallas ett huvudnamn för tjänsten. Den här metoden kan du:

* Tilldela behörigheter till app-identitet som skiljer sig från din egen behörighet. Dessa behörigheter normalt begränsad till exakt vad appen behöver göra.
* Använda ett certifikat för autentisering när du kör ett oövervakat skript.

Det här avsnittet visar hur du använder [Azure PowerShell](/powershell/azure/overview) att ställa in allt du behöver för att program ska köras under sina egna autentiseringsuppgifter och identitet.

## <a name="required-permissions"></a>Behörigheter som krävs
Du måste ha behörighet i både din Azure Active Directory och Azure-prenumerationen för att slutföra det här avsnittet. Mer specifikt måste du att kunna skapa en app i Azure Active Directory och tilldela en roll tjänstens huvudnamn. 

Det enklaste sättet att kontrollera om kontot har tillräcklig behörighet är via portalen. Se [Kontrollera behörighet](resource-group-create-service-principal-portal.md#required-permissions).

Nu kan du gå vidare till ett avsnitt för att autentisera med:

* [lösenord](#create-service-principal-with-password)
* [självsignerat certifikat](#create-service-principal-with-self-signed-certificate)
* [certifikat från certifikatutfärdare](#create-service-principal-with-certificate-from-certificate-authority)

## <a name="powershell-commands"></a>PowerShell-kommandon

Om du vill konfigurera ett huvudnamn för tjänsten måste använda du:

| Kommando | Beskrivning |
| ------- | ----------- | 
| [Ny AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | Skapar ett Azure Active Directory-tjänstens huvudnamn |
| [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) | Tilldelar den angivna RBAC-rollen till den angivna säkerhetsobjekt i det specificerade omfånget. |


## <a name="create-service-principal-with-password"></a>Skapa tjänstens huvudnamn med lösenord

Använd för att skapa ett huvudnamn för tjänsten med deltagarrollen för din prenumeration: 

```powershell
Login-AzureRmAccount
$password = convertto-securestring {provide-password} -asplaintext -force
$sp = New-AzureRmADServicePrincipal -DisplayName exampleapp -Password $password
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Exemplet i viloläge i 20 sekunder att tillåta lite tid för den nya tjänsten säkerhetsobjekt att spridas i Azure Active Directory. Om skriptet inte vänta tillräckligt länge visas ett felmeddelande om ”: PrincipalNotFound: Principal {ID} finns inte i katalogen”.

Följande skript kan du ange en omfattning än standard-prenumeration och försöker rolltilldelningen om ett fel uppstår:

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $Password
)

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 
 # Create Service Principal for the AD app
 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

Några objekt att notera skriptet:

* Om du vill ge åtkomst identitet till standard-prenumeration, behöver inte ange parametrarna ResourceGroup eller prenumerations-ID.
* Ange parametern ResourceGroup bara när du vill begränsa omfattningen av rolltilldelningen i en resursgrupp.
*  I det här exemplet du lägga till tjänstens huvudnamn i deltagarrollen. Andra roller, se [RBAC: inbyggda roller](../active-directory/role-based-access-built-in-roles.md).
* Skriptet i viloläge under 15 sekunder till en stund innan den nya tjänsten säkerhetsobjekt att spridas i Azure Active Directory. Om skriptet inte vänta tillräckligt länge visas ett felmeddelande om ”: PrincipalNotFound: Principal {ID} finns inte i katalogen”.
* Om du behöver ge service principal åtkomst till flera prenumerationer eller resursgrupper kör den `New-AzureRMRoleAssignment` cmdleten igen med olika omfång.


### <a name="provide-credentials-through-powershell"></a>Ange autentiseringsuppgifter med hjälp av PowerShell
Nu kan behöva du logga in som programmet för att utföra åtgärder. Användarnamnet, använda den `ApplicationId` som du skapade för programmet. Lösenordet kan du använda du angav när du skapar kontot. 

```powershell   
$creds = Get-Credential
Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId {tenant-ID}
```

Klient-ID är inte skiftlägeskänslig, så du kan bädda in den direkt i skriptet. Använd om du behöver hämta klient-ID:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

## <a name="create-service-principal-with-self-signed-certificate"></a>Skapa tjänstens huvudnamn med självsignerade certifikat

Använd för att skapa ett huvudnamn för tjänsten med ett självsignerat certifikat och deltagarrollen för din prenumeration: 

```powershell
Login-AzureRmAccount
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Exemplet i viloläge i 20 sekunder att tillåta lite tid för den nya tjänsten säkerhetsobjekt att spridas i Azure Active Directory. Om skriptet inte vänta tillräckligt länge visas ett felmeddelande om ”: PrincipalNotFound: Principal {ID} finns inte i katalogen”.

Följande skript kan du ange en omfattning än standard-prenumeration och försöker rolltilldelningen om ett fel inträffar. Du måste ha Azure PowerShell 2.0 på Windows 10 eller Windows Server 2016.

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
 $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

Några objekt att notera skriptet:

* Om du vill ge åtkomst identitet till standard-prenumeration, behöver inte ange parametrarna ResourceGroup eller prenumerations-ID.
* Ange parametern ResourceGroup bara när du vill begränsa omfattningen av rolltilldelningen i en resursgrupp.
* I det här exemplet du lägga till tjänstens huvudnamn i deltagarrollen. Andra roller, se [RBAC: inbyggda roller](../active-directory/role-based-access-built-in-roles.md).
* Skriptet i viloläge under 15 sekunder till en stund innan den nya tjänsten säkerhetsobjekt att spridas i Azure Active Directory. Om skriptet inte vänta tillräckligt länge visas ett felmeddelande om ”: PrincipalNotFound: Principal {ID} finns inte i katalogen”.
* Om du behöver ge service principal åtkomst till flera prenumerationer eller resursgrupper kör den `New-AzureRMRoleAssignment` cmdleten igen med olika omfång.

Om du **inte har Windows 10 eller Windows Server 2016 Technical Preview**, måste du hämta den [självsignerat certifikat generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) från Microsoft Script Center. Extrahera innehållet och importera den cmdlet som du behöver.

```powershell  
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```
  
Ersätt följande två rader om du vill generera certifikatet i skriptet.
  
```powershell
New-SelfSignedCertificateEx  -StoreLocation CurrentUser -StoreName My -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Ange certifikat via automatisk PowerShell-skript
När du loggar in som ett huvudnamn för tjänsten som du behöver ange klient-ID för katalogen för din AD-app. En klient är en instans av Azure Active Directory. Om du bara har en prenumeration kan du använda:

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertSubject,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match $CertSubject }).Thumbprint
 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

Program-ID och klient-ID är inte skiftlägeskänslig, så du kan bädda in dem direkt i skriptet. Använd om du behöver hämta klient-ID:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Använd om du behöver hämta program-ID:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Skapa tjänstens huvudnamn med certifikat från certifikatutfärdare
Om du vill använda ett certifikat som utfärdats från en certifikatutfärdare för att skapa tjänstens huvudnamn, använder du följande skript:

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -SubscriptionId $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

Några objekt att notera skriptet:

* Åtkomst är begränsad till prenumerationen.
* I det här exemplet du lägga till tjänstens huvudnamn i deltagarrollen. Andra roller, se [RBAC: inbyggda roller](../active-directory/role-based-access-built-in-roles.md).
* Skriptet i viloläge under 15 sekunder till en stund innan den nya tjänsten säkerhetsobjekt att spridas i Azure Active Directory. Om skriptet inte vänta tillräckligt länge visas ett felmeddelande om ”: PrincipalNotFound: Principal {ID} finns inte i katalogen”.
* Om du behöver ge service principal åtkomst till flera prenumerationer eller resursgrupper kör den `New-AzureRMRoleAssignment` cmdleten igen med olika omfång.

### <a name="provide-certificate-through-automated-powershell-script"></a>Ange certifikat via automatisk PowerShell-skript
När du loggar in som ett huvudnamn för tjänsten som du behöver ange klient-ID för katalogen för din AD-app. En klient är en instans av Azure Active Directory.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

Program-ID och klient-ID är inte skiftlägeskänslig, så du kan bädda in dem direkt i skriptet. Använd om du behöver hämta klient-ID:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Använd om du behöver hämta program-ID:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Ändra autentiseringsuppgifter

Om du vill ändra autentiseringsuppgifterna för en AD-app, antingen på grund av ett säkerhetsintrång eller en autentiseringsuppgift upphör att gälla kan den [ta bort AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) och [ny AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential) cmdlets.

Ta bort alla autentiseringsuppgifter för ett program med:

```powershell
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

Om du vill lägga till ett lösenord, använder du:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

Skapa ett självsignerat certifikat för att lägga till ett Certifikatvärde som visas i det här avsnittet. Använd sedan:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="save-access-token-to-simplify-log-in"></a>Spara åtkomst-token för att förenkla logga in
För att undvika att tillhandahålla service principal autentiseringsuppgifterna varje gång som behövs för att logga in, kan du spara den åtkomst-token.

Spara profilen om du vill använda den aktuella åtkomst-token i en senare session.
   
```powershell
Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```
   
Öppna profilen och undersöka dess innehåll. Observera att den innehåller en åtkomst-token. I stället för att manuellt logga in igen, läsa in profilen.
   
```powershell
Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```

> [!NOTE]
> Åtkomst-token upphör att gälla, så att använda en sparad profil fungerar bara för så länge token är giltig.
>  

Alternativt kan du anropa REST-åtgärder från PowerShell för att logga in. Du kan hämta åtkomsttoken för användning med andra åtgärder från autentiseringssvaret. Ett exempel för att hämta åtkomsttoken genom att anropa REST-åtgärder finns [genererar en åtkomst-Token](resource-manager-rest-api.md#generating-an-access-token).

## <a name="debug"></a>Felsökning

Följande fel kan uppstå när du skapar ett huvudnamn för tjänsten:

* **”Authentication_Unauthorized”** eller **”ingen prenumeration hittades i kontexten”.** – Du ser det här felet när ditt konto inte har den [nödvändiga behörigheter](#required-permissions) på Azure Active Directory att registrera en app. Normalt ser du det här felet när endast administrativa användare i Azure Active Directory kan du registrera appar och ditt konto är inte en administratör. Fråga din administratör antingen tilldela dig en administratör eller så att användarna kan registrera appar.

* Ditt konto **”har inte behörighet att utföra åtgärden 'Microsoft.Authorization/roleAssignments/write' över område '/subscriptions/ {guid}'”.**  -Det här felet visas när ditt konto inte har tillräcklig behörighet för att tilldela en roll till en identitet. Be administratören att lägga till dig till rollen Administratör för användaråtkomst prenumeration.

## <a name="sample-applications"></a>Exempelprogram
För information om att logga in som programmet via olika plattformar, se:

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Nästa steg
* Detaljerade anvisningar för att integrera ett program i Azure för att hantera resurser, se [Utvecklarhandbok tillstånd med Azure Resource Manager API](resource-manager-api-authentication.md).
* En mer detaljerad förklaring av program och tjänstens huvudnamn finns [program och tjänstens huvudnamn objekt](../active-directory/active-directory-application-objects.md). 
* Läs mer om Azure Active Directory-autentisering, [Autentiseringsscenarier för Azure AD](../active-directory/active-directory-authentication-scenarios.md).
* En lista över tillgängliga åtgärder som kan beviljas eller nekas till användare finns [Azure Resource Manager Resource Provider operations](../active-directory/role-based-access-control-resource-provider-operations.md).

