---
title: Kom igång med Azure Active Directory-certifikatbaserad autentisering
description: Lär dig att konfigurera certifikatbaserad autentisering i din miljö
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: annaba
ms.openlocfilehash: 37b0067b0879953ca4fbb16af146ba447f29e794
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081576"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Kom igång med certifikatbaserad autentisering i Azure Active Directory

Certifikatbaserad autentisering gör att du ska kunna autentiseras av Azure Active Directory med ett klientcertifikat på en Windows-, Android- eller iOS-enhet när du ansluter din Exchange online-konto:

- Mobila Microsoft-program, till exempel Microsoft Outlook och Microsoft Word
- Exchange ActiveSync (EAS) klienter

Konfigurerar den här funktionen eliminerar behovet av att ange en kombination av användarnamn och lösenord i vissa e-post och Microsoft Office-program på din mobila enhet.

Det här avsnittet:

- Ger dig stegen för att konfigurera och använda certifikatbaserad autentisering för användare med klienter i Office 365 Enterprise, företag, utbildning och US Government-planer. Den här funktionen är tillgänglig som förhandsversion i planer för Office 365 Kina, US Government försvar samt amerikanska myndigheter federala myndigheter.
- Förutsätter att du redan har en [offentlig nyckelinfrastruktur (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) och [AD FS](../hybrid/how-to-connect-fed-whatis.md) konfigurerats.

## <a name="requirements"></a>Krav

Följande instruktioner för att konfigurera certifikatbaserad autentisering, måste vara uppfyllda:

- Certifikatbaserad autentisering (CBA) stöds endast för federerade miljöer för webbläsarprogram eller interna klienter som använder modern autentisering (ADAL). Det enda undantaget är Exchange Active Sync (EAS) för Exchange Online (EXO), som kan användas för federerade och hanterade konton.
- Rotcertifikatutfärdaren och alla mellanliggande certifikatutfärdare måste konfigureras i Azure Active Directory.
- Varje certifikatutfärdare måste ha en certifikatet lista över återkallade (certifikat CRL) som kan refereras via en internet-ansluten-URL.
- Du måste ha minst en certifikatutfärdare som konfigurerats i Azure Active Directory. Du kan hitta relaterade stegen i den [konfigurera certifikatutfärdarna](#step-2-configure-the-certificate-authorities) avsnittet.
- För Exchange ActiveSync-klienter, måste klientcertifikatet ha användarens dirigerbara e-postadress i Exchange online i huvudnamn eller RFC822 namn-värde i fältet alternativt mottagarnamn. Azure Active Directory mappar RFC822 värdet till attributet proxyadress i katalogen.
- Klientenheten måste ha åtkomst till minst en certifikatutfärdare som utfärdar certifikat.
- Ett klientcertifikat för klientautentisering måste har utfärdats till din klient.

## <a name="step-1-select-your-device-platform"></a>Steg 1: Välj din enhetsplattform

Som ett första steg för den enhetsplattform som intresserar dig, måste du kontrollera följande:

- Stöd för mobila Office-program
- Krav för specifik implementering

Det finns relaterad information för följande enhetsplattformar:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Steg 2: Konfigurera certifikatutfärdarna

Om du vill konfigurera din certifikatutfärdare i Azure Active Directory, för varje certifikatutfärdare, överför du följande:

* Den offentliga delen av certifikatet i *.cer* format
* URL: er för webbservergrupper på internet som där den listor över återkallade certifikat (CRL) finns

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

Konfiguration, kan du använda den [Azure Active Directory PowerShell Version 2](/powershell/azure/install-adv2?view=azureadps-2.0):

1. Starta Windows PowerShell med administratörsbehörighet.
2. Installera Azure AD-Modulversion [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) eller högre.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Som ett första konfigurationssteg måste du upprätta en anslutning till din klient. När det finns en anslutning till din klient, kan du granska, lägga till, ta bort och ändra de betrodda certifikatutfärdare som har definierats i din katalog.

### <a name="connect"></a>Anslut

Om du vill upprätta en anslutning till din klient, använder den [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) cmdlet:

    Connect-AzureAD

### <a name="retrieve"></a>Hämta

Använd för att hämta de betrodda certifikatutfärdare som har definierats i din katalog, den [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet.

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>Lägg till

Du kan skapa en betrodd certifikatutfärdare med den [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet och ange den **crlDistributionPoint** attributet ett korrekt värde:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>Ta bort

Ta bort en betrodd certifikatutfärdare med den [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet:

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>Ändra

Om du vill ändra en betrodd certifikatutfärdare, använder den [Set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet:

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>Steg 3: Konfigurera återkallade certifikat

Om du vill återkalla ett klientcertifikat, Azure Active Directory hämtar listan över återkallade certifikat (CRL) från de webbadresser som laddas upp som en del av information om certifikatutfärdare och cachelagrar den. Senaste publicera tidsstämpel (**ikraftträdandedatum** egenskapen) i listan över återkallade certifikat för att kontrollera listan över återkallade certifikat är fortfarande giltig. Listan över återkallade certifikat refereras med jämna mellanrum för att återkalla certifikat som ingår i listan.

Om det krävs en mer direkt återkallade certifikat (till exempel om en användare förlorar en enhet), kan vara ogiltig Autentiseringstoken för användaren. Om du vill göra ogiltig autentiseringstoken, ange den **StsRefreshTokenValidFrom** för den aktuella användaren med hjälp av Windows PowerShell. Du måste uppdatera den **StsRefreshTokenValidFrom** för varje användare som du vill återkalla åtkomst för.

Att säkerställa att återkallas kvarstår måste du ställa in den **ikraftträdandedatum** av listan över återkallade certifikat till ett datum efter värdet som anges med **StsRefreshTokenValidFrom** och se till att det aktuella certifikatet finns i listan över återkallade certifikat.

Följande steg beskriver hur du uppdaterar och ogiltigförklara autentiseringstoken genom att ange den **StsRefreshTokenValidFrom** fält.

**Konfigurera återkallade certifikat:**

1. Anslut med administratörsautentiseringsuppgifter för MSOL-tjänsten:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Hämta det aktuella StsRefreshTokensValidFrom-värdet för en användare:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Konfigurera ett nytt StsRefreshTokensValidFrom värde för den användare som motsvarar den aktuella tidsstämpeln:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

Det datum som du anger måste vara i framtiden. Om datumet inte är i framtiden kan den **StsRefreshTokensValidFrom** egenskapen har inte angetts. Om datumet är i framtiden, **StsRefreshTokensValidFrom** anges till den aktuella tiden (inte det datum som anges med kommandot Set-MsolUser).

## <a name="step-4-test-your-configuration"></a>Steg 4: Testa din konfiguration

### <a name="testing-your-certificate"></a>Testa ditt certifikat

Som ett första konfigurationstest, bör du försöka logga in på [Outlook Web Access](https://outlook.office365.com) eller [SharePoint Online](https://microsoft.sharepoint.com) med hjälp av din **på enhetens webbläsare**.

Om din inloggning lyckades, sedan vet du att:

- Användarcertifikatet har etablerats till testenhet
- AD FS är korrekt konfigurerad

### <a name="testing-office-mobile-applications"></a>Testa mobila Office-program

**Så här testar certifikatbaserad autentisering på din mobila Office-program:**

1. Installera ett mobila Office-program (till exempel OneDrive) på din testenhet.
3. Starta programmet.
4. Ange ditt användarnamn och välj sedan användarcertifikatet för som du vill använda.

Du bör vara har loggat in.

### <a name="testing-exchange-activesync-client-applications"></a>Testa Exchange ActiveSync-klientprogram

För att komma åt Exchange ActiveSync (EAS) via certifikatbaserad autentisering, måste det finnas en EAS-profil som innehåller klientcertifikatet till programmet.

EAS-profil måste innehålla följande information:

- Användarcertifikatet som ska användas för autentisering

- EAS-slutpunkt (t.ex, outlook.office365.com)

EAS-profil kan konfigureras och placeras på enheten via användning av hantering av mobilenheter (MDM), till exempel Intune, eller genom att placera certifikatet manuellt på EAS-profil på enheten.

### <a name="testing-eas-client-applications-on-android"></a>Testa EAS klientprogram på Android

**Testa autentisering med datorcertifikat:**

1. Konfigurera en EAS-profil i programmet som uppfyller kraven i föregående avsnitt.
2. Öppna programmet och kontrollera att e-post synkroniseras.

## <a name="next-steps"></a>Nästa steg

[Ytterligare information om certifikatbaserad autentisering på Android-enheter.](active-directory-certificate-based-authentication-android.md)

[Ytterligare information om certifikatbaserad autentisering på iOS-enheter.](active-directory-certificate-based-authentication-ios.md)
