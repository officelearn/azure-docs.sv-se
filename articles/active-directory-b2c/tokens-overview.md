---
title: Översikt över tokens - Azure Active Directory B2C
description: Lär dig mer om de token som används i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cbbd083a6b62733d71c316af95dffaa188b28955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186496"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Översikt över token i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) avger flera typer av säkerhetstoken när den bearbetar varje [autentiseringsflöde](application-types.md). I det här dokumentet beskrivs format, säkerhetsegenskaper och innehåll för varje typ av token.

## <a name="token-types"></a>Tokentyper

Azure AD B2C stöder [OAuth 2.0- och OpenID Connect-protokollen](protocols-overview.md), som använder token för autentisering och säker åtkomst till resurser. Alla token som används i Azure AD B2C är [JSON-webbtoken (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) som innehåller påståenden om information om innehavaren och ämnet för token.

Följande token används i kommunikation med Azure AD B2C:

- *ID-token* - En JWT som innehåller anspråk som du kan använda för att identifiera användare i ditt program. Den här token skickas säkert i HTTP-begäranden om kommunikation mellan två komponenter i samma program eller tjänst. Du kan använda anspråken i en ID-token som du vill. De används ofta för att visa kontoinformation eller för att fatta beslut om åtkomstkontroll i ett program. ID-token är signerade, men de är inte krypterade. När ditt program eller API tar emot en ID-token måste den validera signaturen för att bevisa att token är äkta. Ditt program eller API måste också validera några anspråk i token för att bevisa att det är giltigt. Beroende på scenariokraven kan anspråk som valideras av ett program variera, men ditt program måste utföra några vanliga anspråksvalideringar i varje scenario.
- *Åtkomsttoken* – En JWT som innehåller anspråk som du kan använda för att identifiera de beviljade behörigheterna till dina API:er. Åtkomsttoken är signerade, men de är inte krypterade. Åtkomsttoken används för att ge åtkomst till API:er och resursservrar.  När ditt API tar emot en åtkomsttoken måste den validera signaturen för att bevisa att token är äkta. Ditt API måste också validera några anspråk i token för att bevisa att det är giltigt. Beroende på scenariokraven kan anspråk som valideras av ett program variera, men ditt program måste utföra några vanliga anspråksvalideringar i varje scenario.
- *Uppdatera token* - Uppdatera token används för att hämta nya ID-token och åtkomsttoken i ett OAuth 2.0-flöde. De ger ditt program långsiktig åtkomst till resurser för användarnas räkning utan att kräva interaktion med dessa användare. Uppdatera token är ogenomskinliga för ditt program. De utfärdas av Azure AD B2C och kan endast inspekteras och tolkas av Azure AD B2C. De är långlivade, men ditt program bör inte skrivas med förväntningen att en uppdateringstoken kommer att pågå under en viss tidsperiod. Uppdatera token kan ogiltigförklaras när som helst av olika skäl. Det enda sättet för ditt program att veta om en uppdateringstoken är giltig är att försöka lösa in den genom att göra en tokenbegäran till Azure AD B2C. När du löser in en uppdateringstoken för en ny token får du en ny uppdateringstoken i tokensvaret. Spara den nya uppdateringstoken. Den ersätter den uppdateringstoken som du tidigare använde i begäran. Den här åtgärden hjälper till att garantera att dina uppdateringstoken förblir giltiga så länge som möjligt.

## <a name="endpoints"></a>Slutpunkter

Ett [registrerat program](tutorial-register-applications.md) tar emot token och kommunicerar med Azure AD B2C genom att skicka begäranden till dessa slutpunkter:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

Säkerhetstoken som ditt program tar emot från Azure `/authorize` `/token` AD B2C kan komma från slutpunkterna eller. När ID-token hämtas `/authorize` från slutpunkten görs det med det [implicita flödet](implicit-flow-single-page-application.md), som ofta används för användare som loggar in på JavaScript-baserade webbprogram. När ID-token hämtas `/token` från slutpunkten görs det med hjälp av [auktoriseringskodflödet](openid-connect.md#get-a-token), vilket håller token dold från webbläsaren.

## <a name="claims"></a>Anspråk

När du använder Azure AD B2C har du finkornig kontroll över innehållet i dina tokens. Du kan konfigurera [användarflöden](user-flow-overview.md) och [anpassade principer](custom-policy-overview.md) för att skicka vissa uppsättningar med användardata i anspråk som krävs för ditt program. Dessa anspråk kan innehålla standardegenskaper som **displayName** och **emailAddress**. Dina program kan använda dessa anspråk för att autentisera användare och begäranden på ett säkert sätt.

Anspråken i ID-token returneras inte i någon särskild ordning. Nya anspråk kan introduceras i ID-token när som helst. Din ansökan bör inte brytas när nya anspråk införs. Du kan också inkludera [anpassade användarattribut](user-flow-custom-attributes.md) i dina anspråk.

I följande tabell visas de anspråk som du kan förvänta dig i ID-token och åtkomsttoken som utfärdats av Azure AD B2C.

| Namn | Begär | Exempelvärde | Beskrivning |
| ---- | ----- | ------------- | ----------- |
| Målgrupp | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifierar den avsedda mottagaren av token. För Azure AD B2C är målgruppen program-ID. Ditt program bör validera det här värdet och avvisa token om den inte matchar. Publiken är synonymt med resurs. |
| Utfärdare | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifierar säkerhetstokentjänsten (STS) som konstruerar och returnerar token. Den identifierar också katalogen där användaren autentiserades. Ditt program bör validera utfärdarens anspråk för att se till att token kom från rätt slutpunkt. |
| Utfärdat på | `iat` | `1438535543` | Den tidpunkt då token utfärdades, representerad i epoktid. |
| Utgångstid | `exp` | `1438539443` | Den tidpunkt då token blir ogiltig, representerad i epoktid. Ditt program bör använda det här anspråket för att verifiera giltigheten för tokenlivslängden. |
| Inte före | `nbf` | `1438535543` | Den tidpunkt då token blir giltig, representerad i epoktid. Den här gången är vanligtvis samma som den tidpunkt då token utfärdades. Ditt program bör använda det här anspråket för att verifiera giltigheten för tokenlivslängden. |
| Version | `ver` | `1.0` | Versionen av ID-token, som definieras av Azure AD B2C. |
| Kodh hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | En kodhh som ingår i en ID-token endast när token utfärdas tillsammans med en OAuth 2.0-auktoriseringskod. En kodhh kan användas för att verifiera äktheten av en auktoriseringskod. Mer information om hur du utför den här valideringen finns i [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Åtkomsttokenhh | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | En åtkomsttokenhh som ingår i en ID-token endast när token utfärdas tillsammans med en OAuth 2.0-åtkomsttoken. En åtkomsttokenhh kan användas för att verifiera äktheten hos en åtkomsttoken. Mer information om hur du utför den här valideringen finns i [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | En nonce är en strategi som används för att minska token repris attacker. Ditt program kan ange en nonce i `nonce` en auktoriseringsbegäran med hjälp av frågeparametern. Värdet som du anger i begäran avges `nonce` inte ändras i anspråk på en ID-token. Med det här anspråket kan ditt program verifiera värdet mot det värde som anges på begäran. Programmet ska utföra den här valideringen under valideringsprocessen för ID-token. |
| Subjekt | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Huvudbeloppet som token bekräftar information om, till exempel användaren av ett program. Det här värdet är oföränderligt och kan inte tilldelas om eller återanvändas. Den kan användas för att utföra auktoriseringskontroller på ett säkert sätt, till exempel när token används för att komma åt en resurs. Som standard fylls ämnesanspråket med användarens objekt-ID i katalogen. |
| Referens för autentiseringskontextklass | `acr` | Inte tillämpligt | Används endast med äldre principer. |
| Politik för förtroenderamar | `tfp` | `b2c_1_signupsignin1` | Namnet på principen som användes för att hämta ID-token. |
| Autentiseringstid | `auth_time` | `1438535543` | Den tidpunkt då en användare senast angav autentiseringsuppgifter, representerad i epoktid. Det finns ingen diskriminering mellan att autentisering är en ny inloggning, en enda inloggningssession (SSO) eller en annan inloggningstyp. Det `auth_time` är sista gången programmet (eller användaren) initierade ett autentiseringsförsök mot Azure AD B2C. Den metod som används för att autentisera är inte differentierad. |
| Omfång | `scp` | `Read`| De behörigheter som beviljas resursen för en åtkomsttoken. Flera beviljade behörigheter avgränsas med ett blanksteg. |
| Godkänd part | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | **Program-ID** för klientprogrammet som initierade begäran. |

## <a name="configuration"></a>Konfiguration

Följande egenskaper används för att [hantera livstider för säkerhetstoken](configure-tokens.md) som avges av Azure AD B2C:

- **Åtkomst & ID-tokenlivstid (minuter)** - Livslängden för OAuth 2.0-innehavartoken som används för att få åtkomst till en skyddad resurs. Standardvärdet är 60 minuter. Det minsta (inklusive) är 5 minuter. Den maximala (inklusive) är 1440 minuter.

- **Uppdatera tokenlivstid (dagar)** - Den maximala tidsperioden före vilken en uppdateringstoken kan användas för att hämta en ny åtkomst eller ID-token. Tidsperioden omfattar även att hämta en ny uppdateringstoken om ditt program har beviljats scopet. `offline_access` Standardvärdet är 14 dagar. Det minsta (inklusive) är en dag. Den maximala (inklusive) är 90 dagar.

- **Uppdatera token glidande fönster livstid (dagar)** - Efter denna tidsperiod förflutit användaren tvingas att återuthenticate, oavsett giltighetsperioden för den senaste uppdateringstoken som förvärvats av programmet. Det kan endast tillhandahållas om växeln är inställd **på Begränsad**. Den måste vara större än eller lika med värdet **uppdatera tokenlivslängd (dagar).** Om växeln är inställd på **Obunden**kan du inte ange ett specifikt värde. Standardvärdet är 90 dagar. Det minsta (inklusive) är en dag. Den maximala (inklusive) är 365 dagar.

Följande användningsfall är aktiverade med hjälp av dessa egenskaper:

- Tillåt att en användare förblir inloggad på en mobilapp på obestämd tid, så länge användaren är kontinuerligt aktiv i programmet. Du kan ställa in livslängden för **skjutfönstret Uppdatera token (dagar)** till Obegränsad i ditt **inloggningsanvändarflöde.**
- Uppfylla branschens säkerhets- och efterlevnadskrav genom att ange lämpliga åtkomsttokens livstider.

Dessa inställningar är inte tillgängliga för användarflöden för återställning av lösenord.

## <a name="compatibility"></a>Kompatibilitet

Följande egenskaper används för att [hantera tokenkompatibilitet:](configure-tokens.md)

- **Utfärdare (iss) anspråk** - Den här egenskapen identifierar Azure AD B2C-klienten som utfärdade token. Standardvärdet är `https://<domain>/{B2C tenant GUID}/v2.0/`. Värdet för `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` inkluderar ID:er för både Azure AD B2C-klienten och användarflödet som användes i tokenbegäran. Om ditt program eller bibliotek behöver Azure AD B2C för att vara kompatibelt med [OpenID Connect Discovery 1.0-specifikationen](https://openid.net/specs/openid-connect-discovery-1_0.html)använder du det här värdet.

- **Ämnesanspråk (under) anspråk** - Den här egenskapen identifierar den enhet för vilken token bekräftar information. Standardvärdet är **ObjectID**, som `sub` fyller i anspråket i token med användarens objekt-ID. Värdet **För** bakåtkompatibilitet anges inte. Vi rekommenderar att du byter till **ObjectID** så snart du kan.

- **Anspråk som representerar princip-ID** - Den här egenskapen identifierar den anspråkstyp som principnamnet som används i tokenbegäran är ifyllt. Standardvärdet är `tfp`. Värdet av `acr` anges endast för bakåtkompatibilitet.

## <a name="pass-through"></a>Direkt

När en användarresa startar får Azure AD B2C en åtkomsttoken från en identitetsprovider. Azure AD B2C använder den token för att hämta information om användaren. Du [aktiverar ett anspråk i ditt användarflöde](idp-pass-through-user-flow.md) eller [definierar ett anspråk i din anpassade princip](idp-pass-through-custom.md) för att skicka token till de program som du registrerar i Azure AD B2C. Ditt program måste använda ett [v2-användarflöde](user-flow-versions.md) för att dra nytta av att skicka token som ett anspråk.

Azure AD B2C stöder för närvarande endast att skicka åtkomsttoken för OAuth 2.0-identitetsleverantörer, som inkluderar Facebook och Google. För alla andra identitetsleverantörer returneras anspråket tomt.

## <a name="validation"></a>Validering

För att validera en token bör ditt program kontrollera både signaturen och anspråken på token. Många bibliotek med öppen källkod är tillgängliga för validering av gemensamma tillsynsmaskiner, beroende på vilket språk du föredrar. Vi rekommenderar att du utforskar dessa alternativ i stället för att implementera din egen valideringslogik.

### <a name="validate-signature"></a>Verifiera signatur

En JVT innehåller tre segment, ett *huvud,* en *brödtext*och en *signatur*. Signatursegmentet kan användas för att verifiera äktheten av token så att den kan lita på ditt program. Azure AD B2C-token signeras med hjälp av asymmetriska krypteringsalgoritmer av branschstandard, till exempel RSA 256.

Tokenhuvudet innehåller information om nyckel- och krypteringsmetoden som används för att signera token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Värdet för **alg-anspråket** är den algoritm som användes för att signera token. Värdet för **barnanspråket** är den offentliga nyckel som användes för att signera token. Azure AD B2C kan när som helst signera en token med hjälp av något av en uppsättning offentliga-privata nyckelpar. Azure AD B2C roterar den möjliga uppsättningen nycklar med jämna mellanrum. Din ansökan ska skrivas för att hantera dessa nyckeländringar automatiskt. En rimlig frekvens för att söka efter uppdateringar av de offentliga nycklar som används av Azure AD B2C är var 24:e timme.

Azure AD B2C har en Endpoint för OpenID Connect-metadata. Med den här slutpunkten kan program begära information om Azure AD B2C vid körning. Den här informationen omfattar slutpunkter, tokeninnehåll och tokensigneringsnycklar. Din Azure AD B2C-klient innehåller ett JSON-metadatadokument för varje princip. Metadatadokumentet är ett JSON-objekt som innehåller flera användbara informationsdelar. Metadata innehåller **jwks_uri**, vilket ger platsen för den uppsättning offentliga nycklar som används för att signera token. Den platsen finns här, men det är bäst att hämta platsen dynamiskt med hjälp av metadatadokumentet och tolkning **jwks_uri:**

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
JSON-dokumentet som finns på den här webbadressen innehåller all offentlig nyckelinformation som används vid en viss tidpunkt. Din app kan `kid` använda anspråket i JWT-huvudet för att välja den offentliga nyckeln i JSON-dokumentet som används för att signera en viss token. Den kan sedan utföra signaturvalidering med rätt offentlig nyckel och den angivna algoritmen.

Metadatadokumentet `B2C_1_signupsignin1` för principen `contoso.onmicrosoft.com` i klienten finns på:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

För att avgöra vilken princip som användes för att signera en token (och vart du ska gå för att begära metadata) har du två alternativ. Först inkluderas principnamnet i `acr` anspråket i token. Du kan tolka anspråk ur kroppen av JWT genom bas-64 avkodning kroppen och deserializing JSON strängen som resultat. Anspråket `acr` är namnet på den princip som användes för att utfärda token. Det andra alternativet är att koda principen `state` i parameterns värde när du utfärdar begäran och sedan avkoda den för att avgöra vilken princip som användes. Båda metoderna är giltiga.

En beskrivning av hur du utför signaturverifiering ligger utanför dokumentets omfattning. Många bibliotek med öppen källkod är tillgängliga för att hjälpa dig att validera en token.

### <a name="validate-claims"></a>Validera anspråk

När dina program eller API tar emot en ID-token bör den också utföra flera kontroller mot anspråken i ID-token. Följande påståenden bör kontrolleras:

- **målgrupp** - Verifierar att ID-token var avsedd att ges till ditt program.
- **inte före** och **utgångsdatum** - Verifierar att ID-token inte har upphört att gälla.
- **utfärdare** - Verifierar att token har utfärdats till ditt program av Azure AD B2C.
- **nonce** - En strategi för token replay attack mitigation.

En fullständig lista över valideringar som ditt program ska utföra finns i [OpenID Connect-specifikationen](https://openid.net).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [använder åtkomsttoken](access-tokens.md).

