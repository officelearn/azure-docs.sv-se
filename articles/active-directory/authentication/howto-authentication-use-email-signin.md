---
title: Logga in med e-post som ett alternativt inloggnings-ID för Azure Active Directory
description: Lär dig hur du konfigurerar och gör det möjligt för användare att logga in på Azure Active Directory med sin e-postadress som ett alternativt inloggnings-ID (för hands version)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/22/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: scottsta
ms.openlocfilehash: ed317039e683ef36054d5ace612e09ca75dfa11e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837390"
---
# <a name="sign-in-to-azure-using-email-as-an-alternate-login-id-preview"></a>Logga in på Azure med e-post som ett alternativt inloggnings-ID (förhands granskning)

Många organisationer vill att användarna ska kunna logga in på Azure med samma autentiseringsuppgifter som den lokala katalog miljön. Med den här metoden, som kallas hybrid autentisering, behöver användarna bara komma ihåg en uppsättning autentiseringsuppgifter.

Vissa organisationer har inte flyttats till hybrid autentisering av följande anledningar:

* Som standard har Azure Active Directory (Azure AD) User Principal Name (UPN) angetts till samma UPN som den lokala katalogen.
* Att ändra Azure AD UPN skapar en mis-matchning mellan lokal-och Azure-miljöer som kan orsaka problem med vissa program och tjänster.
* På grund av affärs-eller efterföljandekrav vill inte organisationen använda det lokala UPN för att logga in på Azure.

För att hjälpa till med att flytta till hybrid autentisering kan du nu konfigurera Azure AD så att användarna kan logga in på Azure med ett e-postmeddelande i din verifierade domän som ett alternativt inloggnings-ID. Om *contoso* till exempel har ändrats till *Fabrikam*, i stället för att fortsätta logga in med det äldre `balas@contoso.com` UPN, kan du nu använda e-post som ett alternativt inloggnings-ID. För att få åtkomst till ett program eller tjänster loggar användare in på Azure med hjälp av deras tilldelade e-post, till exempel `balas@fabrikam.com` .

|     |
| --- |
| Logga in på Azure AD med e-post som ett alternativt inloggnings-ID är en offentlig förhands gransknings funktion i Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.|
|     |

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Översikt över inloggnings metoder för Azure AD

Användarens huvud namn (UPN) är unika identifierare för ett användar konto i både din lokala katalog och i Azure AD. Varje användar konto i en katalog representeras av ett UPN, till exempel `balas@contoso.com` . Som standard, när du synkroniserar en lokal Active Directory Domain Services (AD DS)-miljö med Azure AD, är Azure AD UPN konfigurerat för att matcha det lokala UPN-värdet.

I många organisationer är det bra att ange det lokala UPN och Azure AD UPN som ska matchas. När användarna loggar in på Azure-program och-tjänster använder de sina Azure AD UPN. Vissa organisationer kan dock inte använda matchande UPN för inloggning på grund av affärs principer eller problem med användar upplevelsen.

Organisationer som inte kan använda matchande UPN i Azure AD har några alternativ:

* En metod är att ställa in Azure AD UPN till något annat baserat på affärs behoven, till exempel `balas@fabrikam.com` .
    * Men inte alla program och tjänster är kompatibla med att använda ett annat värde för det lokala UPN: t och Azure AD UPN.
* En bättre metod är att se till att Azure AD och lokala UPN är inställda på samma värde och konfigurerar Azure AD så att användarna kan logga in på Azure med sina e-postmeddelanden som ett alternativt inloggnings-ID.

Med e-post som ett alternativt inloggnings-ID kan användarna fortfarande logga in på Azure genom att ange sitt UPN, men kan också logga in med sin e-post. För att stödja detta definierar du en e-postadress i användarens *proxyAddresses* -attribut i den lokala katalogen. Detta *proxyAddress* -attribut stöder en eller flera e-postadresser.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Synkronisera inloggnings-e-postadresser till Azure AD

Traditionell Active Directory Domain Services-autentisering (AD DS) eller Active Directory Federation Services (AD FS) (AD FS) sker direkt i nätverket och hanteras av din AD DS-infrastruktur. Med hybrid autentisering kan användarna i stället logga in direkt till Azure AD.

För att stödja den här metoden för Hybrid autentisering, synkroniserar du din lokala AD DS-miljö till Azure AD med hjälp av [Azure AD Connect][azure-ad-connect] och konfigurerar den för att använda PHS (Password hash Sync) eller DIREKTAUTENTISERING (PTA).

I båda konfigurations alternativen skickar användaren sitt användar namn och lösen ord till Azure AD, som validerar autentiseringsuppgifterna och utfärdar en biljett. När användarna loggar in på Azure AD tar det bort behovet av att din organisation ska vara värd för och hantera en AD FS-infrastruktur.

