---
title: Felsöka kombinerade registreringen för Azure AD SSPR och MFA (förhandsversion) – Azure Active Directory
description: Felsöka Azure AD-Multifaktorautentisering och lösenordsåterställning via självbetjäning kombinerade registrering (förhandsversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d926f7312b62e788289939dfd81c236a33503b43
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370473"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Felsöka kombineras security information registrering (förhandsversion)

Informationen i den här artikeln hjälper administratörer som felsökning av problem med kombinerade registrerings-funktionerna som rapporteras av slutanvändarna.

|     |
| --- |
| Kombinerade security information registreringen för Azure Multi-Factor Authentication och Azure AD lösenordsåterställning via självbetjäning är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="audit-logs"></a>Granskningsloggar

Händelser som har loggats för registrering av kombinerade finns under kategorin ”autentiseringsmetoder” i Azure AD granskningsloggar.

![Azure AD-granskningsloggar registreringshändelser för gränssnittet som visar](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

Här nedan listas alla granskningshändelserna som genereras av kombinerade registrering:

| Aktivitet | Status | Orsak | Beskrivning |
| --- | --- | --- | --- |
| Användare registrerad all säkerhetsinformation som krävs | Lyckades | Användare registrerad all säkerhetsinformation som krävs. | Den här händelsen inträffar när en användare har slutfört registreringen.|
| Användare registrerad all säkerhetsinformation som krävs | Fel | Användaren avbröt security info registrering. | Den här händelsen inträffar när en användare avbryter registreringen från interrupt läge.|
| Användare registrerad säkerhetsinformation | Lyckades | Användare registrerad ”method”. | Den här händelsen inträffar när en användare registrerar en enskild metod. ”Method” kan vara Authenticator-appen, telefon, e-post, säkerhetsfrågor, applösenord, telefon, osv.| 
| Användare som har granskat säkerhetsinformation | Lyckades | Användaren granskat har säkerhetsinformation. | Den här händelsen inträffar när en användare klickar på ”ser bra” på sidan Granska säkerhetsinformation.|
| Användare som har granskat säkerhetsinformation | Fel | Det gick inte att granska säkerhetsinformation användare. | Den här händelsen inträffar när en användare klickar på ”ser bra ut” på säkerhetsinformationen Granska sidan men något inte fungerar i serverdelen.|
| Användaren tog bort säkerhetsinformation | Lyckades | Användaren tog bort ”method”. | Den här händelsen inträffar när användaren tar bort en enskild metod. ”Method” kan vara Authenticator-appen, telefon, e-post, säkerhetsfrågor, applösenord, telefon, osv.|
| Användaren tog bort säkerhetsinformation | Fel | Det gick inte att ta bort ”method” användaren. | Den här händelsen inträffar när en användare försöker att ta bort en metod men av någon anledning misslyckas. ”Method” kan vara Authenticator-appen, telefon, e-post, säkerhetsfrågor, applösenord, telefon, osv.|
| Användare har ändrats standard säkerhetsinformation | Lyckades | Användaren ändrade standard säkerhetsinformation till ”method”. | Den här händelsen inträffar när en användare ändrar sina standardmetoden. ”Method” kan vara Authenticator appmeddelande, kod från min authenticator-appen eller token, anrop + X XXXXXXXXXX, Text en kod till + X XXXXXXXXX osv.|
| Användare har ändrats standard säkerhetsinformation | Fel | Det gick inte att ändra standard säkerhetsinformation till ”method” användaren. | Den här händelsen inträffar när en användare försöker att ändra sina standardmetoden men av någon anledning misslyckas. ”Method” kan vara Authenticator appmeddelande, en kod från min authenticator-appen eller token, anrop + X XXXXXXXXXX, Text en kod till + X XXXXXXXXX osv.|

## <a name="troubleshooting-interrupt-mode"></a>Felsökningsläge avbrott

| Symtom | Felsökningsanvisningar |
| --- | --- |
| Jag ser inte de metoder som jag förväntar dig. | 1. Kontrollera om användaren har en Azure AD-administratörsroll. Om Ja, granska SSPR administratör princip skillnaderna. <br> 2. Avgör om användaren avbryts på grund av MFA-registrering tvingande eller tvingande för SSPR-registrering. Granska flödesschema under kombinerade registrerings-lägen för att avgöra vilka metoder som ska visas. <br> 3. Avgör hur nyligen principen MFA eller SSPR har ändrats. Om ändringen var senaste, kan det ta lite tid för den uppdaterade policyn att spridas.|

## <a name="troubleshooting-manage-mode"></a>Felsökning av hantera läge

| Symtom | Felsökningsanvisningar |
| --- | --- |
| Jag har inte möjlighet att lägga till en viss metod. | 1. Avgör om metoden är aktiverad för MFA eller för SSPR. <br> 2. Om metoden aktiveras, spara om principerna och vänta 1 till 2 timmar innan du testar igen. <br> 3. Om metoden är aktiverad, kontrollerar du att användaren inte har ställt in det maximala antalet den metod som de har tillåtelse att ställa in.|

## <a name="disable-combined-registration"></a>Inaktivera kombinerade registrering

När en användare registrerar sina telefonnummer och/eller mobila appar i den nya kombineras upplevelse, vår tjänst stämplar flaggor (StrongAuthenticationMethods) för dessa metoder på användaren. Den här funktionen tillåter att användaren utför Azure Multi-Factor Authentication (MFA) med dessa metoder när MFA krävs.

De metoder som användare registrera sig via den nya upplevelsen ha egenskapsuppsättningen StrongAuthenticationMethods. Om en administratör aktiverar förhandsversionen, användare registrera sig via den nya upplevelsen och sedan administratören inaktiverar förhandsversionen, får användare omedvetet registreras för MFA också.

Om en användare som har slutförts kombineras registrering navigerar du till registreringssidan aktuella lösenord för självbetjäning (SSPR) för återställning på [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), uppmanas de att utföra MFA innan de kan komma åt sidan. Det här steget är ett förväntat beteende ur en teknisk synvinkel, men om en användare som tidigare hade registrerats för SSPR endast, det här steget är en ny funktion. Även om den här extra steg förbättrar användarens säkerhetspositionen genom att tillhandahålla en ytterligare nivå av säkerhet, kan administratörer behöva återställa sina användare så att de inte längre kan utföra MFA.  

### <a name="how-to-roll-back-users"></a>Återställa användare

Om du som administratör vill du återställa en användares MFA-inställningar, har vi skapat ett PowerShell-skript som ska ta bort egenskapen StrongAuthenticationMethods för en användares mobilapp och/eller telefonnummer. Kör skriptet för dina användare innebär att de måste registrera för MFA om det behövs. Vi rekommenderar att du testar återställning med en eller två användare innan du återställer alla användare som påverkas.

Stegen nedan hjälper dig att återställa en användare eller grupp av användare:

#### <a name="prerequisites"></a>Förutsättningar

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

#### <a name="rollback"></a>Återställning

Kör följande kommando när du har uppdaterat de markerade platserna i en PowerShell-fönster. Ange autentiseringsuppgifterna för global administratör när du tillfrågas. Skriptets utdata är resultatet av varje åtgärd för uppdatering av användaren.

`<script location> -path <user file location>`

### <a name="disable-preview-experience"></a>Inaktivera förhandsversionen

Om du vill inaktivera förhandsgranskning upplevelse för användarna, gör du följande:

1. Logga in på Azure-portalen som global administratör eller Användaradministratör.
2. Bläddra till **Azure Active Directory**, **användarinställningar**, **hantera inställningar för åtkomst till panelen förhandsversionsfunktioner**.
3. Under **användare kan använda förhandsversionsfunktioner för att registrera och hantera säkerhetsinformation**, inställd väljaren **ingen** och klicka på **spara**.

Användarna uppmanas inte längre att registrera med hjälp av förhandsversionen-upplevelse.

## <a name="next-steps"></a>Nästa steg

* [Mer information om den offentliga förhandsversionen av kombinerade registrering för lösenordsåterställning via självbetjäning och Azure Multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
