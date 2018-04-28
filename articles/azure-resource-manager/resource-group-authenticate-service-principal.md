---
title: Skapa en identitet för Azure-app med PowerShell | Microsoft Docs
description: Beskriver hur du använder Azure PowerShell för att skapa ett Azure Active Directory-program och tjänstens huvudnamn och bevilja åtkomst till resurser via rollbaserad åtkomstkontroll. Den visar hur du autentiserar program med ett certifikat.
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
ms.date: 03/12/2018
ms.author: tomfitz
ms.openlocfilehash: 70255ead4a556204689e9918b9c89e396f8122c0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Skapa ett huvudnamn för tjänsten med ett certifikat med hjälp av Azure PowerShell

När du har en app eller skript som behöver åtkomst till resurser, kan du ställa in en identitet för appen och autentisera appen med sina egna autentiseringsuppgifter. Den här identiteten kallas ett huvudnamn för tjänsten. Den här metoden kan du:

* Tilldela behörigheter till app-identitet som skiljer sig från din egen behörighet. Dessa behörigheter normalt begränsad till exakt vad appen behöver göra.
* Använda ett certifikat för autentisering när du kör ett oövervakat skript.

> [!IMPORTANT]
> Överväg att använda Azure AD hanterade tjänstidentiteten för tillämpningsprogrammets identitet i stället för att skapa ett huvudnamn för tjänsten. Azure AD-MSI är en funktion för förhandsversion av Azure Active Directory som gör det enklare att skapa en identitet för koden. Om din kod körs på en tjänst som stöder Azure AD MSI och har åtkomst till resurser som stöder Azure Active Directory-autentisering, är ett bättre alternativ för Azure AD-MSI. Läs mer om Azure AD MSI, inklusive tjänster som stöds för närvarande, [hanterade tjänstidentiteten för Azure-resurser](../active-directory/managed-service-identity/overview.md).

Den här artikeln visar hur du skapar ett huvudnamn för tjänsten som autentiserar med ett certifikat. Om du vill konfigurera ett huvudnamn för tjänsten med lösenord finns [skapa en Azure tjänstens huvudnamn med Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

## <a name="required-permissions"></a>Nödvändiga behörigheter

Du måste ha tillräckliga behörigheter i Azure Active Directory och Azure-prenumeration för att slutföra den här artikeln. Mer specifikt måste du att kunna skapa en app i Azure Active Directory och tilldela en roll tjänstens huvudnamn.

Det enklaste sättet att kontrollera om kontot har tillräcklig behörighet är via portalen. Se [Kontrollera behörighet](resource-group-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Skapa tjänstens huvudnamn med självsignerade certifikat

I följande exempel innehåller ett enkelt scenario. Den använder [ny AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) att skapa ett huvudnamn för tjänsten med ett självsignerat certifikat och använder [ny AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) att tilldela den [deltagare](../role-based-access-control/built-in-roles.md#contributor)rollen till tjänstens huvudnamn. Rolltilldelningen är begränsad till din valda Azure-prenumeration. Välj en annan prenumeration genom att använda [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Exemplet i viloläge i 20 sekunder att tillåta lite tid för den nya tjänsten säkerhetsobjekt att spridas i Azure Active Directory. Om skriptet inte vänta tillräckligt länge, visas ett felmeddelande om: ”Principal {ID} finns inte i katalogen {DIR-ID}”. Lös det här felet genom att vänta ett ögonblick som kör den **ny AzureRmRoleAssignment** kommandot igen.

I nästa exempel är mer komplicerad eftersom du kan ange omfånget för den rolltilldelning som skiljer sig från din aktuella Azure-prenumeration. Ange parametern ResourceGroup bara när du vill begränsa omfattningen av rolltilldelningen i en resursgrupp. Om ett fel inträffar under rolltilldelningen återförsök tilldelningen. Du måste ha Azure PowerShell 2.0 på Windows 10 eller Windows Server 2016.

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

 Connect-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -Subscription $SubscriptionId
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

Om du **inte har Windows 10 eller Windows Server 2016**, måste du hämta den [självsignerat certifikat generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) från Microsoft Script Center. Extrahera innehållet och importera den cmdlet som du behöver.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

Ersätt följande två rader om du vill generera certifikatet i skriptet.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -StoreName My `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Ange certifikat via automatisk PowerShell-skript

När du loggar in som ett huvudnamn för tjänsten som du behöver ange klient-ID för katalogen för din AD-app. En klient är en instans av Azure Active Directory.

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
 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Program-ID och klient-ID inte skiftlägeskänslig, så du kan bädda in dem direkt i skriptet. Använd om du behöver hämta klient-ID:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Använd om du behöver hämta program-ID:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Skapa tjänstens huvudnamn med certifikat från certifikatutfärdare

I följande exempel används ett certifikat som utfärdats från en certifikatutfärdare för att skapa tjänstens huvudnamn. Tilldelningen begränsas till angivna Azure-prenumerationen. Huvudnamn för tjänsten läggs den [deltagare](../role-based-access-control/built-in-roles.md#contributor) roll. Om ett fel inträffar under rolltilldelningen återförsök tilldelningen.

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

 Connect-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -Subscription $SubscriptionId
 
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
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Program-ID och klient-ID inte skiftlägeskänslig, så du kan bädda in dem direkt i skriptet. Använd om du behöver hämta klient-ID:

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

Skapa ett självsignerat certifikat för att lägga till ett Certifikatvärde som visas i den här artikeln. Använd sedan:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Felsökning

Du får följande fel när du skapar ett huvudnamn för tjänsten:

* **”Authentication_Unauthorized”** eller **”ingen prenumeration hittades i kontexten”.** – Du ser det här felet när ditt konto inte har den [nödvändiga behörigheter](#required-permissions) på Azure Active Directory att registrera en app. Normalt ser du det här felet när endast administrativa användare i Azure Active Directory kan du registrera appar och ditt konto är inte en administratör. Fråga din administratör antingen tilldela dig en administratör eller så att användarna kan registrera appar.

* Ditt konto **”har inte behörighet att utföra åtgärden 'Microsoft.Authorization/roleAssignments/write' över område '/subscriptions/ {guid}'”.**  -Det här felet visas när ditt konto inte har tillräcklig behörighet för att tilldela en roll till en identitet. Be administratören att lägga till dig till rollen Administratör för användaråtkomst prenumeration.

## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera ett huvudnamn för tjänsten med lösenord finns [skapa en Azure tjänstens huvudnamn med Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Detaljerade anvisningar för att integrera ett program i Azure för att hantera resurser, se [Utvecklarhandbok tillstånd med Azure Resource Manager API](resource-manager-api-authentication.md).
* En mer detaljerad förklaring av program och tjänstens huvudnamn finns [program och tjänstens huvudnamn objekt](../active-directory/active-directory-application-objects.md). 
* Läs mer om Azure Active Directory-autentisering, [Autentiseringsscenarier för Azure AD](../active-directory/active-directory-authentication-scenarios.md).
