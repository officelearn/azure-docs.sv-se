---
title: Telefonin loggning och inloggning med anpassade principer
titleSuffix: Azure AD B2C
description: Lär dig hur du skickar eng ång slö sen ord i textmeddelanden till dina program användares telefoner med anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8cb0340d9e04db2bfbf088bce9505351d7588cd9
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840340"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>Konfigurera telefonin loggning och inloggning med anpassade principer i Azure AD B2C

Med telefonin loggning och inloggning i Azure Active Directory B2C (Azure AD B2C) kan användarna registrera sig och logga in på dina program genom att använda eng ång slö sen ord (eng ång slö sen ord) som skickas i ett textmeddelande till sin telefon. Eng ång slö sen ord kan minimera risken för att användarna forgetting eller har sina lösen ord komprometterade.

Följ stegen i den här artikeln för att använda anpassade principer för att låta dina kunder registrera sig och logga in på dina program genom att använda ett eng ång slö sen ord som skickas till telefonen.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Krav

* [Azure AD B2C klient](tutorial-create-tenant.md)
* [Webb program](tutorial-register-applications.md) som är registrerat i din klient
* [Anpassade principer](custom-policy-get-started.md) har laddats upp till din klient

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Hämta inloggnings start paketet för telefon &

Börja med att uppdatera de anpassade principerna för registrering av telefon och inloggning för att arbeta med din Azure AD B2C-klient.

Följande steg förutsätter att du har slutfört [kraven](#prerequisites) och redan klonat den [anpassade principens start paket][starter-pack] lagrings plats till den lokala datorn.

1. Hitta de [anpassade principerna för registrering och inloggning][starter-pack-phone] i din lokala kloning av start paketets lagrings platsen eller ladda ned dem direkt. XML-principfiler finns i följande katalog:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. I varje fil ersätter du strängen `yourtenant` med namnet på din Azure AD B2C-klient. Om namnet på din B2C-klient till exempel är *contosob2c*, blir alla instanser av `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com`.

1. Slutför stegen i avsnittet [Lägg till program-ID: n i avsnittet anpassad princip](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) i [komma igång med anpassade principer i Azure Active Directory B2C](custom-policy-get-started.md). I det här fallet uppdaterar du `/phone-number-passwordless/` **`Phone_Email_Base.xml`** med **program-ID: n** för de två program som du registrerade när du slutförde kraven, *IdentityExperienceFramework* och *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Ladda upp principfiler

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till Azure AD B2C-klienten.
1. Under **principer**väljer du **Identity Experience Framework**.
1. Välj **överför anpassad princip**.
1. Ladda upp principfiler i följande ordning:
    1. *Phone_Email_Base. XML*
    1. *SignUpOrSignInWithPhone. XML*
    1. *SignUpOrSignInWithPhoneOrEmail. XML*
    1. *ProfileEditPhoneOnly. XML*
    1. *ProfileEditPhoneEmail. XML*
    1. *ChangePhoneNumber. XML*
    1. *PasswordResetEmail. XML*

När du överför varje fil lägger Azure till prefixet `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. Under **anpassade principer**väljer du **B2C_1A_SignUpOrSignInWithPhone**.
1. Under **Välj program**väljer du det *webapp1* -program som du registrerade när du slutförde kraven.
1. För **Välj svars-URL**väljer du `https://jwt.ms`.
1. Välj **Kör nu** och registrera dig med en e-postadress eller ett telefonnummer.
1. Välj **Kör nu** en gång och logga in med samma konto för att kontrol lera att du har rätt konfiguration.

## <a name="next-steps"></a>Nästa steg

Du hittar registrerings-och inloggnings paketet för den anpassade principen för telefonsamtal (och andra start paket) på GitHub:

[Azure-samples/Active-Directory-B2C-Custom-policy-starterpack/scenarier/Phone-Number-passwordable][starter-pack-phone]

Princip filen för start paket använder Multi-Factor Authentication-tekniska profiler och antal anspråks omvandlingar:

* [Definiera en teknisk profil för Azure Multi-Factor Authentication](multi-factor-auth-technical-profile.md)
* [Definiera anspråks omvandlingar för telefonnummer](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
