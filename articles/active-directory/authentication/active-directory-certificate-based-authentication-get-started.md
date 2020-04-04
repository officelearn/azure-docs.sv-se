---
title: Certifikatbaserad autentisering – Azure Active Directory
description: Lär dig hur du konfigurerar certifikatbaserad autentisering i din miljö
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a6c44a8253c81b44d02351b2df9c943d9f358f8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654343"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Komma igång med certifikatbaserad autentisering i Azure Active Directory

Med certifikatbaserad autentisering kan du autentiseras av Azure Active Directory med ett klientcertifikat på en Windows-, Android- eller iOS-enhet när du ansluter ditt Exchange-onlinekonto till:

- Microsoft-mobilappar som Microsoft Outlook och Microsoft Word
- Exchange ActiveSync-klienter (EAS)

Om du konfigurerar den här funktionen elimineras behovet av att ange en kombination av användarnamn och lösenord i vissa e-post- och Microsoft Office-program på din mobila enhet.

Detta ämne:

- Innehåller anvisningar för att konfigurera och använda certifikatbaserad autentisering för användare av klienter i Office 365 Enterprise-, Affärs-, utbildnings- och USA-myndighetsplaner. Den här funktionen är tillgänglig i förhandsversionen i Office 365 China, US Government Defense och US Government Federal plans.
- Förutsätter att du redan har konfigurerat en [infrastruktur för offentlig nyckel (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) och AD [FS.](../hybrid/how-to-connect-fed-whatis.md)

## <a name="requirements"></a>Krav

För att konfigurera certifikatbaserad autentisering måste följande satser vara sanna:

- Certifikatbaserad autentisering (CBA) stöds endast för federerade miljöer för webbläsarprogram, inbyggda klienter med hjälp av modern autentisering (ADAL) eller MSAL-bibliotek. Det enda undantaget är Exchange Active Sync (EAS) för Exchange Online (EXO), som kan användas för federerade och hanterade konton.
- Rotcertifikatutfärdaren och eventuella mellanliggande certifikatutfärdare måste konfigureras i Azure Active Directory.
- Varje certifikatutfärdarman måste ha en lista över återkallade certifikat som kan refereras via en internetinriktad URL.
- Du måste ha minst en certifikatutfärdning konfigurerad i Azure Active Directory. Du hittar relaterade steg i avsnittet [Konfigurera certifikatutfärdarna.](#step-2-configure-the-certificate-authorities)
- För Exchange ActiveSync-klienter måste klientcertifikatet ha användarens dirigerbara e-postadress i Exchange online i antingen huvudnamnet eller värdet RFC822-namn i fältet Alternativt ämne. Azure Active Directory mappar RFC822-värdet till attributet Proxy Address i katalogen.
- Klientenheten måste ha åtkomst till minst en certifikatutfärdare som utfärdar klientcertifikat.
- Ett klientcertifikat för klientautentisering måste ha utfärdats till klienten.

>[!IMPORTANT]
>Den maximala storleken på en LISTAD LISTA för Azure Active Directory för att hämta och cachelagra är 20 MB, och den tid som krävs för att hämta lista över återkallade uppgifter får inte överstiga 10 sekunder.  Om Azure Active Directory inte kan hämta en lista över återkallade certifikat misslyckas certifikatbaserade autentiseringar med certifikat som utfärdats av motsvarande certifikatutfärdare. Metodtips för att säkerställa att CRL-filer är inom storleksbegränsningar är att hålla certifikatets livstider inom rimliga gränser och rensa utgångna certifikat. 

## <a name="step-1-select-your-device-platform"></a>Steg 1: Välj enhetsplattform

Som ett första steg, för den enhetsplattform du bryr dig om, måste du granska följande:

- Office-mobilapparna har stöd för
- De särskilda genomförandekraven

Den relaterade informationen finns för följande enhetsplattformar:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Steg 2: Konfigurera certifikatutfärdarna

Så här konfigurerar du certifikatutfärdarna i Azure Active Directory för varje certifikatutfärdare:

* Den offentliga delen av certifikatet, i *.cer-format*
* De internetinriktade url:er där listor över återkallade certifikat (CRL:er) finns

Schemat för en certifikatutfärdare ser ut så här:

    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }

För konfigurationen kan du använda [Azure Active Directory PowerShell Version 2:](/powershell/azure/install-adv2?view=azureadps-2.0)

1. Starta Windows PowerShell med administratörsbehörighet.
2. Installera Azure AD-modul version [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) eller senare.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Som ett första konfigurationssteg måste du upprätta en anslutning till din klient. Så snart det finns en anslutning till din klient kan du granska, lägga till, ta bort och ändra de betrodda certifikatutfärdarna som har definierats i katalogen.

### <a name="connect"></a>Anslut

Om du vill upprätta en anslutning till din klientorganisation använder du cmdleten [Connect-AzureAD:](/powershell/module/azuread/connect-azuread?view=azureadps-2.0)

    Connect-AzureAD

### <a name="retrieve"></a>Hämta

Om du vill hämta de betrodda certifikatutfärdarna som har definierats i katalogen använder du cmdleten [Get-AzureADTrustedCertificateAuthority.](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0)

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>Lägg till

Om du vill skapa en betrodd certifikatutfärdare använder du cmdleten [New-AzureADTrustedCertificateauthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) och anger attributet **crlDistributionPoint** till ett korrekt värde:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>Ta bort

Om du vill ta bort en betrodd certifikatutfärdare använder du cmdleten [Remove-AzureADTrustedCertificateAuthority:](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0)

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>Ändra

Om du vill ändra en betrodd certifikatutfärdare använder du cmdleten [Set-AzureADTrustedCertificateAuthority:](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0)

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>Steg 3: Konfigurera återkallning

Om du vill återkalla ett klientcertifikat hämtar Azure Active Directory listan över återkallade certifikat (CRL) från de url:er som överförs som en del av certifikatutfärdarinformation och cachelagrar den. Den senaste publiceringstidsstämpeln (egenskapen**Giltighetsdatum)** i crl används för att säkerställa att referenslaboratoriet fortfarande är giltig. Listan över återkallade certifikat som är en del av listan.

Om en mer omedelbar återkallning krävs (till exempel om en användare förlorar en enhet) kan användarens auktoriseringstoken ogiltigförklaras. Om du vill ogiltigförklara auktoriseringstoken anger du fältet **StsRefreshTokenValidFrom** för den här användaren med Windows PowerShell. Du måste uppdatera fältet **StsRefreshTokenValidFrom** för varje användare som du vill återkalla åtkomsten för.

För att säkerställa att återkallelsen kvarstår måste du ange **giltighetsdatumet** för referensvärdet till ett datum efter det värde som angetts av **StsRefreshTokenValidFrom** och se till att certifikatet i fråga finns i lista över återkallade certifikat.

I följande steg beskrivs processen för att uppdatera och ogiltigförklara auktoriseringstoken genom att ange fältet **StsRefreshTokenValidFrom.**

**Så här konfigurerar du återkallning:**

1. Anslut med administratörsautentiseringsuppgifter till MSOL-tjänsten:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Hämta det aktuella StsRefreshTokensValidFrom-värdet för en användare:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Konfigurera ett nytt StsRefreshTokensValidFrom-värde för användaren som är lika med den aktuella tidsstämpeln:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

Det datum du anger måste vara i framtiden. Om datumet inte är i framtiden anges inte egenskapen **StsRefreshTokensValidFrom.** Om datumet är i framtiden anges **StsRefreshTokensValidFrom** till aktuell tid (inte det datum som anges av kommandot Set-MsolUser).

## <a name="step-4-test-your-configuration"></a>Steg 4: Testa konfigurationen

### <a name="testing-your-certificate"></a>Testa certifikatet

Som ett första konfigurationstest bör du försöka logga in på [Outlook Web Access](https://outlook.office365.com) eller [SharePoint Online](https://microsoft.sharepoint.com) med webbläsaren på **enheten**.

Om din inloggning lyckas, då vet du att:

- Användarcertifikatet har etablerats på testenheten
- AD FS är korrekt konfigurerad

### <a name="testing-office-mobile-applications"></a>Testa office-mobilappar

**Så här testar du certifikatbaserad autentisering i ditt mobila Office-program:**

1. Installera ett Office-mobilprogram (till exempel OneDrive) på testenheten.
3. Starta programmet.
4. Ange ditt användarnamn och välj sedan det användarcertifikat som du vill använda.

Du bör vara inloggad.

### <a name="testing-exchange-activesync-client-applications"></a>Testa Exchange ActiveSync-klientprogram

För att komma åt Exchange ActiveSync (EAS) via certifikatbaserad autentisering måste en EAS-profil som innehåller klientcertifikatet vara tillgänglig för programmet.

EAS-profilen måste innehålla följande information:

- Användarcertifikatet som ska användas för autentisering

- EAS-slutpunkten (till exempel outlook.office365.com)

En EAS-profil kan konfigureras och placeras på enheten genom användning av MDM (Mobile Device Management), till exempel Intune eller genom att manuellt placera certifikatet i EAS-profilen på enheten.

### <a name="testing-eas-client-applications-on-android"></a>Testa EAS-klientprogram på Android

**Så här testar du certifikatautentisering:**

1. Konfigurera en EAS-profil i programmet som uppfyller kraven i föregående avsnitt.
2. Öppna programmet och kontrollera att e-post synkroniseras.

## <a name="next-steps"></a>Nästa steg

[Ytterligare information om certifikatbaserad autentisering på Android-enheter.](active-directory-certificate-based-authentication-android.md)

[Ytterligare information om certifikatbaserad autentisering på iOS-enheter.](active-directory-certificate-based-authentication-ios.md)
