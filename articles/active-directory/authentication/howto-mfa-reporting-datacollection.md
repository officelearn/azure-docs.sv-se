---
title: Datainsamling för Azure Multi-Factor Authentication-användare
description: Vilken information som används för att autentisera användare av Azure Multi-Factor Authentication?
services: multi-factor-authentication
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.service: multi-factor-authentication
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2018
ms.openlocfilehash: 312e8a0d8281d0f951285461a0b64c0aa3e6a498
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159625"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Datainsamling för Azure Multi-Factor Authentication-användare

Det här dokumentet beskriver hur du söker efter användarinformation som samlas in av Azure Multi-Factor Authentication-servern (MFA-Server) och Azure MFA (molnbaserade) om du vill ta bort den.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Information som samlas in

MFA-servern, NPS-tillägget och Windows Server 2016 Azure MFA AD FS-adaptern samla in och lagra följande information i 90 dagar.

Autentiseringsförsök (används för rapportering och felsökning):

- Tidsstämpel
- Användarnamn
- Förnamn
- Efternamn
- E-postadress
- Användargrupp
- Autentiseringsmetod (telefonsamtal, SMS meddelandet, Mobilapp, OATH-Token)
- Telefonsamtalsläge (Standard, PIN-kod)
- Textmeddelanderiktning (enkelriktad, dubbelriktad)
- Textmeddelandeläge (OTP, OTP + PIN-kod)
- Mobilappläge (Standard, PIN-kod)
- OATH-Tokenläge (Standard, PIN-kod)
- Autentiseringstyp
- Programnamn
- Primär anrop landskod
- Primär anrop telefonnummer
- Primär anrop tillägg
- Primär anrop autentiserad
- Primär samtalsresultat
- Landskod för säkerhetskopiering anrop
- Telefonnummer för säkerhetskopiering anrop
- Tillägget för säkerhetskopiering anrop
- Säkerhetskopiering anrop autentiserad
- Säkerhetskopiering samtalsresultat
- Övergripande autentiserad
- Totalt resultat
- Resultat
- Autentiserad
- Resultat
- Initierar IP-adress
- Enheter
- Enhetstoken
- Typ av enhet
- Mobila Appversion
- OS-version
- Resultat
- Använda kontroll för meddelandet

Aktiveringar (försök att aktivera ett konto i Microsoft Authenticator-mobilappen):
- Användarnamn
- Kontonamn
- Tidsstämpel
- Få aktivering kod resultat
- Aktivera lyckades
- Aktivera fel
- Aktivering Status resultat
- Enhetsnamn
- Typ av enhet
- Programversion
- OATH-Token som aktiverats

Block (används för att fastställa blockerade tillståndet och att rapportera):

- Blockera tidsstämpel
- Blockera efter användarnamn
- Användarnamn
- Landskod
- Telefonnummer
- Telefonnummer som är formaterade
- Anknytning
- Rensa tillägg
- Blockerad
- Blockeringsorsak
- Tidsstämpel för slutförande
- Orsak för slutförande
- Kontoutelåsning
- Bedrägerivarning
- Bedrägerivarning som inte har blockerats
- Språk

Förbikopplingar (används för rapportering):

- Kringgå tidsstämpel
- Förbikopplingssekunder
- Förbikoppling av användarnamn
- Användarnamn
- Landskod
- Telefonnummer
- Telefonnummer som är formaterade
- Anknytning
- Rensa tillägg
- Förbikopplingsorsak
- Tidsstämpel för slutförande
- Orsak för slutförande
- Förbikoppling

Ändringar (används för att synkronisera användarändringar av MFA-servern eller AAD):

- Ändra tidsstämpel
- Användarnamn
- Ny landskod
- Nytt telefonnummer
- Ny anknytning
- Ny landskod för säkerhetskopiering
- Ny reservtelefonnummer
- Nytt tillägg för säkerhetskopiering
- Ny PIN-kod
- PIN-koden måste ändras
- Gamla Enhetstoken
- Ny enhetstoken

## <a name="gather-data-from-mfa-server"></a>Samla in data från MFA Server

