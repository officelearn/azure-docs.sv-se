---
title: Azure MFA-användardatainsamling - Azure Active Directory
description: Vilken information används för att autentisera användare genom Azure Multi-Factor Authentication?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f3b5af972ad6dd15b7c992d5e264ede97bd1dde
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653638"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Användardatainsamling för Azure Multi Factor-autentisering

I det här dokumentet beskrivs hur du hittar användarinformation som samlas in av Azure Multi-Factor Authentication Server (MFA Server) och Azure MFA (Cloud-based) i händelse av att du vill ta bort den.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Insamlad information

MFA Server, NPS-tillägget och Windows Server 2016 Azure MFA AD FS-kort samlar in och lagrar följande information i 90 dagar.

Autentiseringsförsök (används för rapportering och felsökning):

- Tidsstämpel
- Användarnamn
- Förnamn
- Efternamn
- E-postadress
- Användargrupp
- Autentiseringsmetod (telefonsamtal, sms, mobilapp, OATH-token)
- Telefonsamtalsläge (standard, PIN-kod)
- Textmeddelande riktning (enkelriktad, tvåvägs)
- Textmeddelandeläge (OTP, OTP + PIN)
- Läge för mobilappar (standard, PIN-kod)
- ED-tokenläge (standard, PIN)
- Autentiseringstyp
- Programnamn
- Primär landskod för samtal
- Telefonnummer till primärt samtal
- Primärt samtalstillägg
- Primärt anrop autentiserat
- Primärt samtalsresultat
- Landskod för säkerhetskopiering av samtal
- Telefonnummer för säkerhetskopieringssamtal
- Tillägg för säkerhetskopieringssamtal
- Autentiseringsanrop för säkerhetskopiering
- Resultat av säkerhetskopieringssamtal
- Övergripande autentiserade
- Övergripande resultat
- Resultat
- Autentiserad
- Resultat
- Initiera IP-adress
- Enheter
- Enhetstoken
- Enhetstyp
- Version av mobilapp
- OS-version
- Resultat
- Använd söke för meddelande

Aktiveringar (försök att aktivera ett konto i Mobilappen Microsoft Authenticator):
- Användarnamn
- Kontonamn
- Tidsstämpel
- Hämta aktiveringskodresultat
- Aktivera framgång
- Aktivera fel
- Aktiveringsstatusresultat
- Enhetsnamn
- Enhetstyp
- Appversion
- OATH-token aktiverad

Block (används för att fastställa blockerat tillstånd och för rapportering):

- Tidsstämpel för block
- Blockera efter användarnamn
- Användarnamn
- Landskod
- Telefonnummer
- Formaterat telefonnummer
- Anknytning
- Ren förlängning
- Blockerad
- Blockera orsak
- Tidsstämpel för slutförande
- Orsak till slutförande
- Kontoutelåsning
- Varning för bedrägeri
- Bedrägerivarning har inte blockerats
- Språk

Förbifarter (används för rapportering):

- Tidsstämpel för bypass
- Bypass sekunder
- Bypass efter användarnamn
- Användarnamn
- Landskod
- Telefonnummer
- Formaterat telefonnummer
- Anknytning
- Ren förlängning
- Bypass Orsak
- Tidsstämpel för slutförande
- Orsak till slutförande
- Bypass används

Ändringar (används för att synkronisera användarändringar till MFA Server eller Azure AD):

- Ändra tidsstämpel
- Användarnamn
- Ny landskod
- Nytt telefonnummer
- Nytt tillägg
- Ny landskod för säkerhetskopiering
- Nytt telefonnummer för säkerhetskopiering
- Nytt tillägg för säkerhetskopiering
- Ny PIN-kod
- PIN-ändring krävs
- Gammal enhetstoken
- Ny enhetstoken

## <a name="gather-data-from-mfa-server"></a>Samla in data från MFA Server

För MFA Server version 8.0 eller högre tillåter följande process administratörer att exportera alla data för användare:

