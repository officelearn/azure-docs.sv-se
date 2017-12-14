---
title: "Azure Active Directory certifikatbaserad autentisering - komma igång | Microsoft Docs"
description: "Lär dig hur du konfigurerar certifikatbaserad autentisering i din miljö"
author: MarkusVi
documentationcenter: na
manager: mtillman
ms.assetid: c6ad7640-8172-4541-9255-770f39ecce0e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/13/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 4dfc0d2f334404e4984f677a4b2966fc958ca98d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Komma igång med certifikatbaserad autentisering i Azure Active Directory

Certifikatbaserad autentisering gör att du ska kunna autentiseras av Azure Active Directory med ett klientcertifikat på en Windows-, Android eller iOS-enhet när du ansluter din Exchange-onlinekonto till:

- Microsoft mobila program, till exempel Microsoft Outlook och Microsoft Word   

- Exchange ActiveSync (EAS) klienter

Konfigurera den här funktionen eliminerar behovet av att ange en kombination av användarnamn och lösenord i vissa e-post och Microsoft Office-program på din mobila enhet.

Det här avsnittet:

- Ger dig stegen för att konfigurera och använda certifikatbaserad autentisering för användare av klienter i Office 365 Enterprise, företag, Education och som tillhör amerikanska myndigheter planer. Den här funktionen är tillgängliga i förhandsversionen i Office 365 Kina US Government skydd och US Government Federal planer.

