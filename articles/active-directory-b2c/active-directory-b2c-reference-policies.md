---
title: 'Azure Active Directory B2C: Inbyggda principer | Microsoft Docs'
description: Ett ämne på expanderbara principramverk av Azure Active Directory B2C och hur du skapar olika principtyper
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/26/2017
ms.author: davidmu
ms.openlocfilehash: 424186a0acfe17cd7cb96f3ba7f8201e8b2b38ec
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C: Inbyggda principer


Expanderbara principramverk av Azure Active Directory (AD Azure) B2C är core styrkan hos tjänsten. Principer fullständigt beskriver konsumenten identitetsupplevelser som registrering, inloggning eller Redigera profil. Till exempel kan en princip för registrering du styra beteenden genom att konfigurera följande inställningar:

* Kontotyper (sociala konton som Facebook) eller lokala konton, till exempel e-postadresser som konsumenterna kan använda för att registrera dig för programmet
* Attribut (till exempel förnamn, postnummer och sko storlek) som ska samlas in från konsumenten under registreringen
* Användning av Azure Multi-Factor Authentication
* Utseendet och känslan av alla anmälan sidor
* Information (som visar som anspråk i en token) att programmet tar emot när principen Kör slutförs

Du kan skapa flera principer för olika typer i din klient och använda dem i dina program efter behov. Principer kan återanvändas i program. Den här flexibiliteten gör att utvecklare kan definiera och ändra konsumenten identitetsupplevelser med minimal eller utan ändringar av koden.

Principer kan användas via en enkel developer-gränssnittet. Programmet utlöser en princip med hjälp av en standard HTTP autentiseringsbegäran (skicka en princip för parameter i begäran) och får en anpassad token som svar. Till exempel är den enda skillnaden mellan förfrågningar som anropar en princip för registrering och förfrågningar som anropar en princip för inloggning principnamn som används i ”p” frågesträngparametern:

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
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

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
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

Den här principen hanterar upplevelser för registrering och inloggning i båda konsumenten med en enda konfiguration. Konsumenterna ledde till rätt sökvägen (registrering eller inloggning) beroende på kontext. Här beskrivs också innehållet i token som programmet ska ta emot lyckats logga in eller inloggningar.  En kodexempel för den **registrering eller inloggning** policy [tillgänglig här](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  Det rekommenderas att du använder den här principen via en **anmälan** princip eller en **inloggning** princip.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>Skapa en princip för registrering

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>Skapa en princip för inloggning

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>Skapa en profilredigeringsprincip

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>Skapa en princip för återställning av lösenord

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-policies"></a>Principer för förhandsgranskning

Som vi versionen av nya funktioner, kanske vissa av dessa inte tillgänglig på befintliga principer.  Vi planerar att ersätta äldre versioner med den senaste versionen av samma typ när dessa principer ange GA.  De befintliga principerna ändras inte och du behöver skapa nya principer för att kunna dra nytta av de nya funktionerna.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Hur länkar en princip för registrering eller inloggning med en princip för lösenordsåterställning?
När du skapar en **registrering eller inloggning** princip (med lokala konton) som du ser en **har du glömt lösenordet?** länk på den första sidan i miljön. Klicka på den här länken återställs inte utlösaren lösenord automatiskt princip. 

I stället felkoden **`AADB2C90118`** returneras till din app. Din app behöver hantera felet genom att aktivera en specifik princip för lösenordsåterställning. Mer information finns i en [exempel som visar metoden länka principer för](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>Bör jag använda en princip för registrering eller inloggning eller en princip för registrering och en princip för inloggning?
Vi rekommenderar att du använder en **registrering eller inloggning** principen via en **registrering** princip och en **inloggning** princip.  

Den **registrering eller inloggning** principen har fler funktioner än den **inloggning** princip. Dessutom kan du använda sidan anpassningar och har bättre stöd för lokalisering. 

Den **inloggning** princip rekommenderas om du inte behöver localize dina principer endast behöver mindre anpassningsmöjligheter för anpassning och vill lösenord återställning som är inbyggda i den.

## <a name="next-steps"></a>Nästa steg
* [Token, session och konfiguration för enkel inloggning](active-directory-b2c-token-session-sso.md)
* [Inaktivera e-Postverifiering under konsumenten registrering](active-directory-b2c-reference-disable-ev.md)

