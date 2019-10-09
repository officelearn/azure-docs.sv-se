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
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7241c8dfbedb24f95c29ea9e1c3f763218a5668d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025670"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory sömlös enkel inloggning: Vanliga frågor och svar

I den här artikeln tar vi itu med vanliga frågor om Azure Active Directory sömlös enkel inloggning (sömlös SSO). Fortsätt att söka efter nytt innehåll.

**F: Vilka inloggnings metoder fungerar sömlöst enkel inloggning med @ no__t-0

Sömlös SSO kan kombineras med antingen [lösen ordets hash-synkronisering](how-to-connect-password-hash-synchronization.md) eller inloggnings metoder för [direktautentisering](how-to-connect-pta.md) . Den här funktionen kan dock inte användas med Active Directory Federation Services (AD FS) (ADFS).

**F: Är sömlös enkel inloggning en kostnads fri funktion?**

Sömlös SSO är en kostnads fri funktion och du behöver inte några betalda versioner av Azure AD för att använda den.

**F: Är sömlös enkel inloggning tillgänglig i [Microsoft Azure Tyskland molnet](https://www.microsoft.de/cloud-deutschland) och [Microsoft Azure Government molnet](https://azure.microsoft.com/features/gov/)?**

Nej. Sömlös SSO är bara tillgängligt i den globala instansen av Azure AD.

**F: Vilka program utnyttjar `domain_hint`-eller `login_hint`-parameter funktioner i sömlös SSO?**

Nedan visas en icke-fullständig lista över program som kan skicka dessa parametrar till Azure AD, och därför ger användarna en tyst inloggnings upplevelse med sömlös enkel inloggning (dvs. behöver användarna inte ange sina användar namn eller lösen ord):

| Programnamn | Programmets URL som ska användas |
| -- | -- |
| Åtkomstpanel | https: \//Mina appar. Microsoft. com/contoso. com |
| Outlook på webben | https:\//outlook.office365.com/contoso.com |
| Office 365 portaler | https: \//Portal. Office. com? domain_hint = contoso. com, https: \//www. Office. com? domain_hint = contoso. com |

Dessutom får användarna en tyst inloggning om ett program skickar inloggnings förfrågningar till Azure ADs slut punkter som har kon figurer ATS som klienter, det vill säga https: \//login. microsoftonline. com/contoso. com/<.. > eller https: \//login. microsoftonline. com/< tenant_ID >/<.. > – i stället för Azure AD: s vanliga slut punkt – det vill säga https: \//login. microsoftonline. com/common/<... >. Nedan visas en icke-fullständig lista över program som utför dessa typer av inloggnings förfrågningar.

| Programnamn | Programmets URL som ska användas |
| -- | -- |
| sharepoint online | https: \//contoso. SharePoint. com |
| Azure Portal | https: \//Portal. Azure. com/contoso. com |

I tabellerna ovan ersätter du "contoso.com" med ditt domän namn för att komma till rätt program-URL: er för din klient.

Om du vill att andra program använder vår tysta inloggnings upplevelse kan du berätta i feedback-avsnittet.

**F: Stöder sömlös SSO `Alternate ID` som användar namn, i stället för `userPrincipalName`?**

Ja. Sömlös SSO stöder `Alternate ID` som användar namn när de konfigureras i Azure AD Connect som visas [här](how-to-connect-install-custom.md). Alla Office 365-program stöder inte `Alternate ID`. Läs dokumentationen för det specifika programmet för support policyn.

**F: Vad är skillnaden mellan enkel inloggning som tillhandahålls av [Azure AD Join](../active-directory-azureadjoin-overview.md) och sömlös SSO?**

[Azure AD Join](../active-directory-azureadjoin-overview.md) ger enkel inloggning till användare om deras enheter har registrerats med Azure AD. Dessa enheter behöver inte nödvändigt vis vara domänanslutna. SSO tillhandahålls med *primära uppdateringstoken* eller *PRTs*, och inte Kerberos. Användar upplevelsen är mest optimal för Windows 10-enheter. SSO sker automatiskt i Microsoft Edge-webbläsaren. Det fungerar också på Chrome med att använda ett webb läsar tillägg.

Du kan använda både Azure AD Join och sömlös SSO på din klient. Dessa två funktioner är kompletterande. Om båda funktionerna är aktiverade prioriteras SSO från Azure AD Join framför sömlös SSO.

**F: Jag vill registrera icke-Windows 10-enheter med Azure AD utan att använda AD FS. Kan jag använda sömlös SSO i stället?**

Ja, det här scenariot kräver version 2,1 eller senare av [arbets platsen – Anslut till klienten](https://www.microsoft.com/download/details.aspx?id=53554).

**F: Hur kan jag använda Kerberos-dekrypteringsnyckeln för dator kontot `AZUREADSSOACC`?**

Det är viktigt att ofta slås över Kerberos-dekrypteringsnyckeln för dator kontot `AZUREADSSOACC` (som representerar Azure AD) som skapats i din lokala AD-skog.

>[!IMPORTANT]
>Vi rekommenderar starkt att du går igenom Kerberos-dekrypterings nyckeln minst var 30: e dag.

Följ de här stegen på den lokala server där du kör Azure AD Connect:

   **Step 1. Hämta en lista över AD-skogar där sömlös enkel inloggning har Aktiver ATS @ no__t-0

   1. Börja med att ladda ned och installera [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Gå till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importera den sömlös SSO PowerShell-modulen med hjälp av följande kommando: `Import-Module .\AzureADSSO.psd1`.
   4. Kör PowerShell som administratör. I PowerShell anropar du `New-AzureADSSOAuthenticationContext`. Det här kommandot ska ge dig en popup-meny för att ange klient organisationens autentiseringsuppgifter för global administratör.
   5. Anropa `Get-AzureADSSOStatus | ConvertFrom-Json`. Med det här kommandot får du en lista över AD-skogar (se listan "domäner") där funktionen har Aktiver ATS.

   **Step 2. Uppdatera Kerberos-dekrypteringsnyckeln på varje AD-skog som den har ställts in på @ no__t-0

   1. Anropa `$creds = Get-Credential`. När du uppmanas till det anger du autentiseringsuppgifter för domän administratören för den avsedda AD-skogen.

   > [!NOTE]
   >Användar namnet för domän administratörs behörighet måste anges i formatet SAM-kontonamn (contoso\johndoe eller contoso. com\johndoe). Vi använder domän delen av användar namnet för att hitta domänkontrollanten i domän administratören med hjälp av DNS.

   >[!NOTE]
   >Det domän administratörs konto som används får inte vara medlem i gruppen för skyddade användare. I så fall kommer åtgärden att Miss Miss läge.

   2. Anropa `Update-AzureADSSOForest -OnPremCredentials $creds`. Det här kommandot uppdaterar Kerberos-dekrypteringsnyckeln för dator kontot `AZUREADSSOACC` i den här särskilda AD-skogen och uppdaterar det i Azure AD.
   3. Upprepa föregående steg för varje AD-skog som du har konfigurerat funktionen på.

   >[!IMPORTANT]
   >Se till att du _inte_ kör kommandot `Update-AzureADSSOForest` mer än en gång. Annars slutar funktionen att fungera fram till den tid som dina användares Kerberos-biljetter upphör att gälla och återutfärdas av din lokala Active Directory.

**F: Hur kan jag inaktivera sömlös SSO?**

   **Step 1. Inaktivera funktionen på din klient @ no__t-0

   **Option A: Inaktivera med Azure AD Connect @ no__t-0
    
   1. Kör Azure AD Connect, Välj **inloggnings sidan Ändra användare** och klicka på **Nästa**.
   2. Avmarkera alternativet **aktivera enkel inloggning** . Fortsätt med guiden.

   När du har slutfört guiden kommer sömlös SSO att inaktive ras på din klient. Du kommer dock att se ett meddelande på skärmen som läser på följande sätt:

   "Enkel inloggning är nu inaktive rad, men det finns ytterligare manuella steg att utföra för att slutföra rensningen. Läs mer "

   Slutför rensnings processen genom att följa steg 2 och 3 på den lokala server där du kör Azure AD Connect.

   **Option B: Inaktivera med PowerShell @ no__t-0

   Kör följande steg på den lokala server där du kör Azure AD Connect:

   1. Börja med att ladda ned och installera [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Gå till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importera den sömlös SSO PowerShell-modulen med hjälp av följande kommando: `Import-Module .\AzureADSSO.psd1`.
   4. Kör PowerShell som administratör. I PowerShell anropar du `New-AzureADSSOAuthenticationContext`. Det här kommandot ska ge dig en popup-meny för att ange klient organisationens autentiseringsuppgifter för global administratör.
   5. Anropa `Enable-AzureADSSO -Enable $false`.

   >[!IMPORTANT]
   >Att inaktivera sömlös SSO med PowerShell ändrar inte tillstånd i Azure AD Connect. Sömlös SSO visas som aktive rad på **inloggnings sidan för ändrings användare** .

   **Step 2. Hämta en lista över AD-skogar där sömlös enkel inloggning har Aktiver ATS @ no__t-0

   Följ uppgifterna 1 till 4 nedan om du har inaktiverat sömlös SSO med Azure AD Connect. Om du har inaktiverat sömlös SSO med PowerShell i stället, gå vidare till aktivitet 5 nedan.

   1. Börja med att ladda ned och installera [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Gå till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Importera den sömlös SSO PowerShell-modulen med hjälp av följande kommando: `Import-Module .\AzureADSSO.psd1`.
   4. Kör PowerShell som administratör. I PowerShell anropar du `New-AzureADSSOAuthenticationContext`. Det här kommandot ska ge dig en popup-meny för att ange klient organisationens autentiseringsuppgifter för global administratör.
   5. Anropa `Get-AzureADSSOStatus | ConvertFrom-Json`. Med det här kommandot får du en lista över AD-skogar (se listan "domäner") där funktionen har Aktiver ATS.

   **Step 3. Ta manuellt bort `AZUREADSSOACCT`-dator kontot från varje AD-skog som visas i listan.**

## <a name="next-steps"></a>Nästa steg

- [**Snabb start**](how-to-connect-sso-quick-start.md) – kom igång med Azure AD sömlös SSO.
- [**Teknisk djupgående**](how-to-connect-sso-how-it-works.md) – förstå hur den här funktionen fungerar.
- [**Felsök**](tshoot-connect-sso.md) – lär dig hur du löser vanliga problem med funktionen.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för att arkivera nya funktions begär Anden.
