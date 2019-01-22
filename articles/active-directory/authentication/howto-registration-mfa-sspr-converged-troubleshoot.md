---
title: Inaktivera konvergerade registreringen för Azure AD SSPR och MFA (offentlig förhandsversion)
description: Inaktivera Azure AD-Multifaktorautentisering och lösenordsåterställning via självbetjäning registrering (offentlig förhandsversion)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 6a51b1a3050b37fdcc822006f9e25d6662c65fb2
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426360"
---
# <a name="disable-azure-ad-converged-registration-public-preview"></a>Inaktivera Azure AD konvergerat registrering (offentlig förhandsversion)

När en användare registrerar sina telefonnummer och/eller mobila appar i den nya konvergerat upplevelse, vår tjänst stämplar flaggor (StrongAuthenticationMethods) för dessa metoder på användaren. Den här funktionen tillåter att användaren utför Azure Multi-Factor Authentication (MFA) med dessa metoder när MFA krävs.

De metoder som användare registrera sig via den nya upplevelsen ha egenskapsuppsättningen StrongAuthenticationMethods. Detta inträffar även när förhandsversionen är tillgänglig. Om en administratör aktiverar förhandsversionen, användare registrera sig via den nya upplevelsen och sedan administratören inaktiverar förhandsversionen, får användare omedvetet registreras för MFA också.

Om en användare som har slutförts konvergerat registrering navigerar du till den aktuella SSPR-registreringssidan på [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), uppmanas de att utföra MFA innan de kan komma åt sidan. Det här steget är ett förväntat beteende ur en teknisk synvinkel, men om en användare som tidigare hade registrerats för SSPR endast, det här steget är en ny funktion. Även om den här extra steg förbättrar användarens säkerhetspositionen genom att tillhandahålla en ytterligare nivå av säkerhet, kan administratörer behöva återställa sina användare så att de inte längre kan utföra MFA.  

## <a name="how-to-roll-back-users"></a>Återställa användare

Om du som administratör vill du återställa en användares MFA-inställningar, har vi skapat ett PowerShell-skript som ska ta bort egenskapen StrongAuthenticationMethods för en användares mobilapp och/eller telefonnummer. Kör skriptet för dina användare innebär att de måste registrera för MFA om det behövs. Vi rekommenderar att du testar återställning med en eller två användare innan du återställer alla användare som påverkas.

Stegen nedan hjälper dig att återställa en användare eller grupp av användare:

### <a name="pre-requisites"></a>Förutsättningar

1. Du behöver du installera lämplig Azure AD PowerShell-moduler. Kör dessa kommandon för att installera modulerna i ett PowerShell-fönster:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Spara listan över berörda användarobjekt-ID/ID: N till din dator som en textfil med ett ID per rad. Anteckna platsen för filen.
1. Spara följande skript på din dator och Anteckna platsen för skriptet:

```powershell
<# 
//********************************************************
//*                                                      *
//*   Copyright (C) Microsoft. All rights reserved.      *
//*                                                      *
//********************************************************
#>

param($path)

# Define Remediation Fn
function RemediateUser {

    param  
    (
        $ObjectId
    )

    $user = Get-MsolUser -ObjectId $ObjectId

    Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

    $hasMfaRelyingParty = $false
    foreach($p in $user.StrongAuthenticationRequirements)
    {
        if ($p.RelyingParty -eq "*")
        {
            $hasMfaRelyingParty = $true
            Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
        }
    }

    if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
    {
        Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
        Write-Host "Rolling back user ..." -ForegroundColor Yellow
        Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
        Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
    }
    else
    {
        Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
    }

    Write-Host ""
    Start-Sleep -Milliseconds 750
}

# Connect
Import-Module MSOnline
Connect-MsolService

foreach($line in Get-Content $path)
{
    RemediateUser -ObjectId $line
}
```

### <a name="rollback"></a>Återställning

Kör följande kommando när du har uppdaterat de markerade platserna i en PowerShell-fönster. Ange autentiseringsuppgifterna för global administratör när du tillfrågas. Skriptets utdata är resultatet av varje åtgärd för uppdatering av användaren.

`<script location> -path <user file location>`

## <a name="disable-preview-experience"></a>Inaktivera förhandsversionen

Om du vill inaktivera förhandsgranskning upplevelse för användarna, gör du följande:

1. Logga in på Azure-portalen som global administratör eller Användaradministratör.
2. Bläddra till **Azure Active Directory**, **användarinställningar**, **hantera inställningar för åtkomst till panelen förhandsversionsfunktioner**.
3. Under **användare kan använda förhandsversionsfunktioner för att registrera och hantera säkerhetsinformation**, inställd väljaren **ingen** och klicka på **spara**.

Användarna uppmanas inte längre att registrera med hjälp av förhandsversionen-upplevelse.

## <a name="next-steps"></a>Nästa steg

[Mer information om den offentliga förhandsversionen av konvergerade registrering för lösenordsåterställning via självbetjäning och Azure Multi-Factor Authentication](concept-registration-mfa-sspr-converged.md)