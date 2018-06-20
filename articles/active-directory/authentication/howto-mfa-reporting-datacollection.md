---
title: Datainsamling för Azure Multi-Factor Authentication-användare
description: Vilken information som används för att autentisera användare av Azure Multi-Factor Authentication?
services: multi-factor-authentication
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.service: multi-factor-authentication
ms.workload: identity
ms.topic: article
ms.date: 05/01/2018
ms.openlocfilehash: 2281a35d1616aa88b0c646fb96cb9f95c3272536
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264376"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Datainsamling för Azure Multi-Factor Authentication-användare

Det här dokumentet förklarar hur du söker efter användarinformation som samlas in av Azure flerfunktionsautentisering (MFA-Server) och Azure MFA (molnbaserad) om du vill ta bort den.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Information som samlas in

MFA-Server, NPS-tillägget och Windows Server 2016 Azure MFA AD FS-adaptern samla in och lagra följande information om 90 dagar.

Autentiseringsförsök (används för rapportering och felsökning):

- Tidsstämpel
- Användarnamn
- Förnamn
- Efternamn
- E-postadress
- Användargrupp
- Autentiseringsmetod (telefonsamtal, SMS meddelande, Mobilapp, OATH-Token)
- Telefonsamtalsläge (Standard, PIN-kod)
- Textmeddelanderiktning (enkelriktad, dubbelriktad)
- Textmeddelandeläge (OTP, OTP + PIN-kod)
- Mobilappläge (Standard, PIN-kod)
- OATH-Tokenläge (Standard, PIN-kod)
- Autentiseringstyp
- Programnamn
- Primär anropet landskod
- Primär anropet telefonnummer
- Primär anropet tillägg
- Primär anropet autentiserad
- Primär anropet resultat
- Säkerhetskopiering anropet landskod
- Säkerhetskopiering anropet telefonnummer
- Säkerhetskopiering anropet tillägg
- Säkerhetskopiering anropet autentiserad
- Säkerhetskopiering anropet resultat
- Övergripande autentiserad
- Totalt resultat
- Resultat
- Autentiserad
- Resultat
- Inledande IP-adress
- Enheter
- Enhetstoken
- Typ av enhet
- Mobila programversion
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
- Statusresultat för aktivering
- Enhetsnamn
- Typ av enhet
- Programversion
- OATH-Token som aktiverats

Block (används för att avgöra blockerade tillståndet och för rapportering):

- Blockera tidsstämpel
- Block av användarnamn
- Användarnamn
- Landskod
- Telefonnummer
- Telefonnummer formaterad
- Anknytning
- Rensa tillägg
- Blockerad
- Blockeringsorsak
- Tidsstämpel för slutförande
- Slutförande orsak
- Kontoutelåsning
- Bedrägerivarning
- Bedrägerivarning inte blockerad
- Språk

Förbikopplingar (används för rapportering):

- Kringgå tidsstämpel
- Förbikopplingssekunder
- Förbikoppling av användarnamn
- Användarnamn
- Landskod
- Telefonnummer
- Telefonnummer formaterad
- Anknytning
- Rensa tillägg
- Förbikopplingsorsak
- Tidsstämpel för slutförande
- Slutförande orsak
- Förbikoppling

Ändringar (används för att synkronisera användarändringar i MFA-servern eller AAD):

- Ändra tidsstämpel
- Användarnamn
- Ny landskod
- Nytt telefonnummer
- Ny anknytning
- Ny Reservlandskod
- Nya reservtelefonnummer
- Ny Reservanknytning
- Ny PIN-kod
- PIN-koden måste ändras
- Gamla Enhetstoken
- Ny enhetstoken

## <a name="gather-data-from-mfa-server"></a>Samla in data från MFA-Server

Följande process kan administratörer exportera alla data för användare för MFA-servern version 8.0 eller senare:

