---
title: Felsöka kombinerad registrering - Azure Active Directory
description: Felsöka kombinerad registrering av Azure AD Multi Factor-autentisering och självbetjäningslösenordsåterställning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c840df2c53554519f62a3d1d7a7d8b305187ffb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450946"
---
# <a name="troubleshooting-combined-security-information-registration"></a>Felsöka kombinerad registrering av säkerhetsinformation

Informationen i den här artikeln är avsedd att vägleda administratörer som felsöker problem som rapporterats av användare av den kombinerade registreringsupplevelsen.

## <a name="audit-logs"></a>Granskningsloggar

De händelser som loggas för kombinerad registrering finns i kategorin Autentiseringsmetoder i Azure AD-granskningsloggarna.

![Azure AD-granskningsloggar gränssnitt som visar registreringshändelser](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

I följande tabell visas alla granskningshändelser som genereras av kombinerad registrering:

| Aktivitet | Status | Orsak | Beskrivning |
| --- | --- | --- | --- |
| Användaren registrerade all nödvändig säkerhetsinformation | Lyckades | Användaren registrerade all nödvändig säkerhetsinformation. | Den här händelsen inträffar när en användare har slutfört registreringen.|
| Användaren registrerade all nödvändig säkerhetsinformation | Fel | Registrering av säkerhetsinformation avbröts. | Den här händelsen inträffar när en användare avbryter registreringen från avbrottsläge.|
| Användarregistrerad säkerhetsinformation | Lyckades | Användarregistrerad *metod*. | Den här händelsen inträffar när en användare registrerar en enskild metod. *Metod* kan vara Authenticator app, Telefon, E-post, Säkerhetsfrågor, App lösenord, Alternativ telefon, och så vidare.| 
| Användaröversikterad säkerhetsinformation | Lyckades | Användaren har granskat säkerhetsinformationen. | Den här händelsen inträffar när en användare väljer **Ser bra ut** på sidan för granskning av säkerhetsinformation.|
| Användaröversikterad säkerhetsinformation | Fel | Användaren kunde inte granska säkerhetsinformation. | Den här händelsen inträffar när en användare väljer **Ser bra ut** på sidan säkerhetsinformationsgranskning, men något misslyckas på backend.|
| Säkerhetsinformation om användaruttaget | Lyckades | *Användartagen metod*. | Den här händelsen inträffar när en användare tar bort en enskild metod. *Metod* kan vara Authenticator app, Telefon, E-post, Säkerhetsfrågor, App lösenord, Alternativ telefon, och så vidare.|
| Säkerhetsinformation om användaruttaget | Fel | Det gick inte att ta bort *metoden*. | Den här händelsen inträffar när en användare försöker ta bort en metod, men försöket misslyckas av någon anledning. *Metod* kan vara Authenticator app, Telefon, E-post, Säkerhetsfrågor, App lösenord, Alternativ telefon, och så vidare.|
| Användaren har ändrat standardsäkerhetsinformation | Lyckades | Användaren har ändrat standardsäkerhetsinformationen för *metoden*. | Den här händelsen inträffar när en användare ändrar standardmetoden. *Metod* kan vara Authenticator app anmälan, En kod från min autentiserare app eller token, Ring + X XXXXXXXXXX, Text en kod till +X XXXXXXXXX, och så vidare.|
| Användaren har ändrat standardsäkerhetsinformation | Fel | Det gick inte att ändra standardsäkerhetsinformationen för *metoden*. | Den här händelsen inträffar när en användare försöker ändra standardmetoden, men försöket misslyckas av någon anledning. *Metod* kan vara Authenticator app anmälan, En kod från min autentiserare app eller token, Ring + X XXXXXXXXXX, Text en kod till +X XXXXXXXXX, och så vidare.|

## <a name="troubleshooting-interrupt-mode"></a>Felsöka avbrottsläge

| Symptom | Felsökningsanvisningar |
| --- | --- |
| Jag ser inte de metoder jag förväntade mig att se. | 1. Kontrollera om användaren har en Azure AD-administratörsroll. Om ja, visa sspr-administratörsprincipskillnaderna. <br> 2. Avgöra om användaren avbryts på grund av multifaktorautentisering registrering verkställighet eller SSPR registrering verkställighet. Se [flödesschemat](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) under "Kombinerade registreringslägen" för att avgöra vilka metoder som ska visas. <br> 3. Bestäm hur nyligen multifaktorautentisering eller SSPR-principen har ändrats. Om ändringen senaste, kan det ta lite tid för den uppdaterade principen att sprida.|

## <a name="troubleshooting-manage-mode"></a>Felsöka hanteringsläge

| Symptom | Felsökningsanvisningar |
| --- | --- |
| Jag har inte möjlighet att lägga till en viss metod. | 1. Bestäm om metoden är aktiverad för multifaktorautentisering eller för SSPR. <br> 2. Om metoden är aktiverad sparar du principerna igen och väntar 1-2 timmar innan du testar igen. <br> 3. Om metoden är aktiverad, se till att användaren inte redan har ställt in det maximala antalet av den metoden som de tillåts att ställa in.|

## <a name="disable-combined-registration"></a>Inaktivera kombinerad registrering

När en användare registrerar ett telefonnummer och/eller en mobilapp i den nya kombinerade upplevelsen stämplar vår tjänst en uppsättning flaggor (StrongAuthenticationMethods) för dessa metoder på den användaren. Med den här funktionen kan användaren utföra multifaktorautentisering med dessa metoder när multifaktorautentisering krävs.

Om en administratör aktiverar förhandsversionen registrerar sig användarna via den nya upplevelsen och sedan inaktiverar administratören förhandsgranskningen kan användarna omedvetet vara registrerade för multifaktorautentisering också.

Om en användare som har slutfört kombinerad registrering går till den aktuella [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)registreringssidan för självbetjäningslösenordsåterställning (SSPR) på uppmanas användaren att utföra multifaktorautentisering innan de kan komma åt sidan. Det här steget förväntas från en teknisk synvinkel, men det är nytt för användare som tidigare registrerats endast för SSPR. Även om det här extra steget förbättrar användarens säkerhetsposition genom att tillhandahålla en annan säkerhetsnivå, kanske administratörer vill återställa sina användare så att de inte längre kan utföra multifaktorautentisering.  

### <a name="how-to-roll-back-users"></a>Så här återställer du användare

Om du som administratör vill återställa en användares multifaktorautentiseringsinställningar kan du använda PowerShell-skriptet som finns i nästa avsnitt. Skriptet rensar egenskapen StrongAuthenticationMethods för en användares mobilapp och/eller telefonnummer. Om du kör det här skriptet för användarna måste de registrera om för multifaktorautentisering om de behöver det. Vi rekommenderar att du testar återställning med en eller två användare innan du återställer alla berörda användare.

De steg som följer hjälper dig att återställa en användare eller grupp av användare.

#### <a name="prerequisites"></a>Krav

1. Installera lämpliga Azure AD PowerShell-moduler. I ett PowerShell-fönster kör du dessa kommandon för att installera modulerna:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Spara listan över berörda användarobjekt-ID:er på datorn som en textfil med ett ID per rad. Anteckna platsen för filen.
1. Spara följande skript på datorn och anteckna platsen för skriptet:

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

#### <a name="rollback"></a>Rollback

I ett PowerShell-fönster kör du följande kommando och tillhandahåller skript- och användarfilplatser. Ange globala administratörsautentiseringsuppgifter när du uppmanas att göra det. Skriptet kommer att mata ut resultatet av varje användaruppdateringsåtgärd.

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>Inaktivera den uppdaterade upplevelsen

Så här inaktiverar du den uppdaterade upplevelsen för användarna:

1. Logga in på Azure-portalen som användaradministratör.
2. Gå till Azure Active > **Directory-användarinställningar** > **Hantera inställningar för förhandsgranskningsfunktioner på åtkomstpanelen**. **Azure Active Directory**
3. Under **Användare kan använda förhandsgranskningsfunktioner för att registrera och hantera säkerhetsinformation**ställer du in väljaren på **Ingen**och väljer sedan **Spara**.

Användarna uppmanas inte längre att registrera sig med hjälp av den uppdaterade upplevelsen.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om kombinerad registrering för återställning av lösenord för självbetjäning och Azure Multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
