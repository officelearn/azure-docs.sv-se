---
title: Skapa identiteter för Azure-appar med PowerShell | Microsoft Docs
description: Beskriver hur du använder Azure PowerShell för att skapa en Azure Active Directory-app och tjänstens huvudnamn och bevilja åtkomst till resurser via rollbaserad åtkomstkontroll. Visar hur du autentiserar appar med ett certifikat.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/10/2018
ms.author: tomfitz
ms.openlocfilehash: 3dc62ba21458cc91d1858e11e7abbb6e96b0d5dd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358956"
---
# <a name="use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Använd Azure PowerShell för att skapa ett huvudnamn för tjänsten med certifikat

När du har en app eller ett skript som behöver åtkomst till resurser, kan du ställa in en identitet för appen och autentisera den med sina egna autentiseringsuppgifter. Den här identiteten kallas tjänstens huvudnamn. Med den här metoden kan du:

* Tilldela behörigheter till app-identiteten som skiljer sig från din egen behörighet. Vanligen är dessa behörigheter begränsade till exakt vad appen behöver göra.
* Använda ett certifikat för autentisering när du kör oövervakade skript.

> [!IMPORTANT]
> Överväg att använda Azure AD hanterad tjänstidentitet som tillämpningsprogrammets identitet i stället för att skapa ett huvudnamn för tjänsten. Azure AD MSI är en funktion för förhandsversion av Azure Active Directory som gör det enklare att skapa en identitet för koden. Om din kod körs på en tjänst som stöder Azure AD MSI och har åtkomst till resurser som stöder Azure Active Directory-autentisering, är Azure AD MSI ett bättre alternativ för dig. Läs mer om Azure AD MSI, inklusive tjänster som stöds för närvarande under avsnittet om [hanterade tjänstidentiteter för Azure-resurser](../active-directory/managed-service-identity/overview.md).

Den här artikeln visar hur du skapar ett huvudnamn för tjänsten som autentiserar med ett certifikat. Om du vill konfigurera ett huvudnamn för tjänsten med lösenord, se [Skapa tjänstens huvudnamn för Azure med Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Du måste ha den [senaste versionen](/powershell/azure/get-started-azureps) av PowerShell för den här artikeln.

## <a name="required-permissions"></a>Nödvändiga behörigheter

För att avsluta artikeln måste du ha tillräcklig behörighet i både Azure Active Directory och Azure-prenumerationen. Du måste specifikt kunna skapa en app i Azure Active Directory och tilldela tjänstens huvudnamn till en roll.

Det enklaste sättet att kontrollera om kontot har tillräcklig behörighet är via portalen. Se [Kontrollera behörighet som krävs](resource-group-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Skapa huvudnamn för tjänsten med självsignerade certifikat

Följande exempel visar ett enkelt scenario. Det använder [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) för att skapa ett huvudnamn för tjänsten med ett självsignerat certifikat och använder [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) för att ge den rollen [deltagare](../role-based-access-control/built-in-roles.md#contributor) för tjänstens huvudnamn. Rolltilldelningen är begränsad till den valda Azure-prenumerationen. Välj en annan prenumeration med [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

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

Exemplet vilar i 20 sekunder för att ge den nya tjänstens huvudnamn lite tid att spridas i Azure Active Directory. Om skriptet inte väntar tillräckligt länge visas ett felmeddelande: "Principal {ID} does not exist in the directory {DIR-ID}." (Huvudkontot {ID} finns inte i katalogen {DIR-ID}). Lös det här felet genom att vänta lite och sedan köra kommandot **New-AzureRmRoleAssignment** igen.

Du kan ange omfånget för rolltilldelningen till en viss resursgrupp med hjälp av parametern **ResourceGroupName**. Du kan också ange omfånget för en viss resurs med parametrarna **ResourceType** och **ResourceName**. 

Om du **inte har Windows 10 eller Windows Server 2016** måste du ladda ned den [självsignerade certifikatgeneratorn](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) från Microsoft Script Center. Extrahera innehållet och importera den cmdlet som du behöver.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

Ersätt följande två rader i skriptet för att generera certifikatet.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Ange certifikat via det automatiska PowerShell-skriptet

När du loggar in som tjänstens huvudnamn måste du ange klientorganisations-ID för katalogen för din AD-app. En klientorganisation är en instans av Azure Active Directory.

```powershell
$TenantId = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzureRmADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Skapa tjänstens huvudnamn med certifikat från certifikatutfärdaren

I följande exempel används ett certifikat som utfärdats från en certifikatutfärdare för att skapa tjänstens huvudnamn. Tilldelningen begränsas till den angivna Azure-prenumerationen. Det lägger till tjänstens huvudnamn till rollen som [Deltagare](../role-based-access-control/built-in-roles.md#contributor). Om ett fel inträffar under rolltilldelningen försöker det göra om tilldelningen.

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

### <a name="provide-certificate-through-automated-powershell-script"></a>Ange certifikat via det automatiska PowerShell-skriptet
När du loggar in som tjänstens huvudnamn måste du ange klientorganisations-ID för katalogen för din AD-app. En klientorganisation är en instans av Azure Active Directory.

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

App-ID och klientorganisations-ID är inte känsliga data, så du kan bädda in dem direkt i skriptet. Om du behöver hämta klientorganisations-ID, använd:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Om du behöver hämta app-ID, använd:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Ändra autentiseringsuppgifter

Om du vill ändra autentiseringsuppgifterna för en AD-app, antingen på grund av ett säkerhetsintrång eller för att en autentiseringsuppgift upphört att gälla, kan du använda cmdletarna [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) och [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential).

För att ta bort alla autentiseringsuppgifter för en app, använd:

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | Remove-AzureRmADAppCredential
```

Skapa ett självsignerat certifikat för att lägga till ett certifikatvärde som visas i den här artikeln. Använd sedan:

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | New-AzureRmADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Felsökning

Du kan få följande fel när du skapar ett huvudnamn för tjänsten:

* **"Authentication_Unauthorized"** (Ej auktoriserad autentisering) eller **"No subscription found in the context."** (Ingen prenumeration hittades i sammanhanget). – Du får det här felet när ditt konto inte har [nödvändiga behörigheter](#required-permissions) på Azure Active Directory att registrera en app. Du får vanligen det här felet om bara administrativa användare i Azure Active Directory kan registrera appar och du inte har ett administratörskonto. Be din administratör antingen tilldela dig en administratörsroll eller ändra inställningar så att användare kan registrera appar.

* Ditt konto **"har inte behörighet att utföra åtgärden 'Microsoft.Authorization/roleAssignments/write' för omfånget '/subscriptions/{guid}'."** – Du får det här felet om ditt konto inte har tillräcklig behörighet att tilldela en roll till en identitet. Be din prenumerationsadministratör att ge dig rollen som administratör för användaråtkomst.

## <a name="next-steps"></a>Nästa steg
* Om du vill konfigurera ett huvudnamn för tjänsten med lösenord, se [Skapa tjänstens huvudnamn för Azure med Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* För detaljerade anvisningar om hur man integrerar ett program i Azure för att hantera resurser, se [utvecklarguiden för auktorisering med Azure Resource Manager API](resource-manager-api-authentication.md).
* En mer detaljerad förklaring av program och tjänstens huvudnamn finns i [Programobjekt och tjänstobjekt](../active-directory/active-directory-application-objects.md). 
* Mer information om Azure Active Directory-autentisering finns i [Autentiseringsscenarier för Azure AD](../active-directory/active-directory-authentication-scenarios.md).
