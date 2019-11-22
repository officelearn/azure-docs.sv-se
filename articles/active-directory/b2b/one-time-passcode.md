---
title: Autentisering med eng ång slö sen ord för B2B-gäst användare – Azure AD
description: Använda email eng ång slö sen ord för att autentisera B2B-gäst användare utan att behöva en Microsoft-konto.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272310"
---
# <a name="email-one-time-passcode-authentication-preview"></a>E-postautentisering med eng ång slö sen ord (för hands version)

|     |
| --- |
| Email eng ång slö sen ord är en offentlig förhands gransknings funktion i Azure Active Directory. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Den här artikeln beskriver hur du aktiverar autentisering med eng ång slö sen ord för B2B-gäst användare. Med funktionen för eng ång slö sen ord autentiseras B2B-gäst användare när de inte kan autentiseras via andra sätt som Azure AD, en Microsoft-konto (MSA) eller Google Federation. Med autentisering med eng ång slö sen ord behöver du inte skapa en Microsoft-konto. När gäst användaren löser in en inbjudan eller får åtkomst till en delad resurs, kan de begära en tillfällig kod som skickas till deras e-postadress. Sedan anger de den här koden för att fortsätta logga in.

Den här funktionen är för närvarande tillgänglig för för hands version (se [väljer i för hands versionen](#opting-in-to-the-preview) nedan). Efter för hands versionen aktive ras den här funktionen som standard för alla klienter.

> [!NOTE]
> Användare av eng ång slö sen ord måste logga in med en länk som innehåller klient kontexten (till exempel `https://myapps.microsoft.com/?tenantid=<tenant id>` eller `https://portal.azure.com/<tenant id>`eller om en verifierad domän `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Direkt länkar till program och resurser fungerar även så länge de omfattar klient kontexten. Gäst användare kan för närvarande inte logga in med slut punkter som inte har någon klient kontext. Om du till exempel använder `https://myapps.microsoft.com`, `https://portal.azure.com`eller den gemensamma arbets slut punkten leder det till ett fel. 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Användar upplevelse för gäst användare med eng ång slö sen ord
Med autentisering med eng ång slö sen ord kan gäst användaren lösa in din inbjudan genom att klicka på en direkt länk eller via e-postinbjudan. I båda fallen indikerar ett meddelande i webbläsaren att en kod skickas till gäst användarens e-postadress. Gäst användaren väljer **Skicka kod**:
 
   ![Skärm bild som visar knappen Skicka kod](media/one-time-passcode/otp-send-code.png)
 
Ett lösen ord skickas till användarens e-postadress. Användaren hämtar lösen ordet från e-postmeddelandet och anger det i webbläsarfönstret:
 
   ![Skärm bild som visar sidan Ange tecken](media/one-time-passcode/otp-enter-code.png)
 
Gäst användaren är nu autentiserad och kan se den delade resursen eller fortsätta att logga in. 

> [!NOTE]
> Lösen ord för eng ång slö sen ord är giltiga i 30 minuter. Efter 30 minuter är det angivna eng ång slö sen ord inte längre giltigt och användaren måste begära en ny. Användarsessioner upphör att gälla efter 24 timmar. Efter det tar gäst användaren emot ett nytt lösen ord när de får åtkomst till resursen. Sessionen upphör att gälla ger ytterligare säkerhet, särskilt när en gäst användare lämnar företaget eller inte längre behöver åtkomst.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>När får en gäst användare en eng ång slö sen ord?

När en gäst användare löser in en inbjudan eller använder en länk till en resurs som har delats med dem får de ett eng ång slö sen ord om:
- De har inget Azure AD-konto 
- De har inte någon Microsoft-konto 
- Den bjudande klienten har inte konfigurerat Google Federation för @gmail.com och @googlemail.com användare 

Vid tidpunkten för inbjudan finns det ingen indikation på att användaren som du bjuder in ska använda autentisering med eng ång slö sen ord. Men när gäst användaren loggar in blir autentiseringen med eng ång slö sen ord reserv metoden om inga andra autentiseringsmetoder kan användas. 

Du kan visa gäst användare som autentiserar med eng ång slö sen ord i Azure Portal genom att gå till **Azure Active Directory** > **organisations relationer** > **användare från andra organisationer**.

![Skärm bild som visar en eng ång slö sen ord med käll värde för eng ång slö sen ord](media/one-time-passcode/otp-users.png)

> [!NOTE]
> När en användare löser ett eng ång slö sen ord och senare hämtar ett MSA, ett Azure AD-konto eller ett annat federerat konto, fortsätter de att autentiseras med ett eng ång slö sen ord. Om du vill uppdatera deras autentiseringsmetod kan du ta bort deras gäst användar konto och bjuda in dem på samma sätt.

### <a name="example"></a>Exempel
Gäst användare alexdoe@gmail.com bjuds in till Fabrikam, som inte har konfigurerat Google Federation. Alex har inte någon Microsoft-konto. De får ett eng ång slö sen ord för autentisering.

## <a name="opting-in-to-the-preview"></a>Väljer i för hands versionen 
Det kan ta några minuter innan opt-in-åtgärden börjar gälla. Sedan kommer endast nyligen inbjudna användare som uppfyller villkoren ovan att använda autentisering med eng ång slö sen ord. Gäst användare som tidigare har löst en inbjudan fortsätter att använda samma autentiseringsmetod.

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Välja att använda Azure AD-portalen
1.  Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD.
2.  I navigerings fönstret väljer du **Azure Active Directory**.
3.  Under **Hantera**väljer du **organisations relationer**.
4.  Välj **inställningar**.
5.  Under **Aktivera e-post med eng ång slö sen ord för gäster (för hands version)** väljer du **Ja**.
 
### <a name="to-opt-in-using-powershell"></a>Välja att använda PowerShell

Först måste du installera den senaste versionen av Azure AD PowerShell för Graph module (AzureADPreview). Sedan ska du avgöra om B2B-principerna redan finns och köra lämpliga kommandon.

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>Förutsättning: installera den senaste AzureADPreview-modulen
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

#### <a name="check-for-existing-policies-and-opt-in"></a>Sök efter befintliga principer och välj

Kontrol lera sedan att det finns en B2BManagementPolicy genom att köra följande:

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- Om utdata är false finns inte principen för närvarande. Skapa en ny B2BManagementPolicy och välj att förhands granskningen genom att köra följande:

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- Om resultatet är sant finns B2BManagementPolicy-principen för närvarande. Om du vill uppdatera principen och välja att välja för hands versionen kör du följande:
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>Väljer bort från förhands granskningen efter väljer i
Det kan ta några minuter innan den opt-out-åtgärden börjar gälla. Om du inaktiverar för hands versionen kan alla gäst användare som har löst ett eng ång slö sen ord inte logga in. Du kan ta bort gäst användaren och Bjud in användaren så att de kan logga in igen med en annan autentiseringsmetod.

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Så här stänger du av för hands versionen med Azure AD-portalen
1.  Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD.
2.  I navigerings fönstret väljer du **Azure Active Directory**.
3.  Under **Hantera**väljer du **organisations relationer**.
4.  Välj **inställningar**.
5.  Under **Aktivera e-post med eng ång slö sen ord för gäster (för hands version)** väljer du **Nej**.

### <a name="to-turn-off-the-preview-using-powershell"></a>Så här stänger du av för hands versionen med PowerShell
Installera den senaste AzureADPreview-modulen om du inte redan har den (se [förutsättning: installera den senaste AzureADPreview-modulen](#prerequisite-install-the-latest-azureadpreview-module) ovan). Kontrol lera sedan att principen för för hands versionen för eng ång slö sen ord finns för närvarande genom att köra följande:

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

Om resultatet är sant väljer du för hands versionen genom att köra följande:

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

