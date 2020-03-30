---
title: 'Azure AD Connect: Sömlös enkel inloggning – vanliga frågor | Microsoft-dokument'
description: Svar på vanliga frågor om Azure Active Directory Seamless Single Sign-On.
services: active-directory
keywords: vad är Azure AD Connect, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7241c8dfbedb24f95c29ea9e1c3f763218a5668d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025670"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory Sömlös enkel inloggning: Vanliga frågor och svar

I den här artikeln tar vi upp vanliga frågor om Azure Active Directory Seamless Single Sign-On (Seamless SSO). Fortsätt att kontrollera tillbaka för nytt innehåll.

**F: Vilka inloggningsmetoder fungerar Seamless SSO med**

Sömlös SSO kan kombineras med antingen [inloggningsmetoderna Lösenord hash](how-to-connect-password-hash-synchronization.md) eller [direktautentisering.](how-to-connect-pta.md) Den här funktionen kan dock inte användas med Active Directory Federation Services (ADFS).

**F: Är Seamless SSO en gratis funktion?**

Sömlös SSO är en kostnadsfri funktion och du behöver inga betalda utgåvor av Azure AD för att kunna använda den.

**F: Är Sömlös SSO tillgängligt i [Microsoft Azure Germany-molnet](https://www.microsoft.de/cloud-deutschland) och [Microsoft Azure Government-molnet?](https://azure.microsoft.com/features/gov/)**

Nej. Sömlös SSO är endast tillgängligt i den globala instansen av Azure AD.

**F: Vilka program `domain_hint` utnyttjar `login_hint` eller parameterkapaciteten hos Seamless SSO?**

Nedan listas en icke uttömmande lista över program som kan skicka dessa parametrar till Azure AD, och ger därför användarna en tyst inloggningsupplevelse med Seamless SSO (dvs. inget behov för användarna att mata in sina användarnamn eller lösenord):

| Programnamn | Program-URL som ska användas |
| -- | -- |
| Åtkomstpanel | https:\//myapps.microsoft.com/contoso.com |
| Outlook på webben | https:\//outlook.office365.com/contoso.com |
| Office 365-portaler | https:\/https: /portal.office.com?domain_hint=contoso.com,\/https: /www.office.com?domain_hint=contoso.com |

Dessutom får användarna en tyst inloggningsupplevelse om ett program skickar inloggningsbegäranden till Azure AD:s slutpunkter\/som har konfigurerats\/som klienter – det vill login.microsoftonline.com/contoso.com/<..> eller https: /login.microsoftonline.com/<tenant_ID>/<.. > - i stället för Azure AD:s gemensamma slutpunkt\/- det vill > https: /login.microsoftonline.com/common/<...>. Nedan listas en icke uttömmande lista över program som gör dessa typer av inloggningsbegäranden.

| Programnamn | Program-URL som ska användas |
| -- | -- |
| sharepoint online | https:\//contoso.sharepoint.com |
| Azure Portal | https:\//portal.azure.com/contoso.com |

I tabellerna ovan ersätter du "contoso.com" med ditt domännamn för att komma åt rätt programadresser för din klient.

Kontakta oss i feedbackavsnittet om du vill ha andra program som använder vår tysta inloggningsupplevelse.

**F: Har Seamless `Alternate ID` SSO stöd som `userPrincipalName`användarnamn, istället för?**

Ja. Sömlös SSO `Alternate ID` stöder som användarnamn när det konfigureras i Azure AD Connect som visas [här](how-to-connect-install-custom.md). Alla Office 365-program stöder `Alternate ID`inte . Se den specifika programmets dokumentation för supportförklaringen.

**F: Vad är skillnaden mellan den enda inloggningsupplevelsen som tillhandahålls av [Azure AD Join](../active-directory-azureadjoin-overview.md) och Seamless SSO?**

[Azure AD Join](../active-directory-azureadjoin-overview.md) ger SSO till användare om deras enheter är registrerade med Azure AD. Dessa enheter behöver inte nödvändigtvis domänanslutas. SSO tillhandahålls med hjälp av *primära uppdateringstoken* eller *PRT-enheter*och inte Kerberos. Användarupplevelsen är mest optimal på Windows 10-enheter. SSO sker automatiskt i Webbläsaren Microsoft Edge. Det fungerar också på Chrome med hjälp av en webbläsare förlängning.

Du kan använda både Azure AD Join och Seamless SSO på din klientorganisation. Dessa två funktioner kompletterar varandra. Om båda funktionerna är aktiverade har SSO från Azure AD Join företräde framför Sömlös SSO.

**F: Jag vill registrera enheter som inte är Windows 10-enheter med Azure AD, utan att använda AD FS. Kan jag använda Seamless SSO istället?**

Ja, det här scenariot behöver version 2.1 eller senare av [klienten för arbetsplatskoppling.](https://www.microsoft.com/download/details.aspx?id=53554)

**F: Hur kan jag rulla över Kerberos dekrypteringsnyckeln för `AZUREADSSOACC` datorkontot?**

Det är viktigt att ofta rulla över Kerberos-dekrypteringsnyckeln `AZUREADSSOACC` för datorkontot (som representerar Azure AD) som skapats i din lokala AD-skog.

>[!IMPORTANT]
>Vi rekommenderar starkt att du rullar över Kerberos dekrypteringsnyckeln minst var 30:e dag.

Följ dessa steg på den lokala servern där du kör Azure AD Connect:

   **Steg 1. Få lista över AD-skogar där Sömlös SSO har aktiverats**

   1. Hämta och installera [först Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Gå till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importera seamless SSO PowerShell-modulen `Import-Module .\AzureADSSO.psd1`med det här kommandot: .
   4. Kör PowerShell som administratör. Ring `New-AzureADSSOAuthenticationContext`i PowerShell. Det här kommandot bör ge dig en popup för att ange din klients globala administratörsautentiseringsuppgifter.
   5. Ring. `Get-AzureADSSOStatus | ConvertFrom-Json` Det här kommandot ger dig en lista över AD-skogar (titta på listan "Domäner" som den här funktionen har aktiverats på.

   **Steg 2. Uppdatera Kerberos dekrypteringsnyckeln för varje AD-skog som den har konfigurerats på**

   1. Ring. `$creds = Get-Credential` Ange autentiseringsuppgifterna för den avsedda AD-skogen när du uppmanas att göra det.

   > [!NOTE]
   >Användarnamnet för domänadministratörsuppgifter måste anges i namnformatet för SIM-konton (contoso\johndoe eller contoso.com\johndoe). Vi använder domändelen av användarnamnet för att hitta domänkontrollanten för domänadministratören med HJÄLP AV DNS.

   >[!NOTE]
   >Det domänadministratörskonto som används får inte vara medlem i gruppen Skyddade användare. Om så är fallet misslyckas åtgärden.

   2. Ring. `Update-AzureADSSOForest -OnPremCredentials $creds` Det här kommandot uppdaterar Kerberos dekrypteringsnyckeln `AZUREADSSOACC` för datorkontot i den här specifika AD-skogen och uppdaterar den i Azure AD.
   3. Upprepa föregående steg för varje AD-skog som du har konfigurerat funktionen på.

   >[!IMPORTANT]
   >Se till _don't_ att du `Update-AzureADSSOForest` inte kör kommandot mer än en gång. Annars slutar funktionen att fungera tills användarnas Kerberos-biljetter går ut och utfärdas på nytt av din lokala Active Directory.

**F: Hur kan jag inaktivera Sömlös SSO?**

   **Steg 1. Inaktivera funktionen på din klientorganisation**

   **Alternativ A: Inaktivera med Azure AD Connect**
    
   1. Kör Azure AD Connect, välj **Ändra inloggningssida för användare** och klicka på **Nästa**.
   2. Avmarkera alternativet **Aktivera enkel inloggning.** Fortsätt genom guiden.

   När du har slutfört guiden inaktiveras Seamless SSO på din klientorganisation. Du kommer dock att se ett meddelande på skärmen som lyder som följer:

   "Enkel inloggning är nu inaktiverad, men det finns ytterligare manuella steg att utföra för att slutföra saneringen. Läs mer"

   Följ steg 2 och 3 på den lokala servern där du kör Azure AD Connect för att slutföra rensningsprocessen.

   **Alternativ B: Inaktivera med PowerShell**

   Kör följande steg på den lokala servern där du kör Azure AD Connect:

   1. Hämta och installera [först Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Gå till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importera seamless SSO PowerShell-modulen `Import-Module .\AzureADSSO.psd1`med det här kommandot: .
   4. Kör PowerShell som administratör. Ring `New-AzureADSSOAuthenticationContext`i PowerShell. Det här kommandot bör ge dig en popup för att ange din klients globala administratörsautentiseringsuppgifter.
   5. Ring. `Enable-AzureADSSO -Enable $false`

   >[!IMPORTANT]
   >Om du inaktiverar sömlös SSO med PowerShell ändras inte tillståndet i Azure AD Connect. Sömlös SSO visas som aktiverat på inloggningssidan **Ändra användare.**

   **Steg 2. Få lista över AD-skogar där Sömlös SSO har aktiverats**

   Följ uppgifter 1 till 4 nedan om du har inaktiverat Seamless SSO med Azure AD Connect. Om du har inaktiverat Seamless SSO med PowerShell i stället, hoppa vidare till aktivitet 5 nedan.

   1. Hämta och installera [först Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Gå till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importera seamless SSO PowerShell-modulen `Import-Module .\AzureADSSO.psd1`med det här kommandot: .
   4. Kör PowerShell som administratör. Ring `New-AzureADSSOAuthenticationContext`i PowerShell. Det här kommandot bör ge dig en popup för att ange din klients globala administratörsautentiseringsuppgifter.
   5. Ring. `Get-AzureADSSOStatus | ConvertFrom-Json` Det här kommandot ger dig en lista över AD-skogar (titta på listan "Domäner" som den här funktionen har aktiverats på.

   **Steg 3. Ta bort `AZUREADSSOACCT` datorkontot manuellt från varje AD-skog som visas i listan.**

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart**](how-to-connect-sso-quick-start.md) – Kom igång med Azure AD Seamless SSO.
- [**Teknisk djupdykning**](how-to-connect-sso-how-it-works.md) - Förstå hur den här funktionen fungerar.
- [**Felsöka**](tshoot-connect-sso.md) - Lär dig hur du löser vanliga problem med funktionen.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - För att lämna in nya funktionsförfrågningar.