- Logga in på MFA-servern, navigera till fliken **Användare,** välj användaren i fråga och klicka på knappen **Redigera.** Ta skärmdumpar (Alt-PrtScn) på varje flik för att ge användaren deras aktuella MFA-inställningar.
- Från kommandoraden på MFA-servern kör du följande kommando `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` som ändrar sökvägen enligt installationen för att skapa en JSON-formaterad fil.
- Administratörer kan också använda webtjänsten SDK GetUserGdpr-åtgärden som ett alternativ för att exportera all MFA-molntjänstinformation som samlas in för en viss användare eller införlivas i en större rapporteringslösning.
- Sök `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` och eventuella\<säkerhetskopior för " användarnamn>" (inkludera citat i sökningen) för att hitta alla instanser av användarposten läggs till eller ändras.
   - Dessa poster kan begränsas (men inte elimineras) genom att avmarkera **"Logga användarändringar"** i MFA Server UX, Loggning avsnitt, Loggfiler fliken.
   - Om syslog är konfigurerad och **"Logga användarändringar"** är markerat i avsnittet MFA Server UX, Loggning, fliken Syslog, kan loggposterna samlas in från syslog i stället.
- Andra förekomster av användarnamnet i MultiFactorAuthSvc.log och andra MFA Server-loggfiler som rör autentiseringsförsök betraktas som drift och duplicerar informationen med MultiFactorAuthGdpr.exe export eller Web Service SDK GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Ta bort data från MFA-server

Från kommandoraden på MFA-servern kör du följande kommando `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` som ändrar sökvägen enligt din installation för att ta bort all MFA-molntjänstinformation som samlats in för den här användaren.

- Data som ingår i exporten tas bort i realtid, men det kan ta upp till 30 dagar innan drift- eller duplicativa data tas bort helt.
- Administratörer kan också använda webtjänsten SDK DeleteUserGdpr-åtgärden som ett alternativ för att ta bort all MFA-molntjänstinformation som samlats in för en viss användare eller införlivas i en större rapporteringslösning.

## <a name="gather-data-from-nps-extension"></a>Samla in data från NPS-tillägg

Använd [Microsofts sekretessportal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) för att göra en begäran om export.

- MFA-information ingår i exporten, vilket kan ta timmar eller dagar att slutföra.
- Förekomster av användarnamnet i azuremfa/AuthN/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh och AzureMfa/AuthZ/AuthZOptCh-händelseloggar betraktas som operativa och duplicerande för den information som tillhandahålls i exporten.

## <a name="delete-data-from-nps-extension"></a>Ta bort data från NPS-tillägg

Använd [Microsofts sekretessportal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) för att göra en begäran om konto nära för att ta bort all MFA-molntjänstinformation som samlats in för den här användaren.

- Det kan ta upp till 30 dagar innan data tas bort helt.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Samla in data från Windows Server 2016 Azure MFA AD FS-kort

Använd [Microsofts sekretessportal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) för att göra en begäran om export. 

- MFA-information ingår i exporten, vilket kan ta timmar eller dagar att slutföra.
- Förekomster av användarnamnet i AD FS-spårnings-/felsökningshändelseloggarna (om aktiverat) betraktas som drift och duplicerar till den information som anges i exporten.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Ta bort data från Windows Server 2016 Azure MFA AD FS-kort

Använd [Microsofts sekretessportal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) för att göra en begäran om konto nära för att ta bort all MFA-molntjänstinformation som samlats in för den här användaren.

- Det kan ta upp till 30 dagar innan data tas bort helt.

## <a name="gather-data-for-azure-mfa"></a>Samla in data för Azure MFA

Använd [Microsofts sekretessportal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) för att göra en begäran om export.

- MFA-information ingår i exporten, vilket kan ta timmar eller dagar att slutföra.

## <a name="delete-data-for-azure-mfa"></a>Ta bort data för Azure MFA

Använd [Microsofts sekretessportal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) för att göra en begäran om konto nära för att ta bort all MFA-molntjänstinformation som samlats in för den här användaren.

- Det kan ta upp till 30 dagar innan data tas bort helt.

## <a name="next-steps"></a>Nästa steg

[MFA-serverrapportering](howto-mfa-reporting.md)
