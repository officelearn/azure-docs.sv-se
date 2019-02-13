---
title: 'Azure AD Connect: Sömlös enkel inloggning – vanliga frågor och svar | Microsoft Docs'
description: Svar på vanliga frågor och svar om Azure Active Directory sömlös enkel inloggning.
services: active-directory
keywords: Vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c15b42572648f875ac4bda2eae5813f0cf6b17ef
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187193"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory sömlös enkel inloggning: Vanliga frågor och svar

I den här artikeln har upp vi vanliga frågor och svar om Azure Active Directory sömlös enkel inloggning (sömlös SSO). Kontrollera tillbaka för nytt innehåll.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Vilka metoder för inloggning fungerar sömlös SSO med?

Sömlös enkel inloggning kan kombineras med antingen den [Lösenordshashsynkronisering](how-to-connect-password-hash-synchronization.md) eller [direktautentisering](how-to-connect-pta.md) inloggning metoder. Men den här funktionen kan inte användas med Active Directory Federation Services (ADFS).

## <a name="is-seamless-sso-a-free-feature"></a>Är en kostnadsfri funktion för sömlös enkel inloggning?

Sömlös SSO är en kostnadsfri funktion och du behöver inte några betald utgåvor av Azure AD för att använda den.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpswwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Finns sömlös enkel inloggning i den [Microsoft Azure Tyskland](https://www.microsoft.de/cloud-deutschland) och [Microsoft Azure Government-molnet](https://azure.microsoft.com/features/gov/)?

Nej. Sömlös enkel inloggning är endast tillgängligt i den globala instansen av Azure AD.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Vilka program utnyttja `domain_hint` eller `login_hint` parametern möjligheterna för sömlös enkel inloggning?

Nedan följer en ofullständig lista över program som kan skicka dessa parametrar till Azure AD, och därför ger användarna en tyst inloggningsupplevelse med sömlös enkel inloggning (t.ex, du behöver inte för användarna att ange sina användarnamn eller lösenord):

| Programnamn | Programmets URL som ska användas |
| -- | -- |
| Åtkomstpanel | https://myapps.microsoft.com/contoso.com |
| Outlook på webben | https://outlook.office365.com/contoso.com |
| Office 365-portaler | https://portal.office.com?domain_hint=contoso.com, https://www.office.com?domain_hint=contoso.com |

Dessutom kan användare får en tyst inloggning om ett program skickar inloggningsförfrågningar till Azure AD: s klientslutpunkter – det vill säga https://login.microsoftonline.com/contoso.com/<..> eller https://login.microsoftonline.com/<tenant_ID>/<..> – i stället för Azure AD: s vanliga slutpunkt - det vill säga https://login.microsoftonline.com/common/<...>. Nedan följer en ofullständig lista över program som gör att dessa typer av inloggningsförfrågningar.

| Programnamn | Programmets URL som ska användas |
| -- | -- |
| sharepoint online | https://contoso.sharepoint.com |
| Azure Portal | https://portal.azure.com/contoso.com |

I tabellerna ovan ersätter du ”contoso.com” med ditt domännamn för att hämta rätt program-URL: er för din klient.

Om du vill att andra program med vår tyst inloggning berätta för oss i avsnittet feedback.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Har stöd för sömlös SSO `Alternate ID` som användarnamn, i stället för `userPrincipalName`?

Ja. Har stöd för sömlös SSO `Alternate ID` som användarnamn när du konfigurerade i Azure AD Connect enligt [här](how-to-connect-install-custom.md). Inte alla Office 365-program som stöder `Alternate ID`. I dokumentationen för det specifika programmet för support-instruktionen.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>Vad är skillnaden mellan enkel inloggning som tillhandahålls av [Azure AD Join](../active-directory-azureadjoin-overview.md) och sömlös enkel inloggning?

[Azure AD Join](../active-directory-azureadjoin-overview.md) tillhandahåller enkel inloggning för användare om deras enheter är registrerade med Azure AD. Dessa enheter behöver inte nödvändigtvis vara anslutna till en domän. Enkel inloggning har angetts med hjälp av *primära uppdateringstoken* eller *PRTs*, och inte Kerberos. Användarupplevelsen är mest optimala på Windows 10-enheter. SSO sker automatiskt i Microsoft Edge-webbläsare. Fungerar på Chrome med hjälp av ett webbläsartillägg.

Du kan använda Azure AD Join och sömlös enkel inloggning på din klient. De här två funktionerna är kompletterande. Om båda funktionerna aktiveras har enkel inloggning från Azure AD Join företräde framför sömlös enkel inloggning.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Jag vill registrera Windows 10-enheter med Azure AD utan att använda AD FS. Kan jag använda sömlös SSO i stället?

Ja, det här scenariot måste version 2.1 eller senare av den [till arbetsplatsen klienten](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Hur kan jag växla dekrypteringsnyckeln Kerberos för den `AZUREADSSOACC` datorkontot?

Det är viktigt att ofta förnyar Kerberos-krypteringsnyckel för den `AZUREADSSOACC` datorkontot (som representerar Azure AD) skapas i din lokala AD-skog.

>[!IMPORTANT]
>Vi rekommenderar starkt att du förnyar Kerberos-krypteringsnyckel minst var 30: e dag.

Följ dessa steg på den lokala servern där du kör Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Steg 1. Hämta lista över AD-skogar där sömlös enkel inloggning har aktiverats

1. Först, hämta och installera [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Gå till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importera sömlös SSO-PowerShell-modulen med hjälp av det här kommandot: `Import-Module .\AzureADSSO.psd1`.
4. Kör PowerShell som administratör. I PowerShell, anropa `New-AzureADSSOAuthenticationContext`. Det här kommandot bör ge dig ett fönster för att ange autentiseringsuppgifterna för Global administratör för din klient.
5. Anropa `Get-AzureADSSOStatus`. Det här kommandot innehåller en lista över AD-skogar (titt på listan ”domäner”) på som den här funktionen har aktiverats.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Steg 2. Uppdatera Kerberos krypteringsnyckel på varje AD-skog som den var konfigurera det på

1. Anropa `$creds = Get-Credential`. När du uppmanas, anger du inloggningsuppgifterna för domänadministratören för den avsedda AD-skogen.

    >[!NOTE]
    >Vi använder den domänadministratör, det tillhandahållna användarnamnet i User Principal namn (UPN) (johndoe@contoso.com)-format eller domän kvalificerade sam-konto (contoso\johndoe eller contoso.com\johndoe) namnformatet, att hitta rätt AD-skogen. Om du använder domän kvalificerade sam-kontonamnet som vi använder domändelen i användarnamnet för att [hitta domänkontrollant av domänadministratören med hjälp av DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Om du använder UPN i stället vi [översätta det till en domän kvalificerade sam-kontonamnet](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) innan du hitta rätt domänkontrollanten.

2. Anropa `Update-AzureADSSOForest -OnPremCredentials $creds`. Det här kommandot uppdaterar dekrypteringsnyckel Kerberos för den `AZUREADSSOACC` datorkonto i den här specifika AD-skogen och uppdaterar den i Azure AD.
3. Upprepa föregående steg för varje AD-skog som du har ställt in funktionen på.

>[!IMPORTANT]
>Se till att du _inte_ kör den `Update-AzureADSSOForest` kommandot mer än en gång. I annat fall slutar funktionen fungera tills dina användares Kerberos-biljetter går ut och igen med din lokala Active Directory.

## <a name="how-can-i-disable-seamless-sso"></a>Hur kan jag inaktivera sömlös enkel inloggning?

### <a name="step-1-disable-the-feature-on-your-tenant"></a>Steg 1. Inaktivera funktionen på din klient

#### <a name="option-a-disable-using-azure-ad-connect"></a>Alternativ A: Inaktivera med Azure AD Connect

1. Kör Azure AD Connect, Välj **ändra användare på inloggningssidan** och klicka på **nästa**.
2. Avmarkera den **aktivera enkel inloggning** alternativet. Fortsätt genom guiden.

När du har slutfört guiden för inaktiveras sömlös SSO på din klient. Dock visas ett meddelande på skärmen som lyder som följer:

”Enkel inloggning har inaktiverats, men det finns ytterligare manuella steg för att utföra för att slutföra rensningen. Läs mer ”

Om du vill slutföra rensningen, följer du steg 2 och 3 på den lokala servern där du kör Azure AD Connect.

#### <a name="option-b-disable-using-powershell"></a>Alternativ B: Inaktivera med hjälp av PowerShell

Kör följande steg på den lokala servern där du kör Azure AD Connect:

1. Först, hämta och installera [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Gå till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importera sömlös SSO-PowerShell-modulen med hjälp av det här kommandot: `Import-Module .\AzureADSSO.psd1`.
4. Kör PowerShell som administratör. I PowerShell, anropa `New-AzureADSSOAuthenticationContext`. Det här kommandot bör ge dig ett fönster för att ange autentiseringsuppgifterna för Global administratör för din klient.
5. Anropa `Enable-AzureADSSO -Enable $false`.

>[!IMPORTANT]
>Inaktivera sömlös SSO ändrar med hjälp av PowerShell inte status i Azure AD Connect. Sömlös SSO ska visas som aktiverat i den **ändra användarinloggning** sidan.

### <a name="step-2-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Steg 2. Hämta lista över AD-skogar där sömlös enkel inloggning har aktiverats

Följ uppgifter 1 till 4 nedan om du har inaktiverat sömlös enkel inloggning med Azure AD Connect. Om du har inaktiverat sömlös SSO med hjälp av PowerShell i stället kan du gå vidare till uppgift 5 nedan.

1. Först, hämta och installera [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Gå till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importera sömlös SSO-PowerShell-modulen med hjälp av det här kommandot: `Import-Module .\AzureADSSO.psd1`.
4. Kör PowerShell som administratör. I PowerShell, anropa `New-AzureADSSOAuthenticationContext`. Det här kommandot bör ge dig ett fönster för att ange autentiseringsuppgifterna för Global administratör för din klient.
5. Anropa `Get-AzureADSSOStatus`. Det här kommandot innehåller en lista över AD-skogar (titt på listan ”domäner”) på som den här funktionen har aktiverats.

### <a name="step-3-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Steg 3. Ta manuellt bort de `AZUREADSSOACCT` datorkontot från varje AD-skog i listan.

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart** ](how-to-connect-sso-quick-start.md) – komma igång och köra sömlös enkel inloggning för den Azure AD.
- [**Teknisk djupdykning** ](how-to-connect-sso-how-it-works.md) -förstår hur den här funktionen fungerar.
- [**Felsöka** ](tshoot-connect-sso.md) – Lär dig att lösa vanliga problem med funktionen.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för att skicka in nya funktionbegäran.