- Logga in på din MFA-servern, navigerar till den **användare** , Välj användaren i fråga, och klicka på den **redigera** knappen. Ta skärmdumpar (Alt-PrtScn) på varje flik att ge användaren deras aktuella MFA-inställningar.
- Från kommandoraden av MFA-Server, kör du följande kommando om du ändrar sökvägen enligt din installation `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` att skapa en JSON-formaterad fil.
- Administratörer kan också använda Web Service SDK GetUserGdpr igen som ett alternativ för att exportera alla MFA informationen om Molntjänsten har samlats in för en viss användare eller lägga till i en större rapporteringslösning.
- Sök `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` och säkerhetskopior för ”<username>” (inkludera citattecken i sökningen) att hitta alla instanser av användarposten läggs till eller ändras.
   - Dessa poster kan vara begränsad (men inte elimineras) genom att avmarkera **”logga användarändringar”** i MFA-servern UX, i avsnittet loggning, fliken loggfiler.
   - Om syslog har konfigurerats och **”logga användarändringar”** checkas in MFA Server UX, i avsnittet loggning, fliken Syslog, och sedan loggposterna kan samlas in från syslog i stället.
- Andra förekomster av användarnamnet i MultiFactorAuthSvc.log och andra MFA-Server loggfiler som rör verifiering försök anses fungerar och duplicerande i dessa uppgifter med hjälp av export MultiFactorAuthGdpr.exe eller webbtjänst-SDK GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Ta bort data från MFA-Server

Från kommandoraden av MFA-Server, kör du följande kommando om du ändrar sökvägen enligt din installation `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` att ta bort alla MFA cloud serviceinformation samlas in för den här användaren.

- Data som tas med i exporten tas bort i realtid, men det kan ta upp till 30 dagar för operativa eller duplicerande data ska tas bort helt.
- Administratörer kan också använda Web Service SDK DeleteUserGdpr igen som ett alternativ för att ta bort alla MFA informationen om Molntjänsten har samlats in för en viss användare eller lägga till i en större rapporteringslösning.

## <a name="gather-data-from-nps-extension"></a>Samla in data från NPS-tillägg

Använd den [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) och gör en begäran för Export.

- Information om MFA ingår i exporten, vilket kan ta flera timmar eller dagar att slutföra.
- Förekomster av användarnamnet i AuthNOptCh-AzureMfa/AuthN, AuthZAdminCh-AzureMfa/AuthZ och AuthZOptCh-AzureMfa/AuthZ händelseloggar anses fungerar och duplicerande till informationen i exporten.

## <a name="delete-data-from-nps-extension"></a>Ta bort data från NPS-tillägg

Använd den [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) och gör en begäran för kontot Stäng om du vill ta bort alla MFA informationen om Molntjänsten samlas in för den här användaren.

- Det kan ta upp till 30 dagar för data som ska tas bort helt.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Samla in data från Windows Server 2016 Azure MFA AD FS-Adapter

Använd den [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) och gör en begäran för Export. 

- Information om MFA ingår i exporten, vilket kan ta flera timmar eller dagar att slutföra.
- Förekomster av användarnamnet i AD FS spårning/Debug-händelseloggar (om aktiverat) betraktas som fungerar och duplicerande till informationen i exporten.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Ta bort data från Windows Server 2016 Azure MFA AD FS-Adapter

Använd den [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) och gör en begäran för kontot Stäng om du vill ta bort alla MFA informationen om Molntjänsten samlas in för den här användaren.

- Det kan ta upp till 30 dagar för data som ska tas bort helt.

## <a name="gather-data-for-azure-mfa"></a>Samla in data för Azure MFA

Använd den [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) och gör en begäran för Export.

- Information om MFA ingår i exporten, vilket kan ta flera timmar eller dagar att slutföra.

## <a name="delete-data-for-azure-mfa"></a>Ta bort Data för Azure MFA

Använd den [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) och gör en begäran för kontot Stäng om du vill ta bort alla MFA informationen om Molntjänsten samlas in för den här användaren.

- Det kan ta upp till 30 dagar för data som ska tas bort helt.

## <a name="next-steps"></a>Nästa steg

[Rapportering av MFA-Server](howto-mfa-reporting.md)
