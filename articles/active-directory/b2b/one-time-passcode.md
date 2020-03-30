---
title: Engångslösenkodsautentisering för B2B-gästanvändare - Azure AD
description: Så här använder du E-post engångslösenord för att autentisera B2B-gästanvändare utan att behöva ett Microsoft-konto.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6d897bb983eb06baa4f1573f1f875eea8bb8afc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263379"
---
# <a name="email-one-time-passcode-authentication-preview"></a>E-postautentisering med engångslösenord (förhandsgranskning)

|     |
| --- |
| Skicka en engångskod via e-post är en offentlig förhandsgranskningsfunktion i Azure Active Directory. Mer information om förhandsgranskningar finns i [Tilläggsvillkor för microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

I den här artikeln beskrivs hur du aktiverar autentisering av engångslösenord för B2B-gästanvändare via e-post. Funktionen E-postlösenkod autentiserar B2B-gästanvändare när de inte kan autentiseras på annat sätt som Azure AD, ett Microsoft-konto (MSA) eller Google-federation. Med engångsautentisering av lösenkod behöver du inte skapa ett Microsoft-konto. När gästanvändaren löser in en inbjudan eller får åtkomst till en delad resurs kan de begära en tillfällig kod som skickas till deras e-postadress. Sedan anger de den här koden för att fortsätta logga in.

Den här funktionen är för närvarande tillgänglig för förhandsgranskning (se [Välja i förhandsgranskningen](#opting-in-to-the-preview) nedan). Efter förhandsgranskningen aktiveras den här funktionen som standard för alla klienter.

> [!NOTE]
> Engångslösenordanvändare måste logga in med en länk som innehåller `https://myapps.microsoft.com/?tenantid=<tenant id>` `https://portal.azure.com/<tenant id>`klientkontexten (till exempel `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`, eller , eller när det gäller en verifierad domän, ). Direkta länkar till program och resurser fungerar också så länge de innehåller klientkontexten. Gästanvändare kan för närvarande inte logga in med slutpunkter som inte har någon klientkontext. Om du `https://myapps.microsoft.com`till `https://portal.azure.com`exempel använder , eller teams gemensamma slutpunkt kommer det att resultera i ett fel. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Användarupplevelse för engångslösenordsgästanvändare
Med engångsautentisering av lösenkod kan gästanvändaren lösa in din inbjudan genom att klicka på en direktlänk eller genom att använda e-postmeddelandet med inbjudan. I båda fallen anger ett meddelande i webbläsaren att en kod kommer att skickas till gästanvändarens e-postadress. Gästanvändaren väljer **Skicka kod:**
 
   ![Skärmbild som visar knappen Skicka kod](media/one-time-passcode/otp-send-code.png)
 
En lösenkod skickas till användarens e-postadress. Användaren hämtar lösenkoden från e-postmeddelandet och anger den i webbläsarfönstret:
 
   ![Skärmbild som visar sidan Ange kod](media/one-time-passcode/otp-enter-code.png)
 
Gästanvändaren är nu autentiserat och de kan se den delade resursen eller fortsätta logga in. 

> [!NOTE]
> Engångslösenord är giltiga i 30 minuter. Efter 30 minuter är den specifika engångslösenkoden inte längre giltig och användaren måste begära en ny. Användarsessioner upphör att gälla efter 24 timmar. Efter den tiden får gästanvändaren en ny lösenkod när de kommer åt resursen. Session förfallodatum ger ökad säkerhet, särskilt när en gästanvändare lämnar sitt företag eller inte längre behöver åtkomst.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>När får en gästanvändare en engångslösenkod?

När en gästanvändare löser in en inbjudan eller använder en länk till en resurs som har delats med dem får de ett engångslösenord om:
- De har inget Azure AD-konto 
- De har inget Microsoft-konto 
- Den inbjudande klienten har @gmail.com inte @googlemail.com konfigurerat Google-federation för och användare 

Vid inbjudningen finns det inget som tyder på att användaren du bjuder in kommer att använda engångsautentisering av lösenkod. Men när gästanvändaren loggar in blir engångslösenkodautentisering reservmetoden om inga andra autentiseringsmetoder kan användas. 

Du kan visa gästanvändare som autentiserar med engångslösenord i Azure-portalen genom att gå till **Azure Active Directory** > **Organizational relationships** > **Användare från andra organisationer**.

![Skärmbild som visar en engångslösenkodsanvändare med källvärde för otp](media/one-time-passcode/otp-users.png)

> [!NOTE]
> När en användare löser in en engångslösenkod och senare skaffar ett MSA-, Azure AD-konto eller annat federerat konto fortsätter de att autentiseras med hjälp av en engångslösenkod. Om du vill uppdatera deras autentiseringsmetod kan du ta bort deras gästanvändarkonto och skicka in dem igen.

### <a name="example"></a>Exempel
Gästanvändaren alexdoe@gmail.com är inbjuden till Fabrikam, som inte har Google Federation konfigurerad. Alex har inget Microsoft-konto. De får en engångslösenord för autentisering.

## <a name="opting-in-to-the-preview"></a>Välja till förhandsgranskningen 
Det kan ta några minuter innan opt-in-åtgärden träder i kraft. Därefter använder endast nyligen inbjudna användare som uppfyller villkoren ovan en engångsautentisering av lösenkod. Gästanvändare som tidigare löst in en inbjudan fortsätter att använda samma autentiseringsmetod.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Så här väljer du att använda Azure AD-portalen
1.  Logga in på [Azure-portalen](https://portal.azure.com/) som global Azure AD-administratör.
2.  Välj Azure Active **Directory**i navigeringsfönstret .
3.  Välj **Organisationsrelationer**under **Hantera**.
4.  Välj **Inställningar**.
5.  Under **Aktivera lösenord för engångsmeddelanden för gäster (förhandsversion)** väljer du **Ja**.
 
### <a name="to-opt-in-using-powershell"></a>Så här väljer du att använda PowerShell

Först måste du installera den senaste versionen av Azure AD PowerShell for Graph-modulen (AzureADPreview). Sedan avgör du om B2B-principer redan finns och kör lämpliga kommandon.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Förutsättning: Installera den senaste AzureADPreview-modulen
Kontrollera först vilka moduler du har installerat. Öppna Windows PowerShell som upphöjd användare (Kör som administratör) och kör följande kommando:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Om modulen AzureADPreview visas utan meddelande som anger att det finns en senare version så är du klar. Annars gör du något av följande, baserat på utdata:

- Om inga resultat returneras kör du följande kommando för att installera AzureADPreview-modulen:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Om enbart AzureAD-modulen visas i resultaten så kör du följande kommandon för att installera AzureADPreview-modulen: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Om endast AzureADPreview-modulen visas i resultaten, men du får ett meddelande som anger att det finns en senare version så uppdaterar du modulen genom att köra följande kommandon: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Du får eventuellt en uppmaning om att installera modulen från en icke betrodd lagringsplats. Det här inträffar om du inte tidigare angett PSGallery-lagringsplatsen som en betrodd lagringsplats. Installera modulen genom att trycka på **Y**.

#### <a name="check-for-existing-policies-and-opt-in"></a>Sök efter befintliga policyer och välj

Kontrollera sedan om det finns en B2BManagementPolicy genom att köra följande:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Om utdata är Falskt finns principen för närvarande inte. Skapa en ny B2BManagementPolicy och välj till förhandsversionen genom att köra följande:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Om utdata är Sant finns principen B2BManagementPolicy för närvarande. Om du vill uppdatera principen och anmäla dig till förhandsversionen kör du följande:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Välja bort förhandsversionen efter att ha valt att delta
Det kan ta några minuter innan undantagsåtgärderna träder i kraft. Om du inaktiverar förhandsgranskningen kan inte gästanvändare som har löst in en engångslösenkod logga in. Du kan ta bort gästanvändaren och skicka in användaren igen så att de kan logga in igen med en annan autentiseringsmetod.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Så här inaktiverar du förhandsversionen med Azure AD-portalen
1.  Logga in på [Azure-portalen](https://portal.azure.com/) som global Azure AD-administratör.
2.  Välj Azure Active **Directory**i navigeringsfönstret .
3.  Välj **Organisationsrelationer**under **Hantera**.
4.  Välj **Inställningar**.
5.  Under **Aktivera lösenord för engångsmeddelanden för gäster (förhandsversion)** väljer du **Nej**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Så här inaktiverar du förhandsgranskningen med PowerShell
Installera den senaste AzureADPreview-modulen om du inte redan har den (se [Förutsättning: Installera den senaste AzureADPreview-modulen](#prerequisite-install-the-latest-azureadpreview-module) ovan). Kontrollera sedan att förhandsgranskningsprincipen för engångslösenord för närvarande finns genom att köra följande:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Om utdata är Sant väljer du bort förhandsgranskningen genom att köra följande:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

