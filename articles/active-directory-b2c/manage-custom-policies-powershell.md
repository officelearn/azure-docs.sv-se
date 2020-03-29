---
title: Hantera anpassade principer med PowerShell
titleSuffix: Azure AD B2C
description: Använd Azure Active Directory (Azure AD) PowerShell-cmdlet för programmatisk hantering av dina anpassade Azure AD B2C-principer. Skapa, läsa, uppdatera och ta bort anpassade principer med PowerShell.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebf0cfffa410d8dfe2f0e0b42a0fee0c16106fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187414"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Hantera anpassade Azure AD B2C-principer med Azure PowerShell

Azure PowerShell innehåller flera cmdlets för kommandorads- och skriptbaserad anpassad principhantering i din Azure AD B2C-klient. Lär dig hur du använder Azure AD PowerShell-modulen för att:

* Lista anpassade principer i en Azure AD B2C-klient
* Hämta en princip från en klientorganisation
* Uppdatera en befintlig princip genom att skriva över dess innehåll
* Ladda upp en ny princip till din Azure AD B2C-klient
* Ta bort en anpassad princip från en klient

## <a name="prerequisites"></a>Krav

* [Azure AD B2C-klient och](tutorial-create-tenant.md)autentiseringsuppgifter för en användare i katalogen med rollen [B2C IEF-principadministratör](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Anpassade principer](custom-policy-get-started.md) som överförs till din klientorganisation
* [**Förhandsmodulen** Azure AD PowerShell för graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>Ansluta PowerShell-session till B2C-klient

Om du vill arbeta med anpassade principer i din Azure AD B2C-klient måste du först ansluta din PowerShell-session till klienten med kommandot [Connect-AzureAD.][Connect-AzureAD]

Kör följande kommando och `{b2c-tenant-name}` ersätt med namnet på din Azure AD B2C-klientorganisation. Logga in med ett konto som har tilldelats [B2C IEF-principadministratörsrollen](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) i katalogen.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Exempel på kommandoutdata som visar en lyckad inloggning:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Lista alla anpassade principer i klienten

Genom att identifiera anpassade principer kan en Azure AD B2C-administratör granska, hantera och lägga till affärslogik i sina verksamheter. Använd kommandot [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] för att returnera en lista över ID:erna för anpassade principer i en Azure AD B2C-klientorganisation.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Exempel på kommandoutdata:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Ladda ned en policy

När du har granskat listan över princip-ID:n kan du rikta in dig på en specifik princip med [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] för att hämta innehållet.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

I det här exemplet hämtas principen med ID *B2C_1A_signup_signin:*

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

Om du vill redigera principinnehållet lokalt skrivs `-OutputFilePath` kommandot ut till en fil med argumentet och öppnar sedan filen i favoritredigeraren.

Exempelkommando som skickar utdata till en fil:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Uppdatera en befintlig princip

När du har redigerat en principfil som du har skapat eller hämtat kan du publicera den uppdaterade principen till Azure AD B2C med kommandot [Set-AzureADMSTrustFrameworkPolicy.][Set-AzureADMSTrustFrameworkPolicy]

Om du `Set-AzureADMSTrustFrameworkPolicy` utfärdar kommandot med ID:n för en princip som redan finns i din Azure AD B2C-klient, skrivs innehållet i den principen över.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Exempel kommando:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Fler exempel finns i kommandoreferensen [Set-AzureADMSTrustFrameworkPolicy.][Set-AzureADMSTrustFrameworkPolicy]

## <a name="upload-a-new-policy"></a>Ladda upp en ny policy

När du gör en ändring av en anpassad princip som körs i produktion kanske du vill publicera flera versioner av principen för reserv- eller A/B-testscenarier. Du kanske vill göra en kopia av en befintlig princip, ändra den med några små ändringar och sedan ladda upp den som en ny princip för användning av ett annat program.

Använd kommandot [New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] för att ladda upp en ny princip:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Exempel kommando:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Ta bort en anpassad princip

För att upprätthålla en ren verksamhet livscykel, rekommenderar vi att du regelbundet ta bort oanvända anpassade principer. Du kanske till exempel vill ta bort gamla principversioner när du har utfört en migrering till en ny uppsättning principer och verifierat de nya principernas funktioner. Om du försöker publicera en uppsättning anpassade principer och får ett felmeddelande kan det dessutom vara klokt att ta bort de principer som skapades som en del av den misslyckade versionen.

Använd kommandot [Ta bort AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] för att ta bort en princip från din klientorganisation.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Exempel kommando:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Felsöka principuppladdning

När du försöker publicera en ny anpassad princip eller uppdatera en befintlig princip kan felaktig XML-formatering och fel i principfilens arvskedja orsaka verifieringsfel.

Här är till exempel ett försök att uppdatera en princip med innehåll som innehåller felaktig XML (utdata trunkeras för korthet):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

Information om felsökning av anpassade principer finns i [Felsöka anpassade principer för Azure AD B2C och Identity Experience Framework](active-directory-b2c-guide-troubleshooting-custom.md).

## <a name="next-steps"></a>Nästa steg

Information om hur du använder PowerShell för att distribuera anpassade principer som en del av en kontinuerlig ci/cd-pipeline (integration/kontinuerlig leverans) finns i [Distribuera anpassade principer från en Azure DevOps-pipeline](deploy-custom-policies-devops.md).

<!-- LINKS - External -->
[Connect-AzureAD]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/set-azureadmstrustframeworkpolicy
