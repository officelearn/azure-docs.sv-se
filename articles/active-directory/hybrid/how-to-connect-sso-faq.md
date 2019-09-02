---
title: 'Azure AD Connect: Vanliga frågor och svar med enkel inloggning | Microsoft Docs'
description: Svar på vanliga frågor om Azure Active Directory sömlös enkel inloggning.
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
ms.openlocfilehash: 709fb3be37850be37d6378652921ce26f4ff15fe
ms.sourcegitcommit: dcea3c1ab715a79ebecd913885fbf9bbee61606a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2019
ms.locfileid: "60242219"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory sömlös enkel inloggning: Vanliga frågor och svar

I den här artikeln tar vi itu med vanliga frågor om Azure Active Directory sömlös enkel inloggning (sömlös SSO). Fortsätt att söka efter nytt innehåll.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Vilka inloggnings metoder fungerar smidigt med enkel inloggning?

Sömlös SSO kan kombineras med antingen [lösen ordets hash-synkronisering](how-to-connect-password-hash-synchronization.md) eller inloggnings metoder för [direktautentisering](how-to-connect-pta.md) . Den här funktionen kan dock inte användas med Active Directory Federation Services (AD FS) (ADFS).

## <a name="is-seamless-sso-a-free-feature"></a>Är sömlös enkel inloggning en kostnads fri funktion?