- Förutsätter att du redan har en [infrastruktur för offentliga nycklar (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) och [AD FS](connect/active-directory-aadconnectfed-whatis.md) konfigurerats.    


## <a name="requirements"></a>Krav

Följande om du vill konfigurera certifikatbaserad autentisering, den måste vara sant:  

- Certifikatbaserad autentisering (CBA) stöds endast för federerat miljöer för webbläsarprogram eller interna klienter som använder modern autentisering (ADAL). Det enda undantaget är Exchange Active Sync (EAS) för EXO som kan användas för både federerade och hanterade konton.

- Rotcertifikatutfärdaren och alla mellanliggande certifikatutfärdare måste konfigureras i Azure Active Directory.  

- Varje certifikatutfärdare måste ha en lista certifikat (CRL) som kan refereras via en Internetuppkopplad URL.  

- Du måste ha minst en certifikatutfärdare som konfigurerats i Azure Active Directory. Du kan hitta relaterade stegen i den [konfigurera certificate myndigheter](#step-2-configure-the-certificate-authorities) avsnitt.  

- För Exchange ActiveSync-klienter, måste klientcertifikatet ha användarens dirigerbara e-postadress i Exchange online i huvudnamn eller RFC822 namn-värde i fältet Alternativt ämnesnamn. Azure Active Directory mappar RFC822 värdet till attributet proxyadress i katalogen.  

- Klientenheten måste ha tillgång till minst en certifikatutfärdare som utfärdar certifikat.  

- Ett klientcertifikat för klientautentisering måste har utfärdats till klienten.  




## <a name="step-1-select-your-device-platform"></a>Steg 1: Välj din enhetsplattform

Som ett första steg för den enhetsplattform som du bryr dig om du behöver för att granska följande:

- Stöd för mobila Office-program
- Krav för specifik implementering  

Det finns relaterad information för följande enhetsplattformar:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)


## <a name="step-2-configure-the-certificate-authorities"></a>Steg 2: Konfigurera på certifikatutfärdare

Om du vill konfigurera din certifikatutfärdare i Azure Active Directory för varje certifikatutfärdare, överför du följande:

* Den offentliga delen av certifikatet i *.cer* format
* Internet facing URL: er där den listor över återkallade certifikat (CRL) finns

Schemat för en certifikatutfärdare som ser ut som följer:

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
2. Installera Azure AD-modulen. Du måste installera Version [2.0.0.33 ](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) eller högre.  

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Som ett första konfigurationssteg måste du upprätta en anslutning till din klient. Så snart det finns en anslutning till din klient, kan du granska, lägga till, ta bort och ändra betrodda certifikatutfärdare som har definierats i din katalog.

### <a name="connect"></a>Anslut

För att upprätta en anslutning till din klient använder de [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) cmdlet:

    Connect-AzureAD


### <a name="retrieve"></a>Hämta

Använd för att hämta de betrodda certifikatutfärdare som har definierats i din katalog i [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet.

    Get-AzureADTrustedCertificateAuthority


### <a name="add"></a>Lägg till

Så här skapar du en betrodd certifikatutfärdare på [ny AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet och ange den **crlDistributionPoint** attribut till ett korrekt värde:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint=”<CRL Distribution URL>”
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca


### <a name="remove"></a>Ta bort

Ta bort en betrodd certifikatutfärdare med den [ta bort AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet:

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]


### <a name="modfiy"></a>Modfiy

Om du vill ändra en betrodd certifikatutfärdare, använder den [Set AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet:

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]


## <a name="step-3-configure-revocation"></a>Steg 3: Konfigurera återkallade certifikat

Om du vill återkalla ett certifikat för Azure Active Directory hämtar listan över återkallade certifikat (CRL) från URL: er som överförs som en del av information om certifikatutfärdare och cachelagrar den. Senaste publicera tidsstämpel (**giltighetsdatum** egenskapen) i listan över återkallade certifikat för att kontrollera listan över återkallade certifikat är fortfarande giltig. Listan över återkallade certifikat refereras med jämna mellanrum för att återkalla åtkomst till certifikat som ingår i listan.

Om det krävs mer instant återkallas (till exempel om en användare förlorar en enhet), kan vara ogiltig Autentiseringstoken för användaren. För att ogiltigförklara autentiseringstoken, ange den **StsRefreshTokenValidFrom** för den aktuella användaren med Windows PowerShell. Du måste uppdatera de **StsRefreshTokenValidFrom** för varje användare som du vill återkalla åtkomst för.

Att säkerställa att återkallningen kvarstår, måste du ange den **giltighetsdatum** av listan över återkallade certifikat till ett datum efter värdet som angetts av **StsRefreshTokenValidFrom** och se till att det aktuella certifikatet finns i listan över återkallade certifikat.

Följande steg beskriver processen för att uppdatera och ogiltigförklara autentiseringstoken genom att ange den **StsRefreshTokenValidFrom** fältet.

**Så här konfigurerar du återkallade certifikat:**

1. Anslut med administratörsautentiseringsuppgifter för tjänsten MSOL:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Hämta det aktuella StsRefreshTokensValidFrom-värdet för en användare:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Konfigurera ett nytt StsRefreshTokensValidFrom värde för den användare som är lika med aktuell tidsstämpel:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

Det datum som du anger måste vara i framtiden. Om datumet inte är i framtiden kan den **StsRefreshTokensValidFrom** egenskapen har inte angetts. Om datumet är i framtiden, **StsRefreshTokensValidFrom** anges till den aktuella tiden (inte det datum som anges av kommandot Set-MsolUser).


## <a name="step-4-test-your-configuration"></a>Steg 4: Testa din konfiguration

### <a name="testing-your-certificate"></a>Testa ditt certifikat

Som ett första konfigurationstest bör du försöker logga in på [Outlook Web Access](https://outlook.office365.com) eller [SharePoint Online](https://microsoft.sharepoint.com) med din **på enhetens webbläsare**.

Om din inloggning lyckades, vet som:

- Användarcertifikatet har etablerats till testenhet
- AD FS är korrekt konfigurerad  


### <a name="testing-office-mobile-applications"></a>Testa mobila Office-program

**Så här testar certifikatbaserad autentisering på din mobila Office-program:**

1. Installera ett mobila Office-program (t.ex. OneDrive) på enheten test.
3. Starta programmet.
4. Ange ditt användarnamn och välj sedan användarcertifikatet för som du vill använda.

Du bör vara loggat in.

### <a name="testing-exchange-activesync-client-applications"></a>Testa program för Exchange ActiveSync-klienten

För att komma åt Exchange ActiveSync (EAS) via certifikatbaserad autentisering måste det finnas en EAS-profil som innehåller klientcertifikatet till programmet.

EAS-profil måste innehålla följande information:

- Användarcertifikatet som ska användas för autentisering

- EAS-slutpunkt (till exempel outlook.office365.com)

EAS-profil kan konfigureras och placeras på enheten via användning av hantering av mobila enheter (MDM), till exempel Intune eller genom att manuellt certifikatet EAS-profil på enheten.  

### <a name="testing-eas-client-applications-on-android"></a>Testa EAS klientprogram på Android

**Att testa autentisering med datorcertifikat:**  

1. Konfigurera en EAS-profil i programmet som uppfyller kraven ovan.  
2. Öppna programmet och kontrollera att du synkroniserar e-post.