Följande process kan administratörer kan exportera alla data för användare för MFA Server version 8.0 eller senare:

- Logga in på din MFA-servern, navigerar till den **användare** , Välj användaren i fråga och på den **redigera** knappen. Ta skärmdumpar (Alt-PrtScn) för varje flik att ge användaren deras aktuella MFA-inställningar.
- Kör följande kommando ändra sökvägen enligt din installation från kommandoraden för MFA-servern, `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` att skapa en JSON-formaterad fil.
- Administratörer kan också använda Web Service SDK GetUserGdpr igen som ett alternativ för att exportera alla MFA informationen om Molntjänsten som samlats in för en viss användare eller lägga till i en större rapporteringslösning.
- Sök `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` och eventuella säkerhetskopior för ”<username>” (ta med citattecknen i sökningen) att hitta alla instanser av användarposten läggs till eller ändras.
   - Dessa poster kan vara begränsad (men inte har tagits bort) genom att avmarkera **”logga användarändringar”** i MFA Server UX-Gränssnittet, i avsnittet loggning, på fliken loggfiler.
   - Om syslog har konfigurerats och **”logga användarändringar”** har checkats in MFA Server UX-Gränssnittet, i avsnittet loggning, Syslog-fliken, och sedan loggposterna samlas in från syslog i stället.
- Andra förekomster av användarnamnet i MultiFactorAuthSvc.log och andra MFA Server loggfiler som hör till autentisering försök anses drifts- och duplicerande i dessa uppgifter med hjälp av MultiFactorAuthGdpr.exe export eller webbtjänst-SDK GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Ta bort data från MFA Server

Kör följande kommando ändra sökvägen enligt din installation från kommandoraden för MFA-servern, `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` att ta bort alla MFA cloud serviceinformation samlas in för den här användaren.

- Data som ingår i exporten tas bort i realtid, men det kan ta upp till 30 dagar för operativa eller duplicerande data tas bort helt.
- Administratörer kan också använda Web Service SDK DeleteUserGdpr igen som ett alternativ för att ta bort alla MFA informationen om Molntjänsten som samlats in för en viss användare eller lägga till i en större rapporteringslösning.

## <a name="gather-data-from-nps-extension"></a>Samla in data från NPS-tillägget

Använd den [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) och gör en begäran för Export.

- MFA-information ingår i exporten, vilket kan ta timmar eller dagar att slutföra.
- Förekomster av användarnamnet i AzureMfa/AuthN/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh och AzureMfa/AuthZ/AuthZOptCh händelseloggar anses drifts- och duplicerande till informationen i exporten.

## <a name="delete-data-from-nps-extension"></a>Ta bort data från NPS-tillägget

Använd den [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) och gör en begäran för Stäng konto att ta bort alla MFA cloud serviceinformation samlas in för den här användaren.

- Det kan ta upp till 30 dagar innan data tas bort helt.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Samla in data från Windows Server 2016 Azure MFA AD FS-Adapter

Använd den [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) och gör en begäran för Export. 

- MFA-information ingår i exporten, vilket kan ta timmar eller dagar att slutföra.
- Förekomster av användarnamnet i AD FS spårning/Debug-händelseloggarna (om aktiverat) anses drifts- och duplicerande till informationen i exporten.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Ta bort data från Windows Server 2016 Azure MFA AD FS-Adapter

Använd den [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) och gör en begäran för Stäng konto att ta bort alla MFA cloud serviceinformation samlas in för den här användaren.

- Det kan ta upp till 30 dagar innan data tas bort helt.

## <a name="gather-data-for-azure-mfa"></a>Samla in data för Azure MFA

Använd den [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) och gör en begäran för Export.

- MFA-information ingår i exporten, vilket kan ta timmar eller dagar att slutföra.

## <a name="delete-data-for-azure-mfa"></a>Ta bort Data för Azure MFA

Använd den [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) och gör en begäran för Stäng konto att ta bort alla MFA cloud serviceinformation samlas in för den här användaren.

- Det kan ta upp till 30 dagar innan data tas bort helt.

## <a name="next-steps"></a>Nästa steg

[MFA Server rapportering](howto-mfa-reporting.md)