![Diagram över Azure AD hybrid identitet med hash-synkronisering av lösen ord](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Diagram över Azure AD hybrid identitet med direktautentisering](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Ett av användarattribut som synkroniseras automatiskt av Azure AD Connect är *proxyAddresses*. Om användarna har en e-postadress som definierats i AD DS-lokal AD DS som en del av *proxyAddresses* -attributet, synkroniseras den automatiskt till Azure AD. Den här e-postadressen kan sedan användas direkt i inloggnings processen för Azure AD som ett alternativt inloggnings-ID.

> [!IMPORTANT]
> Endast e-postmeddelanden i verifierade domäner för klient organisationen synkroniseras med Azure AD. Varje Azure AD-klient har en eller flera verifierade domäner som du har beprövat ägarskap för och som är unikt kopplade till klient organisationen.
>
> Mer information finns i [lägga till och verifiera ett anpassat domän namn i Azure AD][verify-domain].

Mer information finns i [Välj rätt autentiseringsmetod för din Azure AD hybrid Identity-lösning][hybrid-auth-methods].

## <a name="enable-user-sign-in-with-an-email-address"></a>Aktivera användar inloggning med en e-postadress

När användare med attributet *proxyAddresses* som används synkroniseras till Azure AD med hjälp av Azure AD Connect, måste du aktivera funktionen för användare för att logga in med e-post som ett alternativt inloggnings-ID för din klient. Den här funktionen visar att Azure AD-inloggnings servrarna inte bara kontrollerar inloggnings namnet mot UPN-värden, utan även mot *proxyAddresses* -värden för e-postadressen.

Under för hands versionen kan du för närvarande endast aktivera inloggning med e-post som en alternativ inloggnings-ID-funktion med hjälp av PowerShell. Du måste ha *klient administratörs* behörighet för att utföra följande steg:

1. Öppna en PowerShell-session som administratör och installera sedan *AzureADPreview* -modulen med cmdleten [install-module][Install-Module] :

    ```powershell
    Install-Module AzureADPreview
    ```

    Om du uppmanas att göra det väljer du **Y** för att installera NuGet eller installera från en obetrodd lagrings plats.

1. Logga in på din Azure AD-klient som *innehavaradministratör* med cmdleten [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    Connect-AzureAD
    ```

    Kommandot returnerar information om ditt konto, din miljö och klient-ID.

1. Kontrol lera om *HomeRealmDiscoveryPolicy* -principen redan finns i din klient med hjälp av cmdleten [Get-AzureADPolicy][Get-AzureADPolicy] på följande sätt:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

1. Om det inte finns någon princip som är konfigurerad för tillfället returnerar kommandot ingenting. Om en princip returneras hoppar du över det här steget och går vidare till nästa steg för att uppdatera en befintlig princip.

    Om du vill lägga till *HomeRealmDiscoveryPolicy* -principen till klienten använder du cmdleten [New-AzureADPolicy][New-AzureADPolicy] och anger attributet *AlternateIdLogin* till *"Enabled": true* , vilket visas i följande exempel:

    ```powershell
    New-AzureADPolicy -Definition @('{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    När principen har skapats returnerar kommandot princip-ID: t, som visas i följande exempel på utdata:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Om det redan finns en konfigurerad princip kontrollerar du om attributet *AlternateIdLogin*   har Aktiver ATS, som du ser i följande exempel princip utdata:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Om principen finns men attributet *AlternateIdLogin* som inte finns eller är aktiverat, eller om det finns andra attribut på den princip som du vill behålla, uppdaterar du den befintliga principen med cmdleten [set-AzureADPolicy][Set-AzureADPolicy] .

    > [!IMPORTANT]
    > När du uppdaterar principen kontrollerar du att du inkluderar alla gamla inställningar och det nya *AlternateIdLogin* -attributet.

    I följande exempel lägger till attributet *AlternateIdLogin* och bevarar det *AllowCloudPasswordValidation* -attribut som redan har angetts:

    ```powershell
    Set-AzureADPolicy -id b581c39c-8fe3-4bb5-b53d-ea3de05abb4b `
        -Definition @('{"HomeRealmDiscoveryPolicy" :{"AllowCloudPasswordValidation":true,"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Bekräfta att den uppdaterade principen visar ändringarna och att attributet *AlternateIdLogin* nu är aktiverat:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="test-user-sign-in-with-email"></a>Testa användar inloggning med e-post

Om du vill testa att användarna kan logga in med e-post kan du bläddra till [https://myprofile.microsoft.com][my-profile] och logga in med ett användar konto baserat på deras e-postadress, till exempel `balas@fabrikam.com` , inte deras UPN, till exempel `balas@contoso.com` . Inloggnings upplevelsen bör se ut och kännas likadan som med en UPN-baserad inloggnings händelse.

## <a name="troubleshoot"></a>Felsöka

Om användarna har problem med inloggnings händelser med hjälp av e-postadressen kan du läsa följande fel söknings steg:

1. Se till att användar kontot har sina e-postadresser för attributet *proxyAddresses* i AD DS-miljön för lokal.
1. Kontrol lera att Azure AD Connect har kon figurer ATS och synkroniserar användar konton från AD DS-lokal i Azure AD.
1. Bekräfta att Azure AD *HomeRealmDiscoveryPolicy* -principen har attributet *AlternateIdLogin* inställt på *"Enabled": true*:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hybrid identitet, till exempel Azure AD App proxy eller Azure AD Domain Services, se [Azure AD hybrid identitet för åtkomst och hantering av lokal arbets belastningar][hybrid-overview].

För ytterligare information om hybrid identitets åtgärder, se [hur synkronisering av lösen ord för hash-synkronisering][phs-overview] eller [vidarekoppling][pta-overview] fungerar.

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com
