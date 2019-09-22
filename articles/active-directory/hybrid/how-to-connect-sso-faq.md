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
ms.openlocfilehash: 96d0253e1e656f4bdb5180af0d57824aceb4f0dd
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176658"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory sömlös enkel inloggning: Vanliga frågor och svar

I den här artikeln tar vi itu med vanliga frågor om Azure Active Directory sömlös enkel inloggning (sömlös SSO). Fortsätt att söka efter nytt innehåll.

**F: Vilka inloggnings metoder fungerar sömlöst enkel inloggning med**

Sömlös SSO kan kombineras med antingen [lösen ordets hash-synkronisering](how-to-connect-password-hash-synchronization.md) eller inloggnings metoder för [direktautentisering](how-to-connect-pta.md) . Den här funktionen kan dock inte användas med Active Directory Federation Services (AD FS) (ADFS).

**F: Är sömlös enkel inloggning en kostnads fri funktion?**

Sömlös SSO är en kostnads fri funktion och du behöver inte några betalda versioner av Azure AD för att använda den.

**F: Är sömlös enkel inloggning tillgänglig i [Microsoft Azure Tyskland molnet](https://www.microsoft.de/cloud-deutschland) och [Microsoft Azure Government molnet](https://azure.microsoft.com/features/gov/)?**

Nej. Sömlös SSO är bara tillgängligt i den globala instansen av Azure AD.

**F: Vilka program `domain_hint` utnyttjar eller `login_hint` parameter kapaciteten för sömlös enkel inloggning?**

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

**F: Har sömlös SSO- `Alternate ID` stöd som användar namn, i `userPrincipalName`stället för?**

Ja. Sömlös SSO stöder `Alternate ID` som användar namn när det konfigureras i Azure AD Connect som visas [här](how-to-connect-install-custom.md). Inte alla stöd `Alternate ID`för Office 365-program. Läs dokumentationen för det specifika programmet för support policyn.

**F: Vad är skillnaden mellan enkel inloggnings upplevelsen från [Azure AD Join](../active-directory-azureadjoin-overview.md) och sömlös enkel inloggning?**

[Azure AD Join](../active-directory-azureadjoin-overview.md) ger enkel inloggning till användare om deras enheter har registrerats med Azure AD. Dessa enheter behöver inte nödvändigt vis vara domänanslutna. SSO tillhandahålls med *primära uppdateringstoken* eller *PRTs*, och inte Kerberos. Användar upplevelsen är mest optimal för Windows 10-enheter. SSO sker automatiskt i Microsoft Edge-webbläsaren. Det fungerar också på Chrome med att använda ett webb läsar tillägg.

Du kan använda både Azure AD Join och sömlös SSO på din klient. Dessa två funktioner är kompletterande. Om båda funktionerna är aktiverade prioriteras SSO från Azure AD Join framför sömlös SSO.

**F: Jag vill registrera icke-Windows 10-enheter med Azure AD utan att använda AD FS. Kan jag använda sömlös SSO i stället?**

Ja, det här scenariot kräver version 2,1 eller senare av [arbets platsen – Anslut till klienten](https://www.microsoft.com/download/details.aspx?id=53554).

**F: Hur kan jag återställa Kerberos-dekrypterings nyckeln för `AZUREADSSOACC` dator kontot?**

Det är viktigt att ofta rulla över Kerberos-dekrypterings nyckeln för `AZUREADSSOACC` dator kontot (som representerar Azure AD) som skapats i din lokala AD-skog.

>[!IMPORTANT]
>Vi rekommenderar starkt att du går igenom Kerberos-dekrypterings nyckeln minst var 30: e dag.

Följ de här stegen på den lokala server där du kör Azure AD Connect:

    **Step 1. Get list of AD forests where Seamless SSO has been enabled**

    1. Börja med att ladda ned och installera [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
    2. Gå till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
    3. Importera den sömlös SSO PowerShell-modulen med hjälp av `Import-Module .\AzureADSSO.psd1`det här kommandot:.
    4. Kör PowerShell som administratör. I PowerShell anropar `New-AzureADSSOAuthenticationContext`du. Det här kommandot ska ge dig en popup-meny för att ange klient organisationens autentiseringsuppgifter för global administratör.
    5. Anropa `Get-AzureADSSOStatus | ConvertFrom-Json`. Med det här kommandot får du en lista över AD-skogar (se listan "domäner") där funktionen har Aktiver ATS.

    **Steg 2. Uppdatera Kerberos-dekrypterings nyckeln på varje AD-skog som den har ställts in på**

    1. Anropa `$creds = Get-Credential`. När du uppmanas till det anger du autentiseringsuppgifter för domän administratören för den avsedda AD-skogen.

    > [!NOTE]
    > Vi använder domän administratörens användar namn, som anges i formatet UPN (User Principal Names) (johndoe@contoso.comUPN) eller domän kvalificerat Sam-Account name (contoso\johndoe eller contoso. com\johndoe) för att hitta den avsedda AD-skogen. Om du använder domän kvalificerat SAM-kontonamn använder vi domän delen av användar namnet för att hitta domänkontrollanten [i domän administratören med hjälp av DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Om du använder UPN i stället, [översätter vi det till ett domän kvalificerat SAM-kontonamn](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) innan du hittar lämplig domänkontrollant.

    2. Anropa `Update-AzureADSSOForest -OnPremCredentials $creds`. Det här kommandot uppdaterar Kerberos-dekrypterings nyckeln för `AZUREADSSOACC` dator kontot i den här särskilda AD-skogen och uppdaterar det i Azure AD.
    3. Upprepa föregående steg för varje AD-skog som du har konfigurerat funktionen på.

    >[!IMPORTANT]
    >Se till att du _inte_ kör `Update-AzureADSSOForest` kommandot mer än en gång. Annars slutar funktionen att fungera fram till den tid som dina användares Kerberos-biljetter upphör att gälla och återutfärdas av din lokala Active Directory.

**F: Hur kan jag inaktivera sömlös enkel inloggning?**

    **Step 1. Disable the feature on your tenant**

        **Option A: Disable using Azure AD Connect**

        1. Run Azure AD Connect, choose **Change user sign-in page** and click **Next**.
        2. Uncheck the **Enable single sign on** option. Continue through the wizard.

        After completing the wizard, Seamless SSO will be disabled on your tenant. However, you will see a message on screen that reads as follows:

        "Single sign-on is now disabled, but there are additional manual steps to perform in order to complete clean-up. Learn more"

        To complete the clean-up process, follow steps 2 and 3 on the on-premises server where you are running Azure AD Connect.

        **Option B: Disable using PowerShell**

        Run the following steps on the on-premises server where you are running Azure AD Connect:

        1. First, download, and install [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
        2. Navigate to the `%programfiles%\Microsoft Azure Active Directory Connect` folder.
        3. Import the Seamless SSO PowerShell module using this command: `Import-Module .\AzureADSSO.psd1`.
        4. Run PowerShell as an Administrator. In PowerShell, call `New-AzureADSSOAuthenticationContext`. This command should give you a popup to enter your tenant's Global Administrator credentials.
        5. Call `Enable-AzureADSSO -Enable $false`.

        >[!IMPORTANT]
        >Disabling Seamless SSO using PowerShell will not change the state in Azure AD Connect. Seamless SSO will show as enabled in the **Change user sign-in** page.

    **Step 2. Get list of AD forests where Seamless SSO has been enabled**

    Follow tasks 1 through 4 below if you have disabled Seamless SSO using Azure AD Connect. If you have disabled Seamless SSO using PowerShell instead, jump ahead to task 5 below.

    1. Börja med att ladda ned och installera [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
    2. Gå till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
    3. Importera den sömlös SSO PowerShell-modulen med hjälp av `Import-Module .\AzureADSSO.psd1`det här kommandot:.
    4. Kör PowerShell som administratör. I PowerShell anropar `New-AzureADSSOAuthenticationContext`du. Det här kommandot ska ge dig en popup-meny för att ange klient organisationens autentiseringsuppgifter för global administratör.
    5. Anropa `Get-AzureADSSOStatus | ConvertFrom-Json`. Med det här kommandot får du en lista över AD-skogar (se listan "domäner") där funktionen har Aktiver ATS.

    **Steg 3. Ta manuellt bort `AZUREADSSOACCT` dator kontot från varje AD-skog som visas i listan.**

## <a name="next-steps"></a>Nästa steg

- [**Snabbstart**](how-to-connect-sso-quick-start.md) – kom igång med Azure AD sömlös SSO.
- [**Teknisk djupgående**](how-to-connect-sso-how-it-works.md) – förstå hur den här funktionen fungerar.
- [**Felsök**](tshoot-connect-sso.md) – lär dig hur du löser vanliga problem med funktionen.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för att arkivera nya funktions begär Anden.
