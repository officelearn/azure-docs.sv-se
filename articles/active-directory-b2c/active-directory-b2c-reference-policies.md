---
title: Inbyggda principer i Azure Active Directory B2C | Microsoft Docs
description: Ett ämne på utökningsbart principramverk för Azure Active Directory B2C och om hur du skapar olika principtyper.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4a0dfcbba1867d4792a0e4a383ee097df0ea410b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835819"
---
# <a name="azure-active-directory-b2c-user-flows"></a>Azure Active Directory B2C: Användarflöden


Utökningsbart principramverk för Azure Active Directory (Azure AD) B2C är core styrkan hos tjänsten. Principer helt beskriver konsument identitetsupplevelser som registrering, inloggning och profilredigering. Som hjälper dig att konfigurera de vanligaste uppgifterna för identitet, Azure AD B2C-portalen innehåller fördefinierade, konfigureras principer som kallas **användarflöden**. Exempelvis kan låter en registrering användarflödet dig styra beteenden genom att konfigurera följande inställningar:

* Kontotyper (konton i sociala medier, till exempel Facebook) eller lokala konton, till exempel e-postadresser som användare kan använda för att registrera dig för programmet
* Attribut (till exempel förnamn, postnummer och sko storlek) ska samlas in från konsumenten under registreringen
* Användning av Azure Multi-Factor Authentication
* Utseendet på alla sidor i registrera dig
* Information (som manifest som anspråk i en token) som programmet tar emot när användarflödet kör har slutförts

Du kan skapa flera användarflöden av olika typer i din klient och använda dem i dina program, vid behov. Användarflöden kan återanvändas i program. Den här flexibiliteten gör att utvecklare kan definiera och ändra konsument identitetsupplevelser med minimala kodändringar eller inga ändringar i koden.

Användarflöden kan användas via en enkel developer-gränssnittet. Programmets utlöser ett användarflöde med hjälp av en standard HTTP-autentiseringsbegäran (skicka en parameter för flödet av användaren i begäran) och får en anpassad token som svar. Till exempel är den enda skillnaden mellan begäranden som anropar en registrering användarflödet och begäranden som anropar en inloggning användarflödet userjourney-namnet som används i ”p” för frågesträngparametern:

```

https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow

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
&p=b2c_1_siin                                       // Your sign-in user flow

```

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Skapa ett användarflöde för registrerings- eller logga in

Det här användarflödet hanterar upplevelser för registrering och inloggning i både konsument med en enda konfiguration. Konsumenter leds av rätt väg (registrera dig eller logga in) beroende på kontext. Här beskrivs också innehållet i de token som programmet tar emot vid genomförda registreringar eller inloggningar.  Ett kodexempel för den **registrering eller inloggning** användarflöde är [tillgänglig här](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  Vi rekommenderar att du använder det här användarflödet över en **registrering** användarflödet eller en **inloggning** användarflödet.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-user-flow"></a>Skapa en registrering användarflödet

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-user-flow"></a>Skapa en inloggning användarflödet

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-user-flow"></a>Skapa en profil som redigera användarflödet

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-user-flow"></a>Skapa ett användarflöde för återställning av lösenord

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-user-flows"></a>Förhandsgranskning – användarflöden

Allteftersom vi släpper nya funktioner, kanske vissa av dessa inte tillgänglig på befintliga principer eller användarflöden.  Vi planerar att ersätta äldre versioner med senast av samma typ när dessa användarflöden ange GA.  Din befintliga principer eller användarflöden ändras inte och du behöver skapa nya användarflöden för att kunna dra nytta av de här nya funktionerna.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-do-i-link-a-sign-up-or-sign-in-user-flow-with-a-password-reset-user-flow"></a>Hur länkar jag en registrering eller inloggning användarflödet med ett användarflöde för återställning av lösenord?
När du skapar en **registrering eller inloggning** användaren flöde (med lokala konton), visas en **har du glömt lösenordet?** länk på första sidan i miljön. Klicka på den här länken återställs inte utlösa ett lösenord automatiskt användarflödet. 

I stället felkoden **`AADB2C90118`** returneras till din app. Din app behöver hantera felet genom att aktivera ett särskilt lösenordsåterställning användarflöde. Mer information finns i en [exempel som visar metoden för länkning användarflöden](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-user-flow-or-a-sign-up-user-flow-and-a-sign-in-user-flow"></a>Ska jag använda en registrering eller inloggning användarflödet eller en registrering användarflödet och logga in användarflödet?
Vi rekommenderar att du använder en **registrering eller inloggning** användarflödet över en **registrering** användarflödet och en **inloggning** användarflödet.  

Den **registrering eller inloggning** användarflödet har fler funktioner än den **inloggning** användarflödet. Dessutom kan du använda sidan anpassningar och har bättre stöd för lokalisering. 

Den **inloggning** användarflödet rekommenderas om du inte behöver du kanske lokalisera din användarflöden endast måste mindre anpassningsmöjligheter för anpassning och vill lösenord återställning som är inbyggda i den.

## <a name="next-steps"></a>Nästa steg
* [Token-, sessions- och konfiguration för enkel inloggning](active-directory-b2c-token-session-sso.md)
* [Inaktivera e-Postverifiering under konsument registrering](active-directory-b2c-reference-disable-ev.md)

