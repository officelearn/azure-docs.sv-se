---
title: "Ändringar av Azure AD v2.0-slutpunkten | Microsoft Docs"
description: "En beskrivning av ändringar som görs i app model v2.0 förhandsversion protokoll."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: e6c5b891-0b5d-47dc-8184-5d0ef6a1a006
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 3d344529d1ac9891e5a961de630dea3c1e4caab4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="important-updates-to-the-v20-authentication-protocols"></a>Viktiga uppdateringar till v2.0-autentiseringsprotokoll
Uppmärksamhet utvecklare! Under de kommande två veckorna kommer vi att göra några uppdateringar till vår v2.0-autentiseringsprotokoll som kan innebära att bryta ändringar för alla program som du har skrivit under våra förhandsversionen.  

## <a name="who-does-this-affect"></a>Som påverkar detta?
Alla appar som har skrivits för att använda v2.0 konvergerat autentisering slutpunkt

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

Mer information om v2.0-slutpunkten kan hittas [här](active-directory-appmodel-v2-overview.md).

Om du har skapat en app med v2.0-slutpunkten genom att skriva direkt till v2.0-protokollet bör med hjälp av vår OpenID Connect och OAuth web middlewares eller använda andra 3 part-bibliotek för att utföra autentisering, du förberedas att testa dina projekt och göra ändringar Om det behövs.

## <a name="who-doesnt-this-affect"></a>Som detta påverkar inte?
Alla appar som har skrivits mot autentiseringsslutpunkten produktion Azure AD

```
https://login.microsoftonline.com/common/oauth2/authorize
```

Det här protokollet anges i bricka och ska inte ha några ändringar.

Dessutom om din app **endast** använder våra ADAL-biblioteket för att utföra autentisering, du behöver ändra något.  ADAL har skärmad appen från ändringarna.  

## <a name="what-are-the-changes"></a>Vilka är ändringarna?
### <a name="removing-the-x5t-value-from-jwt-headers"></a>Ta bort värdet x5t från JWT rubriker
V2.0-slutpunkten använder JWT-token i stor utsträckning, som innehåller ett huvud parametrar avsnitt med relevanta metadata om token.  Om du avkoda rubriken för en av våra nuvarande JWTs hittade något som liknar:

