---
title: Omdirigera URI-& url-begränsningar för svar – Microsofts identitetsplattform | Azure
description: Svara på URL:er/omdirigera URls-begränsningar & begränsningar
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656746"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Restriktioner och begränsningar för omdirigerings-URI/svars-URL

En omdirigerings-URI, eller svars-URL, är den plats som auktoriseringsservern skickar användaren till när appen har auktoriserats och har beviljats en auktoriseringskod eller åtkomsttoken. Koden eller token finns i omdirigerings-URI- eller svarstoken så det är viktigt att du registrerar rätt plats som en del av appregistreringsprocessen.

 Följande begränsningar gäller för svarsadresser:

    * Svars-URL:en måste `https`börja med schemat .
    * Svars-URL:en är skiftlägeskänslig. Ärendet måste matcha fallet med URL-sökvägen för det program som körs. Om programmet till exempel ingår som `.../abc/response-oidc`en del `.../ABC/response-oidc` av sökvägen ska du inte ange i svars-URL:en. Eftersom webbläsaren behandlar sökvägar som skiftlägeskänsliga `.../abc/response-oidc` kan cookies som är associerade med uteslutas om de omdirigeras till webbadressen för ärendeomatchade. `.../ABC/response-oidc`
    
## <a name="maximum-number-of-redirect-uris"></a>Maximalt antal omdirigerings-URI:er

I följande tabell visas det maximala antalet omdirigerings-URI:er som du kan lägga till när du registrerar appen.

| Konton som loggas in | Maximalt antal omdirigerings-URI:er | Beskrivning |
|--------------------------|---------------------------------|-------------|
| Microsofts arbets- eller skolkonton i en organisations Azure Active Directory-klientorganisation (Azure AD) | 256 | `signInAudience`fältet i programmanifestet är inställt på antingen *AzureADMyOrg* eller *AzureADMultipleOrgs* |
| Personliga Microsoft-konton och arbets- och skolkonton | 100 | `signInAudience`fältet i programmanifestet är inställt på *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maximal URI-längd

Du kan använda högst 256 tecken för varje omdirigera URI som du lägger till i en appregistrering.

## <a name="supported-schemes"></a>System som stöds
Azure AD-programmodellen stöder idag både HTTP- och HTTPS-scheman för appar som loggar in microsoft-arbets- eller skolkonton i alla organisationers Azure Active Directory-klientorganisation (Azure AD). Det `signInAudience` är fältet i programmanifestet är inställt på antingen *AzureADMyOrg* eller *AzureADMultipleOrgs*. För de appar som loggar in personliga Microsoft-konton `signInAudience` och arbets- och skolkonton (som är inställt på *AzureADandPersonalMicrosoftAccount)* tillåts endast HTTPS-schema.

> [!NOTE]
> Den nya [appregistreringsupplevelsen](https://go.microsoft.com/fwlink/?linkid=2083908) tillåter inte utvecklare att lägga till URI med HTTP-schema i användargränssnittet. Att lägga till HTTP-URI:er för appar som loggar in på arbets- eller skolkonton stöds endast via appmanifestredigeraren. Framöver kan nya appar inte använda HTTP-scheman i omdirigerings-URI:n. Äldre appar som innehåller HTTP-scheman i omdirigerings-URI:er fortsätter dock att fungera. Utvecklare måste använda HTTPS-scheman i omdirigerings-URI:erna.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Begränsningar med hjälp av ett jokertecken i URI:er

Jokertecken-URI:er, till exempel `https://*.contoso.com`, är praktiska men bör undvikas. Att använda jokertecken i omdirigerings-URI har säkerhetskonsekvenser. Enligt OAuth 2.0-specifikationen[(avsnitt 3.1.2 i RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)) måste en endpoint för omdirigering vara en absolut URI. 

Azure AD-programmodellen stöder inte uridik för uridik för appar som är konfigurerade för att logga in på personliga Microsoft-konton och arbets- eller skolkonton. Jokertecken-URI:er tillåts dock för appar som är konfigurerade för att logga in på arbets- eller skolkonton i en organisations Azure AD-klient idag. 
 
> [!NOTE]
> Den nya [appregistreringsupplevelsen](https://go.microsoft.com/fwlink/?linkid=2083908) tillåter inte utvecklare att lägga till urier med jokertecken i användargränssnittet. Att lägga till wilcard URI för appar som loggar in på arbets- eller skolkonton stöds endast via appmanifestredigeraren. Framöver kan nya appar inte använda jokertecken i omdirigera URI. Äldre appar som innehåller jokertecken i omdirigerings-URI:er fortsätter dock att fungera.

Om ditt scenario kräver mer omdirigera URI:er än den högsta tillåtna gränsen, i stället för att lägga till en URI för att omdirigera jokertecken, bör du överväga följande metod.

### <a name="use-a-state-parameter"></a>Använda en tillståndsparameter

Om du har ett antal underdomäner, och om ditt scenario kräver att du omdirigerar användare vid lyckad autentisering till samma sida där de startade, kan det vara till hjälp att använda en tillståndsparameter. 

I detta tillvägagångssätt:

1. Skapa en "delad" omdirigera URI per program för att bearbeta säkerhetstoken du får från auktoriseringsslutpunkten.
1. Ditt program kan skicka programspecifika parametrar (till exempel url för underdomäner där användaren har sitt ursprung eller något liknande varumärkesinformation) i tillståndsparametern. När du använder en tillståndsparameter ska du skydda dig mot CSRF-skydd enligt [avsnitt 10.12 i RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. De programspecifika parametrarna innehåller all information som behövs för att programmet ska rendera rätt upplevelse för användaren, det vill säga konstruera rätt programtillstånd. Slutpunkten för Azure AD-auktorisering tar bort HTML från tillståndsparametern så se till att du inte skickar HTML-innehåll i den här parametern.
1. När Azure AD skickar ett svar till den "delade" omdirigera URI, kommer det att skicka tillståndsparametern tillbaka till programmet.
1. Programmet kan sedan använda värdet i tillståndsparametern för att avgöra vilken URL som användaren ska skicka till vidare. Se till att du validerar för CSRF-skydd.

> [!NOTE]
> Med den här metoden kan en komprometterade klient ändra de ytterligare parametrar som skickas i tillståndsparametern och därmed omdirigera användaren till en annan URL, vilket är det [öppna omdirigerarhotet](https://tools.ietf.org/html/rfc6819#section-4.2.4) som beskrivs i RFC 6819. Därför måste klienten skydda dessa parametrar genom att kryptera tillståndet eller verifiera det på något annat sätt, till exempel validera domännamn i omdirigera URI mot token.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [programmanifestet](reference-app-manifest.md)
