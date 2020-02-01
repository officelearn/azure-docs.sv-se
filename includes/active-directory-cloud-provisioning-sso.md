---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: ba1bdd60a3363cfab694bae9b8ee3cf63e24b054
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907583"
---
## <a name="steps-to-enable-single-sign-on"></a>Steg för att aktivera enkel inloggning
Moln etablering fungerar med enkel inloggning.  För närvarande finns det inget alternativ för att aktivera SSO när agenten har installerats, men du kan använda stegen nedan för att aktivera SSO och använda det. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Steg 1: Ladda ned och extrahera Azure AD Connect filer
1.  Börja med att ladda ned den senaste versionen av [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  Öppna en kommando tolk med administratörs behörighet och navigera till MSI som du precis har laddat ned.
3.  Kör följande: `msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Ändra sökväg och extractfolder så att de matchar din fil Sök väg och namnet på din extraherings mapp.  Innehållet bör nu finnas i extraherings mappen.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Steg 2: importera sömlös SSO PowerShell-modul

1. Ladda ned och installera [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Bläddra till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importera den sömlös SSO PowerShell-modulen med hjälp av följande kommando: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Steg 3: hämta listan över Active Directory skogar där sömlös enkel inloggning har Aktiver ATS

1. Kör PowerShell som administratör. Anropa `New-AzureADSSOAuthenticationContext`i PowerShell. När du uppmanas till det anger du klient organisationens autentiseringsuppgifter för global administratör.
2. Anropa `Get-AzureADSSOStatus`. Med det här kommandot får du en lista över Active Directory skogar (se listan "domäner") där funktionen har Aktiver ATS.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Steg 4: Aktivera sömlös SSO för varje Active Directory skog

1. Anropa `Enable-AzureADSSOForest`. När du uppmanas till det anger du autentiseringsuppgifter för domän administratören för den avsedda Active Directory skogen.

   > [!NOTE]
   >Användar namnet för domän administratörs behörighet måste anges i formatet SAM-kontonamn (contoso\johndoe eller contoso. com\johndoe). Vi använder domän delen av användar namnet för att hitta domänkontrollanten i domän administratören med hjälp av DNS.

   >[!NOTE]
   >Det domän administratörs konto som används får inte vara medlem i gruppen för skyddade användare. I så fall kommer åtgärden att Miss Miss läge.

2. Upprepa föregående steg för varje Active Directory skog där du vill ställa in funktionen.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Steg 5. Aktivera funktionen på din klient

Ring `Enable-AzureADSSO -Enable $true`om du vill aktivera funktionen på klienten.
