---
title: "Azure AD Connect: Sömlös Single Sign-On - vanliga frågor och svar | Microsoft Docs"
description: "Svar på vanliga frågor och svar om Azure Active Directory sömlös enkel inloggning."
services: active-directory
keywords: "Vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 8eb575d6647b123119ceff9452ee8fc4a489f1ca
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory sömlös enkel inloggning: vanliga frågor och svar

Vi adressera vanliga frågor och svar om Azure Active Directory sömlös enkel inloggning (sömlös SSO) i den här artikeln. Kontrollera för nytt innehåll.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Vilka metoder för inloggning fungerar sömlös SSO med?

Sömlös SSO kan kombineras med antingen den [synkronisering av lösenords-hash-](active-directory-aadconnectsync-implement-password-synchronization.md) eller [direkt autentisering](active-directory-aadconnect-pass-through-authentication.md) inloggning metoder. Men den här funktionen kan inte användas med Active Directory Federation Services (ADFS).

## <a name="is-seamless-sso-a-free-feature"></a>Är sömlös SSO en kostnadsfri funktion?

Sömlös SSO är en kostnadsfri funktion och du behöver inte några betald utgåvor av Azure AD för att använda den. Den återstående ledigt när funktionen når allmän tillgänglighet.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Vilka program utnyttja `domain_hint` eller `login_hint` parametern möjligheterna för sömlös SSO?

Vi håller på att sammanställa lista över program som skickar dessa parametrar och de som inte. Om du har program som är intresserade berätta för oss kommentarer.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Stöder sömlös SSO `Alternate ID` som användarnamn, i stället för `userPrincipalName`?

Ja. Sömlös SSO stöder `Alternate ID` som användarnamnet som konfigurerades i Azure AD Connect enligt [här](active-directory-aadconnect-get-started-custom.md). Inte alla Office 365-program stöder `Alternate ID`. Finns det specifika programmet dokumentationen för support-instruktionen.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Jag vill registrera Windows 10-enheter med Azure AD utan att använda AD FS. Kan jag använda sömlös SSO i stället?

Ja, det här scenariot måste version 2.1 eller senare av den [till arbetsplatsen klienten](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Hur kan jag få över krypteringsnyckel Kerberos för den `AZUREADSSOACC` datorkontot?

Det är viktigt att ofta slås över krypteringsnyckel Kerberos för den `AZUREADSSOACC` datorkontot (som representerar Azure AD) skapas i din lokala AD-skog.

>[!IMPORTANT]
>Vi rekommenderar starkt att du distribuerar via Kerberos krypteringsnyckel minst var 30 dagar.

Följ dessa steg på den lokala servern där du kör Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Steg 1. Hämta lista över AD-skogar där sömlös SSO har aktiverats

1. Först ladda ned och installera den [Microsoft Online Services-inloggningsassistent](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Hämta och installera den [64-bitars Azure Active Directory-modulen för Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navigera till den `%programfiles%\Microsoft Azure Active Directory Connect` mapp.
4. Importera sömlös SSO-PowerShell-modulen med det här kommandot: `Import-Module .\AzureADSSO.psd1`.
5. Kör PowerShell som administratör. I PowerShell anropa `New-AzureADSSOAuthenticationContext`. Det här kommandot bör du få ett popup-fönster att ange autentiseringsuppgifter för din klient Global administratör.
6. Anropa `Get-AzureADSSOStatus`. Det här kommandot innehåller en lista över AD-skogar (titt på listan ”domäner”) på som den här funktionen har aktiverats.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Steg 2. Uppdatera Kerberos krypteringsnyckel på varje AD-skog som har angetts den in på

1. Anropa `$creds = Get-Credential`. När du uppmanas, anger du inloggningsuppgifterna för domänadministratören för den avsedda AD-skogen.
2. Anropa `Update-AzureADSSOForest -OnPremCredentials $creds`. Det här kommandot uppdaterar dekrypteringsnyckel Kerberos för den `AZUREADSSOACC` datorkonto i den här specifika AD-skogen och uppdateras i Azure AD.
3. Upprepa föregående steg för varje AD-skog som du har ställt in funktionen på.

>[!IMPORTANT]
>Se till att du _inte_ kör den `Update-AzureADSSOForest` kommandot mer än en gång. Annars slutar funktionen fungera tills användarnas Kerberos-biljetter upphör och återutfärdas av din lokala Active Directory.

## <a name="how-can-i-disable-seamless-sso"></a>Hur kan jag inaktivera sömlös SSO?

Sömlös SSO kan inaktiveras med Azure AD Connect.

Kör Azure AD Connect, Välj ”Ändra användare inloggningssidan” och klicka på ”Nästa”. Sedan avmarkera alternativet ”Aktivera enkel inloggning på”. Fortsätt genom guiden. När guiden har slutförts, är sömlös SSO inaktiverad på din klient.

Dock visas ett meddelande på skärmen som lyder som följer:

”Enkel inloggning är aktiverat, men det finns ytterligare manuella steg för att utföra för att rensa. Lär dig mer ”

Om du vill slutföra processen gör du följande manuellt på den lokala servern där du kör Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Steg 1. Hämta lista över AD-skogar där sömlös SSO har aktiverats

1. Först ladda ned och installera den [Microsoft Online Services-inloggningsassistent](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Hämta och installera den [64-bitars Azure Active Directory-modulen för Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navigera till den `%programfiles%\Microsoft Azure Active Directory Connect` mapp.
4. Importera sömlös SSO-PowerShell-modulen med det här kommandot: `Import-Module .\AzureADSSO.psd1`.
5. Kör PowerShell som administratör. I PowerShell anropa `New-AzureADSSOAuthenticationContext`. Det här kommandot bör du få ett popup-fönster att ange autentiseringsuppgifter för din klient Global administratör.
6. Anropa `Get-AzureADSSOStatus`. Det här kommandot innehåller en lista över AD-skogar (titt på listan ”domäner”) på som den här funktionen har aktiverats.

### <a name="step-2-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Steg 2. Ta manuellt bort de `AZUREADSSOACCT` datorkontot från varje AD-skog i listan.

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart** ](active-directory-aadconnect-sso-quick-start.md) – få och kör Azure AD sömlös SSO.
- [**Tekniska ingående** ](active-directory-aadconnect-sso-how-it-works.md) -förstå hur funktionen fungerar.
- [**Felsöka** ](active-directory-aadconnect-troubleshoot-sso.md) – Lär dig att lösa vanliga problem med funktionen.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för arkivering av nya funktioner som efterfrågas.
