---
title: Felsöka kombinerade registreringen för Azure AD SSPR och Multi-Factor Authentication (förhandsversion) – Azure Active Directory
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
ms.openlocfilehash: ba77772352d3f6f6494abeddc7faf9f12e5f80c2
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262575"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Felsöka kombineras security information registrering (förhandsversion)

Informationen i den här artikeln är avsedd att hjälpa administratörer som felsöker problem som rapporteras av användare av kombinerade registrerings-upplevelsen.

|     |
| --- |
| Kombinerade security information registreringen för Azure Multi-Factor Authentication och återställning av lösenord för Azure Active Directory (Azure AD) är en funktion i offentliga förhandsversionen av Azure AD. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="audit-logs"></a>Granskningsloggar

Händelser som har loggats för kombinerade registrering finns i kategorin autentiseringsmetoder i Azure AD granskningsloggar.

![Azure AD-granskningsloggar registreringshändelser för gränssnittet som visar](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

I följande tabell visas alla granskningshändelserna som genereras av kombinerade registrering:

| Aktivitet | Status | Orsak | Beskrivning |
| --- | --- | --- | --- |
| Användare registrerad all säkerhetsinformation som krävs | Lyckades | Användare registrerad all säkerhetsinformation som krävs. | Den här händelsen inträffar när en användare har slutfört registreringen.|
| Användare registrerad all säkerhetsinformation som krävs | Fel | Användaren avbröt security info registrering. | Den här händelsen inträffar när en användare avbryter registreringen från interrupt läge.|
| Användare registrerad säkerhetsinformation | Lyckades | Användaren har registrerats *metoden*. | Den här händelsen inträffar när en användare registrerar en enskild metod. *Metoden* kan vara Authenticator app, telefon, e-post, säkerhet frågor, App lösenord, Alternativ telefon och så vidare.| 
| Användare som har granskat säkerhetsinformation | Lyckades | Användaren granskat har säkerhetsinformation. | Den här händelsen inträffar när en användare väljer **ser bra ut** på sidan Granska säkerhetsinformation.|
| Användare som har granskat säkerhetsinformation | Fel | Det gick inte att granska säkerhetsinformation användare. | Den här händelsen inträffar när en användare väljer **ser bra ut** på säkerhetsinformationen granskar du sidan men något inte fungerar på serverdelen.|
| Användaren tog bort säkerhetsinformation | Lyckades | Användaren tog bort *metoden*. | Den här händelsen inträffar när användaren tar bort en enskild metod. *Metoden* kan vara Authenticator app, telefon, e-post, säkerhet frågor, App lösenord, Alternativ telefon och så vidare.|
| Användaren tog bort säkerhetsinformation | Fel | Det gick inte att ta bort användaren *metoden*. | Den här händelsen inträffar när en användare försöker att ta bort en metod men försöket misslyckas av någon anledning. *Metoden* kan vara Authenticator app, telefon, e-post, säkerhet frågor, App lösenord, Alternativ telefon och så vidare.|
| Användare har ändrats standard säkerhetsinformation | Lyckades | Användare ändrade standard säkerhetsinformationen för *metoden*. | Den här händelsen inträffar när en användare ändrar standardmetoden. *Metoden* kan vara en kod från min authenticator-appen eller token, anrop + X XXXXXXXXXX, Text i appmeddelande Authenticator en kod till + X XXXXXXXXX och så vidare.|
| Användare har ändrats standard säkerhetsinformation | Fel | Det gick inte att ändra standard säkerhetsinformationen för användaren *metoden*. | Den här händelsen inträffar när en användare försöker ändra standardmetoden men försöket misslyckas av någon anledning. *Metoden* kan vara en kod från min authenticator-appen eller token, anrop + X XXXXXXXXXX, Text i appmeddelande Authenticator en kod till + X XXXXXXXXX och så vidare.|

## <a name="troubleshooting-interrupt-mode"></a>Felsökningsläge avbrott

| Symtom | Felsökningsanvisningar |
| --- | --- |
| Jag ser inte de metoder som jag förväntar dig. | 1. Kontrollera om användaren har en Azure AD-administratörsroll. Om Ja, visa SSPR admin princip skillnaderna. <br> 2. Avgör om användaren avbryts på grund av Multi-Factor Authentication registrering tvingande eller tvingande för SSPR-registrering. Se den [flödesschema](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) under ”kombineras registrering lägen” att avgöra vilka metoder som ska visas. <br> 3. Avgör hur nyligen Multi-Factor Authentication eller SSPR-principen har ändrats. Om ändringen var nyligen kan det ta lite tid för den uppdaterade policyn att spridas.|

## <a name="troubleshooting-manage-mode"></a>Felsökning av hantera läge

| Symtom | Felsökningsanvisningar |
| --- | --- |
| Jag har inte möjlighet att lägga till en viss metod. | 1. Avgör om metoden har aktiverats för multi-Factor Authentication eller för SSPR. <br> 2. Om metoden är aktiverad, spara principerna igen och vänta 1 till 2 timmar innan du testar igen. <br> 3. Om metoden är aktiverad, kontrollerar du att användaren inte har ställt in det maximala antalet den metod som de har tillåtelse att ställa in.|

## <a name="disable-combined-registration"></a>Inaktivera kombinerade registrering

När en användare registrerar ett telefonnummer och/eller mobila appar i den nya kombineras upplevelse, vår tjänst stämplar flaggor (StrongAuthenticationMethods) för dessa metoder på användaren. Den här funktionen gör att användaren kan utföra Multi-Factor Authentication med dessa metoder när Multifaktorautentisering krävs.

Om en administratör aktiverar förhandsversionen, användare registrera sig via den nya upplevelsen och sedan administratören inaktiverar förhandsversionen, kan användare omedvetet registreras för multi-Factor Authentication också.

Om en användare som har slutfört kombinerade registreringen går till registreringssidan för aktuella lösenord för självbetjäning (SSPR) för återställning på [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), uppmanas användaren att utföra Multifaktorautentisering innan de kan komma åt sidan. Det här steget förväntas från en teknisk synvinkel, men det är nytt för användare som tidigare hade registrerats för SSPR endast. Även om den här extra steg förbättrar användarens säkerhetspositionen genom att tillhandahålla ytterligare en säkerhetsnivå, kanske Administratörer vill återställa sina användare så att de är inte längre kunna utföra Multi-Factor Authentication.  

### <a name="how-to-roll-back-users"></a>Återställa användare

Om du, som administratör vill du återställa en användares inställningar för multi-Factor Authentication, kan du använda PowerShell-skriptet som anges i nästa avsnitt. Skriptet ska ta bort egenskapen StrongAuthenticationMethods för en användares mobilapp och/eller telefonnummer. Om du kör det här skriptet för dina användare kan måste de registrera för Multifaktorautentisering om det behövs. Vi rekommenderar testning återställning med en eller två användare innan du återställer alla berörda användare.

Stegen nedan hjälper dig att återställa en användare eller grupp av användare.

#### <a name="prerequisites"></a>Förutsättningar

1. Installera rätt Azure AD PowerShell-moduler. Kör dessa kommandon för att installera modulerna i ett PowerShell-fönster:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Spara listan över berörda användarens objekt-ID till datorn som en textfil med ett ID per rad. Anteckna platsen för filen.
1. Spara följande skript på datorn och Anteckna platsen för skriptet:

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

Kör följande kommando, att tillhandahålla skript och användaren sökvägar i ett PowerShell-fönster. Ange autentiseringsuppgifterna för global administratör när du tillfrågas. Skriptets utdata är resultatet av varje åtgärd för uppdatering av användaren.

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>Inaktivera förhandsgranskningsupplevelse

Om du vill inaktivera förhandsgranskning upplevelse för användarna, gör du följande:

1. Logga in på Azure-portalen som global administratör eller Användaradministratör.
2. Gå till **Azure Active Directory** > **användarinställningar** > **hantera inställningar för åtkomst till panelen förhandsversionsfunktioner**.
3. Under **användare kan använda förhandsversionsfunktioner för att registrera och hantera säkerhetsinformation**, inställd väljaren **ingen**, och välj sedan **spara**.

Användarna uppmanas inte längre att registrera med hjälp av förhandsversionen-upplevelse.

## <a name="next-steps"></a>Nästa steg

* [Mer information om den offentliga förhandsversionen av kombinerade registrering för lösenordsåterställning via självbetjäning och Azure Multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