Sömlös SSO är en kostnads fri funktion och du behöver inte några betalda versioner av Azure AD för att använda den.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpswwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Är sömlös enkel inloggning tillgänglig i [Microsoft Azure Tyskland molnet](https://www.microsoft.de/cloud-deutschland) och [Microsoft Azure Government molnet](https://azure.microsoft.com/features/gov/)?

Nej. Sömlös SSO är bara tillgängligt i den globala instansen av Azure AD.

## <a name="what-applications-take-advantage-of-domain_hint-or-login_hint-parameter-capability-of-seamless-sso"></a>Vilka program `domain_hint` utnyttjar eller `login_hint` parameter kapaciteten för sömlös enkel inloggning?

Nedan visas en icke-fullständig lista över program som kan skicka dessa parametrar till Azure AD, och därför ger användarna en tyst inloggnings upplevelse med sömlös enkel inloggning (dvs. behöver användarna inte ange sina användar namn eller lösen ord):

| Programnamn | Programmets URL som ska användas |
| -- | -- |
| Åtkomstpanelen | https:\//myapps.Microsoft.com/contoso.com |
| Outlook på webben | https:\//outlook.office365.com/contoso.com |
| Office 365 portaler | https:\//Portal.Office.com? domain_hint = contoso. com, https:\//www.Office.com? domain_hint = contoso. com |

Dessutom får användarna en tyst inloggnings upplevelse om ett program skickar inloggnings förfrågningar till Azure Ads slut punkter som har kon figurer ATS som klienter, dvs. https:\//login.microsoftonline.com/contoso.com/<.. > eller https:\//login.microsoftonline.com/<tenant_ID>/<.. > – i stället för Azure Ads vanliga slut punkt – det vill säga\/https:/login.microsoftonline.com/common/<... >. Nedan visas en icke-fullständig lista över program som utför dessa typer av inloggnings förfrågningar.

| Programnamn | Programmets URL som ska användas |
| -- | -- |
| sharepoint online | https:\//contoso.SharePoint.com |
| Azure Portal | https:\//Portal.Azure.com/contoso.com |

I tabellerna ovan ersätter du "contoso.com" med ditt domän namn för att komma till rätt program-URL: er för din klient.

Om du vill att andra program använder vår tysta inloggnings upplevelse kan du berätta i feedback-avsnittet.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Har sömlös SSO- `Alternate ID` stöd som användar namn, i `userPrincipalName`stället för?

Ja. Sömlös SSO stöder `Alternate ID` som användar namn när det konfigureras i Azure AD Connect som visas [här](how-to-connect-install-custom.md). Inte alla stöd `Alternate ID`för Office 365-program. Läs dokumentationen för det specifika programmet för support policyn.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>Vad är skillnaden mellan enkel inloggnings upplevelsen från [Azure AD Join](../active-directory-azureadjoin-overview.md) och sömlös enkel inloggning?

[Azure AD Join](../active-directory-azureadjoin-overview.md) ger enkel inloggning till användare om deras enheter har registrerats med Azure AD. Dessa enheter behöver inte nödvändigt vis vara domänanslutna. SSO tillhandahålls med *primära uppdateringstoken* eller *PRTs*, och inte Kerberos. Användar upplevelsen är mest optimal för Windows 10-enheter. SSO sker automatiskt i Microsoft Edge-webbläsaren. Det fungerar också på Chrome med att använda ett webb läsar tillägg.

Du kan använda både Azure AD Join och sömlös SSO på din klient. Dessa två funktioner är kompletterande. Om båda funktionerna är aktiverade prioriteras SSO från Azure AD Join framför sömlös SSO.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Jag vill registrera icke-Windows 10-enheter med Azure AD utan att använda AD FS. Kan jag använda sömlös SSO i stället?

Ja, det här scenariot kräver version 2,1 eller senare av [arbets platsen – Anslut till klienten](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Hur kan jag återställa Kerberos-dekrypterings nyckeln för `AZUREADSSOACC` dator kontot?

Det är viktigt att ofta rulla över Kerberos-dekrypterings nyckeln för `AZUREADSSOACC` dator kontot (som representerar Azure AD) som skapats i din lokala AD-skog.

>[!IMPORTANT]
>Vi rekommenderar starkt att du går igenom Kerberos-dekrypterings nyckeln minst var 30: e dag.

Följ de här stegen på den lokala server där du kör Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Steg 1. Hämta en lista över AD-skogar där sömlös enkel inloggning har Aktiver ATS

1. Börja med att ladda ned och installera [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Gå till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importera den sömlös SSO PowerShell-modulen med hjälp av `Import-Module .\AzureADSSO.psd1`det här kommandot:.
4. Kör PowerShell som administratör. I PowerShell anropar `New-AzureADSSOAuthenticationContext`du. Det här kommandot ska ge dig en popup-meny för att ange klient organisationens autentiseringsuppgifter för global administratör.
5. Anropa `Get-AzureADSSOStatus | ConvertFrom-Json`. Med det här kommandot får du en lista över AD-skogar (se listan "domäner") där funktionen har Aktiver ATS.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Steg 2. Uppdatera Kerberos-dekrypterings nyckeln på varje AD-skog som den har ställts in på

1. Anropa `$creds = Get-Credential`. När du uppmanas till det anger du autentiseringsuppgifter för domän administratören för den avsedda AD-skogen.

   > [!NOTE]
   > Vi använder domän administratörens användar namn, som anges i formatet UPN (User Principal Names) (johndoe@contoso.comUPN) eller domän kvalificerat Sam-Account name (contoso\johndoe eller contoso. com\johndoe) för att hitta den avsedda AD-skogen. Om du använder domän kvalificerat SAM-kontonamn använder vi domän delen av användar namnet för att hitta domänkontrollanten [i domän administratören med hjälp av DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Om du använder UPN i stället, översätter vi [det till ett domän kvalificerat SAM-kontonamn](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) innan du hittar lämplig domänkontrollant.

2. Anropa `Update-AzureADSSOForest -OnPremCredentials $creds`. Det här kommandot uppdaterar Kerberos-dekrypterings nyckeln för `AZUREADSSOACC` dator kontot i den här särskilda AD-skogen och uppdaterar det i Azure AD.
3. Upprepa föregående steg för varje AD-skog som du har konfigurerat funktionen på.

>[!IMPORTANT]
>Se till att du _inte_ kör `Update-AzureADSSOForest` kommandot mer än en gång. Annars slutar funktionen att fungera fram till den tid som dina användares Kerberos-biljetter upphör att gälla och återutfärdas av din lokala Active Directory.

## <a name="how-can-i-disable-seamless-sso"></a>Hur kan jag inaktivera sömlös enkel inloggning?

### <a name="step-1-disable-the-feature-on-your-tenant"></a>Steg 1. Inaktivera funktionen på din klient

#### <a name="option-a-disable-using-azure-ad-connect"></a>Alternativ A: Inaktivera med Azure AD Connect

1. Kör Azure AD Connect, Välj **inloggnings sidan Ändra användare** och klicka på **Nästa**.
2. Avmarkera alternativet **aktivera enkel inloggning** . Fortsätt med guiden.

När du har slutfört guiden kommer sömlös SSO att inaktive ras på din klient. Du kommer dock att se ett meddelande på skärmen som läser på följande sätt:

"Enkel inloggning är nu inaktive rad, men det finns ytterligare manuella steg att utföra för att slutföra rensningen. Läs mer "

Slutför rensnings processen genom att följa steg 2 och 3 på den lokala server där du kör Azure AD Connect.

#### <a name="option-b-disable-using-powershell"></a>Alternativ B: Inaktivera med PowerShell

Kör följande steg på den lokala server där du kör Azure AD Connect:

1. Börja med att ladda ned och installera [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Gå till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importera den sömlös SSO PowerShell-modulen med hjälp av `Import-Module .\AzureADSSO.psd1`det här kommandot:.
4. Kör PowerShell som administratör. I PowerShell anropar `New-AzureADSSOAuthenticationContext`du. Det här kommandot ska ge dig en popup-meny för att ange klient organisationens autentiseringsuppgifter för global administratör.
5. Anropa `Enable-AzureADSSO -Enable $false`.

>[!IMPORTANT]
>Att inaktivera sömlös SSO med PowerShell ändrar inte tillstånd i Azure AD Connect. Sömlös SSO visas som aktive rad på inloggnings sidan för **ändrings användare** .

### <a name="step-2-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Steg 2. Hämta en lista över AD-skogar där sömlös enkel inloggning har Aktiver ATS

Följ uppgifterna 1 till 4 nedan om du har inaktiverat sömlös SSO med Azure AD Connect. Om du har inaktiverat sömlös SSO med PowerShell i stället, gå vidare till aktivitet 5 nedan.

1. Börja med att ladda ned och installera [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Gå till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importera den sömlös SSO PowerShell-modulen med hjälp av `Import-Module .\AzureADSSO.psd1`det här kommandot:.
4. Kör PowerShell som administratör. I PowerShell anropar `New-AzureADSSOAuthenticationContext`du. Det här kommandot ska ge dig en popup-meny för att ange klient organisationens autentiseringsuppgifter för global administratör.
5. Anropa `Get-AzureADSSOStatus | ConvertFrom-Json`. Med det här kommandot får du en lista över AD-skogar (se listan "domäner") där funktionen har Aktiver ATS.

### <a name="step-3-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Steg 3. Ta manuellt bort `AZUREADSSOACCT` dator kontot från varje AD-skog som visas i listan.

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart**](how-to-connect-sso-quick-start.md) – kom igång med Azure AD sömlös SSO.
- [**Teknisk djupgående**](how-to-connect-sso-how-it-works.md) – förstå hur den här funktionen fungerar.
- [**Felsök**](tshoot-connect-sso.md) – lär dig hur du löser vanliga problem med funktionen.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för att arkivera nya funktions begär Anden.
