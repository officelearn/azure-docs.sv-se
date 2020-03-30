---
title: Telefonanmäla och logga in med anpassade principer (förhandsversion)
titleSuffix: Azure AD B2C
description: Skicka engångslösenord (OTP) i textmeddelanden till programanvändarnas telefoner med anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eadac0e973b361b1fdee63dcc9cfa848a0b2bacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183966"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Konfigurera telefonanmälan och inloggning med anpassade principer i Azure AD B2C (förhandsversion)

Telefon registrering och inloggning i Azure Active Directory B2C (Azure AD B2C) gör det möjligt för dina användare att registrera dig och logga in på dina program med hjälp av ett engångslösenord (OTP) som skickas i ett textmeddelande till sin telefon. Engångslösenord kan minimera risken för att användarna glömmer eller får sina lösenord komprometterade.

Följ stegen i den här artikeln om du vill använda de anpassade principerna för att göra det möjligt för dina kunder att registrera sig och logga in på dina program med hjälp av ett engångslösenord som skickas till deras telefon.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>Prissättning

Engångslösenord skickas till användarna med hjälp av SMS och du kan debiteras för varje meddelande som skickas. Prisinformation finns i avsnittet **Separata avgifter i** Azure Active Directory [B2C-priser](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="prerequisites"></a>Krav

Du behöver följande resurser innan du ställer in yp.

* [Azure AD B2C-klient](tutorial-create-tenant.md)
* [Webbprogram som är registrerade](tutorial-register-applications.md) i din klientorganisation
* [Anpassade principer](custom-policy-get-started.md) som överförs till din klientorganisation

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Skaffa telefonens registrering & inloggningspaket

Börja med att uppdatera telefonuppstäckt och logga in anpassade principfiler för att arbeta med din Azure AD B2C-klient.

Följande steg förutsätter att du har slutfört [förutsättningarna](#prerequisites) och redan har klonat den [anpassade principstartpaketets][starter-pack] databas till den lokala datorn.

1. Hitta [telefonens registrering och logga in anpassade principfiler i][starter-pack-phone] din lokala klon av startpaketets repo, eller ladda ned dem direkt. XML-principfilerna finns i följande katalog:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. I varje fil ersätter du strängen `yourtenant` med namnet på din Azure AD B2C-klient. Om namnet på din B2C-klient är *contosob2c*blir `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com`alla förekomster av .

1. Slutför stegen i [Lägg till program-ID:er i avsnittet Anpassad princip](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) i Komma igång med anpassade principer i Azure Active Directory [B2C](custom-policy-get-started.md). I det här `/phone-number-passwordless/` **`Phone_Email_Base.xml`** fallet uppdaterar du med **application-ID:n för** de två program som du registrerade när du slutförde förutsättningarna, *IdentityExperienceFramework* och *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Ladda upp principfilerna

1. Logga in på [Azure-portalen](https://portal.azure.com) och navigera till din Azure AD B2C-klientorganisation.
1. Under **Principer**väljer du **Identity Experience Framework**.
1. Välj **Ladda upp anpassad princip**.
1. Ladda upp principfilerna i följande ordning:
    1. *Phone_Email_Base.xml*
    1. *Registrera Dig Registrera DigSignInWithPhone.xml*
    1. *Registrera Dig Registrera Dig Innan Du vill haPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfilRedigeradPhoneEmail.xml*
    1. *ÄndraPhoneNumber.xml*
    1. *PasswordResetEmail.xml*

När du laddar upp varje fil `B2C_1A_`lägger Azure till prefixet .

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. Under **Anpassade principer**väljer du **B2C_1A_SignUpOrSignInWithPhone**.
1. Under **Välj program**väljer du det *webapp1-program* som du registrerade när du slutförde förutsättningarna.
1. För **Välj svarsadress**väljer du `https://jwt.ms`.
1. Välj **Kör nu** och registrera dig med en e-postadress eller ett telefonnummer.
1. Välj **Kör nu** igen och logga in med samma konto för att bekräfta att du har rätt konfiguration.

## <a name="get-user-account-by-phone-number"></a>Hämta användarkonto per telefonnummer

En användare som registrerar sig med ett telefonnummer men inte tillhandahåller en återställningsadress registreras i din Azure AD B2C-katalog med sitt telefonnummer som sitt inloggningsnamn. Om användaren sedan vill ändra sitt telefonnummer måste supportteamet eller supportteamet först hitta sitt konto och sedan uppdatera sitt telefonnummer.

Du kan hitta en användare med deras telefonnummer (inloggningsnamn) med hjälp av [Microsoft Graph:](manage-user-accounts-graph-api.md)

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Ett exempel:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Nästa steg

Du hittar telefonens registrerings- och inloggningspaket för principen (och andra startpaket) på GitHub:

[Azure-Samples/active-directory-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless Azure-Samples/active-directory-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless Azure-Samples/active-directory-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless Azure-][starter-pack-phone]

Startpaketets principfiler använder tekniska profiler för multifaktorautentisering och omformningar av telefonnummeranspråk:

* [Definiera en teknisk profil för Azure Multi Factor Authentication](multi-factor-auth-technical-profile.md)
* [Definiera omvandlingar av telefonnummeranspråk](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
