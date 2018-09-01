---
title: Inbyggda principer i Azure Active Directory B2C | Microsoft Docs
description: Ett ämne på utökningsbart principramverk för Azure Active Directory B2C och om hur du skapar olika principtyper.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f26db8bcb50fa09a8d2829d477f90cac8c52533f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337582"
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C: Inbyggda principer


Utökningsbart principramverk för Azure Active Directory (Azure AD) B2C är core styrkan hos tjänsten. Principer helt beskriver konsument identitetsupplevelser som registrering, inloggning och profilredigering. Till exempel kan du med en registreringsprincip styra beteenden genom att konfigurera följande inställningar:

* Kontotyper (konton i sociala medier, till exempel Facebook) eller lokala konton, till exempel e-postadresser som användare kan använda för att registrera dig för programmet
* Attribut (till exempel förnamn, postnummer och sko storlek) ska samlas in från konsumenten under registreringen
* Användning av Azure Multi-Factor Authentication
* Utseendet på alla sidor i registrera dig
* Information (som manifest som anspråk i en token) som programmet tar emot när principen Kör har slutförts

Du kan skapa flera principer för olika typer i din klient och använda dem i dina program, vid behov. Principer kan återanvändas i program. Den här flexibiliteten gör att utvecklare kan definiera och ändra konsument identitetsupplevelser med minimala kodändringar eller inga ändringar i koden.

Principer kan användas via en enkel developer-gränssnittet. Programmets utlöser en princip med hjälp av en standard HTTP-autentiseringsbegäran (skicka en principparametern i begäran) och får en anpassad token som svar. Den enda skillnaden mellan begäranden som anropar en registreringsprincip och begäranden som anropar en inloggningsprincip är till exempel namnet på principen som används i ”p” för frågesträngparametern:

```

https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

## <a name="create-a-sign-up-or-sign-in-policy"></a>Skapa en registrerings- eller inloggningsprincip

Den här principen hanterar upplevelser för registrering och inloggning i både konsument med en enda konfiguration. Konsumenter leds av rätt väg (registrera dig eller logga in) beroende på kontext. Här beskrivs också innehållet i de token som programmet tar emot vid genomförda registreringar eller inloggningar.  Ett kodexempel för den **registrering eller inloggning** principen är [tillgänglig här](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  Vi rekommenderar att du använder den här principen över en **registrering** princip eller en **inloggning** princip.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>Skapa en registreringsprincip

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>Skapa en inloggningsprincip

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>Skapa en profilredigeringsprincip

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>Skapa en princip för återställning av lösenord

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-policies"></a>Förhandsversion av principer

Allteftersom vi släpper nya funktioner, kanske vissa av dessa inte tillgänglig på befintliga principer.  Vi planerar att ersätta äldre versioner med senast av samma typ när dessa principer anger GA.  De befintliga principerna ändras inte och du behöver skapa nya principer för att kunna dra nytta av de här nya funktionerna.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Hur länkar jag en princip för registrering eller inloggning med en princip för återställning av lösenord?
När du skapar en **registrering eller inloggning** princip (med lokala konton), som du ser en **har du glömt lösenordet?** länk på första sidan i miljön. Klicka på den här länken återställs inte utlösare ett lösenord automatiskt principen. 

I stället felkoden **`AADB2C90118`** returneras till din app. Din app behöver hantera felet genom att aktivera en specifik princip för lösenordsåterställning. Mer information finns i en [exempel som visar metoden för länkning principer](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>Ska jag använda en princip för registrering eller inloggning eller en registreringsprincip och en inloggningsprincip?
Vi rekommenderar att du använder en **registrering eller inloggning** princip över en **registrering** princip och en **inloggning** princip.  

Den **registrering eller inloggning** principen har fler funktioner än den **inloggning** princip. Dessutom kan du använda sidan anpassningar och har bättre stöd för lokalisering. 

Den **inloggning** princip rekommenderas om du inte behöver att hitta dina principer endast måste mindre anpassningsmöjligheter för anpassning och vill lösenord återställning som är inbyggda i den.

## <a name="next-steps"></a>Nästa steg
* [Token-, sessions- och konfiguration för enkel inloggning](active-directory-b2c-token-session-sso.md)
* [Inaktivera e-Postverifiering under konsument registrering](active-directory-b2c-reference-disable-ev.md)

