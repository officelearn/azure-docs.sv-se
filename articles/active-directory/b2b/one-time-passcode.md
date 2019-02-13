---
title: Engångskod autentisering för gästanvändare för B2B - Azure Active Directory | Microsoft Docs
description: Hur du använder e-engångslösenkoden för att autentisera B2B-gästanvändare utan behov av ett Microsoft-konto.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 1/25/2019
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9493f7ee3278bb42dc21574cd008fbe2f4376a1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56185153"
---
# <a name="email-one-time-passcode-authentication-preview"></a>E-engångskod authentication (förhandsversion)

|     |
| --- |
| Engångskod för e-post är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Funktionen för e-post engångskod autentiserar B2B-gästanvändare när de inte kan autentiseras via annat sätt som Azure AD, en Microsoft-konto (MSA) eller Google-federation. Med engångskod autentisering finns behöver du inte skapa ett Microsoft-konto. När gästanvändaren redeems inbjudan eller har åtkomst till en delad resurs, kan de begära en tillfällig kod, som skickas till sin e-postadress. De ange koden för att fortsätta att logga in.

Den här funktionen är tillgänglig för förhandsversion (se [valde förhandsversionen](#opting-in-to-the-preview) nedan). Efter förhandsversionen kan aktiveras den här funktionen som standard för alla klienter.

> [!NOTE]
> Engångskod användare måste logga in med en länk som innehåller klient-kontext (till exempel `https://myapps.microsoft.com/?tenantid=<tenant id>` eller `https://portal.azure.com/<tenant id>`, eller när det gäller en verifierad domän `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Direktlänkar till program och resurser fungerar också så länge som de innehåller klient-kontext. Gästanvändare kan för närvarande inte att logga in med slutpunkter som har ingen klient-kontext. Till exempel `https://myapps.microsoft.com`, `https://portal.azure.com`, eller team vanliga slutpunkten kommer att orsaka fel. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Användarupplevelsen för engångslösenord gästanvändare
Med engångskod autentisering kan gästanvändaren Lös in inbjudan genom att klicka på en direktlänk eller med hjälp av e-postinbjudan. I båda fallen indikerar ett meddelande i webbläsaren att en kod kommer att skickas till gästanvändarens e-postadress. Gästanvändaren väljer **skicka kod**:
 
   ![Åtkomstpaneler hantera app](media/one-time-passcode/otp-send-code.png)
 
Ett lösenord skickas till användarens e-postadress. Användaren hämtar lösenordet från e-postmeddelandet och anger den i webbläsarfönstret:
 
   ![Åtkomstpaneler hantera app](media/one-time-passcode/otp-enter-code.png)
 
Gästanvändaren nu autentiseras och de kan se den delade resursen eller fortsätta att logga in. 

> [!NOTE]
> Enstaka lösenord är giltiga i 30 minuter. Den specifika engångskod är inte längre giltig efter 30 minuter och användaren måste begära en ny. Användarsessioner går ut efter 24 timmar. Därefter tas får gästanvändaren ett nytt lösenord när de ansluter till resursen. Sessionen upphör att gälla ger ökad säkerhet, särskilt när en gästanvändare lämnar företaget eller inte längre behöver åtkomst.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>När får ett engångslösenord i en gästanvändare?

När en gästanvändare redeems inbjudan eller använder en länk till en resurs som har delats med dem, får de ett engångslösenord om:
- De har inte en Azure AD-konto 
- De har inte ett Microsoft-konto 
- Bjuder in klienten har inte konfigurerats Google federation för @gmail.com och @googlemail.com användare 

Det finns inget som tyder på att användaren du gärna kommer att använda autentisering med engångslösenord vid tidpunkten för inbjudan. Men när gästanvändare loggar in engångskod autentisering är metoden som reserv om inga andra autentiseringsmetoder kan användas. 

Du kan visa gästanvändare som autentiserats med ett enstaka lösenord i Azure portal genom att gå till **Azure Active Directory** > **organisationens relationer**  >   **Användare från andra organisationer**.

![Visa engångskod användare i Azure-portalen där källan är lika med Engångslösenord](media/one-time-passcode/otp-users.png)

> [!NOTE]
> När en användare redeems ett engångslösenord och senare hämtar en MSA, Azure AD-konto eller andra federerade, fortsätter de att autentiseras med en engångslösenkod. Om du vill uppdatera autentiseringsmetod du reinvite dem ta bort deras gästanvändarkontot.

### <a name="example"></a>Exempel
Gästanvändaren alexdoe@gmail.com bjuds in till Fabrikam, som inte har Google federation ställa in. Alex har inte ett Microsoft-konto. Han kommer att få ett engångslösenord för autentisering.

## <a name="opting-in-to-the-preview"></a>Valde förhandsversionen 
Det kan ta några minuter innan åtgärden valbar ska börja gälla. Efter det kan använder endast nyligen inbjudna användare som uppfyller villkor som ovan autentisering med engångslösenord. Gästanvändare som utnyttjats inbjudan kommer fortsätta att använda samma autentiseringsmetod.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Att välja med hjälp av Azure AD-portalen
1.  Logga in på den [Azure-portalen](https://portal.azure.com/) som en global administratör för Azure AD.
2.  I navigeringsfönstret väljer **Azure Active Directory**.
3.  Under **hantera**väljer **organisationens relationer**.
4.  Välj **inställningar**.
5.  Under **Aktivera e-engångslösenord lösenord för gäster (förhandsversion)** väljer **Ja**.
 
### <a name="to-opt-in-using-powershell"></a>Att välja med hjälp av PowerShell

Först måste du installera den senaste versionen av Azure AD PowerShell för Graph-modul (AzureADPreview). Sedan bestämmer om B2B-principer redan finns och kör rätt kommandon.

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

#### <a name="check-for-existing-policies-and-opt-in"></a>Söka efter befintliga principer och välja

Därefter kontrollerar du om det finns en B2BManagementPolicy för närvarande genom att köra följande:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Om resultatet är False, finns inte för närvarande principen. Skapa en ny B2BManagementPolicy och välja förhandsgranskningen genom att köra följande:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Om resultatet är sant, finns för närvarande B2BManagementPolicy principen. För att uppdatera principen och välj att delta i förhandsversionen, kör du följande:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Väljer bort förhandsgranskningen efter du börjat
Det kan ta några minuter för att välja bort-åtgärden ska börja gälla. Om du stänger av förhandsversionen av alla gästanvändare som har löst in ett engångslösenord inte kunna logga in. Du kan ta bort gästanvändaren och reinvite användaren så att de kan logga in igen med en annan autentiseringsmetod.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Inaktivera förhandsversionen via Azure AD-portalen
1.  Logga in på den [Azure-portalen](https://portal.azure.com/) som en global administratör för Azure AD.
2.  I navigeringsfönstret väljer **Azure Active Directory**.
3.  Under **hantera**väljer **organisationens relationer**.
4.  Välj **inställningar**.
5.  Under **Aktivera e-engångslösenord lösenord för gäster (förhandsversion)** väljer **nr**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Inaktivera förhandsversionen via PowerShell
Installera den senaste modulen AzureADPreview om du inte redan har den (se [nödvändiga: Installera den senaste modulen AzureADPreview](#prerequisite-install-the-latest-azureadpreview-module) ovan). Kontrollera sedan att principen engångskod förhandsgranskning för närvarande finns genom att köra följande:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Om resultatet är sant, kan du välja bort förhandsgranskningen genom att köra följande:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

