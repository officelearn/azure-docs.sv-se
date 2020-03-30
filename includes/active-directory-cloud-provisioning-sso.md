---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 3aa1571b46938b03f556fa124d3f0a2a70f2c5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504323"
---
## <a name="steps-to-enable-single-sign-on"></a>Åtgärder för att aktivera enkel inloggning
Molnetablering fungerar med enkel inloggning.  För närvarande finns det inte ett alternativ för att aktivera SSO när agenten är installerad, men du kan använda stegen nedan för att aktivera SSO och använda den. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Steg 1: Hämta och extrahera Azure AD Connect-filer
1.  Hämta först den senaste versionen av [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  Öppna en kommandotolk med administratörsbehörighet och navigera till den msi du just hämtade.
3.  Kör följande:`msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Ändra filsökvägen och utdragsmappen så att de matchar filsökvägen och namnet på extraheringsmappen.  Innehållet ska nu finnas i extraheringsmappen.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Steg 2: Importera seamless SSO PowerShell-modulen

1. Ladda ned och installera [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Bläddra till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importera seamless SSO PowerShell-modulen `Import-Module .\AzureADSSO.psd1`med det här kommandot: .

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Steg 3: Få en lista över Active Directory-skogar där Sömlös SSO har aktiverats

1. Kör PowerShell som administratör. Ring `New-AzureADSSOAuthenticationContext`i PowerShell. Ange klientens globala administratörsautentiseringsuppgifter när du uppmanas att göra det.
2. Ring. `Get-AzureADSSOStatus` Det här kommandot ger dig en lista över Active Directory-skogar (titta på listan "Domäner" som den här funktionen har aktiverats på.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Steg 4: Aktivera sömlös SSO för varje Active Directory-skog

1. Ring. `Enable-AzureADSSOForest` Ange domänadministratörsuppgifter för den avsedda Active Directory-skogen när du uppmanas att göra det.

   > [!NOTE]
   >Användarnamnet för domänadministratörsuppgifter måste anges i namnformatet för SIM-konton (contoso\johndoe eller contoso.com\johndoe). Vi använder domändelen av användarnamnet för att hitta domänkontrollanten för domänadministratören med HJÄLP AV DNS.

   >[!NOTE]
   >Det domänadministratörskonto som används får inte vara medlem i gruppen Skyddade användare. Om så är fallet misslyckas åtgärden.

2. Upprepa föregående steg för varje Active Directory-skog där du vill konfigurera funktionen.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Steg 5. Aktivera funktionen på din klientorganisation

Om du vill aktivera funktionen `Enable-AzureADSSO -Enable $true`på din klient ringer du .