```
{ 
    "type": "JWT",
    "alg": "RS256",
    "x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
    "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Där både ”x5t” och ”barn” identifiera den offentliga nyckeln som ska användas för att validera token signatur, som hämtas från metadataslutpunkten OpenID Connect.

Ändra vi gör här är att ta bort egenskapen ”x5t”.  Du kan fortsätta att använda samma mekanismer för att validera token, men bör enbart använda egenskapen ”barn” för att hämta rätt offentlig nyckel som anges i protokollet OpenID Connect. 

> [!IMPORTANT]
> **Jobbet: Kontrollera att din app inte beror på förekomsten av x5t-värdet.**
> 
> 

### <a name="removing-profileinfo"></a>Ta bort profile_info
Tidigare v2.0-slutpunkten har tagits returnerar ett JSON-objekt med base64-kodade i token svar som anropas `profile_info`.  När du begär en åtkomst-token från v2.0-slutpunkten genom att skicka en begäran om att:

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Svaret ser ut som följande JSON-objekt:

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Den `profile_info` värdet finns information om den användare som signerade i app - deras visningsnamn, förnamn, efternamn, e-postadress, identifierare och så vidare.  Främst i `profile_info` användes för cachelagring av token och visa syften.

Vi nu bort den `profile_info` värdet – men oroa dig inte, vi fortfarande tillhandahåller denna information för utvecklare i en något annan plats.  I stället för `profile_info`, v2.0-slutpunkten nu returnerar ett `id_token` i varje token svar:

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Du kan avkoda och parsa id_token för att hämta samma information som du har fått från profile_info.  Id_token är en JSON-Webbtoken (JWT), med innehåll som anges av OpenID Connect.  Koden för detta så bör vara mycket lik – du behöver att extrahera id_token mellersta segmentet (brödtext) och base64 avkoda komma åt JSON-objekt i.

Under de kommande två veckorna bör du skapa kod din app att hämta informationen från antingen den `id_token` eller `profile_info`, beroende på vad som finns.  På så sätt när ändringen har gjorts kan din app kan sömlöst hantera övergången från `profile_info` till `id_token` utan avbrott.

> [!IMPORTANT]
> **Jobbet: Kontrollera att din app inte beror på förekomsten av den `profile_info` värde.**
> 
> 

### <a name="removing-idtokenexpiresin"></a>Ta bort id_token_expires_in
Liknar `profile_info`, vi också bort den `id_token_expires_in` parametern från svar.  Tidigare v2.0-slutpunkten kan returnera ett värde för `id_token_expires_in` tillsammans med varje id_token svar, till exempel på ett auktorisera svar:

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

Eller i ett token svar:

```
{ 
    "token_type": "Bearer",
    "id_token_expires_in": 3599,
    "scope": "openid",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Den `id_token_expires_in` värdet anger hur många sekunder som id_token gälla för.  Nu ska vi bort den `id_token_expires_in` värdet helt.  Du kan i stället använda OpenID Connect-standard `nbf` och `exp` utger sig för att kontrollera giltigheten för en id_token.  Finns det [v2.0 tokenreferens](active-directory-v2-tokens.md) mer information om dessa anspråk.

> [!IMPORTANT]
> **Jobbet: Kontrollera att din app inte beror på förekomsten av den `id_token_expires_in` värde.**
> 
> 

### <a name="changing-the-claims-returned-by-scopeopenid"></a>Ändra de anspråk som returneras av omfånget = openid
Den här ändringen kommer att de viktigaste – faktum, påverkar nästan alla program som använder v2.0-slutpunkten.  Många program skicka begäranden till v2.0-slutpunkten med den `openid` omfång som:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

Idag, när användaren ger ditt medgivande för det `openid` omfång, appen tar emot en mängd information om användaren i den resulterande id_token.  Dessa anspråk kan innehålla användarens namn, prioriterade användarnamn, e-postadress, objekt-ID och mer.

I den här uppdateringen kan vi ändrar informationen som den `openid` omfång ger din appåtkomst till, att bättre comform med OpenID Connect-specifikationen.  Den `openid` omfång kommer endast att din app kan logga in användaren i och ta emot en appspecifika identifierare för användare i den `sub` anspråk för id_token.  Anspråk i en id_token med endast den `openid` omfång beviljas kommer att vara fritt från personligt identifierbar information.  Exempel id_token anspråk är:

```
{ 
    "aud": "580e250c-8f26-49d0-bee8-1c078add1609",
    "iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
    "iat": 1449520283,
    "nbf": 1449520283,
    "exp": 1449524183,
    "nonce": "12345",
    "sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
    "tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
    "ver": "2.0",
}
```

Om du vill hämta personligt identifierbar information (PII) om användaren i din app behöver din app och begär ytterligare behörighet från användaren.  Vi introducerar stöd för två nya scope från OpenID Connect-specifikationen – den `email` och `profile` scope – som gör att du gör.

* Den `email` scope är mycket enkelt – den kan din appåtkomst till användarens primära e-postadress via den `email` anspråk i id_token.  Observera att den `email` anspråk alltid visas inte i id_tokens – den endast ska ingå om sådana finns i användarens profil.
* Den `profile` omfång ger din appåtkomst till andra grundläggande information om användare – deras namn, prioriterade username, objekt-ID och så vidare.

Du kan code din app på en minimal avslöjande sätt – du kan be användaren för just uppsättningen information att din app kräver för att utföra sitt jobb.  Om du vill fortsätta få en fullständig uppsättning användarinformation som din app erhåller bör du inkludera alla tre scope i din auktoriseringsförfrågningar:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

Din app kan börja skicka den `email` och `profile` scope omedelbart och v2.0-slutpunkten accepterar dessa två scope och börja begär behörighet från användare efter behov.  Men ändringen i tolkning av den `openid` scope börjar inte gälla för några veckor.

> [!IMPORTANT]
> **Jobbet: lägga till den `profile` och `email` scope om information om användaren krävs för din app.**  Observera att ADAL innehåller båda dessa behörigheter i begäranden som standard. 
> 
> 

### <a name="removing-the-issuer-trailing-slash"></a>Ta bort utfärdaren avslutande snedstreck.
Tidigare utformades utfärdaren värdet som visas i token från v2.0-slutpunkten

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

Guid var där klient-ID för Azure AD-klienten som utfärdade token.  Med de här ändringarna blir utfärdaren värdet

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

i båda token och OpenID Connect discovery-dokumentet.

> [!IMPORTANT]
> **Jobbet: Kontrollera att din app accepterar utfärdaren värde både med och utan avslutande snedstreck vid verifiering av utfärdare.**
> 
> 

## <a name="why-change"></a>Varför ändra?
Huvudsyftet med introduktion till dessa ändringar är att vara kompatibel med OpenID Connect standard-specifikationen.  Genom OpenID Connect kompatibla, hoppas vi att minimera skillnaderna mellan integrera med Microsoft identity-tjänster och andra tjänster identitet i branschen.  Vi vill att utvecklare kan använda sina favorit öppen källkod autentiseringsbibliotek utan att behöva ändra biblioteken för att hantera Microsoft skillnader.

## <a name="what-can-you-do"></a>Vad kan du göra?
Från och med idag, kan du börja gör alla ändringar som beskrivs ovan.  Du bör omedelbart:

1. **Ta bort eventuella beroenden på den `x5t` huvud-parametern.**
2. **Hantera övergången från `profile_info` till `id_token` i token svar.**
3. **Ta bort eventuella beroenden på den `id_token_expires_in` parametern svar.**
4. **Lägg till den `profile` och `email` scope till din app om din app behöver grundläggande information.**
5. **Acceptera utfärdaren värden i token både med och utan avslutande snedstreck.**

Vår [v2.0-protokollet dokumentationen](active-directory-v2-protocols.md) har redan uppdaterats för att återspegla ändringarna, så att du kan använda den som en referens i hjälpa uppdatera din kod.

Om du har fler frågor om omfattningen av ändringarna gärna nå oss på Twitter på @AzureAD.

## <a name="how-often-will-protocol-changes-occur"></a>Hur ofta sker protokollet ändringar?
Vi förutser inte några ytterligare bryta ändras till autentiseringsprotokollet.  Vi avsiktligt paketering ändringarna i en versionen så att du inte behöver gå igenom den här typen av uppdateringen igen när snart.  Naturligtvis vi kommer att fortsätta att lägga till funktioner till Autentiseringstjänsten konvergerade v2.0 som du kan dra nytta av, men ändringarna bör vara tillsatsen och inte break befintlig kod.

Slutligen vill vi säga Tack för provat saker i förhandsversionen.  Insikter och erfarenheter från våra tidiga brukare har ovärderlig hittills och vi hoppas att du kommer att fortsätta att dela dina åsikter och idéer.

Kodning Grattis!

Microsoft Identity-delning

