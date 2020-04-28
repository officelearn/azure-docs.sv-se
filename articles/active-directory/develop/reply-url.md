---
title: Omdirigera URI & svar på URL-begränsningar – Microsoft Identity Platform | Azure
description: 'Svars-URL: er/omdirigera URL-adresser begränsningar & begränsningar'
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 8fdc64632be8b5fcb3dca8de2ee833fef25719fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77656746"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Restriktioner och begränsningar för omdirigerings-URI/svars-URL

En omdirigerings-URI eller svars-URL är den plats som auktoriseringsservern skickar användaren till när appen har godkänts och beviljats en auktoriseringskod eller åtkomsttoken. Koden eller token finns i omdirigerings-URI eller Reply-token, så det är viktigt att du registrerar rätt plats som en del av registrerings processen för appen.

 Följande begränsningar gäller för svars-URL: er:

    * Svars-URL: en måste börja `https`med schemat.
    * Svars-URL: en är Skift läges känslig. Dess fall måste matcha fallet med URL-sökvägen till det program som körs. Om ditt program t. ex. ingår som en del av `.../abc/response-oidc`sökvägen ska du inte `.../ABC/response-oidc` ange i svars-URL: en. Eftersom webbläsaren behandlar sökvägar som Skift läges känsliga, `.../abc/response-oidc` kan cookies som är kopplade till uteslutas om de omdirigeras till den Skift `.../ABC/response-oidc` läges fel matchnings bara URL: en.
    
## <a name="maximum-number-of-redirect-uris"></a>Maximalt antal omdirigerings-URI: er

Följande tabell visar det maximala antalet omdirigerings-URI: er som du kan lägga till när du registrerar din app.

| Konton som är inloggade | Maximalt antal omdirigerings-URI: er | Beskrivning |
|--------------------------|---------------------------------|-------------|
| Microsoft arbets-eller skol konton i en organisations Azure Active Directory-klient (Azure AD) | 256 | `signInAudience`fältet i applikations manifestet har angetts till antingen *AzureADMyOrg* eller *AzureADMultipleOrgs* |
| Personliga Microsoft-konton och arbets-och skol konton | 100 | `signInAudience`fältet i applikations manifestet har angetts till *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maximal URI-längd

Du kan använda högst 256 tecken för varje omdirigerings-URI som du lägger till i en app-registrering.

## <a name="supported-schemes"></a>Scheman som stöds
Azure AD-programmodellen stöder idag både HTTP-och HTTPS-scheman för appar som loggar in på Microsoft arbets-eller skol konton i en organisations Azure Active Directory-klient (Azure AD). `signInAudience` Fältet i applikations manifestet har angetts till antingen *AzureADMyOrg* eller *AzureADMultipleOrgs*. För appar som loggar in på personliga Microsoft-konton och arbets-och skol konton ( `signInAudience` som är inställt på *AzureADandPersonalMicrosoftAccount*) tillåts bara https-schemat.

> [!NOTE]
> Med den nya [Appregistreringars](https://go.microsoft.com/fwlink/?linkid=2083908) miljön kan utvecklare inte lägga till URI: er med http-schemat i användar gränssnittet. Det går bara att lägga till HTTP-URI: er för appar som loggar in på arbets-eller skol konton via appens manifest redigeraren. Nya appar kommer inte att kunna använda HTTP-scheman i omdirigerings-URI: n. Men äldre appar som innehåller HTTP-scheman i omdirigerings-URI: er fortsätter att fungera. Utvecklare måste använda HTTPS-scheman i omdirigerings-URI: er.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Begränsningar med jokertecken i URI: er

URI: er med `https://*.contoso.com`jokertecken, till exempel, är praktiska men bör undvikas. Att använda jokertecken i omdirigerings-URI: n har säkerhets effekter. Enligt OAuth 2,0-specifikationen ([Section 3.1.2 i RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)) måste en URI för omdirigerings slut punkt vara en absolut URI. 

Azure AD-programmodellen stöder inte URI-jokertecken för appar som är konfigurerade för att logga in på personliga Microsoft-konton och arbets-eller skol konton. URI: er för jokertecken tillåts dock för appar som har kon figurer ATS för att logga in på arbets-eller skol konton i en organisations Azure AD-klient idag. 
 
> [!NOTE]
> Med den nya [Appregistreringars](https://go.microsoft.com/fwlink/?linkid=2083908) miljön kan utvecklare inte lägga till jokertecken i användar gränssnittet. Det går bara att lägga till jokertecknet-URI för appar som loggar in på arbets-eller skol konton via appens manifest redigeraren. Nya appar kommer inte att kunna använda jokertecken i omdirigerings-URI: n. Men äldre appar som innehåller jokertecken i omdirigerings-URI: er fortsätter att fungera.

Om ditt scenario kräver fler omdirigerings-URI: er än max gränsen tillåts, i stället för att lägga till en omdirigerings-URI för jokertecken bör du tänka på

### <a name="use-a-state-parameter"></a>Använda en tillstånds parameter

Om du har ett antal under domäner och om ditt scenario kräver att du omdirigerar användare vid en lyckad autentisering till samma sida där de startade, kan det vara bra att använda en tillstånds parameter. 

I den här metoden:

1. Skapa en "delad" omdirigerings-URI per program för att bearbeta säkerhetstoken som du tar emot från behörighets slut punkten.
1. Ditt program kan skicka programspecifika parametrar (till exempel underdomäns-URL: en där användaren har sitt ursprung eller något som liknar varumärkes information) i status parametern. När du använder en tillstånds parameter skyddar du mot CSRF-skydd enligt vad som anges i [avsnitt 10,12 i RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. De programspecifika parametrarna innehåller all information som behövs för att programmet ska återge rätt upplevelse för användaren, det vill säga att skapa rätt program tillstånd. HTML AD Authorization Endpoint stripe-HTML från läges parametern ser till att du inte skickar HTML-innehåll i den här parametern.
1. När Azure AD skickar ett svar till omdirigerings-URI: n skickas status parametern tillbaka till programmet.
1. Programmet kan sedan använda värdet i parametern State för att avgöra vilken URL som användaren kan skicka till. Kontrol lera att du validerar för CSRF-skydd.

> [!NOTE]
> Med den här metoden kan en komprometterad klient ändra de ytterligare parametrar som skickas i parametern State, vilket innebär att användaren omdirigeras till en annan URL, vilket är det [Öppna omdirigeraren](https://tools.ietf.org/html/rfc6819#section-4.2.4) som beskrivs i RFC 6819. Därför måste klienten skydda dessa parametrar genom att kryptera statusen eller verifiera den på något annat sätt, till exempel Verifiera domän namn i omdirigerings-URI mot token.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [applikations manifestet](reference-app-manifest.md)
